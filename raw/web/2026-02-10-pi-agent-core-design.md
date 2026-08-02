---
title: "下一代Agent架构——Pi Agent Core 设计逻辑深度解析"
source: "https://zhuanlan.zhihu.com/p/2004665077618458930"
author:
  - "[[王鹏LLM​]]"
published:
created: 2026-07-11
description: "项目地址： https://github.com/badlogic/pi-mono核心哲学: \"An autonomous agent is just an LLM + tools + a loop.\" — Mario Zechner, What I learned building an opinionated and minimal coding age…"
tags:
  - "clippings"
---
286 人赞同了该文章

项目地址： [github.com/badlogic/pi-](https://link.zhihu.com/?target=https%3A//github.com/badlogic/pi-mono)

> **核心哲学**: *"An autonomous agent is just an LLM + tools + a loop."*  
> — [Mario Zechner](https://zhida.zhihu.com/search?content_id=270225515&content_type=Article&match_order=1&q=Mario+Zechner&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3ODM5MzEzNjcsInEiOiJNYXJpbyBaZWNobmVyIiwiemhpZGFfc291cmNlIjoiZW50aXR5IiwiY29udGVudF9pZCI6MjcwMjI1NTE1LCJjb250ZW50X3R5cGUiOiJBcnRpY2xlIiwibWF0Y2hfb3JkZXIiOjEsInpkX3Rva2VuIjpudWxsfQ.WMBBJrL3PJGvnjHcJbS_8743O0unv92AG-xJlAAjbDM&zhida_source=entity), [What I learned building an opinionated and minimal coding agent](https://link.zhihu.com/?target=https%3A//mariozechner.at/posts/2025-11-30-pi-coding-agent/)

## 一、Pi 的反直觉立场

在当前 Agent 框架生态中，大多数项目在做 **加法** ：更多工具、更长提示词、更复杂的规划链、更多子 Agent。Pi 的创作者 Mario Zechner 认为这是一条弯路。他的核心论点是：

> *"前沿模型已经被 RL 训练得足够理解'编码 Agent'是什么。你不需要 10,000 token 的系统提示词。"*

这不是空谈。Pi 在 [Terminal-Bench 2.0](https://link.zhihu.com/?target=https%3A//github.com/laude-institute/terminal-bench) 上使用 [Claude Opus 4.5](https://zhida.zhihu.com/search?content_id=270225515&content_type=Article&match_order=1&q=Claude+Opus+4.5&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3ODM5MzEzNjcsInEiOiJDbGF1ZGUgT3B1cyA0LjUiLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoyNzAyMjU1MTUsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.ije7x7tl9B4_I_Ma9ZohhS9CoFBdH9thM7jQbMRqb-Q&zhida_source=entity) 进入了排行榜前列，与 Codex、Cursor、Windsurf 等拥有复杂工具链的 Agent 竞争——而 Pi 的系统提示词 + 工具定义加起来 **不到 1000 token** 。

| 对比维度 | Claude Code | Codex | Pi |
| --- | --- | --- | --- |
| 系统提示词 | 约 10,000+ tokens | 适中 | < 1,000 tokens |
| 内置工具数 | 数十个 | 适中 | 4 个 (read/write/edit/bash) |
| Plan Mode | 有（黑盒子 Agent） | 有 | 无（用文件代替） |
| MCP 支持 | 有 | 有 | 无（用 CLI 工具代替） |
| Sub-Agent | 有（不可观测） | — | 无（通过 bash 自我调用） |

## 二、架构分层：5 个文件构成的运行时

`pi-agent-core` 的全部源码只有 **5 个文件、约 1,500 行代码** ：

![](https://pica.zhimg.com/v2-a1828edb00961517a1010dbb4c343ae0_1440w.jpg)

下面逐层剖析每个核心模块的设计决策。

## 三、类型系统：少即是多

> 源码： [types.ts](file:///C:/Users/Administrator/Downloads/pi-mono-main/pi-mono-main/packages/agent/src/types.ts)

### 3.1 AgentMessage — 应用状态与模型上下文的分离

这是整个设计最精妙的抽象：

```
// 空接口 —— 通过 Declaration Merging 扩展
export interface CustomAgentMessages {
    // Empty by default - apps extend via declaration merging
}

// AgentMessage = LLM 消息 + 自定义消息
export type AgentMessage = Message | CustomAgentMessages[keyof CustomAgentMessages];
```

**为什么不直接用 LLM 的** **`Message`** **类型？** 因为真实应用中存在大量"非 LLM 消息"：

| 场景 | 消息类型 | 交给 LLM？ |
| --- | --- | --- |
| 用户提问 | UserMessage | ✅ |
| Agent 回复 | AssistantMessage | ✅ |
| 工具结果 | ToolResultMessage | ✅ |
| UI 通知 | { role: "notification" } | ❌ 过滤掉 |
| 文件变更事件 | { role: "artifact" } | ❌ 过滤掉 |
| 会话分支标记 | { role: "branch" } | ❌ 过滤掉 |

应用层通过 Declaration Merging 扩展自定义消息类型，编译器自动推断联合类型。内部所有逻辑（压缩、分支、UI 渲染）都基于 `AgentMessage` 运转， **仅在调用 LLM 的瞬间** 才通过 `convertToLlm()` 过滤为 `Message[]` 。

这就是 Zechner 所称的" **最晚转换** "（late conversion）策略。

### 3.2 AgentLoopConfig — 可插拔的行为注入

```
export interface AgentLoopConfig extends SimpleStreamOptions {
    model: Model<any>;
    convertToLlm: (messages: AgentMessage[]) => Message[] | Promise<Message[]>;
    transformContext?: (messages: AgentMessage[], signal?: AbortSignal) => Promise<AgentMessage[]>;
    getSteeringMessages?: () => Promise<AgentMessage[]>;
    getFollowUpMessages?: () => Promise<AgentMessage[]>;
    getApiKey?: (provider: string) => Promise<string | undefined> | string | undefined;
}
```

注意这里 **没有** `maxSteps` 、 `maxTokens` 、 `temperature` 等"常见"配置项（它们继承自 `SimpleStreamOptions` ，但循环本身不关心这些）。循环只关心两件事：

1. **如何把 AgentMessage 转为 LLM 能懂的格式** （ `convertToLlm` ）
2. **工具执行间隙有没有人要说话** （ `getSteeringMessages` / `getFollowUpMessages` ）

### 3.3 AgentEvent — 细粒度的生命周期事件

```
export type AgentEvent =
    | { type: "agent_start" }
    | { type: "agent_end"; messages: AgentMessage[] }
    | { type: "turn_start" }
    | { type: "turn_end"; message: AgentMessage; toolResults: ToolResultMessage[] }
    | { type: "message_start"; message: AgentMessage }
    | { type: "message_update"; message: AgentMessage; assistantMessageEvent: AssistantMessageEvent }
    | { type: "message_end"; message: AgentMessage }
    | { type: "tool_execution_start"; toolCallId: string; toolName: string; args: any }
    | { type: "tool_execution_update"; toolCallId: string; toolName: string; args: any; partialResult: any }
    | { type: "tool_execution_end"; toolCallId: string; toolName: string; result: any; isError: boolean };
```

三层嵌套的生命周期： **Agent > Turn > Message/Tool** 。每一层都有 start/end 事件，构成完整的可观测性——这正是 Zechner 反复强调的：

> *"Claude Code 的 Plan Mode 会 spawn 一个子 Agent，你对子 Agent 内部的运作零可见性。这是黑盒中的黑盒。"*

## 四、核心循环：双层 While 的精密设计

> 源码： [agent-loop.ts](file:///C:/Users/Administrator/Downloads/pi-mono-main/pi-mono-main/packages/agent/src/agent-loop.ts)

### 4.1 入口：agentLoop vs agentLoopContinue

两个入口函数体现了一个重要区分：

| 函数 | 用途 | 前置条件 |
| --- | --- | --- |
| agentLoop(prompts, context, config) | 用户发了新消息 | 可以从空上下文开始 |
| agentLoopContinue(context, config) | 重试/恢复 | 上下文最后一条非 assistant |

`agentLoopContinue` 的存在让 **重试** 变得优雅：出错后不需要重新构造 prompt，直接 `continue()` 即可从当前上下文继续。

### 4.2 双层循环结构

这是 `runLoop()` 的完整控制流——它是理解 Pi Agent 的关键：

![](https://pic4.zhimg.com/v2-eeff8a8422b0b8ac0d6ac183a027134f_1440w.jpg)

关键设计点：

**1\. 外层循环: FollowUp 驱动**

```
// agent-loop.ts L116-194
while (true) {
    // ... 内层循环处理 tool calls + steering ...

    // Agent 即将停止，检查 follow-up
    const followUpMessages = (await config.getFollowUpMessages?.()) || [];
    if (followUpMessages.length > 0) {
        pendingMessages = followUpMessages;
        continue; // 重启内层循环！
    }
    break; // 真正停止
}
```

**2\. 内层循环: ToolCall + Steering 驱动**

```
// agent-loop.ts L122-182
while (hasMoreToolCalls || pendingMessages.length > 0) {
    // 注入 pending messages
    // 流式调用 LLM
    // 执行工具
    // 检查 steering
}
```

**3\. Steering 中断的实现细节**

当用户在工具执行期间发送 steering message 时， `executeToolCalls` 会 **立即停止执行后续工具** ，将剩余工具全部标记为"Skipped due to queued user message"并返回错误结果：

```
// agent-loop.ts L363-374
if (getSteeringMessages) {
    const steering = await getSteeringMessages();
    if (steering.length > 0) {
        steeringMessages = steering;
        const remainingCalls = toolCalls.slice(index + 1);
        for (const skipped of remainingCalls) {
            results.push(skipToolCall(skipped, stream)); // "Skipped due to queued user message."
        }
        break;
    }
}
```

这意味着 LLM 会在下一轮看到：

- 已执行工具的正常结果
- 被跳过工具的错误结果（告知 LLM 这些工具被用户打断了）
- 用户的 steering message

LLM 因此能理解发生了什么并做出适当调整。

### 4.3 流式应答处理

`streamAssistantResponse()` 是 AgentMessage → Message 转换的唯一边界：

```
// agent-loop.ts L204-289
async function streamAssistantResponse(...) {
    // 1. 可选的上下文变换（剪枝、注入外部上下文）
    let messages = context.messages;
    if (config.transformContext) {
        messages = await config.transformContext(messages, signal);
    }

    // 2. 转换为 LLM 格式（这是唯一的转换点！）
    const llmMessages = await config.convertToLlm(messages);

    // 3. 动态解析 API Key（支持过期 OAuth Token）
    const resolvedApiKey = ...;

    // 4. 流式调用 LLM
    const response = await streamFunction(config.model, llmContext, { ... });

    // 5. 实时转发事件到 EventStream
    for await (const event of response) {
        // partial message 实时更新到 context.messages
        // 每个 delta 都通过 stream.push() 转发给 UI
    }
}
```

> \[!IMPORTANT\]  
> `partial message` 被 **就地更新** 到 `context.messages` 数组中（line 262: `context.messages[context.messages.length - 1] = partialMessage` ），这意味着上下文始终是"当前最新状态"。

## 五、Agent 类：状态容器 + 消息队列

> 源码： [agent.ts](file:///C:/Users/Administrator/Downloads/pi-mono-main/pi-mono-main/packages/agent/src/agent.ts)

### 5.1 两种队列模式

Agent 类引入了思考周到的队列机制：

```
// Steering mode: "all" = 一次性发送全部 | "one-at-a-time" = 每次只发一条
private steeringMode: "all" | "one-at-a-time";
private followUpMode: "all" | "one-at-a-time";
```

**为什么需要 one-at-a-time？** 考虑这个场景：用户在 Agent 工作时快速发送了 3 条修正。 `one-at-a-time` 模式让 Agent 逐条处理，每条都能得到充分响应，而不是一股脑收到 3 条然后只回应最后一条。

### 5.2 prompt() vs continue() vs steer() vs followUp()

![](https://picx.zhimg.com/v2-2d7d99d29ef5f67e2df32dc6074bf74d_1440w.jpg)

| 方法 | 调用时机 | 效果 |
| --- | --- | --- |
| prompt(msg) | Agent 空闲时 | 开一轮新对话 |
| continue() | Agent 空闲时 | 从当前状态续接（重试/消费队列） |
| steer(msg) | Agent 工作时 | 中断当前工具链，插入消息 |
| followUp(msg) | 任何时候 | 排队到 Agent 完成后执行 |
| abort() | Agent 工作时 | 取消当前 LLM 调用 |

### 5.3 错误恢复

Agent 类在 `catch` 中构造了一个完整的 `AssistantMessage` （带 `stopReason: "error"` ），确保错误状态也会成为上下文历史的一部分：

```
// agent.ts L497-518
catch (err: any) {
    const errorMsg: AgentMessage = {
        role: "assistant",
        content: [{ type: "text", text: "" }],
        stopReason: this.abortController?.signal.aborted ? "aborted" : "error",
        errorMessage: err?.message || String(err),
        // ... usage: all zeros
    };
    this.appendMessage(errorMsg);
}
```

这意味着用户可以 `continue()` 从错误处重试，LLM 会看到之前的错误信息并据此调整策略。

## 六、Proxy Stream：带宽优化的客户端重建

> 源码： [proxy.ts](file:///C:/Users/Administrator/Downloads/pi-mono-main/pi-mono-main/packages/agent/src/proxy.ts)

这个模块为 Web 应用场景设计（浏览器 → 代理服务器 → LLM）。核心创新是 **带宽优化** ：服务器不传输 `partial` 字段（完整的局部消息对象），仅传输轻量的 delta 事件：

![](https://pic4.zhimg.com/v2-0b5ad169c55cb377f39eaae6b9f6ca3b_1440w.jpg)

`ProxyAssistantMessageEvent` 比原始 `AssistantMessageEvent` 轻得多——没有 `partial` 字段，仅包含 `contentIndex` + `delta` 等最小信息。客户端通过 `processProxyEvent()` 逐步重建完整消息。

## 七、设计哲学总结

### 7.1 "不做什么"比"做什么"更重要

| 刻意不做 | 理由 |
| --- | --- |
| 无 Plan Mode | 用文件 PLAN.md 替代。有完整可观测性，可版本控制，可跨会话共享 |
| 无 MCP 支持 | MCP 工具描述占 7-9% 上下文窗口。用 CLI + README 通过 bash 调用，按需加载 |
| 无 Sub-Agent | "黑盒中的黑盒"，失去可观测性。通过 bash 自我调用，保留完整输出可见性 |
| 无 maxSteps | 循环自然结束。"我从来没找到需要 maxSteps 的用例，所以为什么要加？" |
| 无权限检查 | "安全措施大多是安全剧场。一旦 Agent 能写代码和运行代码，就 game over。" |

### 7.2 核心设计原则

![](https://pica.zhimg.com/v2-69cf56b455b11001fbb172e0375dc3de_1440w.jpg)

Pi Agent设计原则极简主义可观测性可干预性最晚转换自我进化&lt; 1000 token 系统提示4 个核心工具5 个源文件三层事件生命周期所有工具执行可见拒绝黑盒子 AgentSteering 中断FollowUp 排队Abort 取消内部用 AgentMessage仅 LLM 边界转换自定义消息不泄露通过 bash 自我调用运行时动态扩展不依赖预制 Skills

### 7.3 写给开发者：何时借鉴 Pi 的设计

- ✅ **你的 Agent 需要高可观测性** → 学习 Pi 的三层事件系统
- ✅ **你在构建编码/CLI Agent** → 学习"4 工具 + bash"的极简策略
- ✅ **你需要跨 Provider 会话迁移** → 学习 `convertToLlm` 的最晚转换模式
- ✅ **你需要用户中途干预** → 学习 Steering/FollowUp 双队列机制
- ❌ **你需要复杂的多 Agent 编排** → Pi 的设计理念与此相悖
- ❌ **你需要严格的安全沙箱** → Pi 明确选择了"YOLO by default"

编辑于 2026-02-10 21:30・湖北

赞同 286