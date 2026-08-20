---
title: "LangChain OpenWiki: CLI for Code Wikis (v0.2 with OKF)"
type: raw-transcript
created: 2026-08-20
video_id: XNX-1h2K-9U
url: https://www.youtube.com/watch?v=XNX-1h2K-9U
duration: 00:16:44
language: English
auto_generated: false
source_api: youtube_transcript_api
segments: 420
timestamp_format: "[MM:SS]"
---

# LangChain OpenWiki: CLI for Code Wikis (v0.2 with OKF)

> Duration: 00:16:44 | Segments: 420 | Language: English | Source: API

## Transcript

[00:05] [MUSIC PLAYING]
[00:11] Thank you, everybody, for coming.
[00:13] Like Sean said, I'm going to be talking about our project OpenWiki, how we built it, the ideas behind it, how it works, and where we're going next with it.
[00:22] So what is OpenWiki?
[00:24] OpenWiki is a CLI that you can use to generate and maintain your repo documentation built specifically for agents to consume.
[00:33] And that's a key theme of OpenWiki.
[00:36] And I'll talk about this a little bit more later.
[00:39] But to start, I'll talk about why we built it.
[00:41] So Harrison, a couple weeks ago, our CEO, he asked, what is the next big thing in the AI space?
[00:47] Last year, it was OpenClaw.
[00:49] I'm not trying to claim OpenWiki is OpenClaw.
[00:51] Obviously, it's a little bit of a different audience.
[00:53] But he was saying, if OpenClaw or personal agents was the big theme last year, what's the big theme for this year or right now?
[01:02] And my answer to that was general purpose memory.
[01:04] I think memory has been kind of a frontier research area of agents for the last four years, three and a half years.
[01:12] But I don't think anybody's really done them very well in a general purpose way yet.
[01:17] So I think general purpose memory is the next big thing that will actually work.
[01:22] We decided to start with code-based docs, because that's a well-defined area where You can still build, or where we believe, we can build a general purpose for any type of code base memory solution for that, and then eventually expand that into more memory areas.
[01:35] So why we build this?
[01:36] We think it's finally possible now with modern LLMs and agent architectures.
[01:43] So what is our thesis?
[01:44] The first is it's built for agents.
[01:46] So a lot of these memory or documentation agents or sites four repos are built for both agents and humans.
[01:56] But that changes a lot of the structure and how you need to present the content, how you generate and maintain the content.
[02:01] And our thesis is that people use agents for everything nowadays.
[02:04] You're probably not writing a ton of code manually anymore.
[02:07] Your agent's probably doing a lot of that, which means these docs should be built specifically for agents to consume.
[02:11] And that changes how these docs should be generated and structured.
[02:15] The second-- this is a developer tool, so it needs to be trivial to set up.
[02:18] We decided to go with the CLI for this because that's really easy for people to install.
[02:22] And then a very easy onboarding, right?
[02:24] These developers, if you want them to try your stuff, they need to be really easy to set up.
[02:27] So we thought a CLI with an easy onboarding is the best way to do that.
[02:30] And then updating itself.
[02:31] So you can generate Wiki or documentation for code-based repo by telling Claude Code to do it.
[02:37] And it'll do a pretty good job at that.
[02:39] But keeping your docs up to date automatically and maintaining those docs is a much trickier problem.
[02:44] So this needs to be able to handle the memory or Wiki updates automatically without you need to do any extra work.
[02:51] So our three things here were build the docs specifically for agents to consume, make it incredibly easy to get set up and running, and then make sure they can update themselves automatically without you needing to do anything after that.
[03:04] So the first part, building docs for agents, not humans, what does this actually change?
[03:08] Well, if you're building docs for humans, you're probably going to have to tell a story.
[03:13] You're going to have a quick start with background on the repo.
[03:18] All your doc pages will need to flow.
[03:20] You can't just have isolated snippets here and there.
[03:24] You're probably going to have screenshots, maybe videos.
[03:28] And all of this is great for humans to consume, but not very optimized for agents to consume.
[03:34] So if you build it for agents, your agents are going to retrieve fragments, probably not the entire doc's page itself, or definitely not entire directories or folders within your doc's page.
[03:45] Every concept needs to be self-contained because they're going to be pulling snippets.
[03:48] You need to have predictable, precise, accurate headings for filtering and for searching.
[03:54] And then you need to make sure everything's optimized to fit in the context window.
[03:56] So you don't want to have some random base 64 string, which gets included in the agent's tool call that adds 50,000 tokens.
[04:03] So thinking about how to generate these docs and how to structure them is very different depending on who your audience is.
[04:09] And because we think agents are-- I mean, the ones writing-- not because.
[04:13] Because agents are writing all the code for us nowadays.
[04:16] The doc should be tailored specifically for agents.
[04:20] Getting it set up, this needs to be really easy.
[04:22] So like I said before, we want the CLI.
[04:23] The CLI is very easy to set up.
[04:25] I'm sure everyone here has used CLI.
[04:27] They're using them every day.
[04:28] It's one command to install, just through NPM, and then one command to initialize your wiki.
[04:33] You run open wiki init.
[04:35] It steps you through setting your API keys, configuring your model, your wiki brief, which is like a prompt about how it should navigate your repo.
[04:44] And then it automatically starts generating the wiki.
[04:47] And it writes a bunch of files for you, like your AGENTS.md, or it'll modify that if you already have one.
[04:51] And that's what tells the agent to consult the wiki.
[04:54] It'll write a GitHub Actions file, which will automatically update your wiki going forward.
[04:59] So all of this needs to be really easy or else a lot of developers will churn.
[05:03] So like I said before, we thought a CLI, with a very concise onboarding, that automatically writes docs and writes all the other files you need to use as wiki, was the easiest way to do that.
[05:14] OK, so what does it actually write?
[05:16] If anybody has seen Karpathy's LLM wiki, it'll look very similar to that.
[05:19] It's just a bunch of markdown files that have a specific structure and then links between them.
[05:24] So OpenWiki will always have a quickstart.md, which is what we prompt the agent to look at to get a high-level idea of the repo and the other docs content in the Wiki.
[05:35] And then it just breaks up docs into different directories, which it deems necessary.
[05:41] So we don't provide tons of guidance and what directories to write, what types of topics or concepts to document, we want to leave that all to the agent.
[05:49] Instead, we give it some guidance on roughly how many files to write, the size of each file.
[05:55] We want every file to have a very specific topic it's focused on.
[06:00] And that way, agents don't need to get context from multiple different areas when they're reading individual files.
[06:07] And then OKF, which is Google's open knowledge format, we adopted that.
[06:11] And that adds a deterministic front matter, a YAML front matter, to every markdown file, which has a type, title, description.
[06:18] And I'll get into this a little bit later.
[06:20] An index file, which gives you an index of all the files in that directory, so your agent can very quickly get a high level look at what is in a given directory.
[06:28] And a log file, which is like a change log.
[06:31] This, I actually think, is very important for agents, so it can look at the history of the wiki, but also for humans.
[06:35] We don't need humans to read the wiki.
[06:39] But these wikis are getting checked into your code base, and you probably want to read what is changing every time your wiki updates.
[06:45] So the log.md, you can think of it like a change log.
[06:47] And for this, we tell people, look at your change log.
[06:51] That's how you can see what changed in your wiki.
[06:53] And then if you want to dive deeper and look at individual files that have changed, go from there.
[06:57] But you shouldn't really need to unless you want to read individual pieces of context from the wiki.
[07:02] The change log should give you everything you need to understand what changed in the most recent wiki update.
[07:08] Great.
[07:09] So before I mentioned OKF, this is Google's Open Knowledge Format.
[07:12] It says v0.1 here, but they just released v0.2, which we're going to add support for in the next day or so.
[07:19] But what is OKF?
[07:20] OKF is a very simple spec.
[07:22] Essentially, all it is is a specific front matter, which you add to the top of all of your markdown files.
[07:28] You can see what it looks like here.
[07:29] We have type, title, description, resource, tags, and timestamp.
[07:33] And then there's also a-- you can add arbitrary fields as well for specific concepts or tags you might want to add.
[07:40] But these are the main ones which are defined by the OKF spec.
[07:44] And this will allow us to do a lot of interesting things around retrieval and filtering and searching in the repo because we have types we can filter on.
[07:53] So you can say, give me every architecture doc.
[07:56] Or there's the tags field, so you can filter on tags.
[07:59] So adding this very small section to the top of every wiki doc actually compounds a lot when it comes to retrieval, which is arguably a bigger challenge than dox generation.
[08:11] So generating dox is fairly easy with agents.
[08:14] Retrieving from these dox is a lot more difficult.
[08:17] And OKF allows us to do that easier because it gives us all these deterministic fields we can very quickly pull from to use in filtering or searching.
[08:25] And then links between dox is a big thing.
[08:27] So OKF, you can have these resource tags, which can link to individual files in your code base.
[08:33] But then also mark down links between dox.
[08:35] So your agent can pull a snippet from doc A, see its link-- a reference to something in doc B, and very quickly visit that other doc A references through these markdown links.
[08:44] And the markdown links, obviously, are not specific to OKF, but OKF provides a big-- has a big emphasis on these docs to cross link pages.
[08:53] And that way, your agent can very quickly discover context which is related to the context that it's reading.
[08:59] Great.
[08:59] So I've talked about what OpenWiki is.
[09:00] Now, does it actually do anything?
[09:03] So we're still very early in our eval process for this.
[09:07] Right now, we're just pulling from DeepSWE, which is a coding agent benchmark, and running a subset of those tasks on-- or the subset of tasks without open wiki, so just plain DeepSWE tasks, and then generating a wiki for those tasks and rerunning it.
[09:23] What do we see so far?
[09:24] There's a few stats.
[09:25] So it's essentially all around more concise agent usage.
[09:30] So fewer tool calls, fewer searches, fewer outputs, while still yielding the same or better results.
[09:36] So I don't have it here, but in these 20 tasks, we were usually seeing about seven or eight successful tasks without OpenWiki and then nine or 10 with OpenWiki.
[09:47] So it's very slightly better.
[09:48] But as you can see, there's a significant drop in token consumption, which is the big thing here.
[09:54] These docs should provide better context for your agent so that your agent can more efficiently navigate your code and implement changes.
[10:01] So this is still very early.
[10:03] We're going to be publishing a lot more on benchmarks soon.
[10:04] But initially, from these DeepSWE tasks, it is looking somewhat promising.
[10:09] All right, so that's what we've done so far.
[10:11] What did we get wrong?
[10:13] The first is that only agents would read it.
[10:15] I went into this thinking, I only use agents to write code.
[10:19] So I'm going to build this for agents to consume.
[10:22] Very quickly, we got a lot of feedback from people saying, I want to read these docs as well, which in hindsight, makes sense.
[10:27] like humans are still in the picture for engineering.
[10:29] So these humans want to read the code as well.
[10:32] So before it was designed purely for agents, we ended up having to make a few modifications so that it could be a little bit more friendly for humans.
[10:41] And what does this mean?
[10:42] It essentially means diagrams.
[10:45] This is a big thing for humans to consume it.
[10:47] So before it was already all marked down, which is natural language, which you can very easily read.
[10:52] But a big change is adding diagrams, which we didn't add initially because it seems less important where they can just read the text and consume that.
[11:00] After writing the diagrams, I don't have evals proving that they are better or worse for the agent.
[11:04] But I think it kind of makes sense that these diagrams could also help the agent.
[11:07] But more importantly, they help the user a lot by showing sequences or state diagrams or flow charts.
[11:14] It's not very hard for the agents to generate these.
[11:16] You could argue maybe it helps the agent as well, because they can generate them, which means they can also consume them.
[11:20] But the main thing here is it helps humans consume these docs a lot easier than when it's just plain text.
[11:27] So that's one thing where we went in thinking, only agents are going to read these.
[11:30] Very quickly, we found out people want to read these as well.
[11:35] All right, so now I'm going to talk a little bit about how the OpenWiki CLI actually works.
[11:40] It's very simple.
[11:40] We have two commands, so OpenWiki init and update, which I'll talk about later.
[11:44] An init is the first one you'll run when you're setting up your repo.
[11:46] So this initializes your repo.
[11:48] We have a setup wizard.
[11:49] So you'll set your API keys.
[11:51] You'll pick your model.
[11:53] You'll set your instructions, which is essentially high level prompts telling the CLI agent some more specifics about your repo.
[12:00] It's not the only prompt for generating the wiki, but it's an extra context that you can provide to your agent.
[12:06] So set of wizard, very simple, LLM keys, model, agent config.
[12:13] We would then automatically write these files for you, like I said before.
[12:15] So there's a GitHub Actions workflow.
[12:16] This is what's going to automatically keep your wiki up to date by running a cron daily and then updating your wiki based on any changes to your code base.
[12:23] It'll modify your AGENTS.md or CLAUDE.md, or both if they're there.
[12:28] This essentially just injects a little piece of context, telling the agent about OpenWiki, what it is, how it can access it, and when it should reference it.
[12:36] Right now, this is the only way agents are made aware of OpenWiki.
[12:41] I'll talk about some changes we're going to add later, but automatically updates or writes to your AGENTS.md.
[12:47] And then it sets a cron, right?
[12:48] So there's a GitHub Action set to run daily, to automatically keep your Wiki up to date.
[12:53] And then as soon as you accept these files and merge them, there's a cron in a GitHub Action.
[12:57] And then you can kind of forget about a wiki.
[12:58] And it just continuously updates the wiki every day based on new changes.
[13:02] Your agent is already made aware of it.
[13:04] And then you don't really need to think about it, and you just get these efficiency gains without having to do anything else.
[13:08] So after you configure your agent, set the repo scaffolding, it runs the actual agent.
[13:14] So this looks at the repo.
[13:15] It writes a plan.
[13:16] It looks at the Git history.
[13:17] This is a pretty big thing.
[13:19] We don't want it to just read the code that's in the repo right now.
[13:22] but also the Git history so we can see how things have changed and progressed throughout the repo.
[13:27] You can look at git commit messages, git titles as well.
[13:29] So yeah, this is a big thing where you don't want it to just read the current snapshot of the repo, but also go deeper into the history of changes made to that repo.
[13:39] And then it writes the actual docs.
[13:40] And at the end, we have a deterministic pass which runs over all your docs.
[13:43] Make sure they're all OKF compliant.
[13:45] It automatically writes your index.md and your change log.
[13:50] And then we have the agent add the specifics about what it changed later on.
[13:54] And then it updates a few other metadata files at the end of the agent run.
[13:58] So that's the init run.
[14:00] This is what you run when you first onboard it into OpenWiki in your repo.
[14:04] The next is update.
[14:05] This is very similar.
[14:06] So you have the scheduled run that we run in the GitHub Action, which OpenWiki init will write for you.
[14:12] This will run OpenWiki updates.
[14:14] The first thing it does is it looks at the Git history.
[14:17] So if there hasn't been any changes since it last ran and we tracked these changes in this last update.json file, then we don't run OpenWiki.
[14:24] If you haven't made any changes, we don't need to update the repo.
[14:28] If there are changes, then we run the agent.
[14:30] And we look at the Git log.
[14:31] We fetch every commit that has been merged since your agent last ran.
[14:35] So by default, we run this once a day.
[14:38] If you're checking in 2,000 commits to your repo every day, you might want to update that recurring action to run every four hours or six hours or eight hours.
[14:47] So it looks at the Git history, updates your repo based on what has changed, and then opens the PR for you, and you just get to merge that, and then you have your Wiki up to date.
[14:59] So where does it stand today?
[15:02] It's an open source repo, so a bunch of stars on GitHub, a lot of forks where people are taking this, forking it, and modifying it for their own use, or you're just using it on NPM.
[15:11] And it's MIT license, right?
[15:12] So you can use this.
[15:13] You can install it on NPM.
[15:14] You can use any provider that you want.
[15:17] We support like 10 or 15 different providers.
[15:21] Or you can fork it, modify it for your specific use case, and use it in your repo.
[15:25] We thought this should be an open source project because that's one way to get great adoption, because developers love open source.
[15:33] But then it also means that you can customize this for your own usage.
[15:35] That's a big thing in Agent Docs, where we build in a very general way.
[15:39] But if you have specific workflows or readings to use your agent, or open Wiki, you probably want to fork it and make those modifications directly.
[15:48] So what's next?
[15:49] Obviously better prompting.
[15:50] So we've started to invest more in evals.
[15:53] This is those DeepSWE evals.
[15:55] Better prompting for it to analyze larger repos and update itself as well.
[16:00] And then search and retrieval tools.
[16:01] So like I said before, right now we write in AGENTS.md and/or CLAUDE.md telling the agent about OpenWiki.
[16:09] But this is obviously not the end state.
[16:12] That's great.
[16:13] And agents will ingest this.
[16:15] But getting it dedicated tools for searching and filtering and querying the Wiki is a clear next step.
[16:20] And we've already seen gains from that in some of the PRs we've been running that are not merged.
[16:24] So better prompting, of course, and more specific retrieval tools for your Wiki is what we're going to come out with soon.
[16:33] And yeah, that's it.
[16:34] So try it out on your worst document repo.
[16:36] The QR code just brings you to the GitHub repo.
[16:39] And you can also install it with npm install openWiki.
[16:43] (applause)