---
okf_version: "1.0"
source_kind: youtube
source_url: "https://youtu.be/IMLwvK08JVc"
video_id: "IMLwvK08JVc"
title: "Loop vs Graph Engineering — AI Agent Concepts Demystified"
fetched_at: "2026-08-03"
duration: "21:30"
segments: 610
language: en
---

[00:00] Everyone's the Sean. So, today let's
[00:01] talk about loop versus graph
[00:03] engineering. Two biggest buzzwords in AI
[00:06] agent space recently.
[00:08] I am probably exactly like most of you
[00:10] guys, which is I don't know how to keep
[00:12] up with all these new concepts, but I
[00:14] still find it very interesting because
[00:15] whenever a new concept becomes viral,
[00:18] you don't care about what triggered it.
[00:20] What I find out is that this guy called
[00:22] Peter Steinberger, who's also the author
[00:24] of this little lobster, which is open
[00:26] claw, said that are we still talking
[00:28] about loops or did we shift to graphs
[00:31] yet? And he posted this on July 18th,
[00:34] 2026, which is about 13 days ago.
[00:37] And he got 3 million views. And look at
[00:39] this first comment. It said, "Bro, stop.
[00:41] I am on vacation." So, sometimes, you
[00:44] know, these important people in the
[00:46] space who mention a keyword
[00:49] and then everybody's going to start
[00:50] talking about it. I remember exactly
[00:51] something like that in context
[00:53] engineering. And for today's video,
[00:56] we're going to demystify all of these
[00:57] core AI agent concepts and especially on
[01:00] loop and the graph. We're going to jump
[01:02] in a little bit on the system design
[01:04] real quick. And at the same time, we're
[01:06] also going to walk you through real
[01:07] coding example
[01:08] called Waku-Agent, which is under my
[01:11] GitHub, Shen Sean Chen. And we published
[01:13] this about 2-3 weeks ago and we got more
[01:15] than 700 stars. If you think this video
[01:17] is helpful for you, please give us a
[01:18] star, give us a like, and that will be
[01:20] very helpful for us.
[01:22] With Waku Agent, you can launch a
[01:23] dashboard like this and you can just ask
[01:24] something like,
[01:26] "What's up on my Google calendar
[01:30] on Thursday?"
[01:33] Right? And then it's going to check what
[01:34] kind of graph it's going to use, decide
[01:36] if it needs to retrieve some memories,
[01:39] and then eventually decide, you know,
[01:40] what kind of tools it should be using,
[01:41] right? Right here it's listing the
[01:43] events from my Google calendar and
[01:45] eventually send you the reply. And it
[01:47] did a bit of a testing in the eval
[01:49] system and then, you know, it spit out
[01:51] the gave me the final answer for the
[01:54] results. I'm I'm to talk a bit more
[01:56] details into this, but without further
[01:57] ado, let's get started on the system
[01:59] design. First thing first, I want to
[02:01] talk about the AI agent engineering
[02:03] ladder that we have come across over the
[02:05] past 3 years, because I think that it
[02:07] helps us to kind of understand where we
[02:11] came from and where we're going. And the
[02:13] first one is obviously prompt
[02:15] engineering. And to me, prompt
[02:16] engineering was the very first or early
[02:19] version of how people start to get used
[02:21] to how to use an LLM. I feel the most
[02:24] accurate word to describe this was
[02:26] role-playing, because at the very
[02:27] beginning, we didn't know how to use
[02:28] LLMs. We had ChatGPT 3.5, and we tell
[02:32] it, "Hey, you are the best poet, like
[02:34] you're Shakespeare or Li Bai, right?
[02:37] Write me a poem about this scenario I'm
[02:39] looking at right now in Switzerland."
[02:42] Then, it's going to pretend that it's
[02:44] one of those poets you just mentioned
[02:46] and then talk to you back, right? That
[02:47] was prompt engineering. You basically
[02:49] draft the prompt so that you control the
[02:52] LLM to talk to you in a certain way you
[02:54] want. And then we quickly evolved into
[02:57] what we call context engineering, and
[02:59] that is because when people move from
[03:03] just playing with LLM as a consumer
[03:05] product to actual workflows, you realize
[03:08] that you not only need prompt, but also
[03:10] you need to feed in data. For example,
[03:13] if you build a customer service chatbot
[03:15] or if you build a sales agent like
[03:18] Automaton, which is my company, you need
[03:19] to really think about how do you
[03:21] construct the context for the agent so
[03:24] that the agent will be able to talk to
[03:26] the clients on behalf of the businesses,
[03:29] right? So, you not only will say, "Hey,
[03:31] you are the best salesperson in the
[03:33] world." You will talk in certain ways,
[03:36] do not say certain things. You also need
[03:38] to feed in data such as what are some of
[03:40] the what are some of the customer
[03:41] relationship data people already have on
[03:43] their Excel sheets, Google sheets, CRM
[03:46] system, all these kind of things. And
[03:48] you need to really put them together and
[03:50] and sure that the agent can accurately
[03:52] be fed with the right information. So,
[03:54] we moved from prompt to context very
[03:56] quickly to fix that problem. And then we
[03:58] moved on to skills, which I think is
[04:02] sort of teaching the AI what kind of
[04:04] procedure it should follow. Right? It's
[04:07] a procedure following process. In AI
[04:10] agent harness, this is called procedural
[04:12] memory. It's a memory that you tell say
[04:14] you tell a kid, "Hey, when you walk
[04:17] home,
[04:18] walk on the right-hand side if you're in
[04:20] the US or in China. Right? Walk on the
[04:22] left-hand side if you're in the UK or
[04:24] Japan."
[04:25] That is a procedural memory that you
[04:28] want a person or you want an LLM to
[04:31] remember and there's no, you know, extra
[04:34] data. It's just fact that it should be
[04:35] doing when a certain situation happens.
[04:39] Okay? So, why do we need skills? It's
[04:41] because that if you just if you if you
[04:44] provide a lot of context to the AI,
[04:46] sometimes it can be a little repetitive.
[04:49] You don't want to always provide, you
[04:51] know, the same order to AI again and
[04:53] again and again. So, having a skill to
[04:56] determine a workflow becomes really
[04:58] really handy. Just like for example, if
[05:01] you're coding on Claude code, you don't
[05:03] want to explain to Claude that do not
[05:06] use emoji. Do not use emoji. Do not use
[05:08] emoji. Or I prefer to use emoji. Use
[05:11] these emojis. Don't use the brain emoji.
[05:13] I hate that. Right? These kind of things
[05:14] you have to repeatedly tell the context,
[05:16] then it's becoming less convenient. So,
[05:19] you can build up skill to make sure the
[05:21] LLM is following that procedure. Then
[05:23] comes with loop.
[05:25] What does loop do? A loop is basically
[05:28] saying, "Hey,
[05:29] maybe sometimes you have a goal and you
[05:32] want to finish that goal,
[05:34] but we don't know the exact skills that
[05:37] you should have to finish that goal. We
[05:39] might tell you, "Hey, there are a bunch
[05:40] of tools you can use." Right? That's why
[05:42] Anthropic came up with MCPs. You can
[05:44] call your Google calendar. You can call
[05:46] your Gmails, APIs, you can call your
[05:48] GitHub APIs. These tools become handy
[05:51] and then you're telling the LM be like,
[05:54] "Okay, run a loop. Know your goal, which
[05:56] is maybe help me fix this bug that the
[05:58] customer come up with.
[06:00] Run this loop and here a bunch of tools.
[06:02] Here's a web search tool. Here's a bunch
[06:04] of API calls. Here's a bunch of MCPs.
[06:07] Use them.
[06:08] Loop it until at some point you finish
[06:11] the goal and that's the end of the
[06:13] iteration. And then the question comes,
[06:15] why do we need graph?
[06:18] What does graph do? Because graph
[06:19] technically is a procedure that is
[06:21] predetermined.
[06:23] Many people are criticizing graph
[06:24] engineer is not new because maybe in
[06:27] 2023, I remember people were already
[06:29] using Airflow, people are using step
[06:30] functions, people are talking about how
[06:33] do we make sure that a deterministic
[06:35] workflow can be set up properly so that
[06:37] we don't just tell LM to make all the
[06:39] decisions. Because sometimes we know
[06:40] exactly how a certain task needs to be
[06:42] done. There's a step there. There's an
[06:43] SOP there.
[06:45] So, to me, it kind of feels like, "Okay,
[06:48] we moved from skills, which is strict
[06:50] procedure following, to loops, which is,
[06:53] 'Hey, agents, go figure it out
[06:54] yourself.'
[06:55] To eventually we realize that, 'Hey, we
[06:57] need a mixture of both.'
[06:59] That, in my opinion, is graph. Okay.
[07:02] Technically, sometimes you should write
[07:04] the skill first.
[07:06] Okay? How do you cross the road? On what
[07:09] side do you walk in the pavement
[07:11] depending on which country you're in?
[07:13] And how do you respond to a client? How
[07:15] do you respond to me when I'm coding
[07:16] with a coding agent, right? And then you
[07:19] should turn that into graph. If you see
[07:21] that there's a lot of repetition in the
[07:22] workflows, especially if you're building
[07:23] a workflow for corporate, right? If
[07:25] you're working in uh e-commerce, maybe
[07:28] you see exactly how your customer
[07:30] service should be answering questions
[07:32] related to logistics, to refund, to
[07:34] checking samples. You realize that you
[07:37] can really consolidate this into a graph
[07:39] when the workflow stops changing.
[07:41] Perhaps there's still part of the
[07:42] workflow that you need to use a loop,
[07:44] where the loop is basically doing this
[07:47] exploratory work out there, right? It's
[07:49] probably doing, you know, a bunch of
[07:50] research for you. Um, I think deep
[07:52] research is one of the early examples of
[07:55] a loop engineering workflow here, where
[07:57] you just say, "Hey, go crazy. Just go
[07:59] search the internet. I want a report."
[08:01] These kind of work are, you know, less
[08:03] standardized or it doesn't have an SOP
[08:05] in it. It's just about, "I want more
[08:08] information or I have a certain goal.
[08:11] Use the tools available to you to figure
[08:13] it out for me." It's quite different
[08:15] from graph, because sometimes we know
[08:17] exactly what tools they should be using
[08:19] to uh finalize a task for me. Okay,
[08:22] guys, that was a conceptual walk-through
[08:25] of this AI agent engineering ladder.
[08:28] Now, let's take a look at what a loop
[08:30] and a graph look like. What a loop does
[08:33] is that it discovers what to do next.
[08:35] So, maybe this is you, and you ask a
[08:37] question to the LLM, and the LLM is
[08:39] basically saying, "Hey, do I need to use
[08:41] any tools? If yes, choose a tool, run
[08:44] it, and check if it finished the task,
[08:47] come back to the LLM, and then loop it
[08:49] again and again and again until at some
[08:51] point LLM is like, "Hey,
[08:53] we don't need a tool anymore. Let's
[08:55] reply to the user." Examples here could
[08:57] be like, you are trying to fix a bug on
[09:00] a pull request on GitHub. You basically
[09:02] ask Claude Code or a Codex and be like,
[09:05] "Fix this bug. Tell me when it's fixed."
[09:07] And it's going to go ahead and then use
[09:09] a bunch of tools like web search, GitHub
[09:11] CLI, checking your Supabase, checking
[09:13] your AWS, checking your Google Cloud,
[09:15] all these kind of stuff. And then at the
[09:17] end saying, "Okay, we're done. Okay, and
[09:20] the bug is fixed because ABCD." That is
[09:22] a loop.
[09:23] A graph, on the other hand,
[09:26] is somewhat similar, but uh it's not
[09:28] exactly the same. So, you might have a
[09:30] So, you might have a standardized
[09:31] process every day and be like, "Ooh, I
[09:33] want to understand how many people have
[09:35] submitted pull requests overnight, and
[09:37] who are these guys who submitted some
[09:39] tasks? Can we take a look at them? And
[09:42] uh tell me if uh things are fixed or
[09:45] not. And maybe at the same time, I want
[09:47] to understand you know, what are some of
[09:49] the latest news out there on AI agents?
[09:51] And maybe we want to do some web search
[09:53] as well. Maybe we want to run some, you
[09:55] know, get commands. At the same time, we
[09:57] know exactly how it works. Okay.
[09:59] And then, you probably want this to be
[10:02] done in parallel. So, you ask a
[10:04] question, and then it's going to check
[10:05] out the GitHub to check out the pull
[10:07] request. It's going to search the
[10:08] website for you to do some exploratory
[10:11] analysis.
[10:12] And it's probably also going to check
[10:13] your calendar, plus the memories stored
[10:16] locally or on the cloud.
[10:19] And then it's going to synthesize these
[10:20] information and tell us, "Hey, is there
[10:22] anything else to do? If not, end this
[10:25] and reply. If yes, please explain what
[10:28] kind of things do you still need to do?"
[10:30] Okay. You see the difference here? So,
[10:32] sometimes you can have some loops here.
[10:34] Okay? Maybe having agent
[10:36] Maybe having an agent loop to search the
[10:38] web or have an agent loop to fix the
[10:40] bugs on GitHub is part of this graph.
[10:42] Okay? So, you can see graph is basically
[10:44] saying, "Okay, I know exactly what you
[10:46] should be checking. Maybe they're in
[10:48] parallel. Maybe they are happening in
[10:50] sequences.
[10:51] Do it the way I want. And uh once you
[10:54] finish, uh synthesize it and tell me the
[10:56] answer." Are you guys still with me?
[10:58] Let's check a real example first.
[11:00] Come back here. Let's come to Waku agent
[11:02] dashboard. The way you set it up, by the
[11:04] way, is come to this website,
[11:06] github.com/jasonchan/waku-agent.
[11:09] You can either click on code and copy
[11:11] this and then type into your terminal
[11:14] and say get clone and paste that in and
[11:17] hit enter, which is this way, and you
[11:19] can just copy this and paste in your
[11:20] terminal. Or recently, we released a new
[11:23] package in Python called waku-agent. All
[11:26] you need to do is copy this, pip install
[11:28] the Waku agent into your terminal, set
[11:30] up your environmental keys, and then you
[11:32] can launch a dashboard. Copy this.
[11:36] Paste this in.
[11:39] Because I'm already using port 777, so
[11:42] let's use 778 as an example.
[11:45] Paste that in. You can see, this is it.
[11:47] Okay.
[11:48] Since I already set it up, I'll come
[11:49] back to this local host 7777.
[11:52] So, what you're seeing right here is is
[11:54] an entire AI agent harness starting from
[11:56] the gateway, which can be the chat of
[11:58] here, or you can use some other channels
[12:01] such as Discord, Telegram, WhatsApp,
[12:03] stuff like that.
[12:04] And then it's going to check out the
[12:06] retrieval gate to see if you we need any
[12:08] procedural memory, which is skills, as
[12:10] we mentioned, or semantic memory or
[12:12] episodic memory, which are durable facts
[12:15] or the dated events that happened in
[12:17] your local memories. Okay. And then it's
[12:19] going to run through an agent loop using
[12:20] LLM agents and calling tools, and
[12:23] eventually give you the reply, during
[12:24] which the LLM ops is going to trace the
[12:26] data, test it, and then release it, the
[12:29] new version of the prompt, and the
[12:31] feedback to the harness. But this is the
[12:33] entire harness, okay? And loop
[12:35] engineering is happening here in this
[12:36] little loop.
[12:38] A graph workflow is basically after this
[12:40] gateway, you can predefine some of the
[12:43] process in between here. So, we have a
[12:44] new tab called graph. We currently have
[12:46] two graphs here. One is called triage,
[12:48] another one is called gather. For triage
[12:51] graph, what it does is that it's saying,
[12:53] okay, start point, and then it's going
[12:55] to classify if it requires some agent
[12:58] calls, and then at the same time, it
[12:59] might just check out my calendar and see
[13:01] what's going on. Okay. And I can just be
[13:03] like, what's up today?
[13:07] All right. You can see the trigger this
[13:09] triage first.
[13:10] Right. It checked the Google calendar
[13:11] for me, and also at the same time it was
[13:13] checking, you know, if we need to have
[13:15] some uh
[13:16] need to do some serious agent calls
[13:18] here. And it eventually decided that,
[13:21] okay, it's going to use some tools, so
[13:22] they used the list events, read the
[13:24] Apple calendar for me. So, you just saw
[13:26] a very simple graph kind of call
[13:28] already, okay? So, what it did was that
[13:31] it checked, as I mentioned, if it needs
[13:34] some serious agent calls, and in
[13:36] parallel at the same time was already
[13:38] checking my calendar because I was
[13:39] asking what's up today. And then after
[13:41] that, use this loop to run to call the
[13:43] tools like list events, read Apple
[13:45] calendar, and all these kind of stuff,
[13:47] and then give me the reply, okay? But
[13:49] what if I need to test something else?
[13:51] So, here in this local agent harness,
[13:53] what I recently updated is that you can
[13:56] very clearly mention the workflow you
[13:58] built in the graph we have gathered. So,
[14:00] we can clearly say {slash} gather, and
[14:03] then say, "Tell me what's up with Waku
[14:06] agents and any new PRs, any competitive
[14:09] projects?" Let's see what happens.
[14:12] You see that it triggered this graph
[14:13] instead, okay? Let's come back to the
[14:15] overview. It's showing me that it was
[14:17] triggering triggering this graph,
[14:19] and it used these tools simultaneously,
[14:23] and then it's synthesizing the answers
[14:25] for me, okay? So, So, what it did was
[14:28] that it gave me a morning brief on what
[14:31] kind of PRs are out there, and we have
[14:34] released a new package, which you can
[14:36] check in this GitHub. If you scroll down
[14:38] a little bit, we have released a new
[14:39] package for agent graphs recently.
[14:42] And also there are some PRs to be
[14:44] reviewed, okay? Let's come here.
[14:47] So, if you click into pull request, you
[14:49] can see there are some PRs here for me
[14:50] to be reviewed. And for the web search,
[14:52] you can see that it researched about
[14:55] Harrison Chase published your harness,
[14:56] your memory, and your own videos in
[14:59] Harness Eval our ranking.
[15:01] Okay? It was doing the committed
[15:03] research for me as well.
[15:05] All right. So, you might be wondering,
[15:07] how did this work? If we come back to
[15:08] the tab for graph, you can see that we
[15:11] clearly defined two different use cases
[15:13] of graph. One is triage, another one is
[15:14] gather, and they have very specific ways
[15:18] of running the agents. And they both use
[15:21] loops because when it does the web
[15:22] search, it needs to search for the
[15:24] information and come back to me, right?
[15:26] When it does the check calendar, it's
[15:27] going to check the calendar until it
[15:29] find out what's going on in my calendar
[15:31] and then come back to me. Okay? These
[15:33] things are all kind of intertwined in
[15:34] this agent harness system. So, if we
[15:36] come back to the system design, there's
[15:38] something interesting I wanted to show
[15:39] you, which is how exactly did the
[15:42] workflow finish from the beginning to
[15:43] the end from a time usage and parallel
[15:46] processing perspective. For the loop, an
[15:49] agent loop, for example, web search is
[15:51] going to decide what's going to do
[15:52] first, right? It's going to check out my
[15:54] GitHub calling tools one by one, right?
[15:57] Maybe after the first call, the agent
[15:59] decided, "Okay, we need to do one more
[16:01] call." And then it called again. And
[16:03] then it checked the calendar. And then
[16:05] it synthesized information for me.
[16:07] But in the graph example, which is
[16:10] this which is this workflow for
[16:12] gathering information, it used multiple
[16:15] tools in parallel because we already
[16:16] told it, "You need these four tools."
[16:18] All right? Maybe each one of them spent
[16:19] different amount of time. And then it
[16:21] did the GitHub first and the web search
[16:23] did the most amount of time. Calendar
[16:25] check and memory retrieval did the least
[16:27] amount of time because it's instant or
[16:29] maybe because you did not need any
[16:31] memory retrieval at all. And then it's
[16:33] going to synthesize the information
[16:35] because it's got a lot of context at the
[16:36] same time. So, you cannot say So, you
[16:39] cannot say graph engineering is
[16:41] replacing loop engineering because
[16:42] they're coexisting. And you cannot say
[16:44] loop engineering or graph engineering is
[16:46] better than the other one because we
[16:47] need them in different use cases. A loop
[16:50] is something you need when the model
[16:51] decides what to call one step at a time.
[16:54] A graph is something like when you know
[16:55] the shape and you just want them to move
[16:59] together. Okay? It really depends on the
[17:01] situations you're building for. In your
[17:03] local files, we have a Waku folder and
[17:06] then we have a graph folder.
[17:08] And within the graph folder, we have
[17:10] defined the graph engines, which is
[17:14] literally a graph.
[17:16] And you're going to add nodes, which in
[17:18] our case could be web search tools,
[17:20] could be agent calls, could be MCPs,
[17:22] anything. Right? And then you're also
[17:24] defining edges, which is okay, do we
[17:26] where do we go after using one tool?
[17:29] Right? After the web search, do we go
[17:30] synthesizing or do we go somewhere else?
[17:32] Right?
[17:34] graph We also defined nodes, which is
[17:37] basically saying what kind of things can
[17:39] be a node, right? Tool calls, LLM calls,
[17:42] agent calls,
[17:44] router, these kind of things.
[17:46] And then under this graph folder, we
[17:47] also have another folder called
[17:48] workflows.
[17:49] You can see we have triage here. Right?
[17:52] Remember triage was the chart we showed
[17:53] you, which is it's going to classify if
[17:55] it needs to use some complex models to
[17:57] do agent calls
[17:58] or is it doing some calendar checking at
[18:01] the same time? These two things are
[18:02] happening in parallel at the same time.
[18:05] And the code is actually very simple.
[18:06] You define
[18:07] these functionalities to make sure that
[18:09] the triage graph is working properly
[18:12] with this predefined workflows. Right?
[18:15] You add node when you need to add in one
[18:16] more tools. You add edges by defining
[18:19] initial prompt from start can go to
[18:22] either classify or check calendar at the
[18:24] same time.
[18:26] If we check out the gather tool as well,
[18:29] remember it's the similar thing as our
[18:31] previous chart. You can scan the GitHub,
[18:33] website, check calendar, memory. Okay?
[18:36] GitHub, website, calendar, memory.
[18:39] These are all the nodes and after that
[18:41] you synthesize it and then you decide if
[18:43] we should return the answer. Obviously,
[18:45] we're doing the same thing. We're
[18:46] building the graph here, right? We're
[18:48] adding these nodes
[18:50] and we are adding those edges too. I
[18:53] hope this is easy to understand. And you
[18:55] can feel free to add more .py files here
[18:58] to make this graph workflows even
[19:01] larger. And in that case, I will have to
[19:03] work with you and then you can feel free
[19:04] to contribute to this repo and be one of
[19:07] our contributors and submit your own
[19:08] workflows because once you submit your
[19:11] own workflows and it's approved, it will
[19:13] be available here. If I type in {slash}
[19:16] graphs, it's going to tell me that
[19:20] it has gather, it has triage, and triage
[19:22] is the router. It's basically going to
[19:24] read the local files of these workflows
[19:26] from graphs, and people can use it. This
[19:29] would be very cool, and it will probably
[19:31] become a community if you guys are
[19:32] interested. And feel free to be our
[19:34] contributors. And also, if you're
[19:36] interested in talking more in depth of
[19:38] these concepts with us, if you feel you
[19:39] want to discuss with me about your
[19:41] workflows and any questions you have
[19:42] about system design, about these agent
[19:44] harness, all these kind of stuff, you
[19:45] can feel free to come to my personal
[19:47] website, shaunchand.io, and then click
[19:49] on join over here to join our community.
[19:52] I'm just getting started to do this
[19:54] because I don't have enough time to
[19:55] answer all of your questions. I feel
[19:57] like the most efficient way is that I
[19:58] can host some live sessions with you uh
[20:00] twice a month, so that I'll be able to
[20:02] answer most of your questions, and we
[20:04] can prepare some, you know, build
[20:05] sessions real time, showing you my real
[20:07] setup, all these kind of stuff. If you
[20:08] join this community, you will be
[20:10] assigned to a private Discord,
[20:13] and I will share more details there,
[20:15] including the original files of all of
[20:17] these system design charts that I have
[20:19] built in the past uh in real code, so
[20:21] that you can sort of open it in your own
[20:23] Excalidraw website to learn about it.
[20:26] So, last but not least, I think this is
[20:27] an important question we should ask
[20:29] ourselves, which is
[20:31] isn't this just a deterministic workflow
[20:32] from 2023? What is new now is that some
[20:35] of the nodes we're using right now are
[20:37] non-deterministic. Could be a LLM call.
[20:39] And at the same time, sometimes the
[20:41] model can pick the edge, all right? The
[20:43] routing is the way that, you know,
[20:45] you're letting the LLM as a judge, do I
[20:47] pick a simple model to answer the
[20:49] questions, or do I use a more complex
[20:53] model to answer this question? And also,
[20:55] you need some guards, which a previous
[20:58] directional graph scheduling never did.
[21:00] These are buzzwords, as I mentioned, but
[21:02] buzzwords are viral for a reason.
[21:04] Sometimes it's because of famous people,
[21:05] sometimes because it's actually useful.
[21:07] So, I hope this kind of videos is
[21:10] helpful for you. And again, if you have
[21:12] any questions, feel free to ask me. And
[21:14] I would love to answer your questions
[21:16] live in our community. Just come to my
[21:18] personal website shaun.io and then there
[21:20] are a bunch of sources here. Looking
[21:22] forward to And if you love this project,
[21:24] please give a star on GitHub repo and I
[21:26] would love to work with you. Thank you
[21:28] so much for your attention. Appreciate
[21:30] it.