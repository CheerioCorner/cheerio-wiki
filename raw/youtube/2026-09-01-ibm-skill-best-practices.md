---
title: "IBM: 5 Best Practices for Building Agent Skills"
type: raw-transcript
created: 2026-09-01
video_id: qYNs80FKIVc
url: https://www.youtube.com/watch?v=qYNs80FKIVc
duration: 00:13:09
language: en
auto_generated: false
source_api: youtube_transcript_api
segments: 175
timestamp_format: "[MM:SS]"
---

# IBM: 5 Best Practices for Building Agent Skills

> Duration: 00:13:09 | Segments: 175 | Language: en | Source: API (manual)

## Transcript

[00:00] Agent skills are about the simplest way there is to make an AI agent better at a specific job.
[00:07] Because they're so simple, they're also really easy to get wrong.
[00:10] Yeah, so we are going to cover the five agent skills best practices for building these skills.
[00:17] And a quick reminder of what a skill even is.
[00:21] It's procedural knowledge handed to an AI agent.
[00:25] The model already knows plenty of facts, but what it doesn't know is your particular way of doing a particular job.
[00:32] So a skill teaches it that.
[00:34] And the format for this, I mean, it's almost comically simple.
[00:39] It is basically just a skill dot MD file.
[00:44] So MD markdown in a folder.
[00:47] But Martin, here's the part that worries me.
[00:50] Agent skills are handing a probabilistic model of folder of text and trusting it to run a fragile multi-step job.
[00:58] Yeah, and a skill can also contain and run code.
[01:04] So in sorting one off the internet means running a random person software on your machine.
[01:09] And while there is an open agent skills standard defined at agent skills dot I O.
[01:18] These are some of the things we need to consider when creating AI agent skills.
[01:23] So Martin, how about we get started with the best practice?
[01:26] Number one.
[01:26] Let's do it!
[01:27] Best practice number one.
[01:29] The description is the trigger, by which I mean it's the agent description that decides whether a skill ever actually runs.
[01:37] And by description, you mean?
[01:40] Every skill.md file like this guy here opens with a bit of YAML so in here we define a name and we also define a description.
[01:54] And there on the short side, according to agentskills.io, a name can be a maximum of 64 characters, and a description, which describes what the skill actually does, is limited to 1,024 characters.
[02:11] So I might have, let's say 100 of these skills installed.
[02:15] Now the agent can't read all of them at once without filling up its context window.
[02:20] So at startup, it just loads the name and the description of each skill.
[02:26] And here's where the best practice comes in, right?
[02:28] Yeah, yeah.
[02:28] So the name and the description need to contain enough information by themselves so that the agent knows when to use it.
[02:36] So a compliance skill of generates reports might be just a little bit too vague.
[02:45] Right, right.
[02:45] The fix is to say what the skill does and when the agent should make use of it.
[02:51] Give me a good version of the description.
[02:54] Ah, this sounds like a bit of a test, but fortunately here is one I made earlier.
[03:00] So generates the monthly compliance report from internal data used when someone asks for the compliance report or the monthly filing, something like that, it says what the skill does.
[03:10] And it also says when the skill should do it.
[03:14] I've also heard the description should lean a bit on the pushy side.
[03:19] Pushy?
[03:21] Models tend to under trigger.
[03:23] They might skip a skill they should have used.
[03:25] So it's safer to oversell the description a touch rather than undersell it.
[03:29] Oh, like LinkedIn posts, got it.
[03:32] So that's getting a skill to trigger.
[03:35] What goes inside it once it does?
[03:38] Well, that part's all yours, brief.
[03:40] All right.
[03:41] Yeah, and this is where a lot of skills go sideways, because the temptation is to just have the LLM write the skill for you.
[03:48] Yeah, the first skills I created were all like this.
[03:50] I'd say like, hey, AI agent, write me a skill that does X and it generated stuff, which honestly, I only glanced at that then call the skill, build a complete success.
[04:02] And what you get out of that is very generic mush.
[04:05] Handle errors appropriately, validate inputs, stuff the model already knew.
[04:11] Which brings us to the best practice number two, build from real expertise.
[04:17] Meaning what exactly?
[04:19] The whole point of a skill is your specific way of doing a specific job.
[04:23] So the content has to come from somewhere the model can't get to on its own.
[04:28] And there are two ways to do that.
[04:30] One, you walk through the task by hand once and write down what actually worked, including the corrections you made along the way.
[04:37] Or two, you synthesize it from artifacts you already have, so things like old reports, run books, review comments, PR feedback.
[04:46] Yeah, and fellow Brit ,Simon Willison, has a line about this.
[04:49] He says, keep the domain expertise and let the agent do the routine part.
[04:55] That's right.
[04:56] And Mr. Willison is absolutely right about this.
[04:58] You bring the expertise.
[05:00] The model brings the typing.
[05:02] And what does this mean for the skill.md body?
[05:06] I feel like I've drawn this box a few times already.
[05:08] Yes, you have.
[05:10] It means the highest value section you can put in the skill body is gotchas.
[05:19] Environment-specific facts that defy reasonable assumptions.
[05:23] Every time you correct the agent by hand, that correction is a gotcha.
[05:27] Write it down.
[05:28] Otherwise, you'll be making the same correction next week.
[05:31] And the week after.
[05:32] Mm-hmm, you're not wrong.
[05:34] Now, even when you do all this right, it doesn't always work for the first time around.
[05:39] I built a skill for a monthly compliance report from an actual real report I had done, which is real expertise, exactly what we're talking about.
[05:48] But on the first run, the row totals didn't add up to the common totals.
[05:52] So the math was right.
[05:54] Yeah, the math was wrong.
[05:56] On a compliance report.
[05:57] Yeah, but that's the good kind of wrong because it's the kind you can catch.
[06:01] We'll come back to this one.
[06:02] Point is, a good skilled body gets thorough.
[06:06] And thorough, that means it can get...
[06:09] really, really long.
[06:11] Yeah, and long, as it turns out, gets expensive.
[06:15] Which brings us to best practice number three, which is spend context wisely.
[06:21] At startup, the agent only sees the name and the description, but when it selects a skill, that's when it actually reads the rest.
[06:29] So now we take a look at the body of the skill.md file and bring that into context.
[06:36] And that context is shared with everything else already in the context window.
[06:40] Yeah, every line here in the skill body is now competing for the model's attention, which means the goal is to write left.
[06:49] Which feels a little bit in opposition to our last best practice, because the more detailed and thorough a skill body is, the better.
[06:56] You'd think so, but the model is already smart.
[07:00] It knows stuff.
[07:00] It knows what a PDF is.
[07:02] It knows, what a database migration does.
[07:05] So only write down what the agent wouldn't know on its own.
[07:08] Stuff that's not already part of the AI model's training data.
[07:12] And we can put this into numbers.
[07:14] It's recommended to keep this body here of skill.md to under about 500 lines of text, or we could say that's roughly about five thousand tokens and just keep it at that.
[07:29] And when it's bigger than that?
[07:30] Well, when it's bigger than that, just split it out.
[07:33] The skill folder can actually hold a sub-folder called references, and this is a directory, and the agent will only open the files in the references when it actually needs them.
[07:46] It's part of a pattern called progressive disclosure, which is to say disclose additional information like this only when it is needed.
[07:54] So staying lean is one thing, but some steps you really don't want the agent guessing out at all.
[08:00] So guessing at what?
[08:02] Every time the model runs your skill, it reads the instructions and improvises through them.
[08:08] For loose steps, that's fine.
[08:09] A lot of paths get you to the right answer.
[08:12] But for a step that has to be exactly right every single time, you don't want the model regenerating the logic on the fly.
[08:19] Hmm, this sounds to me like a...
[08:21] Best practice number four?
[08:23] It is.
[08:24] Make use of deterministic scripts.
[08:27] Match how prescriptive you are to how fragile the step is.
[08:32] So, loose step, write instructions.
[08:34] Fragile step, right code.
[08:36] Okay, and code, that code is where?
[08:38] The skills folder that holds a scripts directory.
[08:43] So scripts directory, same idea as references.
[08:50] You drop a script in there, say script one.
[08:56] And the skill body just tells the agent, run this.
[09:01] The script doesn't get loaded into context, so you'd save tokens as well.
[09:05] And it's more reliable than having the model improvise from scratch every time.
[09:09] Now you do have to be explicit about intent though right, so the model doesn't just kind of read the script as reference material.
[09:16] Yeah, exactly.
[09:18] Say, run this script or read this as reference.
[09:24] Don't leave that to a guess.
[09:26] Yeah, and that works in Claude Code, but it isn't just an Anthropic thing.
[09:29] Opening a codex works roughly the same way as well.
[09:32] So back to my compliance report, the one where the row totaled and reconciled.
[09:37] Yeah, the math one.
[09:38] Yeah, that one.
[09:39] The masked up is now a deterministic math script.
[09:45] The model doesn't add the numbers anymore.
[09:47] It calls a script that adds the numbers.
[09:50] And that whole class of bug just goes away.
[09:54] Yeah, because the script doesn't guess.
[09:56] This script, math script here.
[09:58] It's running code.
[09:59] Right.
[09:59] And the answer isn't write more test.
[10:02] You can test your way to trust.
[10:05] A test only catches what you already thought to check.
[10:08] The answer is, for the parts that have to be right, take the guess out of the loop.
[10:13] Yeah, right.
[10:13] So this is moving away from probabilistic, which is what an agent does by itself into being more of a deterministic model instead.
[10:24] And I've been doing this a lot in the skills that I build.
[10:26] I don't want to make the agent make guesses if something can be hard coded as logic deterministic, because if the agent is having to make probabilistic decisions, those decisions will not always be consistent across multiple runs.
[10:41] So it was skill you built yourself and that you've read and that your hardened the fragile steps on, that's a skill you can trust.
[10:50] But not all skills you'll run, you build yourself, right?
[10:54] Right? What about the skills you'll run that came from a stranger?
[10:59] Which brings us to best practice number five, vet a skill before you run it.
[11:04] So I said a skill can run code.
[11:07] The stranger software on your machine thing.
[11:10] So a skill folder can contain, as we said, executable scripts that we can run, scripts that can access stuff, access stuff like, for example, the local file system on your computer, or in fact, it could access any API keys that you happen to have lying around.
[11:33] Which is exactly what makes skills so powerful.
[11:36] But I saw an audit from earlier this year that scanned almost 4,000 public skills, and over 35% of them had a security flaw of some kind, and 13% had something critical going on like a prompt injection or straight up malware.
[12:00] Which means we have to treat an agent skill like, well, like any other dependency, just the same way we'd check a random package before pulling it into a project, which is to say, read what it does and check what it reaches out to.
[12:13] So just because an agent's skills are an open standard, that doesn't say anything about whether a given skill is safe.
[12:21] Okay, so that's our five best practices.
[12:24] A good skill is...
[12:26] A skill the agent will actually trigger.
[12:30] Built with real hands-on expertise.
[12:34] Kept lean so it doesn't fill up the context window.
[12:38] With a deterministic script whenever a guess would be dangerous or just inconsistent.
[12:45] And vetted before it ever runs.
[12:48] And look, this is all moving fast.
[12:50] It's an open standard and more agentic platforms are adopting it.
[12:54] So this list is going to grow.
[12:56] Which is really us asking what did we miss?
[13:00] If you've built agent skills and maybe you've got a sick best practice, well drop it in the comments.
[13:06] Preferably one that isn't malware.
[13:08] Yeah, preferably.
