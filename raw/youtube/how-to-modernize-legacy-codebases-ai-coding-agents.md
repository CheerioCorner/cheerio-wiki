---
title: "How to modernize legacy codebases using AI coding agents (Step by step guide)"
type: raw-transcript
created: 2026-08-25
video_id: RKvckuC6Bsk
url: https://www.youtube.com/watch?v=RKvckuC6Bsk
duration: "0:08:13"
language: English (United States)
auto_generated: False
source_api: youtube_transcript_api
segments: 158
timestamp_format: "[MM:SS]"
---

# How to modernize legacy codebases using AI coding agents (Step by step guide)

> Duration: 0:08:13 | Segments: 158 | Language: English (United States) | Source: API (manual captions)

## Transcript

[00:00] Everyone uses AI to build shiny
new apps from scratch, but can it help you tackle a messy legacy
codebase?
[00:08] Absolutely. It completely changes.
[00:11] Hey, tackle code, and we've got a few
strategies today to make it painless.
[00:24] Welcome back!
[00:25] JK please remind viewers what you do.
[00:28] Good to see you again. Martin.
[00:30] I'm currently
working with two startups on Google Cloud, helping them grow and scale, building another startup with a co-founder
and squeezing in a few projects.
[00:37] It's a busy time. Yeah.
[00:39] Sounds like you have a lot going on.
[00:41] So let's talk
about the nightmares of legacy code.
[00:46] Zero docks,
complex data flows, and zero tests.
[00:51] I've worked with way too many projects.
[00:53] Like that can be overwhelming at first.
[00:55] But here's the good part.
[00:58] AI is surprisingly good at tracing
data across complex systems.
[01:01] If you make a field
required on the front end, the agent can instantly trace that back
through to the APIs down to the database, and it can update all the code
along that path.
[01:11] That's great for adding a new field,
but how do you even begin to getting to understand a massive,
unfamiliar codebase?
[01:19] Well, my favorite approach is to make
a change request driven approach.
[01:23] You just learn the code base organically, block by block,
only when you need to change something.
[01:29] I think of it in three steps
plan, execute, and verify.
[01:33] All right, I love lists of three.
[01:35] So first was the planning step.
[01:37] What happens there.
[01:38] Well you have to prepare the sandbox
first.
[01:40] And my golden rule is document
your local setup and read me.
[01:44] If your agent knows
exactly how to run your local tools like compiler, your linter,
your unit tests, all of it.
[01:51] You unlock a tight feedback loop.
[01:54] The agent can try out
dozens of different code changes, run the tests, catch syntax errors,
and autocorrect itself in real time, completely on its own, without you
having to step in and play the umpire.
[02:06] I like that.
[02:07] This also gives you two side benefits.
[02:09] First, if you use Docker,
it acts as a disposable sandbox where the AI can make a mess
without breaking anything, and you can wipe
it clean with one command.
[02:19] Second, you get solid documentation
out of the box and it stays up to date and makes onboarding
new human teammates much easier.
[02:26] That's a double win.
[02:27] So once the sandbox is ready, what's next?
[02:31] Well, whenever I use tools
like the anti-gravity IDE or CLI, I always switch on planning mode first
instead of letting AI jump right in straight to the coding,
I have it do some recon.
[02:43] I'll tell it something like explore
the code base and show me what files, APIs, or database tables will break.
[02:49] If we push this and it maps the entire blast radius
before you even touch a line of code.
[02:54] That's smart.
[02:55] And once you have that list, what then?
[02:58] That's when I treat the
I like a human coworker.
[03:00] If you and I were working on a code base
together, for example, we wouldn't just hand each other a specification
and start typing.
[03:07] We talk things through
and maybe bounce ideas back and forth.
[03:10] Same thing with an AI agent.
[03:12] I treat it like a coworker,
not an automated typewriter just yet.
[03:15] Not just yet.
[03:16] And what kind of prompts do you use
for that? JK.
[03:19] Well, sticking with the rule of three, there are some prompting rules
that I use across different scenarios.
[03:24] On discussing new features.
Ask for options.
[03:27] Have the AI generate
three different architectural designs, and then ask it to critique its own plans
first.
[03:33] You can even ask it to give you
the one that it prefers.
[03:37] Then let it drill.
[03:38] You force the agent to ask you questions
to smoke out requirements that you might have not have thought about.
[03:44] And when you're squishing bugs, explain
the problem clearly and give a concrete example or two of what should happen
instead of this as AI to write the failing test
to verify that understands the problem.
[03:57] It's really helpful in that scenario.
[03:59] Very good.
[04:00] All right,
so after the plan stage comes, execute.
[04:04] And now it's time to modify
some code in our legacy code base.
[04:07] How do you do that?
[04:08] Well, as you might have guessed,
I like to do three things.
[04:11] The first is to manage the context.
[04:13] The second is to lead with tests.
[04:15] And lastly agent rules.
[04:18] Another list of threes.
[04:19] I love it.
So how do you manage the context?
[04:22] Well I try to keep the conversation
about a single, changed, really focused AI agents can easily lose the forest for the
trees in long multi-hour chat sessions.
[04:32] So for big changes,
I'll break it down into subtasks.
[04:35] I do the first one,
then get a quick summary of what's changed and feed that summary into the next task.
[04:41] I keep it running file like a markdown file or something
like that of the whole feature, and it acts as like a checklist of
what's been done and what's still to do.
[04:50] It keeps both me
and the agent focused on the current task.
[04:53] Got it.
[04:54] And next you said next on
your list was leading with tests.
[04:58] Yeah.
[04:58] Test driven development or behavior
driven development keeps agents honest.
[05:03] I use pro models to draft
the acceptance criteria and the tests.
[05:06] If there's not enough
information in the ticket.
[05:08] And then I'll hand the execution off to a cheaper model
like flash to write the code.
[05:14] Once the test turned green, I move on
and you can easily parallelize this work out to speed things
up even more.
[05:21] Very nice.
[05:22] And last of new list agent rules.
[05:24] Agent rules? That's right. It's
probably the most important one.
[05:27] I think agent rules that you define
how an AI behaves when working with you or the team.
You can teach at your standard workflow.
[05:34] Like write the test first,
or check that the app builds and set the hard boundaries
like never commit without permission.
[05:43] That way it stays aligned with your
working standards on every piece of work.
[05:47] This is especially important
when working in a team on legacy code.
[05:51] Great.
[05:52] AI agents hallucinate though, so I guess
that's why you added a verify step.
[05:58] How do you keep the agent
from shipping bugs to production?
[06:01] Yeah. That's right.
[06:02] I use a three layer filter or rule threes.
[06:05] First human reviewer
clean code is for future humans and AI's second, a ci, CD,
ai review.
[06:13] Another model reviews the PR and
the original agent defends its choices.
[06:20] Third, smoke tests
automate automate deployments to staging and then click through it
to make sure it's behaving as you expect.
[06:28] Very good.
[06:29] That was a lot of good info.
[06:31] Now what are the takeaways?
[06:33] JC well, I've got another list of three
for you Martin.
[06:36] Here are my top three rules
for tackling legacy code with AI.
[06:40] First, document your tools in a Readme
and use planning mode.
[06:43] Set your tools in Readme so that the agent can self-correct
and treat it like a coworker.
[06:49] Second right, tests
first and set agent rules.
[06:54] Break big changes into tiny subtasks
and write BDD or TDD tests before implementation so that the agent
doesn't write its own exam and use project rules as automatic
guardrails to run your winters.
[07:08] And third, build a three layer filter.
[07:12] Never let AI ship directly to production.
[07:15] You can use manual diff reviews,
secondary CI, CD, AI checks, and real world
smoke tests in staging.
[07:22] Those three rules turn a scary legacy
project into a predictable process.
[07:28] Exactly.
[07:29] Writing code is cheap now, but building
great software is still expensive and requires
real design.
[07:35] That loop of planning, testing,
and talking with your agent.
[07:40] That's your new superpower.
[07:42] Thanks for sharing this with us.
[07:44] JK so I actually have to add a feature
to some legacy code next week, and I'll be putting this new superpower
to the test.
[07:53] Best of luck with that man.
Let me know how it goes.
[07:55] Thanks for having me.
[07:56] Will do.
[07:57] And thank you everyone for watching.
[07:59] If you have any questions for JK or me,
please let us know in the comments.
[08:04] Also, do let me know
what you thought of today's episode.
[08:08] I read every single comment.
[08:11] Until next time.
