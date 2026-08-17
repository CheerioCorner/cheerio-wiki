---
title: "AI Agent Memory Systems - 完整解析"
type: raw-transcript
created: 2025-07-25
video_id: 072eNztI06k
url: https://www.youtube.com/watch?v=072eNztI06k
duration: 00:30:26
language: zh
auto_generated: false
source_api: youtube_transcript_api
segments: 122
---

# AI Agent Memory Systems - 完整解析

> Duration: 00:30:26 | Segments: 122 | Language: zh | Source: API (en-US manual)

## Transcript

[00:00] 大家好，我是肖恩 最近，记忆在AI智能体系统中非常流行 而这是因为 任何LLM调用都不会长期携带任何记忆权重 为什么你的Chat GPT和CLAUDE CODE能记住你的聊天内容？

[00:13] 你从过去谈论的内容 是因为它已经制作了这个 为其AI智能体 harness构建的记忆系统 今天我们要讨论 五种不同的方式来设计你的架构 构建的 harness 周围的 AI 记忆 在之前，你已经看过我们制作的视频 这是我们的智能体harness，称为Waku Agent 它基本上运行了这个智能体，该智能体有一个循环供智能体 调用工具，将任务分配给子智能体 同时，它还会准备 这是一个工作记忆，从中提取信息 从三个主要支柱中，其中一个是程序性记忆 在我们的案例中，这是一种技能 语义记忆，也就是一些 持久的事实和情节记忆，这是一些过去的时间的事件 这和爱马仕智能体的方式类似 设计了自己的记忆系统 但如果你真的想构建智能体， 我相信你已经被大量信息淹没 例如，rags、agentic rag、graph rag等 回调生成一直存在 但没有检索或向量嵌入 爱马仕和Waku智能体 我们将告诉你两者之间的区别 因为这两个系统是分开的 有两种不同的记忆检索方式 我希望这对你有所帮助，因为它 因为最后都取决于你 什么类型的记忆系统？

[01:24] 最重要的是适用于你的使用场景 然后你应该自己做出决定 如果你想尝试所有这些智能体 harness和记忆系统等一切 我们构建了一个开源项目，名为 ShenSeanChen/Waku-agent 我们最近获得了超过一千三百个星星 感谢大家的支持 我们非常欢迎您加入这个repo并贡献 使用方法非常简单 你可以通过pip安装Waku Agent 或者你可以git clone 这份仓库，然后开始工作吧嗯 一旦你开始运行它，你就会有一个这样的智能体 你可以问它问题，就像“我的……” 今天的日历 如果你的日历设置正确，你就知道 它会先通过检索门 然后你可以使用如列表中的工具 这是一个完整的，可以用来检查日历和其他相关信息的工具 智能体harness 可以让你 尝试你的记忆系统 以及你自己构建的工具 完成跟踪后，然后 给我们答案 我们几乎完成了一次智能体运行 我们也建立了这个竞技场 用于记忆系统 比较几个不同的记忆层 市场上的选项，我们稍后会给你看 我们将向您展示所有的代码 在视频的后半部分，我们将展示实现方式 现在让我们回到系统设计 先解释概念 首先是第一件事 以下是我对智能体记忆的看法 我首先会考虑三支主柱

[02:40] 什么是它？如何找到它？如何维护它？

[02:44] 通常是智能体记忆 将以三种方式存储 第一种是文本或Markdown文件 就像你的memory.MD一样 例如，如果你来到爱马仕 只需问任何问题，并说“嗨” 然后你会看到你的根目录 如果你往下滚动，会看到一个名为Dot Hermes的文件 就在这里 当你往下滚动时，可以发现 然后你会看到一个名为“记忆”的文件夹 然后双击这个memory.MD 这里会显示你的记忆 好的，这只是纯文本，它将被...

[03:14] 它将被输入到上下文窗口中 每当你提出任何问题时 在Waku Agent中，如果你克隆这个存储库，也是如此 在你提出一些初始问题后 你将能够找到这个名为Dot Waku的文件夹 然后你可以找到SOUL.MD 这是你的人工智能智能体记忆的系统提示 所以基本上 它可以只是纯文本，也可以 如果事情变得复杂 可以将其存储在表格中，类似于电子表格 或者像谷歌表格 有行和列 这些东西就像一个Excel表格 最后但并非最少 我们也可以用节点来存储信息的图形 以及边界 对于不熟悉图表的人来说 它本质上是建立联系的方式 信息之间 我发现了这个非常酷的工具，叫做Zep 他们有这个关系图 当你使用这个后 存储信息的记忆层 它会为你绘制这些，比如说 让我们看一下，假设这个创始人叫阿历克斯 Alex去过里斯本AI聚会 对于他的机器人初创公司和 你可以看到这些信息都在 互联 所以它开始于一个名为Quick Star Z3的用户名 然后Alex同样有一个产品发布日 这些是在五月 他们推出了产品 也有一个人，他在其中，他在其中，所以 所以，图谱只是一种存储信息的方式 帮助你发现信息之间的联系 我认为这是三种主要的存储方式

[04:42] 记忆，但人们可能会问 向量嵌入式在检索增强生成中的应用 你使用的是哪种向量库，这决定了你的结果 如果你像我一样使用Supabase PGVector 它将仍然在表中的行中，但然后...

[04:55] 你知道一些列 将要成为向量 或者你可以将其存储在无序列数据库中 所以这就是它吗？

[05:02] 现在我们来谈谈我们的智能体是如何进行的 了解它的方法是怎样的？好的 AI 智能体找到记忆的方式有四种 第一种方法就是不做任何事 还记得我们之前展示的memory.MD吗 对于爱马仕来说，基本就是不做任何事情，因为 因为如果memory.md不是太长 它应该被LLM默认读取，这样 无论如何，它总是在上下文中 记住如果你使用CLAUDE CODE 你可以看到文本的百分比 你是否已经使用过，有时会很大 原因是因为一些记忆 它们会被预加载在那里，当然了 有很多你知道的工具调用 定义，MCP定义，嗯 你的SOUL.MD是系统提示 对于智能体所扮演的角色 除此之外，我们还可以使用关键词 这是一个东西 SQLITE有一个标准，叫做FTS5 你需要知道的基本就是 它正在进行关键词搜索，好吗？

[05:58] 在爱马仕的例子 如果你去，如果你来到爱马仕 再往下滚动，同一个文件夹里有 叫做state. db的东西 如果你打开它，你可以看到一个sqlite 这里有一个schema 好的，关键词搜索就是基本的 给定这些state.db 我们只需搜索信息的关键词 第三个是增强生成的获取 这是基本的 检查两个词之间的相似性 假设你正在问关于...的问题 我最喜欢的食物 食物这个词 可能被向量嵌入到空间的向量中 你知道1024乘以1，然后 然后它就会计算 还有哪些词？

[06:39] 在同一个维度中向量嵌入的 在我们的数据库中 与食物有很高的相似性 可能会是苹果，也可能会是香肠 等等，好的 我们正在尝试进行语义搜索 而不是只使用关键词 最后但同样重要的是Graph RAG Graph RAG的作用非常简单，请记住 记得我之前给你介绍过图表吗？

[06:59] 它做的就是基本的 它仍在向量嵌入信息 但向量嵌入了节点之类的东西 好的，可能是产品发布日期 它还向量嵌入了那个边，就像 这是观察的一部分吗？

[07:12] 或者这是一个关系？好的，和 这些都将被放入包裹中 相似搜索会直接找到 你知道什么问题与你问的相似吗？

[07:21] 用户提出的问题，然后最终 它可能会在这里返回一个关系图 让上下文更丰富，让智能体有更多的上下文 关于关系中的记忆 那么现在我们如何维护它呢？

[07:31] 我将此总结为四点 维护记忆系统的主要方式 第一点是，你必须做出决定，那就是 我们是否添加，我们是否删除？

[07:42] 我们是否覆盖了一些之前的信息？

[07:44] 我们不做任何操作吗？这个意思是不进行操作 还是我们要淘汰一些信息 这与删除不同 因为我们不删除它，而是使其无效 之前的信息在某个时间范围内 那是什么意思？

[07:58] 一个月前，我们推出了Waku Agent，它得到了 在前二十五天获得一千颗星星，好吧 然后第二天，我告诉了智能体 今天是第26天，而Waku的智能体...

[08:08] 得到了一千三百个星星，所以增加了 在一天内增加了30%，然后应该发生什么呢？

[08:15] 是的，而不是删除这个事实 这个Github存储库 在25天内获得了1000个星星，这仍然是 重要信息，可能只需...

[08:25] 验证并说：“嘿，这是最新的” 星星已达1300，可追溯 回到前一天，也就是一千颗星星 这些上下文仍然很重要 为了将来的追踪 第三个叫做属性 在人类语言中，这意味着它基本上是 追踪源头，好的 可能我从用户那里得到了记忆 与你的智能体沟通 或者我可能是从网络搜索中获取的 或者是从某个日历搜索中得到的，对吧？

[08:56] 源头取决于什么 最后但并非最少，我们谈论的是反思 这是 它将删除或合并部分重复数据 或者一些过去的时间的信息 我想说，这有点像删除和 类似于退休，但有时“反思”意味着 但反思 有时可以与Anthropic提出的有关联 这就是做梦 当我们不工作时，会有梦 当智能体运行时，它就像忙碌地积累 事实和 episodic 记忆积累 将过程转化为技能 但当它不运行时 我们可能可以安排一些任务让它进行 让我们回顾我们收集的所有内容 并合并一些重复的内容 或者丢弃一些不重要的东西 不再重要了 所以它类似于一个后事或后处理过程 更新或维护记忆系统的步骤 你们还跟我在一起吗？好的，继续吧 现在让我们看看一些不同的类型 关于实际产品的记忆系统 让我在这里添加向量存储 首先，首先 存储记忆的纯文本方式 例子包括Hermes和Waku Agent 我已经向你展示了灵魂，我已经展示了灵魂 之前的文本，它是系统提示 你可以直接编辑它 如果我们回到 如果我们查看爱马仕的灵魂， 你可以看到我的爱马仕有个性 你应该总是像皮卡丘那样说话 它应该一直说皮卡皮 还有一些变体 你可以说“pika”或“pika pika”或“pika pi”

[10:25] 对，而“pikapi”是用来和我交流的 直接称呼我为“皮卡皮” 因为在动画中 皮卡丘也叫小智Pikapi 你可以将这个编辑成任何其他内容，对吧？所以 所以理解和技能都很简单 例如，技能是一个程序 研究所有互联网上的AI视频 然后每天早上告诉我 最新的新闻有哪些，肖恩的故事？

[10:50] 下一期视频，肖恩AI故事应该涵盖 对，这是一个流程，也是一种技能 通常情况下，它会被触发加载 好的，所以如果我问， 我的智能体会说：“嘿，触发那个技能” 肖恩的人工智能帖子，然后它应该 让它跑，然后给我拿来 我应该随时可以自行编辑它 memory.MD这是一些持久的事实 你应该保留的 在与这样的智能体交谈时，积累 这是从之前的 视频展示如何构建 真实代码中的智能体 harness 我们构建语义记忆的方式 也就是基本的记忆点MD吧 每次和智能体进行对话时 从用户提示到LLM到回复，它应该 它应该将历史保存到state.db中 这是我们的SQLITE数据库 然后应该整合 每次你知道之后的信息 说五次或十次对话 使用一些更便宜的辅助模型 然后它应该提取事实 进入memory.MD 这些记忆通常是在上下文中加载的 因为这很重要 但也可以让它可检索 你可以构建一个检索门 就像我们在Waku智能体中做的那样 你应该决定是否让它引发 调用一些技能 唤醒一些语义记忆和其他东西 是的，不需要构建 总是在那里，但如果你想的话，你可以这么做 记住，记住，智能体 harness 非常灵活 你无需遵循任何标准，可以自己定义

[12:11] 你可以自行制作你的护甲，最终会 什么是永恒的，什么是至关重要的？

[12:17] 最后，最重要的是你的记忆 如果你积累这些记忆，然后 妥善保存并准备好 妥善保管它们，然后照顾它们 那么这些记忆就是最宝贵的资产 对于任何人工智能智能体马具 我们是否应该有一个检索门？

[12:30] 这完全取决于你，好吗？

[12:32] 最后但并非最少 请记住，我们刚刚向您展示了state. db 这是sqlite数据库 它是一个包含行的关系数据库 你正在进行关键词搜索 如何找到它的方法 然后我们维护它的方式是，你可以直接修改它 你自己就好 如果你点击这里，就像这样 是的，你可以直接写入状态数据库 或者你可以等待智能体 将其整合到 harness 中 就像我在Waku Agent中展示的那样，合并 在实际代码中，我们也有一个合并 在这个模型中，你可以点击进入 你可以在合并中看到发生了什么 在过去的历史中，好的 这是纯文本风格 还有什么呢？

[13:13] 我们有Supabase 维埃特松籽有向量存储，而Supabase则是关系型数据库 是一个关系型数据库，所以它技术上 不是一个向量存储，但他们有一个 SQLITE扩展称为PG Vector 这实际上是一个可使用的向量存储库 通常，向量存储器存储信息以向量形式 如我之前提到的，它将向量嵌入 将每个单词转换为高维度的 带有数字的向量，因为 计算机不能处理它们只能处理的单词 他们只能处理 数字或更高维度的数字 这就是为什么向量非常有用 然后你可以存储 一些元数据，例如这个向量是什么？

[13:54] 真的意味着什么，还有一些什么？

[13:55] 其他信息也应随之携带，好吗？

[13:58] 我们找到它的方式是这样的： 相似搜索 记得之前我们说要把一个数字放上去，对吧？

[14:05] 将高维数值向量放置在“食物”这个词上 然后你会搜索 你知道空间相似性吗？

[14:12] 食物和苹果和香肠之间的空间距离 然后从我们的储存中取出那些食物 在我们的记忆中，好吗？

[14:18] 我们维护它的方式是，你基本上 只需从中插入或删除信息 但你可以让它稍微...

[14:24] 如果你有一个向量嵌入，你就会更聪明，你知道 如果你有一个向量 存储在你的智能体 harness 中 你可以设计它以这样的方式，使得 类似于我们的合并方式， 我之前展示的状态DB模块 但它实际上适用于向量存储 现在我们正在查看一些记忆工具 在市场上 mem0是一个有趣的例子 他们有两种存储记忆的方式 一种称为行记忆 另一种叫做图形记忆 行记忆的作用是，我将展示一个示例 这是我在mem0中的一个门户 然后他们有一个名为记忆的选项卡 如果我单击其中一个， 这一条说我们有一位德国买家 这位德国买家要求所有产品都要有素食认证 他们售出的每一个SKU上都有这个这只是一个 这就是基本的 仅仅是一个语义记忆，类似于永恒的事实 这将永久存在，如果你有智能体 你可以向他们提出问题 它应该从Meme Zero中获取信息 记忆层，我一点也不知道 我不知道，我不知道他们是否这样做了 在这里进行简单的关键字搜索或进行一些向量嵌入或RAG 因为我是说，是的，他们是开源的 但这是企业版，所以我不确定 他们实际在幕后使用的是哪一个？

[15:30] 对于行记忆，您维护它的方式基本上是 很简单，你只需添加、更新、删除 或者不做任何事，或者被替换被替换就是说， 取代基本上是 我们之前提到的，就是不要删除 信息，但你让前一个失效 过去的时间了，你说，“我们有一千三百个星星” 对于Waku Agent，现在不再是一千星星了 但我们不想要删除事实，即 在前二十五天，它获得了一千颗星星 mem0也有图形记忆，也有图形记忆 图，记住我们说过的话 它基本上是节点和边 然后它可以用向量存储东西 行记忆也被存储 抱歉，我忘记了关于它的 两者都存储在引擎中，并将进行检查 遍历，这意味着你将要 检查整个图表获取信息 它也可以添加、更新、删除和无操作 我不太确定它是否会“取代”，也许它也会 嗯 对于这些问题，你应该查看源代码 不幸的是，mem0的图形功能 需要我升级到他们的专业版，但我不会这么做 因为我找到了一个名为Zep的替代工具 记得我之前给你看的图表吗？这就是他们的图表 所以Zep所做的是 一个名为“时空”的记忆 图形记忆，这意味着这个图 正在随着时间演变 好的，它有节点，有边，还有...

[16:47] 他们声称的有效性 你如何找到这些信息？

[16:52] 使用向量搜索对边和顶点进行搜索 遍历整个图谱 不同之处在于，这里可以进行重叠 在某个时间范围内进行验证，类似于超出 就像替换一样，你永远不会删除它，但历史会在这里保留下来 如果你查看Zep，它基本就是这样 默认使用图表 你可以查看之前的聊天记录 你可以查看线程和其他相关内容，好吗？

[17:20] 马上我会给你展示一些实例 在一瞬间，将所有这些记忆层都展示给你看 最后但并非最少，我们得到了 LangChain记忆，叫做LangMem LangMem只是LangMem，好吧，没有数据存储 它基本上是一个包裹 允许你本地存储的 你正在使用的是自己的数据存储搜索 你正在提取和解决，你知道 在写入任何东西之前，更新存储器 这就是对...的高层概述 对于AI智能体的记忆层，我 我和我，我之前已经解释过 你应该选择适合你自己智能体的 harness 和 你应该好好照顾它 你应该好好照顾它 我们正在开发一些记忆层，适用于我们的AI助手 我们目前正在为Agent工作 正在开发更多全面的 请关注我们，我们正在开发新的记忆层 如果你感兴趣 请在我的主页上发表评论或加入我们的社区seanchen.io 如果你想参与 在这或尝试早期版本 Waku Memory Agent层 你可以在seanchen.io来我们的社区 在这里，你可以加入 我们的社区，我会在这里回答问题 每两周我都会回复问题 每两周和我们的社区一起生活 或者你可以直接加入我们的，或者你可以直接点击 可以通过任何我的社交媒体与我们联系 让我们现在开始测试，让我们开始测试

[18:23] 再次来到Waku Agent的GitHub仓库 你可以在终端中运行此命令以安装pip 或者你可以直接克隆它 UV 运行 Waku 仪表板将带你到 将带你到智能体 harness 这种视觉化 在竞技场部分 我们构建了一个名为“记忆比赛”的东西 我们处理记忆竞争的方式是这样的： 我们有一组问题 例如，如果我选择晚宴 通过一些事实和问题 当我稍微缩放时，我可以看到 我可以看到我要告诉的内容 我要告诉这些记忆层的内容 如果你来，你会听到他们被问到什么 它会说 我要对这些记忆层说什么 例如，黄仁勋将来 上次他把我的辣椒油倒了 在白色地毯上 而埃隆·马斯克也来了，他说他会来的 他会在七点到达，还有汤姆·霍兰德 蜘蛛侠也在名单上，他告诉我了他下一部电影的结局 上周二过咖啡 记住，汤姆·霍兰德永远不能保密 关于未发表的 这基本上是我想讲述的记忆 这些记忆层 还有一些示例问题 对，如果问到黄仁勋，它会说，“好的，他” 他基本上把我的辣椒油打掉了 如果我问它有关我问的问题 关于保罗·格雷姆欠我多少钱 在中文中，保罗·格雷厄姆欠我20美元，因为 因为这里写着他欠我二十英镑 我们在里斯本遇到的酸面团 “Quid”是说“磅”的方式

[19:51] 在英国，因为保罗·格雷厄姆是英国人 底部可以看到我们列出了一些 我们列出了一些记忆数据存储 包括SQLITE mem0 LangMem Zep对照组 我不做Supabase 是的，因为我们这里不测试向量嵌入式 如果你对RAGs和嵌入感兴趣 请查看我的之前的视频，在 智能体Rags和RAG它们都是 在这个频道里，你可以查找这些视频 好的，我可以点击“读取存储” 检查当前的记忆 目前日期上，它基本空空如也 因为我已经清理了其他的记忆 你之前看到的只是一些遗产 从之前的版本 现在我要做的是点击“问” 如果我往下滑动，会有五家店 可以看到，我们现在正在告诉这些事实 对每一个记忆，好的，控制 成为一个没有记忆的人 让我们看看是否真的有效 如果我们问同样的问题给对照组 这是一个播种过程，我们要问 我们要告诉 将这些事实传送给每个记忆层 之后，它会问问题 并观察他们的反应速度，好吗？因为写作...

[20:59] 需要一些时间，我就先...

[21:00] 暂停一下，我们稍后再来这个 稍后 我要向你展示如何使用 如何在普通代码中使用这些记忆层 如果你来了Waku智能体 我们可以查看名为例子的文件夹，好吗？

[21:14] 我们打开示例，发现有一个名为 Memory Native的文件夹，这里我们有一个langmem本地 这是LangChain记忆 如果你往下滚到行42， 我们添加了一些事实 就像我在里斯本AI聚会上遇到了阿历克斯 产品发布计划在五月 实际上，发布日期已经推迟到了六月 记住，这是替代的 就像更新之前的信息一样 不删除它，但基本已过去的时间 还有一些问题，产品是什么？

[21:46] 发布什么日期推迟发布日期？还有一个问题，什么是这个产品？

[21:49] “发布会是什么时候”的中文 让我们看看它是否真的有效 然后，你可以看到我们正在创建 这个记忆管理器和这个管理器 然后对于每一个事实 我们要启动一个对话，好吗？

[22:06] 嗯，另一个例子是mem0原生的 如果我们点击并稍微滚动一下，就可以看到它的内容 你可以看到我们有相同的事实和问题 对于mem0，您需要先创建一个记忆客户端端 然后对于每一个事实 我们可以进行客户端添加，也就是写入 内存到mem0 然后，你可以用它来测试 类似的，有些真实问题和搜索结果 我们这里有一个本地的suabase 相同的过程，但 对于Supabase，你需要做一些向量嵌入 之后，你会进行检索 使用向量嵌入 最后，但同样重要的是，我们与Zep合作 我们再次输入相同的问题和事实，我们已经 从Zep构建客户端，对于每一个事实 Zep在这里略有不同 因为它是默认的时间图记忆 对于每个客户端 对于每个图，你要添加事实，好吗？

[23:02] 然后它会帮你构建图谱 好的，然后你可以使用...

[23:09] 搜索客户端图谱来查找结果 使用这个查询找到结果 好的，试一试吧 好的，回到这里，我们可以看到除了Zep 其他人都完成了工作 嗯，让我们看看 放大它，黄仁勋什么时候敲门？

[23:29] 黄仁勋敲了我的地毯什么 而英伟达应该为此承担责任 它通过了所有这些记忆中的每一个，除了 Zep正在构建图表，花了时间 我不知道，但我觉得是因为 构建图表需要时间 好吧，可能就是因为这个 SQLITE花了4.5秒，花了4.6秒 答案正确，黄仁勋打翻了 辣椒油放在白地毯上，mem0只是嗯 说辣椒油非常快，非常直接 但比sqlite花的时间稍长 而LangChain LangMem花了最长的时间 七点五秒 Zep还在等待，这太慢了，而且...

[24:14] 对组绝对 对这个没有任何信息 答案是正确的 因为不应该，我没有任何记录 对，没错 当我问了“保罗多少？”这个问题时， 格雷厄姆欠我20英镑 sqlite回答正确 英文中，它花了10.3秒我不知道 也许是因为呃sqlite 太简单了 对于关键词搜索，所以它实际上不太...

[24:37] 搜寻能力在中文中是有问题的 因为记忆是英文的 但似乎mem0得到了它，然后它说 用中文回复我，它说Paul Graham仍然欠我20英镑 他在和我打赌时输掉了 在里斯本的面包店，就是这样 嗯，LangMem也有一个正确的答案 控制组也没有任何东西 当埃隆什么时候到达这里？

[25:05] 应该是晚上九点，而不是晚上七点 为什么是这样呢？

[25:11] 哦，因为有更新，你看，关于埃隆的更新 他不能在七点钟到达，而是在九点钟 所以我们正在进行一些覆盖或替换 是的，因为 可能这不是覆盖 这是替代的，因为，嗯 它应该保留之前的信息 但让它看起来有点过去的时间 如果你往下滑动，就可以看到 三个人都回答正确 我的对照组说没有任何活动 在日历上和Elon一起，所以它搜索 记忆和工具使用 查看日历，没发生什么好的 酷哥们 如果我再点击“阅读存储” 你可以看到SQLITE mem0和呃 也有一些记忆了 LangMem什么都没有，因为它是一个包裹 控制组是控制组，点击“查看所有”可以看到所有的记录 你可以看到所有的记忆，所以 您也可以在这些平台中找到它们 如果你来查看记忆，你会看到很多 好的，这是我们六分钟前运行的内容 对于这些持久的事实和Zep 我们也应该能看到它们嗯，让我们看看 在Zep上到底发生了什么是非常不直观的 我真的不知道去哪里找它们 好的，我认为在用户中，每次我执行智能体任务时都会产生新用户 它正在创建一个新用户，我可能应该这样做 点击这个新的，查看图表 好的，很好，它来自Waku Agent Arena 它知道酸面团赌注 好吧，保罗·格雷厄姆欠我20英镑

[26:49] 它在里斯本，好的，很好，埃隆（实际上是黄仁勋）基本上 把辣椒油倒了 在我的白色地毯上，你可以看到它正在构建图表 这很酷，但花了一些时间 really long time 我勒个天 但花了我勒个天 正在播种它，哦我的天啊 是的，可能正在保存数据 使用时间图表是痛苦的，因为它...

[27:10] 延误了这么久，但 但我认为这个关系 用图谱、节点和边，可能还是值得的嗯 我们还在等待Zep的时候 让我们看看我的主网站seanchen.io 每两周我都会举办一次直播

[27:32] 在这个Whop社区 如果你加入我们，我就能回答你的问题 你的问题 在我们的Discord频道里进行直播，所以之前的...

[27:40] 在上次社群交流上，有人问我问题 关于人们问我问题 关于我们的系统设计 他们有一些实现 或者部署问题，关于你知道Waku智能体 和爱马仕智能体、派智能体 如果你感兴趣，欢迎加入我们 感兴趣与我们交谈吗？

[27:55] 欢迎加入我们，谢谢 回到Zep，终于开始提问了 等待，等待，好了 让我们回到Zep，再看一下它的图表 查看图表 汤姆·霍兰德在这儿，他的下一部电影 哦，你看，这个边带信息 因为汤姆·霍兰德和他的下一部电影 这没什么意义，但看看这个边 这个边缘是说揭示的结局 所以汤姆·霍兰德泄露了他下一部电影的结局 所以它帮我做了一些总结 好的，然后保罗·格雷厄姆是一个节点 程序员欠我二十英镑，他还欠 Waku Agent Arena，我不明白，但在这里 你知道的，有人把东西掉在你知道的地方 有人把辣椒油倒在了我的白色地毯上 好的 对辣椒油不太确定 弄脏了白石，但上面没有写埃隆（实际上是黄仁勋） 我还不太确定它是否完成了任务 好的，但我不确定，它看起来有点聪明 它构建了一个草图，但是的，它是一个 嗯，我觉得这里丢失了一些信息 而且它花了很久 也许对于许多这些较小的用途来说，这是一种杀鸡用牛刀了 案件 这就是为什么我认为爱马仕 和Pi Agent都试图让事情变得非常简单 它只会，你知道，让事情变得 让每个人都更容易 开始并不会花费太多时间 好吧，我有点失去耐心了 哇，终于

[29:32] 问完问题，黄仁勋放下辣椒油

[29:38] 好的，现在终于提问了 之前，大家都在等着看 四点九秒 四点九秒 六点四秒 好吧，Zep太慢了，我觉得 我已经失去了耐心 我会保持这种状态，泽普 团队，如果你们在看 我认为这是一个很大的痛点，我喜欢你们的产品 喜欢你的可视化，但请加快速度 或者至少对其进行一些处理，以提高空间利用率 简化任务速度更快 好的，这是一个 快速概述五种方法，我们如何创作 AI 智能体的记忆 我希望这对你有所帮助 如果你有任何问题，请随时提问 欢迎在评论区留言，并加入我们的社区 并在Github上给我们一颗星 并尝试我们的Waku Agent在您的实现中 谢谢，非常感谢下次见 谢谢 hey everyone this is Sean so memory is a super popular term in AI agent systems recently and the reason for that is that any LLM call does not carry any memory weight for long terms the reason why your chat GPT and Claude remembers

[00:13] what you talked about from the past is because it has already crafted this memory system for their own AI agent harness today we're gonna cover 5 different ways of how you're gonna architect a AI agent memory around the harness you build in previous you have watched our videos we have created this our agent harness called Waku Agent it basically has this agent run that has a loop for the agent to call tools delegate some agents for tasks and at the same time it's gonna prepare this working memory with retrieving from

[00:42] from three main pillars which is procedural memories and in our case it's a skill semantic memory which is some durable facts and episodic memory which is some dated events and that was pretty much similar to how Hermes agents crafted their own memory system but if you're serious about building agents I'm sure you have been bombarded with information such as rags agentic rag graph rag retrieval algorithm generation has always been there but there's no retrievals or embeddings in Hermes and Waku agent

[01:13] we're gonna tell you the difference between these two separate systems because there are two different ways of retrieving memories and I hope this would be helpful for you because it because eventually it depends on you what kind of memory systems matters the most to your use case and then you should make a decision by yourself and if you wanna try out all these agent harness and memory systems and everything we have built a open source project called ShenSeanChen/Waku-agent and we recently received more than 1.3 thousand stars

[01:41] so thanks everyone for your support and we would love to have you to contribute to this repo and the way to use it is very simple you can either pip install Waku Agent or you can git clone this repo and then start working on it um once you start running it you will have an agent like this and you can ask it questions and be like “what is on my ...” calendar today if you have your calendar you know properly set up and it's gonna pass through the retrieval gate before and then you know use the tools like list of

[02:07] events to check out calendar and all these kind of stuff so this is a proper agent harness that will allow you to play around with your memory system and tools that you build for yourself after finishing the tracing and then giving us back the answers we're pretty much done for the one Agent run and and we also built up this arena for memory systems to compare a few different memory layers options on the market and we're gonna show you all the code and implementations at the latter half of the video

[02:30] for now let's jump back to the system design for the concept explanations first first things first here's how I would think about Agent memories I would think about three main pillars first what is it how to find it and how do we maintain it normally an agent memory will be stored in three different ways the first one is text or markdown file just like your memory.MD for example if you come to Hermes and just ask any questions and say hi and then you will see your root directory and if you scroll down to Dot Hermes

[03:02] right here and then you scroll down you can find out that and then you'll see a folder called memory and then double click on this memory.MD it will show you the memories right here okay so this is just plain text and it's going to be fed into the context window whenever you ask any questions in and similarly in Waku Agent if you clone this repo after you ask after you ask some first questions you will be able to find this folder called Dot Waku and then you can find things like SOUL.MD which is a system prompt for your AI agent memory

[03:33] so basically it can be just in plain text or if things get more complicated it can store them in a table like a spreadsheet or like a Google sheet with rows and columns all these kind of stuff just like an Excel sheet and last but not least we can also store information in a graph with nodes and edges and for people who are not familiar with graphs it's basically a way to build up connections between information and I found this really cool tool called Zep and they have this relational graph that after you use this

[04:02] uh memory layer to store information it will plot this out for you for example let's take a look uh let's say this founder called Alex and Alex has been to this Lisbon AI meet up event and for his robotics startup and and you can see that these information are all interconnected so it started with this username called Quick Star Z3 and then Alex similarly has a product launch day and those were in say in may they had a product launch in June also had a product launch so graph is just a way to store information that

[04:37] helps you find connections between infos and I think these are the three major ways of storing memories but people might be asking where is embedding for retrieval augmented generation it depends on what vector stores you're using if you're using Supabase PGVector like myself it's gonna be still in rows in the table but then you know some of the columns are gonna be are gonna be vectors or you can store them in a Nosql database so that's what is it right and then now it comes to how does our agent

[05:04] find out about it there are four ways for AI agent to find the memories the first one is do nothing remember the memory.MD that we showed you earlier for Hermes that's basically do nothing because because if the memory.md is not too long it's supposed to be read by default by the LLMs so that it's always in the context anyways remember if you use CLAUDE CODE you can see how much percentage of the context window have you used and sometimes it's huge and the reason is because some of the memories are just gonna be preloaded there and also of course

[05:36] there are a bunch of you know tool calls definitions MCP definitions um your SOUL.MD which is the system prompt for the role that the agent is playing other than that we can also do keyword which is something that SQLITE has which is a standard called FTS5 all you need to know is basically it's doing keyword searching okay and an example in Hermes is that if you go if you come to Hermes and you scroll down again the same folder there's something called state.db and if you open that you can see a sqlite

[06:09] schema here alright and the and the keyword search is basically given these state.db we're just gonna search the keywords of the information and the third one is for retrieval augmented generation it's something that it's basically checking the similarity between two words say that say let's let's say you're asking a question regarding my favorite food and the word food might be embedded into a vector of space you know 1,024 by 1 and then and then it's gonna just calculate what other words that are embedded in the same dimensions

[06:41] in our database have a high similarity to food maybe it's gonna be apple maybe it's gonna be sausages or so on and so forth okay so what we're trying to do is doing a semantic search instead of just doing keywords last but not least is Graph RAG what Graph RAG does is very simple remember remember I showed you about the graphs earlier what it does is basically it's still embedding the information but embeds things like the node OK maybe embed you know product launch date and it also embed that that edge like

[07:10] is it is it a part of the observation or is it a relationship okay and and all of these are will be embeddings the similarity search will just find out you know what's similar to the question that the user asked and then eventually it might return a relationship graph here so that the context so that the agent has more context about the memories in relationships so now how do we maintain it I have summarized this into four main ways of maintaining a memory system the first one is you gotta make a decision which is

[07:41] do we add do we delete do we override some previous information do we do nothing okay this one means no operations or do we retire some information which by the way is different from deleting because we're not deleting it we're invalidating the previous information with some time range what does that mean we launched Waku Agent one month ago and it got 1,000 stars in the first 25 days okay and then the next day I'm telling the agent today is day 26 and the Waku agent has got 1.3 thousand stars so it increased

[08:11] 30% in just one day and then what would should happen is that instead of deleting the fact that this Github repo has gained 1 thousand stars in 25 days which is still important information you can probably just invalidate it and be like hey the latest stars is 1.3 thousand already and you can trace back to one day before which is 1000 stars and these context are still important for future tracing purposes the third one is called attribute which in human language is that it's basically tracing where the source comes from okay

[08:46] maybe I got the memory from the users communicating with your agent or maybe I got it from some web search or maybe I got it from some calendar search right depends on where the source comes from and last but not least we are talking about reflect which is it's gonna drop or merge some of the duplicates or some of the outdated information I mean it's kind of similar to delete and kind of similar to retire but reflect sometimes means but reflect sometimes can link to what anthropic has proposed which is dreaming

[09:16] dreaming happens when we are not working when the agent is running it's like busy accumulating facts accumulating episodic memories accumulating procedures into skills but then when it's not running we can probably schedule some tasks to let it you know reflect on everything that we have collected and then merge some of the duplicates or drop some of the stuff that are not important anymore so it's kind of like a post mortem or post process um step to update or maintain the memory system are you guys still with me good let's keep going

[09:47] now let's see some different types of memory systems with real products and let me add the vector store here so first things first so first thing first plain text ways of storing memories examples are Hermes and Waku Agent I have shown you soul I've shown you soul earlier which is text and it's basically system prompt and you can just edit it right if you if we come back to if we check out the soul of Hermes you can see that my Hermes has a personality which is you should always talk like Pikachu

[10:18] it should say pikapi all the time and and there's some variations you can say pika or pika pika or pika pi right and the pika pi reserved for addressing me directly like you should call me as Pikapi because in the anime Pikachu also called Satoshi or Ash Pikapi and you can edit this to anything else right so so it's very simple to understand and skills and skills is a procedure for example research about all AI agent videos on the internet and then inform me every morning what are some of the latest news that Sean stories

[10:50] should Sean AI stories should cover for the next video right this is a procedure this is skill and it's usually loaded by trigger okay so if I'm asking my agent then be like hey trigger that skill for Sean's AI posts and then it should run it and then fetch it for me and I should be always be able to just edit it myself memory.MD which is some durable facts that you should keep accumulating when you're talking to agents like these this is from a previous video for showing you how to build agent harness in real code

[11:19] and the way that we build up semantic memory which is basically memories Dot MD is that every time when you have a conversation with the agent from the user prompt to the LLM to the reply it should it should save the history into the state.db which is our SQLITE database and it should consolidate the information after every you know say five or 10 conversations using some cheaper auxiliary models and then it should distill the facts into the memory.MD and these memories usually are loaded in context

[11:49] because that's important but you can also make it retrievable you can build up a you can build up a retrieval gates like what we do in a Waku agent and you should decide if it should call call up some skills call up some semantic memories and stuff right it doesn't have to always be there but you can do that if you want to and remember and remember agent harness is very flexible you don't have to stick to any standard you can you can just craft your own harness what eventually what what makes what lasts forever or what matters

[12:17] most to you eventually is the memories you're if you're accumulating these memories and then store them well and prepare them well and then take care of them then these memories are the most valuable assets for any AI agent harness whether or not we should have a retrieval gate that's completely up to you okay and last but not least remember we just showed you the state.db which is the sqlite database and it's a relational database with rows and you're doing the keyword searching for how to find it

[12:43] and then the way we maintain it is that you can you can just edit it yourself like like if you click on here yeah you can just write things into the state DB or you can wait for the agent harness to consolidate it like like what I showed you in Waku Agent the consolidation and in the real code we also have a consolidation model right here you click into it you can see what happened in consolidations in the past history okay so that was plain text style what else do we have we've got Supabase Weviet Pinecone which have vector stores well superbase

[13:20] is a relational database so it's technically not a vector store but they do have a SQLITE extension called PG Vector which is technically a vector store that you can use vector stores usually store information in vectors which as I mentioned earlier is gonna embed every single word into a high dimensional vectors with numbers in it because computers cannot process on words they can only they can only process numbers or higher dimensions of numbers that's why vectors are very useful and then you can store

[13:51] some metadata for example what does this vector really mean and what some other information should be carried with it okay and the way we find it is that we're gonna do this thing called similarity search remember earlier we said that we're gonna put a number put a high dimensional number vector on the word food and then you're gonna search you know the space similarity space distance between food and apple and sausages and then grab those food that's in our storage in our memories okay and the way we maintain it is that you basically

[14:21] just upsert or delete information from it but you can make it a little bit smarter you know if you have a embedding if you if you have a vector store in your agent harness and you can just you know design it in a way such that in in in a similar way like our consolidation module I showed you earlier for state DB but instead it's for vector stores now we and now we're looking at some memory tools out there on the market mem0 is one of the interesting examples they have two ways of storing memories

[14:44] one is called row memory another one is called graph memory what a row memory does is that I'll just show an example this is this is a portal I'm in for mem0 and then they have a tab called memories if I just click into one of them and this one says we have a German buyer and this German buyer requires vegan certifications on every SKU that they got sold for okay this is just a this this is basically just a semantic memory like a durable fact that will last here forever and if you have an agent and you can ask them questions

[15:15] and and it should pull information from the meme zero uh memory layer I have no idea I don't know I don't know if they did plain keyword search here or doing some embeddings or RAGs because I mean I mean yes they are open source but this is an enterprise version so I'm not sure which one they're actually using behind the scene for row memory the way you maintain it is basically is very simple you add update delete or do nothing or supersede it supersede it means that supersede is basically what we covered earlier which is you don't delete

[15:40] the information but you make the previous one go outdated and you say hey we have 1.3 thousand stars for Waku Agent now not 1,000 stars anymore but we do not but we do not wanna delete the fact that it has 1,000 stars in the first 25 days and mem0 also has graph memory also has graph memory graph, remember what we said it's basically nodes and edges and then it can store things in vectors and row memory also is stored in vectors sorry I forgot about that both of them stored in vectors and it's gonna check

[16:08] traversal which means you're gonna check the entire graph for information and also it does add update delete noop I'm not too sure if it does ‘supersede’ maybe it does too hmm for these things you should check uh the source code unfortunately the graph feature for mem0 needs me to upgrade to their Pro which I'm not gonna do because I found an alternative tool called Zep remember this chart I showed you this is their graph so so what Zep does is a memory called temporal graph memory which means that this graph

[16:43] is evolving over time okay it's got the nodes got the edges it's got the validity which as they claim the way you find these information is using vector search on nodes and edges and across the entire graph traversal and the difference is that here you can invalidate with some time range like like superseding like similar to superseding you never delete it but the history survives here and if you check Zep it's basic it's by default using graph and you can check the previous batches of conversations

[17:16] and you can check the threads stuff like that OK in just a moment I will show you real examples of all these memory layers in just a moment last but not least last but not least we got LangChain memories which is called LangMem LangMem is just LangMem okay there's no stores it's basically a package that allows you to store locally and it's your own stores search that you're using and you're extracting and resolving you know the the the store update before writing into anything so this is kind of the high level overview of

[17:47] of memory layers for AI Agent and as I and I and I as I as I explained earlier you should pick the right one for your own agent harness and you should take good and you should take good care of it we are currently working on some memory layers for Waku Agent we are currently working on some more comprehensive memory layers so please stay tuned and if you're interested please leave a comment or join our community in my main page seanchen.io and if you want to participate in this or try the early versions

[18:05] of Waku Memory Agent layers you can come to our community in seanchen.io and here you'll be able to join our community where I will be replying questions every 2 weeks I'll be replying questions live every 2 weeks with our community or you can just join our or you can just click into any of my social media to communicate with us let's start testing things now let's start testing things again come to Waku Agent get hub repo you can either run this in a terminal to pip install or you can just get clone it

[18:30] UV run Waku dashboard will bring you to will bring you to an agent harness visualization like this in the arena section we have built up this thing called Memory Race and the way we do we deal with Memory Race is that we have a set of questions for example if I choose the dinner party with some facts and questions and I zoom in a little bit I can see that I can see that the stuff I'm gonna tell stuff I'm gonna tell each one of these memory layers and if you come to and if you come to what they get asked

[18:56] it's gonna say what I'm gonna tell each one of these memory layers for example Jensen Huang is coming and last time he knocked my chili oil onto the white rug and Elon Musk is coming too and he said he he would get here at 7:00 and Tom Holland the Spider Man is also on the list and he told me the ending of his next film over coffee last Tuesday remember Tom Holland can never keep the secrets of a unpublished film that's basically the memory I wanna tell to these uh memory layers and there are some sample questions

[19:27] right if you ask about Jensen it's gonna say okay he he basically knocked off my chili oil if I ask it questions related to if I ask a question related to how much does Paul Graham owe me in Chinese Paul Graham owes me $20 alright because cause here it says that he owes me 20 quid from the sourdough we met in Lisbon quid is the way you say pounds in the UK cause Paul Graham is British anyways um and at the bottom you can see that we have listed a few we have listed a few memory stores including SQLITE mem0 LangMem Zep control group

[20:03] I'm not doing Supabase yet because we're not testing embeddings here if you're interested in RAGs and embeddings please check out my previous videos on Agentic Rags and RAGs they're all in this channel so feel free to look them up okay and I can click on Read Store to check the current memories so it's pretty much empty for the current date because I have cleaned up the other memories you saw earlier were just some legacies from the previous iterations so what I'm gonna do now is that I'm gonna click on ask

[20:29] 5 stores if I scroll down you can see that we are now telling these facts to each one of these memories OK with control being the one that will just be you know having no memories and let's see if it actually works um if we ask the same question to the control group this is a seeding process we're gonna we're gonna ask we're gonna tell these facts to each one of these memory layers and then later it's gonna ask the questions and see how fast they respond okay and because writing it takes some time so I'll just

[21:00] leave this for a second and we can come back to this in a bit I wanna show you exactly how to use how to use some of these memory layers in plain code if you come to Waku Agent and we can check out the folder called examples OK we open examples and there's a folder called Memory Native and here we have a langmem native which is the LangChain memory and if you scroll down to row 42 we have added some facts like I met Alex at Lisbon AI Meetup product launch is scheduled for may actually the launch moved to June

[21:38] remember this is superseding like making the previous information not deleting it but it's basically outdated and there's some questions what is the product launch what date do we push to ship date to and a “When is the product launch date” in Chinese so that we can see if it actually works um and then later you can see that we are creating this memory manager with this manager and then for every fact we're gonna invoke a conversation alright um another example is a mem0 native so if we click into it and scroll down a little bit

[22:12] you can see that we have the same facts and questions for mem0 you need to create a memory client first and then for every fact we can do client add which is writing the memory into mem0 and then later you can test it with some real questions with some searches okay similarly we have a supabase native here exact the same process but for superbase you need to do some embeddings and later you're gonna do retrievals using the embeddings and last but not least with Zep we are we fit the same questions and facts again and we have

[22:48] built up a client from Zep and for every fact it's slightly different here for Zep because it's by default a temporal graph memory for every client for every graph you're gonna add the fact okay and then it's gonna build up the graph for you okay and then you can use the clientograph search to find out uh find out the results using this query okay feel free to try this out okay let's come back to here we can see that except Zep everybody else has finished the work uh let's see so make it bigger when did Jensen knock

[23:29] what did Jensen Huang knock onto my rug and Nvidia should be paying for this uh it passed for each one of these memories except uh Zep is still taking time to build the graph I have no idea but I feel it's because that building the graph takes time okay maybe that's why and it took 4.5 it took 4.6 seconds for SQLITE and the answer is correct Jensen knocked chili oil onto the white rug and mem0 just um said chili oil very fast very very straightforward but took a slightly longer time than sqlite and the LangChain LangMem took the longest time

[24:09] 7.5 seconds uh Zep is still seeding which is taking forever and the control group absolutely have no information about this which the answer is correct because it should not I don't have any record of that that's right and when I asked the question how much did Paul Graham owe me which is supposed to be 20 quid sqlite answered it correctly in English it took it 10.3 seconds I don't know maybe because uh sqlite is a little bit too simple for keyword searching so it doesn't really know how to search in Chinese

[24:39] because the memory was in English but seems like mem0 got it and then it said reply to me in Chinese it said Paul Graham still owes me £20 and he he lost it when he was betting with me in a a bread store in Lisbon that's right and uh the LangMem also has a correct answer and and the control group also doesn't have anything and when did Elon arrive here it's supposed to be nine PM instead of seven PM why is that because oh because we have an update you see update on Elon he can't get here until nine PM instead of seven PM

[25:17] so we're doing a bit of a overwriting or superseding right because maybe this I don't think this is overwriting this is superseding because uh it's supposed to keep the previous information but make it you know kind of outdated and if you scroll down you can see that uh all three of them answered correctly and my control group says there's no events on the calendar with Elon so it searched the memory and used some tools to check the calendar it didn't happen okay cool guys uh if I click on read stores again

[25:49] you can see that SQLITE mem0 and uh Zep or also has some memories already LangMem has nothing because it's a package control group is control group if you click on see all you can see all of the memories so you should also be able to find them in each one of these platforms if you come to memories you can see a lot of them OK this was the the stuff we run 6 minutes ago for these durable facts and for Zep we should be able to see them too uh let's see it's very unintuitive on Zep what exactly is happening

[26:25] I I don't know where to find them to be honest okay I think in users every time when I do an agent run it's creating a new user so maybe I should click on this new one and view the graph okay good it's from Waku Agent Arena and it knows the sourdough bet alright Paul Graham owes me 20 quid and it was in Lisbon okay good and Elon (Jensen actually) basically knocked off the chili oil onto my white rug you see it's building the graph which is pretty cool but it took some really long time 我勒个天 but it took some really long time 我勒个天 it's still

[27:03] seeding it oh my God yeah maybe saving data using temporal graph is a pain because it's been delayed for so long but but I think this relationship with graphs nodes and edges probably still worth it hmm while we're still waiting for Zep let's take a look at my main website seanchen.io and every two weeks I will be hosting a live session

[27:32] on this Whop community and if you join us I will be able to answer your questions um live in our discord channel so um previous in our previous session people ask me questions regarding people ask me questions regarding all of our system design and they have some implementation or deployment questions regarding you know Waku Agent and Hermes Agent Pi Agent come join us if you're interested in having a conversation with us come join us thanks back to Zep now it's asking the questions finally waiting waiting OK

[28:01] let's come to Zep and check again about its graph view the graph huh Tom Holland is here his next film oh you see you see this edge carries information because Tom Holland and his next film it means nothing but look at this edge this edge is saying revealed ending of so Tom Holland revealed the ending of his next movie so it did some summarization for me okay and then Paul Graham is a node and program owes me 20 quid and he owes Waku Agent Arena which I don't understand but here there is you know dropped on you know

[28:40] somebody dropped the chili oil onto my white rug alright not not entirely sure you know the chili oil stained the White Rock but it didn't say Elon (Jensen Huang actually) I'm not entirely sure this is doing its its job alright but I don't know it it looks kind of smart that it it built this graph but yeah it it's a um I feel like it lost some information here hmm and it's taking forever maybe it's an overkill for a lot of these smaller use cases which is why I think that Hermes and Pi Agent all try to make things very simple and

[29:17] it will just you know make things easier for everyone to uh get started with and it doesn't take that much time okay I kind of lost my patience whoa finally

[29:32] finished asking the questions Jensen drop the chili oil

[29:38] okay now it's finally asking the question previously was just all waiting you see 4.9 seconds 4.9 seconds 6.4 seconds okay Zep is taking forever I think uh I have lost patience for it uh I'll just keep it that way, Zep team if you're watching this I think this is a big pain point love your product love your visualization but please fix speed or at least do something about it for making simple tasks faster cool guys so this is a quick summary of 5 different ways of how we can craft Agent Memories for our AI Agent harness

[30:12] and I hope this is helpful if you have any questions feel free to leave us a comment and join our community and give us a star on Github and try out our Waku Agent for your own implementation thank you thank you very much I will see you next time thanks
