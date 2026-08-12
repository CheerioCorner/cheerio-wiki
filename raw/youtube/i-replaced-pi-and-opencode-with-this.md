---
title: "I Replaced Pi and OpenCode With This"
type: raw-transcript
created: 2025-07-22
video_id: eQZQl_7-X40
url: https://www.youtube.com/watch?v=eQZQl_7-X40
duration: 00:08:29
language: en
auto_generated: true
source_api: pytubefix
segments: 185
merged_paragraphs: 22
---

# I Replaced Pi and OpenCode With This

> Duration: 00:08:29 | Segments: 185 | Merged: 22 | Language: en | Source: API (auto-generated)

## Transcript

[00:00] So, I've been using by an open code for a while now cuz I like lightweight agents, picking out my own model, and controlling the workflow myself. But, I think I've found something that I like [music] even more, and it's called Prime Agents. It's completely open source.

[00:17] Actually, [music] it's built on top of by, but it works pretty, pretty different [music] compared to basically every other coding agent out there. So, yeah, I've basically replaced by and open code with [music] Prime Agent. And in this video, I'm going to be talking about why and why you should consider [music] at least trying out Prime yourself. So, let's get started with probably the weirdest thing about Prime cuz most coding agents basically assume do something like this. Uh they get a bunch of tools that they can use, a list of

[00:54] tools, and then the model basically uh starts doing a bunch of tool calls. Does the first tool call, gets the result, the result gets back loaded back into the model uh as context.

[01:08] Uh then another tool call is done, and so on, right? Instead, Prime gets a runtime, a Python runtime, to write Python scripts, Python programs, whatever, that can do many operations, uh probably if the model decides, uh the Python program could actually be equivalent of, I don't know, 50 tool calls. And then, as this is also Python, the result kind of also be filtered. So, uh the model could be writing down uh that the not relevant information is being filtered out so that only the most important information is loaded back

[01:53] into the model as context. I don't know about cost, but these has increased quite a lot the the speed of doing, specially if the model has to do a lot of actions uh and would have to do, I don't know, 30 tool calls one at a time. Uh this really really speeds up everything. And also, it has helped me quite a lot with context windows and and all of this.

[02:22] So, I'm going to be showing you um how it looks. Basically, this is a completely empty direct And I asked it uh to create a simple hello world HTML file. I asked it to use the Python tool that it has the Python runtime tool.

[02:39] And yeah, it created the hello.html file.

[02:44] And as you can see, it used Python to create the HTML file instead of using, I don't know, a bash command or whatever.

[02:54] This is the code the Python code that it wrote. Uh as you can see, it also saves the path inside of a variable cuz this is not only um an environment that lets it write the code that it wants and do much more custom actions and all of that. It is also persistent. Okay, so it would also help uh the agent remember things if I uh get get far down into the conversation. So, it will be able to go back to the variable and get its value. Okay? It would also be useful for that.

[03:39] I asked it uh what else this Python environment thing could help it do that a traditional coding agent uh would find harder or much more inefficient. And there are a lot of stuff, actually. What it caught my eye is that it can do parallel delegation, well, sub agents and all of this, but it is not either a tool call or the way that Cloud Code or Codex do sub agents.

[04:06] It is something pretty different cuz Prime Intelligence, the developers of Prime Agent, created a new concept or I don't know if it's the concept itself is new, but they added a new function that the agent can use, that can write inside of the Python code, that will basically trigger a sub agent, okay? It's basically this function that I'm showing you here. And it's a completely independent agent session.

[04:39] So, it's not just a sub agent that we could think about in Cloud Code or Codex.

[04:46] It's more like Codex orchestrating different chats, different threads. I don't know if you have used that inside of Codex, but yeah, it would be something much more similar, but the cool stuff it's that the sub agent call itself can be written inside of the program or the Python logic. So, imagine that the agent decides that in some specific condition a sub agent will be needed, it can write it down in the same Python logic and it can do it.

[05:21] Also, inside of fours, inside of whatever. I think this is pretty, pretty powerful. Let me show you how it looks in practice. So, as you can see, I asked it just to improve the HTML Hello World file.

[05:35] And it started using this Python tool that it has and it basically called two childs, two sub agents with the RLM function I was talking about. This is the prompt and the name that it gave to the sub agent. And in this case, as my request was very very simple, they just started directly. I also asked it explicitly for two sub agents.

[06:04] And the orchestrator was just here checking how they were going.

[06:11] But yeah, in a much more complex task, something like uh I talked about the orchestrator deciding inside of the Python script when to call other sub agents and all of that would be pretty pretty beneficial. Oh, and by the way, the RLM function it's actually standing for recursive language model. That's how it was called by the developers.

[06:35] And it not only helps the LLM to be able to call sub agents and all of that through code, it also helps it manage the context. And in the official article, they also explain that they have something called continual harness.

[06:54] However, I didn't really give it that much importance to it cuz it's basically that the agent improves the harness automatically. It's something similar to Hermes automatic learning, but instead of just modifying the behavior or the system prompt or generating skills, uh it is also modifying the harness itself.

[07:21] But yeah, I think it's much more of pretty words instead of something that you would actually want cuz here you may have the same issue that you have with Hermes automatic learning is that they may learn the wrong things instead of learning what you want. So, you should be really, really careful with this.

[07:45] Review all the learnings once in a while to make sure that these learnings are not driving your agent towards some behaviors that you do not want them to to be having. So, these are the main aspects that really stuck out to me using Prime these last two days cuz it's really, really new. It just dropped.

[08:08] There are also some minor things that I haven't mentioned in the video. You have the official article down below in the description if you want to read it. But, yeah, I hope that you at least try it out and leave a comment down below telling us your experience with Prime Agent. So, yeah, see you on the next video.
