---
title: "mattpocock/skills: A complete AI Coding workflow, end-to-end"
type: youtube-transcript
source_url: https://www.youtube.com/watch?v=M6mYodf0dJM
video_id: M6mYodf0dJM
language: en (auto-generated)
segments: 505
duration: 17:16
created: 2025-01-01
tags: [youtube, transcript, matt-pocock, ai-coding, skills-repo]
---

# Raw Transcript — mattpocock/skills

> Video: [YouTube](https://www.youtube.com/watch?v=M6mYodf0dJM)
> Duration: 17:16 | Segments: 505

---

**[00:00]** Hello friends, it occurs to me that I've
**[00:01]** never actually put together a proper
**[00:04]** tutorial for my skills repo. At the time
**[00:06]** of recording this repo is up to 162,000
**[00:09]** stars. We have 7.5 million downloads and
**[00:12]** I've never made a tutorial for it. I get
**[00:14]** questions all the time like what is the
**[00:15]** sequence I should use these skills in,
**[00:17]** how should I install them, how should I
**[00:18]** set them up? So this video is going to
**[00:20]** be a walk through of the main flow you
**[00:22]** use when you're using these skills.
**[00:25]** We're not going to look at the advanced
**[00:26]** stuff, we're not going to look at the
**[00:27]** new stuff really, we're just going to
**[00:29]** focus on the main flow. The stuff you
**[00:31]** need to get started. To walk you through
**[00:33]** this, I'm going to be using one of my
**[00:34]** work repos which is the AI Hero CLI.
**[00:37]** This is the command line interface that
**[00:38]** drives a lot of my exercises that I use
**[00:41]** on my courses. I've never actually set
**[00:43]** up my skills to work with this repo so
**[00:44]** now is a great chance. If you want to
**[00:46]** set up my skills on a brand new project,
**[00:48]** you just do this except you do it in an
**[00:49]** empty directory. So it works the same
**[00:51]** whether you're using a brownfield code
**[00:52]** base or a greenfield code base. I'm
**[00:54]** going to open up the command line
**[00:55]** interface here and I'm going to type NPX
**[00:58]** skills at latest add mattpocock/skills.
**[01:03]** This assumes a couple of things. It
**[01:05]** assumes that you've got Node.js
**[01:06]** installed. This NPX comes from Node.js
**[01:10]** and it runs the skills.sh command line
**[01:13]** installer from Vercel. What this
**[01:15]** basically does is it installs a GitHub
**[01:17]** repo of skills called mattpocockskills
**[01:20]** and it can walk through a few setup
**[01:22]** questions here. It first says it needs
**[01:24]** to install the following packages. Yes,
**[01:25]** that seems fine to me and then it did a
**[01:28]** couple of things and we now have a long
**[01:30]** long list of all the skills that we
**[01:31]** could install. It found 38 skills here
**[01:34]** which is a lot and you can see if I
**[01:36]** scroll up and down that they're in two
**[01:37]** groups. We've got the mattpocockskills
**[01:39]** and then we've got other skills. So the
**[01:42]** mattpocockskills are the ones that I
**[01:43]** have blessed as the skills that I think
**[01:46]** are good enough to be public facing. The
**[01:48]** other ones are ones that I'm
**[01:49]** experimenting with right now and may
**[01:51]** delete in future. What I recommend you
**[01:53]** do is you go to the top here and you can
**[01:55]** kind of go up and down. It's kind of
**[01:58]** broken, I have to say. And if you press
**[02:00]** space here, and you should see that if
**[02:02]** you scroll up, okay, they're now all
**[02:04]** selected. You press space, and then you
**[02:06]** press return, and now uh you've selected
**[02:09]** all of the official skills. I'm not
**[02:11]** terribly happy with Versel's CLI here,
**[02:13]** so I may change it in future, or maybe
**[02:15]** even just ship my own. But uh for now,
**[02:18]** that's as good as it gets. One thing
**[02:19]** that is good is that it will set up your
**[02:21]** skills to work with any agent. So, I use
**[02:24]** Claude Code, but you can go down here
**[02:26]** and sort of just select the ones that
**[02:27]** you want. I'm using space to select. I
**[02:30]** think by default, if I zoom out a touch,
**[02:33]** then it supports all of these universal
**[02:36]** ones up here. So, cursor, Codex, Claude,
**[02:38]** etc. But anything that uses like Claude
**[02:41]** skills, such as Claude Code, then you
**[02:42]** need to set up yourself. So, I'm going
**[02:44]** to press return here, and it should now
**[02:47]** be configured to set up my skills for
**[02:48]** Claude Code. The installation scope
**[02:50]** defines where your skills are installed,
**[02:52]** whether they're installed just in the
**[02:53]** current directory, or whether they're
**[02:55]** global. This will depend on what your
**[02:56]** team's conventions are. If you're
**[02:58]** working in a team, I would say that
**[02:59]** project skills are the right way to go.
**[03:01]** That way, everyone is using the same
**[03:03]** skill set on every project, and it means
**[03:05]** that you can contribute to the skills
**[03:07]** together and make those decisions
**[03:08]** together. But global is fine if you're
**[03:11]** just a solo developer working on your
**[03:12]** own stuff, like I am. So, I'm going to
**[03:14]** press return here, install it in my home
**[03:16]** directory, and I'm going to choose
**[03:17]** symlink as the recommended way. The
**[03:20]** choice here is whether you copy it to
**[03:22]** the agents folder as well as the dot uh
**[03:25]** Claude folder. And
**[03:27]** it's kind of not a nice way to do it.
**[03:28]** Symlink is just the nice, easy way to do
**[03:30]** it. So, I wouldn't even make a decision
**[03:31]** here, just choose symlink. So, it now
**[03:33]** gives you a summary of all the things
**[03:34]** that you're installing here. There seems
**[03:36]** to be an alert on Socket about to spec.
**[03:38]** I'll take a look at that later. But yes,
**[03:40]** we can proceed with installation, and
**[03:42]** it's now installed all of the skills.
**[03:44]** This means then that I can run Claude
**[03:46]** inside here, or whatever your agent is,
**[03:48]** and I'm going to create a new So, I'm
**[03:51]** just going to say hello to get out of
**[03:52]** this agent's view here. Now, depending
**[03:54]** on the harness you're using, this will
**[03:55]** show up in different ways, but on Cloud
**[03:57]** Code, I can press forward slash and I
**[03:59]** now see that I have a few skills
**[04:01]** available to me. I have grill me,
**[04:03]** grilling, way finder, grill with docs,
**[04:05]** etc. Loads of stuff. Now, the difference
**[04:07]** between my skills and lots of other
**[04:09]** skills repos that are out there is my
**[04:11]** skills are mostly user invoked. That
**[04:14]** means that if I run context here, not
**[04:17]** many of my skills actually leech their
**[04:19]** way into the description. And the
**[04:22]** descriptions I have are quite short and
**[04:24]** precise. So, that means that even though
**[04:26]** we've downloaded all of my skills, the
**[04:28]** skills only take up 660 tokens here. So,
**[04:32]** it's very, very light in terms of
**[04:33]** context load. So, okay, we got the
**[04:35]** skills. Now, what we have to do is we
**[04:37]** have to run set up Mac Pocock skills.
**[04:40]** And this will do a few things. My skills
**[04:42]** rely on some uh configuration inside the
**[04:46]** repository, and this does a few things
**[04:49]** for you. The first thing is it means you
**[04:51]** need to use an issue tracker. We're
**[04:53]** going to be saving um specs, we're going
**[04:56]** to be saving tickets, and we need to
**[04:57]** save them somewhere. You've got kind of
**[05:00]** an infinity of choices here. You can use
**[05:01]** GitHub, you can use local markdown, or
**[05:04]** you can literally use anything. The way
**[05:06]** that the skill works is that it looks at
**[05:09]** your local configuration. And so, you
**[05:11]** can set it up for Jira, you can set it
**[05:13]** up for Linear. And the way you do that
**[05:14]** is you just tell the agent what you want
**[05:16]** to set it up for, and it will go and set
**[05:18]** it up for it. I just want to emphasize
**[05:19]** that. People are asking me all the time,
**[05:20]** "How do I make my skills work with Jira,
**[05:22]** work with Beads, work with Linear?" It
**[05:25]** already does. All you need to do is just
**[05:27]** run set up Mac Pocock and just say, "Set
**[05:29]** it up with Jira." Except, I don't want
**[05:31]** to set it up with Jira. I'm just going
**[05:33]** to set it up with local markdown,
**[05:35]** please. So, that's fine by me. The next
**[05:37]** question to answer here is about triage
**[05:39]** labels. So, there are a set of labels
**[05:41]** that the skill relies on to communicate
**[05:42]** information about the tickets that it
**[05:44]** produces. It's not really that important
**[05:46]** here, so I'm just going to accept the
**[05:48]** defaults. You can look at the docs on
**[05:50]** the triage skill if you want to learn
**[05:51]** more. So, defaults is fine. The next one
**[05:54]** is about the domain documentation. So,
**[05:57]** my skills like to have a little bit of
**[05:59]** docs, a context .md file, and an ADR
**[06:03]** inside the repo. And it's basically
**[06:05]** asking if it's going to be a single
**[06:07]** context or a multi-context. I think
**[06:09]** single context is the way to go here.
**[06:12]** Multi-context is if you have a big mono
**[06:14]** repo and you need lots of different
**[06:17]** bounded contexts within it. But for 99%
**[06:21]** of people, a single context is going to
**[06:22]** be fine. All right, so it's gone ahead
**[06:24]** and written a few things here. The first
**[06:26]** thing it's written is inside claw.md,
**[06:28]** it's added a few little links here. So,
**[06:31]** this is the new stuff. It's just linking
**[06:33]** to the issue tracker docs, linking to
**[06:35]** the triage labels, and linking to the
**[06:37]** domain docs. And each of these are at
**[06:40]** docs, agents, domain issue tracker. So,
**[06:43]** we can see here that it's going to save
**[06:45]** all of the issues and specs inside a
**[06:49]** scratch file. So, with that, our setup
**[06:50]** for this repo is complete. And so, you
**[06:52]** might be thinking, how do I get started?
**[06:54]** Well, before we get started, I'm going
**[06:56]** to show you one more really cool thing.
**[06:58]** You can, if you're following along, just
**[07:00]** stop the video now and use one skill.
**[07:02]** You can use the ask Matt skill. This ask
**[07:05]** Matt skill is essentially me as a skill.
**[07:08]** It knows everything that is needed about
**[07:10]** the skills repo and what you should do
**[07:12]** first. So, we can say, ask Matt, how do
**[07:15]** I get started? I want to make some code
**[07:16]** changes here. What is the main flow I
**[07:18]** should use? I can now submit that and
**[07:20]** see what it says. By the way, I'm using
**[07:22]** Whisper flow as my transcription. So,
**[07:23]** here we go. It's saying the main flow,
**[07:25]** idea to ship. Since you have a code
**[07:27]** base, start at the top of the main flow
**[07:29]** and walk down in it in one unbroken
**[07:32]** context window. So, it's very um
**[07:34]** kind of really telling you how to use
**[07:38]** your sessions as well. I I believe that
**[07:40]** being conscious about the context window
**[07:43]** that you're using, the tokens that
**[07:44]** you're using is essential to using AI
**[07:46]** well. It says you should start with
**[07:48]** grill with docs. It interviews you to
**[07:50]** sharpen the idea and because you're in a
**[07:52]** repo it's stateful. It records what it
**[07:54]** learns in context.md and ADRs. This is
**[07:57]** where you turn I want to change X into a
**[07:59]** crisp defensible plan. Defensible is
**[08:01]** such an LLM phrase, honestly. Can you
**[08:03]** settle every open question just by
**[08:04]** talking? If a question needs a runnable
**[08:06]** answer, then you can use prototype,
**[08:08]** which I've done a video about on it.
**[08:10]** Bridged in and out of by handoff, if not
**[08:12]** skip this. Once you've done the
**[08:14]** interview and grill with docs, you can
**[08:15]** either go straight to the implement
**[08:17]** skill, or if it needs multiple sessions
**[08:20]** to go through, then you can go to to
**[08:22]** spec and to tickets. Let me make this a
**[08:24]** little bit clearer for you. The default
**[08:26]** flow looks like this. You start with
**[08:27]** grill with docs, and this interviews you
**[08:30]** based on the idea that you want to
**[08:31]** produce. For instance, if I clear out of
**[08:33]** ask math, of course I could go and ask
**[08:35]** follow-ups here, use it as a tutorial
**[08:37]** itself, but let me just show you. I'm
**[08:39]** going to say grill with docs, and I'm
**[08:40]** going to kick it off with an idea. I'm
**[08:42]** going to say, I would like to remove
**[08:44]** most of the internal tooling on this CLI
**[08:47]** to make it just only public-facing.
**[08:49]** There's a lot of craft here. I want to
**[08:51]** just take this repo down a notch. It
**[08:53]** really can be as vague as this. You
**[08:55]** don't need to do too much here. Grill
**[08:56]** with docs is going to do the heavy
**[08:58]** lifting by asking you a bunch of
**[08:59]** follow-up questions. It's going and
**[09:01]** exploring a bunch of code here. And by
**[09:03]** the way, I'm [clears throat] using
**[09:03]** Claude Code, I'm using Opus 4.8 on
**[09:06]** medium effort, but you really don't have
**[09:09]** to use the same setup as me. These
**[09:10]** skills are being used by a bunch of
**[09:12]** different people, bunch of different
**[09:13]** harnesses, different models, different
**[09:14]** effort levels. And we can see it's
**[09:16]** already asked the first question here.
**[09:18]** So, it's gotten a clear map of the
**[09:19]** entire repo. It's looking at the
**[09:21]** internal namespace with 11 subcommands.
**[09:23]** So, this is what a grilling session
**[09:25]** looks like. You go through all of the
**[09:27]** questions until you feel or you and the
**[09:30]** agent feel that you've reached a shared
**[09:32]** understanding. I'm going to do that now,
**[09:33]** and then I'll check in with you once I'm
**[09:35]** done.
**[09:36]** Okay, it didn't end up taking too long.
**[09:38]** We ended up with what, six questions?
**[09:41]** That's not very much for grilling
**[09:42]** session. Usually mine end up being about
**[09:44]** sort of 20 questions depending on the
**[09:46]** size of it, but we've ended up with a
**[09:48]** decent plan. We're going to delete uh 10
**[09:50]** command files, delete three tests,
**[09:52]** rewire shared modules. And all I did
**[09:55]** here was I just answered questions until
**[09:57]** it said, "Okay, we've walked the whole
**[09:59]** tree. We've reached a shared
**[10:01]** understanding. Let me lay out the plan."
**[10:03]** Notice here, I wasn't using plan mode
**[10:05]** for this. I was actually in auto mode in
**[10:06]** claw code, which is kind of like the
**[10:08]** default mode. And you now have a fork in
**[10:11]** the road. If you think that this work is
**[10:13]** going to be big enough that it will need
**[10:15]** multiple agent sessions, then you can
**[10:17]** skip numbers two and three here and go
**[10:20]** straight into implement. The way you
**[10:22]** would do that is you would just say, uh
**[10:24]** forward slash implement this. And in
**[10:26]** this case, I do think that is what we
**[10:28]** should do. I've still got about, uh I
**[10:31]** think of my context window is kind of
**[10:33]** like ending or getting significantly
**[10:35]** dumber around 140k
**[10:38]** mark. I think of that as kind of like
**[10:40]** the smart zone of the LLM. If you go
**[10:42]** above 140k, you end up sort of with, you
**[10:44]** know, attention degradation. It ends up
**[10:46]** getting stupider, does weird
**[10:48]** hallucinations. So, I think of having
**[10:50]** like, okay, we've got 100k of budget
**[10:52]** here to remove 10 commands. That seems
**[10:54]** super easy. Definitely something, you
**[10:57]** know, we can definitely do that. So,
**[10:58]** this is what I would usually do. I would
**[10:59]** just say implement and then I would
**[11:01]** leave it, let it run, and it would
**[11:03]** finish the work. However, in the
**[11:04]** interest of showing you everything, I'm
**[11:05]** going to pretend that this work is going
**[11:07]** to take more than one session, that I've
**[11:09]** maybe run out of context window in the
**[11:11]** current or run out of smart zone in the
**[11:13]** current window, and I'm going to need to
**[11:15]** spread this out over multiple context
**[11:16]** windows. So, I'm going to call to spec
**[11:19]** here instead. So, instead of writing
**[11:21]** implement this, I'm going to say to spec
**[11:23]** here. And that's it. What this is going
**[11:25]** to do is it's going to take all of the
**[11:27]** discussion that I've had, all of this
**[11:29]** 46.1k tokens, and it's going to compress
**[11:32]** it into a document that we can use
**[11:34]** later. This is where our issue tracker
**[11:36]** comes in. So, this issue tracker we're
**[11:38]** just going to use local markdown files
**[11:40]** and so it's just going to spit out the
**[11:41]** spec into a local directory. This spec
**[11:43]** is going to be the destination that
**[11:45]** we're heading to over this multi-ticket
**[11:48]** sprint. In other words, this is what
**[11:50]** we're going to end up with. This is the
**[11:51]** description of everything of how it's
**[11:53]** going to look at the end. And then the
**[11:54]** tickets is the description of how we're
**[11:56]** going to get there. Okay, we can see
**[11:58]** it's been written and published to the
**[12:00]** issue tracker. If I open this up, we can
**[12:02]** see it is in here. So, it's very nice
**[12:05]** and detailed. It's got a problem
**[12:06]** statement, a solution, a bunch of user
**[12:08]** stories, implementation decisions,
**[12:10]** testing decisions, a lot of stuff here.
**[12:12]** And this is going to be really useful
**[12:14]** cuz we'll be able to compare this at the
**[12:16]** end to make sure that our implementation
**[12:19]** matched the spec. So, now that we've got
**[12:20]** the spec, I'm going to go into the same
**[12:22]** session, not going to change sessions
**[12:23]** here, and now I'm going to say to
**[12:25]** tickets. And this is where it will
**[12:28]** basically try to turn the spec into an
**[12:31]** implementation plan. Each one of these
**[12:32]** tickets is supposed to just be the size
**[12:35]** of a single context window or a single
**[12:37]** smart zone. And if we look here, we can
**[12:39]** see that it's kind of given us three
**[12:42]** tickets here. So, three slices. I think
**[12:45]** that these three slices are a little bit
**[12:47]** much. I actually think it can be done in
**[12:49]** one slice. Do it in one slice instead.
**[12:53]** And so it's now put this in a file. So,
**[12:55]** it's put the ticket in tickets.md. Now,
**[12:58]** this is quite a bad example cuz it's
**[13:00]** kind of copying the stuff that's in our
**[13:02]** product requirements documents. So, let
**[13:03]** me show you an actual real example. Here
**[13:05]** is a spec that I implemented a couple of
**[13:07]** days ago to remove a bunch of stuff from
**[13:10]** a repo. So, I'm on a real removal spree
**[13:13]** recently. And you can see that this is
**[13:15]** the spec and underneath it has 11 sub
**[13:18]** issues. So, 11 tickets underneath it.
**[13:21]** And each of these tickets, so this is a
**[13:22]** very detailed spec, each of these
**[13:25]** tickets is a single context window
**[13:27]** session. So, if we click into here, we
**[13:29]** can see it's just pretty short. Most of
**[13:32]** the acceptance criteria is already in
**[13:35]** the main spec, and so this one is just
**[13:37]** literally what do you build in this
**[13:39]** session? So, that's number session one,
**[13:41]** then session two, then session three.
**[13:44]** You can see that how this breaks down a
**[13:46]** huge chunk of work into manageable
**[13:49]** pieces that the agent can then go and
**[13:51]** do. However, here we are left with a
**[13:53]** single manageable piece. So, what I'm
**[13:55]** going to do is I'm going to clear the
**[13:57]** context here. Now, we have everything we
**[14:00]** need so that we can just run a bunch of
**[14:02]** agents to tackle this problem. So, I can
**[14:04]** clear the context, and then I'm just
**[14:06]** going to say at tickets here, and I'm
**[14:09]** before that I'll say
**[14:11]** implement this. So, now because we have
**[14:14]** the spec that decides where we're going
**[14:16]** and the tickets that decide how we get
**[14:18]** there, the agent has everything it needs
**[14:20]** and we're ready to implement. Now, when
**[14:22]** you're doing this by hand, the idea is
**[14:23]** that you then implement each ticket one
**[14:26]** by one. So, you don't say do every
**[14:28]** single ticket, you say okay, we go and
**[14:31]** implement, then we see if we've hit the
**[14:32]** smart zone. If we haven't, maybe we can
**[14:34]** squeeze in one more ticket here, but
**[14:37]** usually I would say you clear in between
**[14:39]** every single ticket. Then, once you've
**[14:41]** done all of the implementation, you've
**[14:43]** got your full thing all implemented,
**[14:46]** then you can go and code review and do
**[14:48]** the final check against the spec. We can
**[14:50]** see here it really was a very small
**[14:52]** piece of work actually. It's only 42
**[14:55]** .7k, and it's now about to, as part of
**[14:58]** the implement, run the code review. As
**[15:00]** part of the implement script, it goes
**[15:01]** and runs all the type check, runs the
**[15:03]** build, run is doing even more
**[15:06]** verification, checking AI Hero internal
**[15:08]** help, shows only edit commits, and it's
**[15:10]** gone and loaded the code review skill.
**[15:12]** This review is based on two axes. First,
**[15:15]** it compares the work done against the
**[15:17]** original spec. This is really useful
**[15:20]** when you've done a huge chunk of work
**[15:22]** and the agent might have forgotten
**[15:24]** things in tickets or the tickets might
**[15:25]** have been unspecified. Doing a final
**[15:27]** pass means you actually nail everything.
**[15:30]** And then it also checks against the
**[15:32]** standards documentation that you've got
**[15:33]** in your own repo. In this repo we don't
**[15:35]** really have any coding standards
**[15:37]** documented anywhere, but if it doesn't
**[15:39]** detect any, then it uses some classic
**[15:40]** ones kind of from Martin Fowler. So it
**[15:43]** looks at code smells, tries to figure
**[15:45]** out if there's any bad stuff. Doing
**[15:46]** these in sub agents is really important
**[15:48]** because if you do it in the main agent,
**[15:51]** it means that the main agent already has
**[15:53]** written the code and agents are often
**[15:55]** really bad at editing code or improving
**[15:58]** code they've just written. Because
**[16:00]** they've wrote it, so they just think,
**[16:02]** "Okay, that's fantastic. That's fine."
**[16:04]** Whereas if you spawn some agents, then
**[16:06]** they're going to have a clear context
**[16:07]** window and they're going to do a much
**[16:08]** better job reviewing the code. Okay, we
**[16:10]** can see that both came back. So
**[16:14]** cross-checked every acceptance criteria
**[16:16]** against the spec, checked everything
**[16:18]** against the standards, and cool. We're
**[16:20]** good to go. And it's now committed
**[16:21]** against the current branch. Beautiful.
**[16:23]** So that is our flow complete. We aligned
**[16:25]** before we got started. We created some
**[16:28]** spec and tickets in order to make sure
**[16:30]** it worked over multiple sessions. We
**[16:32]** then implemented it and the implement
**[16:33]** skill itself used the code review. This
**[16:36]** is the main flow that all of my work
**[16:38]** runs through. And the stuff that isn't
**[16:39]** in the main flow is stuff that I'm
**[16:41]** experimenting with, stuff that I'm
**[16:42]** improving, always trying to get this
**[16:44]** loop faster, better, easier to run. And
**[16:48]** for that, if you're interested in that,
**[16:50]** then you should check out my newsletter
**[16:51]** for these skills. This YouTube channel
**[16:52]** is a great place to be for subscribing
**[16:54]** to understanding more about the skills,
**[16:56]** but really the good stuff is on the
**[16:58]** newsletter. If you want on the day
**[16:59]** updates when I ship new skills, when I
**[17:01]** add updates to
**[17:03]** kind of that you need to keep updated
**[17:05]** with, then this is the place to be. But
**[17:06]** thank you so much for watching.
**[17:07]** Hopefully this tutorial gives you an
**[17:09]** idea on how to get set up with the
**[17:11]** skills and what the main flow is
**[17:12]** supposed to be. Thanks for watching,
**[17:14]** happy skilling, and I will see you very
**[17:16]** soon.