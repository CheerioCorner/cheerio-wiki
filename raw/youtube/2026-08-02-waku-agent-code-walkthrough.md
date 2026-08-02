---
title: "Waku Waku! Waku agent is your personal AI agent, on your own laptop, in code you can read in an afternoon — harness + loop + memory + eval"
source_url: "https://youtu.be/rvRyBhILrls"
video_id: rvRyBhILrls
author: "Sean Chen (ShenSeanChen)"
language: en
duration_seconds: 1249
segment_count: 601
clipped: 2026-08-02
type: "raw-source"
source_kind: "youtube"
tags:
  - "raw"
  - "youtube-transcript"
---

# Waku Waku! Waku agent — code walkthrough

> Source: [YouTube — Waku Waku! Waku agent is your personal AI agent](https://youtu.be/rvRyBhILrls)
> Channel: [Sean's AI Stories](https://www.youtube.com/@SeanAIStories)
> Duration: 20:49 / 601 segments

## Transcript

**[00:00]** Here's a Sean. So, today I'm going to
**[00:01]** walk you through a real coding example
**[00:03]** of Asian harness system with loop
**[00:05]** engineering eval and memories. I have
**[00:07]** open sourced a local first-person
**[00:09]** assistant that will cover the entire
**[00:11]** four pillars that matters to an Asian
**[00:13]** system today, which is harness, loop,
**[00:15]** memory, eval and LM ops as we mentioned.
**[00:17]** Many people were asking me if I could
**[00:18]** show some real examples, so here we are.
**[00:21]** GitHub is called Waku-Agent under my
**[00:23]** GitHub called Shen Sean Chan. I'm going
**[00:25]** to walk you through exactly how to use
**[00:27]** this thing. A working example looks
**[00:28]** something like this. If you have tried
**[00:30]** Open Claw or Hermes agent, it works very
**[00:33]** similarly. A very quick example would be
**[00:35]** find me the rest of the World Cup games
**[00:36]** and add all of them to my calendar.
**[00:39]** Let's see. So, you can see that it
**[00:41]** checked if you needed some retrieval
**[00:43]** and then it realized that no retrieval
**[00:45]** was needed. So, now it's running a loop
**[00:46]** engine here using all the tools such as
**[00:49]** search the web
**[00:50]** and it's still thinking looping until
**[00:53]** it's finishing its task.
**[00:55]** So, you can see that the loop
**[00:56]** engineering is working around the LM
**[00:58]** agent. And meanwhile, you can click into
**[01:00]** the tools to see what kind of tools are
**[01:02]** available.
**[01:03]** And you can click into the loop it to
**[01:05]** see the live streaming of looping, but
**[01:07]** we can see that in the chat, too. So, we
**[01:09]** can come back to this overview to watch
**[01:11]** this system architecture. In the
**[01:13]** meantime, it's showing us how much money
**[01:15]** it's spending. Currently, we're by
**[01:16]** default using Anthropic APIs, but you
**[01:18]** can definitely use some cheaper models
**[01:20]** like open source ones. Cool. I think it
**[01:22]** finished it and then it's asking me if
**[01:23]** it can access my calendar. I'll just
**[01:25]** allow it. So, you can see this is a
**[01:26]** completely local agent that ran through
**[01:28]** the loop to search across the internet
**[01:31]** and eventually create the event on my
**[01:32]** calendar. So, we can go to my calendar
**[01:34]** and see, okay, you successfully added
**[01:36]** the Norway game for tonight. I'm a big
**[01:39]** fan of Holland. Let's see if they could
**[01:40]** win, but I live in London, so I also
**[01:41]** support England. So, it's going to be a
**[01:43]** fun game. I think by the time when we
**[01:44]** publish this video, we will already know
**[01:46]** the result, so we'll see about that. So,
**[01:48]** again, if we recap, we have a history of
**[01:49]** the entire loop that the agent ran
**[01:51]** through. Apparently, the agent called
**[01:53]** the create event tool and the search web
**[01:55]** tool. And you can also check the results
**[01:57]** here
**[01:58]** where I added the World Cup event
**[02:01]** between Norway and England for today
**[02:03]** over here. This is a previous event I
**[02:05]** added for with my friend Sergey. So, if
**[02:07]** I don't know who Sergey is, I can just
**[02:09]** ask Wakakku and be like, "Who is
**[02:11]** Sergey?"
**[02:12]** So, you can see that it did the
**[02:13]** retrieval.
**[02:14]** It went through the memory system, and
**[02:16]** then it told me that it went through the
**[02:18]** gate for retrieval checking, and they
**[02:20]** find out that Sergey is my close friend.
**[02:22]** We can double check that. So, this kind
**[02:24]** of information usually will be stored in
**[02:26]** semantic memory because it's durable
**[02:28]** fact. So, if I click into that, I have
**[02:30]** three main semantic memory here. One is
**[02:32]** that Sergey is a close friend who loves
**[02:34]** swimming and often cooks delicious food.
**[02:37]** Raj is a close friend who plays really
**[02:38]** great tennis and always teaches me great
**[02:40]** British slangs. And for myself, my
**[02:43]** channel is usually called Shawn's AI
**[02:44]** Stories, and my X account is Shen Shawn
**[02:47]** Shan. All of my Chinese account is
**[02:49]** called the Xiao Engine Shawn. Okay. I
**[02:52]** can ask you things like, "What are my
**[02:53]** social media accounts?"
**[02:55]** So, again, it retrieved the memory
**[02:56]** again, and it ran through the LLM agent.
**[03:00]** It turns out that there's no tool that's
**[03:02]** needed, so it just returned the results
**[03:04]** for me. Okay. What you have seen already
**[03:05]** is a demo of this open-source project
**[03:07]** that runs through the agent harness with
**[03:09]** loop, with memory retrieval, and last
**[03:12]** but not least with LLM ops. So, if you
**[03:13]** click into trace, you can see the exact
**[03:15]** tracing of how much tokens, how much
**[03:17]** money I have spent, how many LLM calls.
**[03:20]** And if you scroll down a little bit, you
**[03:21]** can see the exact things that activated
**[03:23]** the retrieval gate in the system that
**[03:26]** fetch information from the database.
**[03:28]** And also, what are some of the tasks
**[03:30]** that took the longest time. So, the one
**[03:33]** that I asked about with the World Cup
**[03:34]** games took me almost 100 seconds. All of
**[03:38]** these information are actually stored
**[03:40]** in the local files called traces. I'm
**[03:43]** going to show you exactly where that is
**[03:44]** in the code, and how we can implement
**[03:46]** this step by step. Another way to test
**[03:48]** this is using a gateway such as
**[03:50]** Telegram.
**[03:51]** So, what you need to do is if you wanted
**[03:53]** to build a bot yourself, you can come to
**[03:55]** this agent called BotFather. And
**[03:57]** literally what you do is that you do
**[03:58]** {slash} start, {slash} new bot, and then
**[04:02]** give it a name. I tried many names, but
**[04:04]** many of them were taken. So, my bot is
**[04:05]** called Waku Waku AI Bot.
**[04:07]** So, if I come here
**[04:09]** and click on start, I can just ask a
**[04:11]** question, be like, "What World Cup game
**[04:14]** is on my calendar?" Cool, it says that
**[04:17]** we have the Norway game. Just for sure,
**[04:19]** I can ask, "Who is Raj?" In the
**[04:21]** meantime, we can see that there should
**[04:23]** be a history. There we go.
**[04:25]** Yeah, it's real-time over here. Okay,
**[04:27]** it's been synced here. By the way,
**[04:29]** everything you have seen right now is
**[04:30]** local. It's stored in your computer, so
**[04:32]** it's safe and secure with your own
**[04:34]** machine.
**[04:35]** I can even add a memory to it. I can be
**[04:37]** like, "Please remember that Vincent and
**[04:39]** I went to Paris to do an
**[04:40]** entrepreneurship interview together, and
**[04:42]** now we're both building AI startups and
**[04:44]** doing great." And if we come back here,
**[04:46]** you can see that it's using the tools.
**[04:49]** And I'm pretty sure it's just users, you
**[04:51]** see? It says that the user was storing
**[04:53]** the memory, and it called the tool
**[04:54]** called save note. So, technically now it
**[04:57]** should have saved the memory. This kind
**[04:59]** of thing should be semantic memory. So,
**[05:01]** if we click into semantic, you can see
**[05:03]** that Vincent has a new fact called
**[05:04]** Vincent and the user went to Paris
**[05:06]** together. Okay.
**[05:07]** Anyways, what I did was just I just went
**[05:09]** in and showed you how the entire harness
**[05:11]** and memory and eval and tool calling and
**[05:13]** loop system works, right? For those of
**[05:15]** you who are not familiar with these kind
**[05:16]** of things, just remember that it's all
**[05:18]** buzzwords. What I literally demoed just
**[05:20]** now is exactly what these things mean,
**[05:23]** okay? And now in the in the second part
**[05:25]** of this video, I will walk through this
**[05:26]** codebase that I built. And ideally, if
**[05:28]** you're interested, you can click on the
**[05:30]** star and contribute to this public repo
**[05:32]** together. Are we ready to read the code?
**[05:34]** Let's get started. Again, we're here on
**[05:35]** this GitHub repo, Shen Shen Chen {slash}
**[05:37]** Waku {slash} agent. And you can scroll
**[05:40]** down here to quick start.
**[05:42]** And literally, we just copy this.
**[05:45]** Okay.
**[05:45]** Come to a terminal and paste that in.
**[05:50]** So, you just clone the Waku agent. It's
**[05:52]** trying localhost:778
**[05:54]** because 777
**[05:56]** was already occupied, but that's fine.
**[05:58]** So, let's come here. So, you can see
**[06:00]** this is a new repo that we just built,
**[06:01]** so everything is fresh. All right,
**[06:03]** because we just started a new project,
**[06:05]** so it's empty. So, in that case, we
**[06:07]** should go check out the project we just
**[06:09]** run because it should already have
**[06:11]** something there. Let's come back here.
**[06:13]** And we go to database and click on SQL
**[06:16]** console and then hit on run.
**[06:19]** You can see that we've got all these
**[06:21]** data tables right here. I'm also just
**[06:24]** going to switch back to my previous
**[06:25]** folder.
**[06:26]** And if I come to dot Waku and check on
**[06:28]** database,
**[06:31]** you can see that it's much longer
**[06:32]** already. All right.
**[06:34]** And this is exactly where we're going to
**[06:36]** store the data. All right. We have the
**[06:38]** second thing called soul.md. This is
**[06:40]** basically the system prompt in this
**[06:42]** open-source project. And you can just
**[06:44]** modify this in the dashboard, too. If
**[06:46]** you come to memory, and there's a soul
**[06:49]** file here. And by default says, "If you
**[06:52]** feel Waku Waku, you can often say Waku
**[06:54]** Waku as part of the catchy phrases."
**[06:56]** Again, feel free to modify and add
**[06:57]** something like, "Also say muchas gracias
**[07:02]** when you feel thankful." That means
**[07:05]** thank you very much in Spanish. And you
**[07:06]** can click on save.
**[07:08]** And if we come back to the code,
**[07:10]** you will be able to see that it's
**[07:12]** updated real time. Okay? So, this is
**[07:15]** your local system prompt, soul.md, that
**[07:18]** will modify your local agent to talk the
**[07:20]** way you want. We can just test this one
**[07:22]** more time. Maybe click on new chat. I'll
**[07:24]** just say, "You're the best AI agent in
**[07:26]** the world." And he says, "Muchas
**[07:28]** gracias." There we go. So, what else is
**[07:30]** in the code? We have a memory.md, okay?
**[07:33]** This is where we stored some facts or
**[07:36]** durable facts, okay? These durable facts
**[07:39]** are the ones that are default, and you
**[07:41]** can see that previously we added
**[07:42]** Vincent, who I went to Paris together
**[07:45]** for entrepreneurship interview, it's
**[07:46]** also saved here.
**[07:47]** If we come back to the chart, that's
**[07:49]** still the semantic memory. If you click
**[07:51]** into it, right? You can also edit it
**[07:53]** right here. All right. Now, all the
**[07:55]** stuff that we run are also in the trace
**[07:57]** here. All right, if we click into that,
**[08:00]** you can see there is a whole list of
**[08:02]** events that happened. Let's take a few
**[08:04]** look. Okay, it recorded the tools, the
**[08:07]** tools used, for example, search the web,
**[08:10]** and then later it shows you the results
**[08:13]** with uh the usage of tokens in and out,
**[08:16]** and when it happened, all right? And
**[08:19]** these are very important things because
**[08:21]** if you use some tracing tools like
**[08:22]** LangFuse or LangGraph, that basically
**[08:25]** helps you track these kind of things.
**[08:27]** But with a local machine, what you want
**[08:28]** is to save this in your local computer
**[08:31]** because that's your personal assistant.
**[08:33]** And of course, if you want to trace this
**[08:35]** on a cloud, you can upgrade this and
**[08:37]** connect it to Superbase or just use
**[08:39]** LangFuse directly. Again, this project
**[08:41]** is an implementation of the harness
**[08:43]** system on your own local machine.
**[08:46]** What else is here? That's pretty much it
**[08:47]** for the memory stuff. We can also check
**[08:49]** out the eval folder. Okay. So, there are
**[08:52]** two main folders here. One is called
**[08:53]** deterministic. Another one is called
**[08:55]** judge.
**[08:57]** A deterministic place is basically we
**[09:00]** wrote out some rules of exactly what we
**[09:03]** want to test, okay? And we come back to
**[09:05]** the system.
**[09:06]** It's basically here. Right after the
**[09:08]** agent replied to you, it will go through
**[09:10]** the tracing and then run the eval, okay?
**[09:13]** Now, we're looking at the deterministic
**[09:14]** test. When we have a few tools, here is
**[09:16]** checking if Apple Calendar was working
**[09:18]** perfectly, and this one is checking if
**[09:20]** the working memory is working properly
**[09:21]** cuz some of cuz sometimes the eval can
**[09:23]** be very simple rules and you can just
**[09:25]** test if it's working, all right? But
**[09:27]** sometimes it's not true because you
**[09:28]** might want LLM or AI as a judge. This
**[09:31]** was also mentioned in our original
**[09:33]** system design. If you zoom in a little
**[09:34]** bit, after we trace every single run,
**[09:37]** where we use a valve to to check out
**[09:40]** some very qualitative questions, right?
**[09:41]** Was this something good or not? Was it
**[09:43]** healthy or not?
**[09:44]** So, in the code, let's say, we have
**[09:46]** Anthropic as a judge. It basically runs
**[09:49]** Anthropic models to assess some results,
**[09:51]** but I didn't write anything complicated
**[09:53]** here yet.
**[09:54]** Um for example, this one is for testing
**[09:57]** the response quality. You go crazy,
**[09:59]** guys. Give a star, clone this repo, and
**[10:01]** add your own test, and you can check it
**[10:03]** out. All of those will be traced here in
**[10:06]** the eval, okay? If you click into that,
**[10:08]** it's everything is in the ops tab. What
**[10:10]** else do we have? So, we also have skills
**[10:13]** here, okay? So, remember we scheduled a
**[10:16]** meeting for the World Cup.
**[10:19]** So, we have a skill here that says
**[10:22]** resolve the relative dates,
**[10:24]** check the memory context for attendees'
**[10:26]** preferences, call the tool create event,
**[10:29]** uh which will allow the agent to run the
**[10:31]** tools, okay? So, this is technically a
**[10:33]** memory, too, but it's called procedural
**[10:35]** memory. It's basically how you expect
**[10:37]** the agent to act. So, if we come back
**[10:39]** and check out the overview,
**[10:41]** again, when the user asks a question
**[10:43]** through the gateway, it could be a
**[10:45]** Telegram, it could be from this chat
**[10:47]** itself, and then it runs through the
**[10:49]** memory system, it checks if it's going
**[10:50]** to retrieve memory from these kind of
**[10:52]** things, and then it's going to tell the
**[10:54]** agent what to do. All we're doing right
**[10:56]** now is preparing the right context for
**[10:57]** the agent, okay? And the skills is is as
**[11:00]** I mentioned, the procedural memory,
**[11:02]** okay? If I click in procedural memory,
**[11:04]** you can see we have two skills here. One
**[11:06]** is schedule meeting, okay? Which is
**[11:08]** literally what we just saw in the code.
**[11:10]** We can modify and be like, always add a
**[11:12]** heart emoji on the calendar event, and
**[11:15]** save that skill.
**[11:16]** So, the next time if I say, for example,
**[11:18]** help me schedule a meeting with Sam
**[11:20]** Altman at 10:00 p.m. today in London,
**[11:22]** and send it. Let's see what's happening.
**[11:24]** You see, it created the event, and then
**[11:25]** put a heart there. If we check the
**[11:27]** tools,
**[11:28]** we can see that uh it scheduled this new
**[11:31]** meeting with Sam Altman.
**[11:32]** If I check my calendar, you can see
**[11:34]** right here, meeting with Sam Altman with
**[11:36]** a heart. So, basically the procedure
**[11:37]** memory worked, which was running the
**[11:39]** updated skill.
**[11:40]** Guys, this is really fun. We should just
**[11:42]** keep adding skills to this, okay? You
**[11:44]** can either Well, I I probably need a
**[11:45]** feature here to let you add it, but you
**[11:48]** can just add it in the code. Remember
**[11:49]** there's a folder called skills, and
**[11:51]** currently we have two skills here. You
**[11:53]** can literally just create a new folder
**[11:54]** here. Let's call it Sean AI story
**[11:56]** YouTube title.
**[11:58]** And then within this folder, let's
**[11:59]** create a new file called skill
**[12:02]** .md. We can just copy this and come
**[12:05]** here, or you can use clock code to write
**[12:07]** it for you, but we can just do it
**[12:08]** ourselves. Let's call it Sean AI stories
**[12:13]** YouTube title.
**[12:16]** And this is just description, let's say
**[12:19]** uh
**[12:20]** read Sean's AI stories on YouTube and
**[12:24]** find out the most
**[12:26]** popular way to write titles given the
**[12:30]** video content.
**[12:32]** So, we're just going to keep it simple,
**[12:33]** right? We're going to say read the
**[12:35]** memory from Sean's YouTube channel and
**[12:38]** then search the web using the tool
**[12:39]** search web
**[12:41]** to confirm the latest 5 to 10 videos.
**[12:45]** And then read the user's brief on the
**[12:46]** latest video content and return a good
**[12:48]** title like my own style. All right?
**[12:50]** Let's see if it works, okay? Firstly,
**[12:52]** let's come to the memory and check out
**[12:54]** the skills, which is the procedure
**[12:55]** memory. You can see that there's a new
**[12:56]** skill here.
**[12:58]** If I just create a new chat, so my
**[13:00]** problem is that help me create a new
**[13:01]** video title for my YouTube channel to
**[13:03]** promote this Waku agent, um which is
**[13:06]** this current codebase. Let's see.
**[13:11]** So, let's see the loop.
**[13:13]** Nice. It searched the web first because
**[13:15]** we asked it to.
**[13:18]** Still thinking.
**[13:20]** And then got the results. Cool. It It
**[13:22]** read my channel and then it gave me uh
**[13:24]** some titles.
**[13:25]** I I my own AI agent that runs my life.
**[13:27]** Meet Waku.
**[13:28]** All right, this this skill is not that
**[13:30]** good, but you see what I mean. You can
**[13:31]** just make it better by continuing to
**[13:33]** iterate it. And over time, everything
**[13:34]** will be stored locally. Remember that
**[13:36]** this entire thing is a local host.
**[13:38]** There's nothing on the cloud. You
**[13:39]** literally own this agent, just like
**[13:41]** Hermes, just like Openclaw. But it's
**[13:43]** very simple and straightforward. You
**[13:45]** literally just clone this GitHub repo
**[13:47]** and then run it
**[13:48]** and run the dashboard. And then you get
**[13:50]** it. You have an agent on your laptop.
**[13:52]** Oh, by the way, almost forgot. You need
**[13:54]** to come to settings and then paste in
**[13:58]** the API keys for the relevant models you
**[14:00]** have, okay? So, currently I have put in
**[14:03]** Anthropic key and Gemini key, but you
**[14:05]** can put in other keys, too. Web search,
**[14:07]** you also need to put in this thing
**[14:09]** called Tavily Tavily API key, okay? Just
**[14:12]** to keep that in mind, which is something
**[14:14]** you need to do as a setup.
**[14:16]** We have walked through all the memory
**[14:17]** stuff and the skills, which is the
**[14:19]** procedural memory. We also walked
**[14:21]** through the evals. What's left is that
**[14:23]** in this Waku folder, we have all the
**[14:27]** code here, okay? The gateway literally
**[14:29]** has the Telegram right here, and we also
**[14:32]** have a voice mode,
**[14:33]** which means that you can activate it
**[14:35]** through voice.
**[14:36]** Uh we can try real quick. We can go for
**[14:38]** UV run Waku voice. And then now it's
**[14:40]** listening to me, okay? So, it's trying
**[14:42]** to capture these these activation word.
**[14:45]** If I say, "Waku Waku."
**[14:50]** Waku Waku.
**[14:51]** >> Yes, sir.
**[14:52]** >> So, I'm going to come here.
**[14:54]** Hey, uh how are you two doing today?
**[14:56]** >> Waku Waku. I'm ready whenever you are.
**[14:59]** What can I help you with?
**[15:00]** >> I want you to show me what we have set
**[15:04]** up on our calendar.
**[15:09]** All right, did the retrieval.
**[15:11]** Running the agent loop. Come on.
**[15:15]** Good. Look at this.
**[15:16]** >> Here's what's on your local calendar
**[15:18]** from today onward. Waku Waku. Event when
**[15:21]** with swim with Sergi sat gel 11 5006
**[15:26]** 00:00 p.m. Sergi.
**[15:30]** >> My Sergi and Sergi.
**[15:32]** >> Norway versus England sat gel 11 10:00
**[15:34]** 00:00 p.m. 12:00 00:00 a.m.
**[15:38]** >> Okay, that's it Waku Waku. Thank you
**[15:40]** very much. So apparently the voice was
**[15:42]** not that good yet. If you guys would
**[15:43]** love to contribute to this open source,
**[15:45]** you can work on the voice side. I would
**[15:48]** really appreciate that. And let's build
**[15:49]** this up together. But you can see that
**[15:51]** we can use voice to control this and how
**[15:52]** exciting is that? And some of you
**[15:54]** probably realized that in the previous
**[15:57]** part of this video, you sometimes see
**[15:59]** Jarvis in this because I previously was
**[16:01]** calling this a Jarvis project. But then
**[16:03]** I just realized that you know, it's not
**[16:05]** very exciting. Everybody's building a
**[16:07]** Jarvis. So I just thought about what
**[16:09]** mean what excitement means, right? And
**[16:11]** in Japanese is Waku Waku. So that's why
**[16:13]** I wanted Waku Waku to be the wake up
**[16:15]** word for this voice agent. I'll continue
**[16:17]** to build on this as a fun project. But
**[16:20]** the point is that you can control this
**[16:22]** entire harness with memory using voice
**[16:24]** as well. Okay.
**[16:26]** So that was the gateway and the loop
**[16:29]** code so here can click into agent.
**[16:32]** You can see that there's this entire
**[16:35]** loop agent right here. There's an
**[16:37]** iteration. Okay. So by default we set
**[16:40]** the maximize maximum iterations to be
**[16:42]** 10. So it's going to run through this
**[16:44]** thing across at least 10 times until it
**[16:47]** reaches the goal for the LM agent. And
**[16:49]** the model is basically setting up all of
**[16:51]** these AI models. And memory code right
**[16:55]** here is basically how we store the
**[16:57]** episodic memory and store or load the
**[17:00]** procedure memory which are the skills
**[17:01]** and the semantic memory. Okay. Semantic
**[17:04]** memory. So this is the system. By the
**[17:07]** way, for the episodic memory we haven't
**[17:08]** covered this so far. Let's come back to
**[17:10]** the chart. Remember procedure is skill,
**[17:12]** semantic is for the some doable facts
**[17:14]** which are constantly being updated by
**[17:15]** this consolidation task. And then an
**[17:18]** episodic one is basically a dated event.
**[17:21]** For example, I was asking it to to help
**[17:23]** me label all the World Cup games. These
**[17:25]** are all episodic memory because they're
**[17:27]** they're dated.
**[17:28]** On the other hand, the semantic memory
**[17:30]** is basically consolidated every single
**[17:32]** time when it feels like there's some
**[17:34]** durable facts that it should be saved.
**[17:36]** And all of these are in these code. You
**[17:38]** can feel free to dive into it and then
**[17:39]** make it better if you're contributing to
**[17:41]** this open source.
**[17:42]** Ops is how we're going to run the
**[17:44]** dashboard, release some new prompts,
**[17:47]** and how do we do the tracing. Runtime is
**[17:50]** basically it controls all the sessions
**[17:53]** for every agent run.
**[17:54]** Tools are very important. Uh we have the
**[17:58]** Apple calendar tool. We can also add in
**[18:00]** some MCPs, which will be some iterations
**[18:02]** in the future.
**[18:04]** You can even write some notes, do some
**[18:07]** search using the Tavily API, okay? These
**[18:10]** things we have already showed you.
**[18:12]** All the code is right here. And the main
**[18:14]** app is here. Class Waku, which is
**[18:16]** literally just a Q&A agent because it's
**[18:18]** thinking about how to respond to you
**[18:19]** after it has digested the model, the
**[18:21]** client system prompt, messages, tools,
**[18:25]** maximum iterations, maximum tokens,
**[18:27]** observers, streaming data, all these
**[18:29]** things. All right, I think this is a
**[18:32]** very brief walk-through of this coding
**[18:36]** example with an open-source project to
**[18:39]** go through agent harness as a whole
**[18:41]** concept. Just to recap again, it happens
**[18:44]** when you have a gateway, when the user
**[18:46]** is sending a request,
**[18:47]** and all the agents trying to do is how
**[18:51]** do we prepare the right memory, which
**[18:53]** could require some task. So there
**[18:54]** there's a retrieval gate here deciding
**[18:56]** if we should retrieve or not. You know,
**[18:58]** sometimes it skips, sometimes it
**[18:59]** retrieves. When it retrieves, should it
**[19:01]** retrieve from procedural memory, which
**[19:03]** is how the agent act, like how do you
**[19:05]** schedule a meeting, how do you write a
**[19:07]** title for Sean's YouTube channel.
**[19:09]** Semantic memory, which are some durable
**[19:11]** facts, right? Who are Sean's friends?
**[19:13]** Who's Sergey? Who's Rach? Who's Vincent,
**[19:15]** right? And episodic memory, when did
**[19:18]** Sean exactly build up those dates for
**[19:20]** the World Cup event, for talking to Sam
**[19:22]** Altman, all these kind of things?
**[19:24]** Then the agent will decide, okay, with
**[19:26]** all these memories and context, what
**[19:28]** kind of tools do I want to ask and run
**[19:30]** this entire loop until we're like, okay,
**[19:32]** we're done. Let's tell the result back
**[19:35]** and at the same time trace all these
**[19:37]** things that happened with an eval system
**[19:40]** to test things out, you know, release
**[19:41]** some new prompt when there requires a
**[19:43]** new update.
**[19:45]** And at the same time, after the reply,
**[19:47]** the agent will consolidate some of the
**[19:49]** facts into semantic memory.
**[19:52]** You see,
**[19:53]** this is an entire walk-through with real
**[19:55]** code and implementation
**[19:58]** on the system design that I have shown
**[20:01]** in the previous three videos. I really
**[20:04]** hope this was very helpful because a lot
**[20:05]** of people ask me how do we actually
**[20:07]** implement this in code? And if you feel
**[20:09]** like this video is a bit too complicated
**[20:11]** for you, I highly, highly recommend that
**[20:13]** you watch some of those previous videos
**[20:15]** on agent harness, memory system, and
**[20:18]** even Hermes agent. That'll be very
**[20:20]** helpful for you to understand this
**[20:21]** video. But for those of you who are
**[20:22]** technical, I hope this was a fun project
**[20:24]** for you to play around with. Please give
**[20:26]** me a star, come to this GitHub repo
**[20:28]** waku-agent under Shinkan Chan, write out
**[20:31]** some pull requests, let's contribute to
**[20:32]** this and make this the best AI personal
**[20:35]** agent on your local machine. And I think
**[20:37]** this will be fun. If you have any
**[20:38]** questions, feel free to ask me and leave
**[20:40]** a comment and subscribe and like the
**[20:42]** video if you liked it. Give me a star on
**[20:45]** GitHub. I'll see you in the next video.
**[20:46]** Thank you very much.