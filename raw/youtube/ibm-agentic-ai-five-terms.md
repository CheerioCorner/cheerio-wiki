---
title: "5 Terms You Need to Know About Agentic AI"
type: raw-transcript
created: 2026-09-04
video_id: k5jYwyhDMxA
url: https://www.youtube.com/watch?v=k5jYwyhDMxA
duration: 00:10:55
language: en
auto_generated: false
source_api: youtube_transcript_api
segments: 136
paragraphs: 90
timestamp_format: "[MM:SS]"
---

# 5 Terms You Need to Know About Agentic AI

> Duration: 00:10:55 | Segments: 136 | Paragraphs: 90 | Language: en | Source: youtube_transcript_api (manual)

## Transcript

[00:00] Frontier AI agents, they're pretty capable.
[00:03] They're really good at planning out tasks and  writing code with minimal human involvement but there are a handful of specific  pieces under the hood that enable this.
[00:14] So let's cover five of those pieces, the  five terms you need to know about agentic AI and let's start with stuff that's inside  the agent that kind of shapes how it behaves.
[00:28] Inside an agent of course there is  a model, a large language model.
[00:34] That's what's doing the actual text  generation and the reasoning and by itself well it's just a conversational partner.
[00:40] What turns it into an agent is the instruction  layer that's wrapped around the model.
[00:46] So that brings us to term number one, term  number one that you need to know, that is agents.md.
[00:56] So what's that?
[00:58] Well, .md, that's markdown,  so it's just a text file.
[01:03] It sits at the root of a project, and whenever  the agent starts work in that project, it reads whatever is in that agent's .mdfile.
[01:15] Now the file tells the agent things  like which commands to run for tests or which coding conventions this code base uses.
[01:21] So we can really think of this  as being kind of like a...
[01:25] Readme file but it's a readme files  specifically written for agents.
[01:29] It tells the agents things like  specific setup commands to use and any code style rules or maybe  how a PR title should be formatted.
[01:39] So the agent executes the commands it finds in  agents.md when they're contextually relevant.
[01:45] So if the file says run PMPM test  before committing well then the agent will run PMPM test before it does a commit.
[01:54] And agents.md files can also be nested,  meaning there can be multiple of them.
[01:59] So maybe we have one at the root and then multiple other ones for sub-projects  with its own set of rules.
[02:06] And files that are closer to the working directory override the earlier ones  because they appear later.
[02:12] Now agents.md was introduced by OpenAI  and later contributed to the agentic AI foundation that runs under the Linux foundation.
[02:24] Now a quick wrinkle worth mentioning  here some agents use a different file name from agents.md so  Claude for example does this.
[02:33] Claude's one that is actually called  Claude.md because of course it is so it's different name but it's  more or less the same idea.
[02:44] So agents.md is read by an agent every  time it starts work in a given project.
[02:50] But what about knowledge that the agent only needs  sometimes and isn't necessarily project specific.
[02:57] So let's say the agent needs to  know how to build a PowerPoint deck.
[03:02] Well, loading all of that context  every single time the agent starts, that would just really clog up the  context window for no real reason if the task at hand has nothing  to do with PowerPoint slides.
[03:14] So that brings us to term  number two and term number two is agent skill so what's that well an agent skill is a folder and inside that folder is  a file that file is called skill.md.
[03:34] So .md again that's more markdown now also in that folder is whatever scripts or resources  the task needs and then inside skill.
[03:45] Md is some metadata including a description.
[03:50] And that tells the agent something like,  invoke me when the user wants to X.
[03:56] So X could be when the use  wants to make a PowerPoint.
[04:00] And if the user's request matches that  description, the agent pulls the skill in.
[04:05] If it doesn't match, well, the  skill is just gonna kind of sit there out of the way, not taking up any context.
[04:12] Agent skills are another open standard and  they're supported by multiple agent platforms.
[04:18] Agents.md, that's how a specific project works, and an agent skill tells the agent  how to do a specific kind of task.
[04:29] All right, so that's two of our five terms down.
[04:32] The agent now knows what to do, but doing  things also means reaching outside the box, as in outside the AI agent itself.
[04:42] So that's where we're going to go next.
[04:43] So agents need to reach all kinds of  external things like APIs or databases or developer tools or SaaS platforms you name it.
[04:52] And the challenge here is that every one of  those targets might have its own interface.
[04:57] So without some kind of standard  every AI agent would need a custom connector for every external thing  it might touch which would be a mess.
[05:05] So that brings us to term number three, MCP - Model Context Protocol.
[05:14] Now MCP is an open protocol for connecting  AI applications to tools and data sources and workflows and it comes with  something called an MCP server.
[05:29] Now an MCP server wraps up a tool or  a data source in a standard interface and any agent that can speak  MCP can now talk to that tool.
[05:39] So let's say an agent needs to pull data  from it needs to go to something in Notion.
[05:46] So we've got Notion here, or maybe it needs to go  a Stripe payment link, whatever the backend is.
[05:52] Well, the agent speaks MCP to the server and it's the server now that handles the  underlying API for in this case, Notion. Now, MCP started at Anthropic  and is now governed under the AAIF, again at the Linux foundation.
[06:11] And it has broad industry support.
[06:13] So that covers agents talking to tools and data.
[06:17] What about agents talking other agents?
[06:21] Well, time for term number four.
[06:25] That is A2A.
[06:28] Otherwise known as agent to agent.
[06:31] So A2A is an open protocol for  agent to agent communication.
[06:36] So let's kind of think of  a scenario for using this.
[06:39] Let's say we've got a procurement agent  here and that handles vendor contracts.
[06:46] And then maybe we've also got a finance  agent over here and that approves spend.
[06:54] And yeah, I know financial  processing stuff, trying to.
[06:58] Contain your excitement but the the  procurement agent needs to negotiate a contract and then it needs to hand off  to the finance for approval and without A2A these two agents would  need some form of custom integration or they wouldn't really coordinate very well but  with A2A each agent publishes something called an agent cart. And that's just basically a description  of what the agent does and how to talk to it.
[07:29] And other agents can read that card and  then figure out how to delegate work.
[07:35] The procurement agent in this case  is going to find the agent card and read it for the finance agent  and then hand off the contract.
[07:46] So that's A2A and this A2A  standard comes from Google.
[07:52] It's now also an open standard under,  you guessed it, the Linux foundation.
[07:58] So MCP is how agents talk to tools and data  and A2A is how agent's talk to each other.
[08:06] All right, so how we're doing here, now the agent knows what to do and it  knows how to reach outside of its borders.
[08:16] What else?
[08:16] Well, sometimes one agent just isn't enough.
[08:21] Maybe the task is too big for one context window, so say the agent's reviewing a code base  with thousands of files loading every file, that would blow out the context on its own.
[08:33] Or maybe the work is embarrassingly parallel, like you've got to run a check on 20 different  functions and each check is independent, and you could do those one  at a time but that's slow, doing them all at once would be 20 times faster.
[08:48] So, term number five that you need to know.
[08:52] It's subagents, which means using  and spawning multiple agents.
[08:58] So a subagent is a child agent that the main  agent spawns to do a specific piece of work and each sub agent runs in  its own fresh context window, it does its job and it  returns a result when it is done .
[09:15] So this main agent here, it could  spawn a sub agent and give it some work to do.
[09:25] Let's say go read 500 files, and then just kind of hand back to the  main agent a summary of those files.
[09:33] So that would keep the main agents  context window pretty clean.
[09:37] And we could have lots of agents in  parallel, maybe we've got like 20 agents here running in parallel handling  20 independent checks at the same time.
[09:47] Now, sub agents are a little  bit different from the other four terms because sub agents  are a common pattern in modern agent systems but they don't really have  a formal standard document behind them.
[09:59] But the concept shows up  almost identically everywhere.
[10:03] I mean the very basic idea is you  have this big parent agent here.
[10:09] That parent agent spawns one or more child agents.
[10:15] The child gets the same context.
[10:16] The child does whatever work it was told to do and then it returns a result  and the parent carries on.
[10:23] With its context intact.
[10:25] So there we've got five terms.
[10:28] We've got agents.md and agent skills, which live  inside the agent and they shape how it behaves.
[10:36] We've go MCP and we've go A2A.
[10:39] That's how the agent reaches outwards  to tools and to other agents.
[10:43] And we've gone sub agents.
[10:46] That's the agent handles the work  that doesn't fit into one context.
[10:50] That's what a front-end AI  agent actually looks like under the hood today.
