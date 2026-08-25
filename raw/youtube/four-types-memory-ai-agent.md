---
title: "The Four Types of Memory Every AI Agent Needs"
type: raw-transcript
created: 2026-08-25
video_id: BacJ6sEhqMo
url: https://www.youtube.com/watch?v=BacJ6sEhqMo
duration: 00:10:41
language: en
auto_generated: false
source_api: youtube_transcript_api
segments: 92
timestamp_format: "[MM:SS]"
---

# The Four Types of Memory Every AI Agent Needs

> Duration: 00:10:41 | Segments: 92 | Language: en | Source: API

## Transcript

[00:00] AI agents have different ways to remember stuff and each serves a different purpose.

[00:05] So let's take a look at the four main types of AI agent memory from some pretty foundational stuff to what I think are some quite interesting emerging areas.

[00:17] And I think it's really, first of all, worth considering how we do it.

[00:21] How does human memory actually.

[00:27] We can think of human memory as having first of all short-term memory.

[00:35] So that's the stuff that is active in the brain right now like what I'm saying at this very moment.

[00:43] That's one type of memory but there's also a type of memory called factual knowledge.

[00:49] So this is things like the company security policies that you remember or it could be facts like Python is an interpreted language.

[00:59] Then there are learned skills.

[01:03] Like, I don't know, writing backwards on a sheet of glass, for example, which I am totally doing here.

[01:12] There is absolutely no camera trickery involved.

[01:16] And then there is personal experience.

[01:20] Like the time I spent three hours debugging a Kubernetes cluster only to discover...

[01:25] I was pointing at the wrong cluster the entire time.

[01:31] Seriously, that was three hours of my time.

[01:34] Anyway, anyway, it turns out that well-designed AI agents, they also need these three types of memory or these four types of memories that I've got here.

[01:44] And there's actually a well-known framework for this.

[01:47] And it's from a Princeton research team and they gave it the name of CoALA.

[01:53] That's Cognitive Architectures for Language Agents, and CoALA maps out four distinct types of memory that agents need.

[02:01] So let's walk through each one and see how they actually work in real agentic systems today.

[02:09] So type one, that is working memory.

[02:14] This is the agent's context window.

[02:17] It's everything the agent can see right now, the current conversation, if there's any system instructions, they'll be in there.

[02:23] If there's any files or data that have been loaded into the prompt, that's where they'll be as well.

[02:28] So it's really kind of the scratch pad.

[02:30] And the analogy everybody uses for this is this is just like RAM, random access memory.

[02:39] It's fast and immediately accessible, but it's volatile.

[02:44] When the session ends, it's gone.

[02:47] And it's also limited in size.

[02:50] I mean the- the biggest context windows available today are pretty big.

[02:55] I mean, it could be like one million tokens or even more than that, but that still has a ceiling and try to stuff too much in there and performance is gonna degrade as the model starts losing track of things that are kind of buried in the middle of the context window.

[03:13] So every agent has working memory, but then so does every chat bot, it's just the context windows.

[03:19] So the question is...

[03:20] What else do agentic systems need?

[03:25] Well let me add to that list number two semantic memory and this is the agent's knowledge base, so semantic memory stores facts and rules and conventions, documentation and in the academic literature this often gets described in terms of things like vector databases or as knowledge graphs, and yeah, those are real implementations, but, in a lot of production agentic systems today, semantic memory is something much simpler than that.

[03:59] It's just simply Markdown files, .md files.

[04:03] So take Claude code as an example of this.

[04:07] So it has one of these Markdown Files.

[04:10] It's one is called Claude.md and that sits in the root of a project.

[04:17] And that file contains the project architecture, the coding conventions, the build commands, what frameworks to use, and also what not to do.

[04:25] And that far gets loaded into the context window at the start of every session.

[04:31] So semantic memory tells the agent what it needs to know in general.

[04:37] And without it, the agent is, well, it's kind of destined to make the same mistakes over and over again, because it has no persistent knowledge to draw from.

[04:47] Working memory, semantic memory, what else is there?

[04:51] Well, number three, that is procedural memory.

[04:55] Now procedural memory is how the agent knows how to do things.

[04:59] And there's an open standard for this that's called agent skills.

[05:07] And it uses a file format called skill.md.

[05:13] A skill is just a folder with a markdown file that describes the skill and what that skill does and some step-by-step instructions for how to perform that skill and it could be anything from creating a PowerPoint presentation to running a structured code review.

[05:29] Now skills use something called progressive disclosure so the agent doesn't load all of its skills into the context window or I guess I should say into the working memory at once because that can blow through the working memory budget if there are a lot of defined agent skills.

[05:49] So instead the agent just sees a lightweight index, which is just the name and the description of each available skill.

[05:57] So maybe that's a hundred tokens per skill.

[06:01] Then when a task comes in that matches one of these skill descriptions, the agent loads the full instructions and if the skill references other stuff like other files or templates or scripts.

[06:12] Well, those only get pulled in when the agent actually needs them during execution.

[06:17] So the agent advertises what skills it has, it loads the instructions in when they're needed and then executes with the additional resources pulled in as they're needed as well.

[06:27] And all that is quite different from semantic memory where the knowledge is always present in context.

[06:36] All right, number four is episodic memory.

[06:40] Episodic memory is the agent's record of what happened in past interactions and past decisions and what it learned from them.

[06:48] Now a naive implementation of this is just to save every conversation transcript and then just search through them as you need to.

[06:58] And that technically counts as episodic memory but often that's not very useful.

[07:03] So what production systems actually do is a bit more distillation.

[07:12] So as the agent works across sessions it kind of accumulates notes for itself, but it doesn't save everything.

[07:19] It decides what's worth remembering based on whether that information would actually be useful in a future conversation.

[07:26] So the result is distilled or compressed experience.

[07:31] So things like last time we debugged the auth module, the issue was in the middleware layer.

[07:37] That's something that's a lot more useful to remember than just a full transcript of a 45 minute debugging session.

[07:45] And this is where memory starts to kind of genuinely look like learning because the agent is gonna get better over time.

[07:52] But episodic memory is also the hardest type of these to get right because what do you delete?

[07:59] When does information become obsolete?

[08:02] If a user changes jobs, do you keep the old project memories around?

[08:07] Or should we forget them?

[08:09] Well, humans are actually pretty good at forgetting.

[08:12] I do it all the time.

[08:14] And as frustrating as that can be, it can be quite useful.

[08:17] But for agents, forgetting is an engineering problem.

[08:22] So four types of memory, working, semantic, procedural, episodic, but not every agent necessarily needs all four.

[08:32] Let me give you an example.

[08:34] So let's say, we're building a simple reflex agent.

[08:37] So that's something like a thermostat or it's just like a basic routing bot.

[08:42] Doesn't need all four.

[08:43] It might only need access to working memory, the context window, and that's basically it.

[08:51] Now, if we take something a little bit more complicated like a customer support agent, but one that's still fairly simple and narrow, like an agent that resets passwords, for example.

[09:04] That will still have access to the working memory, of course, but it probably also needs access to procedural memory as well because it needs to recall the password reset skill.

[09:18] But that might be it.

[09:21] Whereas if we take a look at something like a coding agent, it probably needs access to all four, so it certainly needs access to the working memory, the context window, but it also needs the product knowledge it would get from semantic and then the skill system from procedural and also the auto memory from episodic that learns across sessions.

[09:49] So memory is really what separates a chatbot from an agent because a chat bot gives a response, but an agent can give a response shaped by persistent knowledge.

[10:01] Accumulated experience.

[10:02] It remembers the project.

[10:04] It remembers preferences and a good memory architecture also remembers the mistakes so we're not destined to repeat them which honestly would have been wonderful if an agent had told me about that Kubernetes cluster before hour three.

[10:21] So four types of AI agent memory.

[10:24] Which of these are you using in your own agentic workflows?

[10:40] Thank you.

