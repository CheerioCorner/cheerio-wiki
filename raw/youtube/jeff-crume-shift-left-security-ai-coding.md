---
title: "Jeff Crume: Shift-Left Security for AI-Assisted Software Development"
type: raw-transcript
created: 2026-09-14
video_id: X0UI0O8YzJM
url: https://www.youtube.com/watch?v=X0UI0O8YzJM
duration: 00:11:16
language: English
auto_generated: False
source_api: youtube_transcript_api
segments: 121
timestamp_format: "[MM:SS]"
---

# Jeff Crume: Shift-Left Security for AI-Assisted Software Development

> Duration: 00:11:16 | Segments: 121 | Language: English | Source: API (manual)

## Transcript

[00:00] AI is changing software development faster than any technology shift we've seen in decades.
[00:05] 40 years ago, when I was banging out code in the early days of my career, the process was incredibly complex, time-consuming, and error-prone.
[00:15] Today, developers can generate code, tests, agents, multi-step AI tasks, and even workflows with just a prompt.
[00:24] Quite simply, AI is a game changer.
[00:27] But there's a challenge.
[00:28] Security practices that were designed for human written code and slower development cycles don't automatically scale to AI assisted development.
[00:36] If security only happens at the end of the process as a retrofit, the quality suffers and the whole process comes to a screeching halt.
[00:46] The solution comes from an unexpected source.
[00:50] It turns out that Beyonce knew what she was talking about when she sang to the left, to the lift.
[00:55] Yeah, security needs to shift.
[00:58] To the left.
[00:59] Security begins at the earliest stages of development and continues throughout the software development cycle if we're doing it correctly.
[01:07] These concepts aren't new, although to be honest, we haven't always practiced them as we should, but in the AI era, shift left means more than just running a few security tools earlier in the process.
[01:20] It means things like this.
[01:22] What?
[01:23] Are we gonna validate?
[01:25] When do we validate it?
[01:27] And how do we build confidence in AI-generated solutions?
[01:32] Let's take a look at five key principles of shift-left security to improve the results of AI-assisted development.
[01:40] AI-Generated code does not equal trusted code.
[01:44] So the first principle is simple.
[01:46] Trust the outcome, not just the generation.
[01:50] One of the most deceptive things about AI-generated code.
[01:53] Is that it often looks correct.
[01:55] It compiles, it runs, it passes all the security tests.
[02:00] So we think this is okay, but it can still create a security risk down below that you don't see.
[02:08] And that's the part that we're not aware of.
[02:11] Just because the technical implementation appears successful doesn't mean the solution is secure.
[02:17] Developers increasingly need to validate things like the permissions, the data leaks.
[02:23] If those are occurring, and how are we handling failures?
[02:27] Does the system fail safe or fail in an open dangerous condition?
[02:31] For example, an AI-generated service might correctly retrieve customer information from the database, but should it really have access to all of the information that's in there?
[02:43] Is sensitive data potentially being exfiltrated from the organization?
[02:48] What happens if authentication fails?
[02:50] And also what happens if malformed data ends up into this database and corrupts it.
[02:57] Well, this is the stuff that's not so easy to see.
[03:01] This is the part that has asked the question, not just does the code work, but is it secure?
[03:08] The question that does the cold provide expected results and behave securely under real world conditions is what we need to be asking.
[03:17] That's the outcome we need validate.
[03:20] The second principle is security starts during development.
[03:24] Traditionally, many organizations treated security reviews almost as an afterthought.
[03:29] So what would happen is, we've got developers finish their coding, then the components are built, the functions are tested, the code gets committed, and then the security review happens.
[03:42] Basically, it's a checkbox at the end of the process.
[03:45] That model never really worked well and struggles even more in an AI assisted development environment.
[03:51] Because history teaches us that the earlier you find a bug, the cheaper it is to fix.
[03:58] If you find it once it's already gone into production, super expensive.
[04:03] Find it early in the process is pretty cheap.
[04:06] And security bugs are actually no different in that regard.
[04:09] And when code generation can happen in seconds, waiting until the end creates bottlenecks and increases risk.
[04:17] So shift left security improves validation into the development process itself.
[04:23] This means automatically running static source analysis, dynamic pin testing, penetration testing, scanning for embedded secrets, policy validation, compliance checks, all of that stuff, and doing this while the code is being created.
[04:41] The goal here is to reduce unpleasant surprises later in the life cycle by shifting left.
[04:47] Here's an important reality of AI-generated development.
[04:50] AI doesn't just create code.
[04:53] It also introduces dependencies.
[04:56] So we've got things like new packages, new libraries, new services, new integrations.
[05:04] Every dependency adds capability, which is good, but every one of those dependencies also adds risk, which isn't so good.
[05:12] Developers often focus on reviewing generated source code while overlooking what that code depends on.
[05:18] It's all happening under the covers through the miracles of AI.
[05:22] So it's kind of out of sight, out of mind.
[05:24] But security incidents originate from the software supply chain rather than just the application logic itself in some cases.
[05:32] That's why generated dependencies should also undergo the same level of scrutiny as the generated code.
[05:39] We need to review things like package reputation, vulnerabilities, licensing, source integrity.
[05:48] Organizational standards.
[05:50] In AI assisted development, dependency validation isn't optional.
[05:55] It's part of secure code generation.
[05:58] This may be the most important shift of all.
[06:01] Many serious security issues aren't caused by poor coding practices.
[06:05] They're caused by incorrect assumptions.
[06:08] Consider questions like who should actually access the data and what actions are allowed?
[06:15] What business rules need to be applied?
[06:19] And what information should remain protected.
[06:22] An AI model may generate code that is technically elegant and follows development best practices, but if the requirements are misunderstood, the solution can still violate security policies.
[06:34] Here's what I mean.
[06:35] A generated workflow may correctly process financial data, yet at the same time, it may also expose that information to users who should never see it in the first place.
[06:48] That's not as much a coding problem as it is an intent problem.
[06:51] So we have to consider the intent with this.
[06:54] And with AI, you have to be especially clear about that intent.
[06:59] In Greek mythology, King Midas wished that everything he touched would turn to gold.
[07:04] He got his wish and nearly starved as a result because it turns out you can't eat gold.
[07:10] As AI generates increasingly sophisticated code at machine speed, developers become more responsible for validating the output.
[07:18] There's still work for you to do here.
[07:20] In other words, we're no longer just reviewing code quality, we're reviewing whether the AI solved the right problem in the right way.
[07:29] The fifth principle is that security must become an ongoing practice, not just some final checkpoint at the end.
[07:36] As I mentioned previously, organizations have historically concentrated security activities near release at the ended.
[07:43] But AI assisted development produces change continuously.
[07:48] Which also means security validation must also happen, wait for it, continuously.
[07:55] That means it's gonna continue even after release.
[07:59] So if you think about the process, we develop, test, deploy, monitor, and improve, and all of that stuff needs security.
[08:10] Security operates in every one of those components in the loop.
[08:15] So how would we realize that?
[08:17] Where we've got to have things like vulnerability detection, using some of the best AI frontier models as well so that we're right on top of all the vulnerabilities.
[08:27] We're going to do dependency monitoring and patching.
[08:30] We're gonna also do policy verification and enforcement and change validation.
[08:37] So the question isn't just did it pass once, but does it continue to pass?
[08:44] Does it continue be secure?
[08:46] The question is whether confidence is maintained over time.
[08:50] For instance, an AI frontier model recently found a zero day vulnerability in an open source operating system that had been hiding in plain sight for 27 years.
[09:02] Lesson from that, security is never done.
[09:05] It has to become a living part of the process that evolves alongside the software.
[09:11] As AI becomes more agentic, it will increasingly perform multi-step work.
[09:16] Across services, repositories, pipelines, and configurations.
[09:21] At that point, reviewing individual files becomes insufficient.
[09:26] Security depends on understanding the entire workflow.
[09:29] The hardest security problems aren't hidden in a single function.
[09:33] They're found in unintended consequences across connected systems.
[09:37] That means agents need guardrails to tell them what they can and can't do, what is appropriate and what's not.
[09:44] They need identities so that they're accountable and we can see who did this and why they need access controls that limit the access capabilities that they have so that they can't change things we didn't intend and they need monitoring and human in the loop oversight without the proper oversight and enforcement agents can run amok and act as risk amplifiers rather than productivity tools.
[10:14] Here's the new reality. We have to secure fast and validate continuously.
[10:19] AI is accelerating software development.
[10:22] That means we can generate more code in a minute than we used to be able to do in an entire day.
[10:27] And that means we're gonna generate a lot more code.
[10:30] And that mean more code means more functionality.
[10:34] It also means more complexity.
[10:37] And complexity is the enemy of security.
[10:40] Speed alone doesn't create value, trust does.
[10:43] The organizations that succeed will be the ones that embed security directly into everyday development workflows.
[10:50] Not as a compliance exercise, not as a release gate, but as a continuous validation practice.
[10:56] Because in the age of AI assisted development, the goal is no longer to simply review the code.
[11:02] The goal is to confidently verify outcomes across an ever expanding and increasingly complex code base and do it faster than we've ever done before.
[11:13] And remember to the left, to the left.
