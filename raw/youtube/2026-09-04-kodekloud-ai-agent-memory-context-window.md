---
title: "AI Agent Memory: Why Context Window Expansion Is Not Enough"
type: raw-transcript
created: 2026-09-04
video_id: 0P-ACuHyu-0
url: https://www.youtube.com/watch?v=0P-ACuHyu-0
duration: 00:04:56
language: en
auto_generated: true
source_api: youtube_transcript_api
segments: 16
timestamp_format: "[MM:SS]"
---

# AI Agent Memory: Why Context Window Expansion Is Not Enough

> Duration: 00:04:56 | Segments: 16 | Language: en | Source: youtube_transcript_api (auto-generated)

## Transcript

[00:00] Before agents became a major focus, most LLM applications relied almost entirely on context window. You would have a system prompt, the current user message, and maybe some recent chat history, but that was essentially all that the model could remember. But, the most important thing to remember is that the model does not automatically remember everything that happened yesterday, 10 tool calls ago, or across previous sessions. The application has to put in that information back into the prompt or give

[00:28] the model the right tool to retrieve it.

[00:30] Around late 2022 and 2023, when ChatGPT took off, memory was basically whatever we could fit into the context. And you can probably guess this creates an obvious problem. As the task got longer, agents lose track of earlier decisions, repeat mistakes, and fail to maintain coherence over time. So, the first thing we need to do is to understand the difference between context and memory.

[00:55] Context is active. It is what the model sees during the current forward pass.

[01:00] But, memory is different. Memory is information that is stored outside of the model that may or may not be used later on. So, the key idea is that short-term and long-term memory lives in different places. A useful analogy that we might all know by is computer memory.

[01:16] When you're working on a document, the active version is stored in RAM. It is fast and immediately accessible, but it is not permanent. So, if your computer suddenly loses power before you save the document, the state actually disappears.

[01:30] To make it more persistent, you would have to write it somewhere more durable, like a disk or solid-state drive. Agent memory works in a very similar way. The context window is kind of like RAM, where it's active, fast, and limited, and long-term memory is more like a disk, persistent. But, the agent needs a system for deciding what to save, what to retrieve, and when to actually load it back into the context. So, before agents had dedicated memory systems, the field mostly tried to work around the

[02:00] context window in three main ways: rag, summaries, and profiles. Rag helped agents reference large bodies of information without stuffing everything into the prompt. Instead of putting an entire document or knowledge base into the context, the system retrieves the most relevant chunks. Summaries helped compress long conversations rather than keeping every single message. The system could just summarize everything that had happened so far. And profiles helped with personalizations. They stored tables of facts about the users, such as

[02:33] preferences, writing style, or ongoing project that the user might be working on. But each approach had its own limitations. Rag, for example, can retrieve wrong information, and it is also harder to set up than it actually looks. You need to chunk the data, embedded, index it, and retrieve the right pieces, and rank them correctly.

[02:53] All of this is a lot of overhead set it up properly. Summaries are lossy because they rely on the LLMs to decide which detail are worth preserving. If the summary drops on one detail that matters later on, the agent may not be able to recover it properly. And profiles can become stale and contradictory in some cases because user behavior changes over time. Something that was true last month may no longer be true today, but the system still needs to treat it like a permanent fact. So, you can imagine even

[03:25] when the model context gets larger, it does not fully solve the problem. And that's because memory is not just about storing more tokens. The deeper issue is memory management. And modern agents are no longer just an LLM that we ask questions. So, clearly, the industry needed a more serious approach to the problem when it comes to memory. As agents became more capable, memory had to become more than just an extra context. It needed its own serious layer in the agentic stack. And the reason why this layer needs to exist is because

[03:58] memory is not just a one-time action, it is a loop, and it looks something like this. The first half is writing memory.

[04:06] The agent observes what happened, decides what matters, and stores it somewhere persistent. That alone is difficult because most things that agent sees are not worth remembering forever.

[04:17] The second half is actually using the memory. The agent retrieves the relevant information, puts it back into the context, and updates it if something has changed. And for long-running tasks, the agent does not just need to remember the facts, it needs to remember experience as well. For example, what approach worked last time or what mistake it should avoid next time. That is why memory matters when it comes to long-horizon agents benchmarks, where the model has to stay coherent over many steps. And that may be one of the

[04:47] biggest difference between today's assistant and the next generation of agents that are coming in the future.

[04:54] >> [music]
