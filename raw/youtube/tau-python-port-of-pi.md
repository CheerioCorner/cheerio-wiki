---
type: raw-transcript
title: "Tau: A Python Port of Pi - Coding Harness Deep Dive"
youtube_id: qo1QNxWcm28
url: https://www.youtube.com/watch?v=qo1QNxWcm28
slug: tau-python-port-of-pi
published: ""
duration: "25:03"
fetched_at: 2026-08-02
immutable: true
---

## Transcript

**[0:00:00]** So, recently, it has become very clear that coding harnesses are one of the most important parts when creating reliable AI workflows. And what we're going to be doing today is we're going to be taking a look at one coding harness that we're developing internally right here called Tau.

**[0:00:14]** Tau, in case you're not familiar with it, is a coding harness completely developed in Python, and it is actually a port of Pi in Python. So, if you like Pi, you will probably like Tau because it's literally the same thing. Everything behind the scenes works exactly the same way.

**[0:00:27]** The only thing that's a little bit different is the terminal user interface, as we're going to see in a moment. So, what we're going to be doing today is first of all, take a look at what is Tau and how it works, which is basically the same way as Pi.

**[0:00:37]** Second, we're going to take a very quick tour of Tau, basically a crash course, so you see how it works and how you can use it, and some other tips on how to use it more effectively. And those also apply to Pi, by the way. And thirdly, we're going to take a very quick and high-level tour of the architecture to see how it all fits into modern harness design.

**[0:00:55]** So, without any further ado, let's take a look at it. It now works pretty well, but it was a hard battle to fight. Alright, so let's take a very quick look at Tau. Just for the record, if you want to install it, all you're going to have to do is go right here to Tau.

**[0:01:06]** You copy this thing right here and you go right here to your terminal and you just run this script right here. This will install it either on Mac, Linux, or Windows, so no problem about that. Once it is installed, you're going to be able to just run Tau like this and you will be greeted with a nice little user interface.

**[0:01:24]** I told you that this is a port of Pi. In other words, it is exactly like Pi, but in Python, at least architecturally. But the terminal user interface does change a little bit because I did not build the entire terminal user interface. I'm actually using Textual, which is a very nice framework for building terminal user interfaces in case you're not familiar with it.

**[0:01:44]** I'll leave a link in the description. Great, great resource. But it also means that some of the terminal features are a little bit different from Pi. But the commands and everything else is pretty much the same. And so let me tell you: once you start off Tau, you're going to be greeted with this nice user interface.

**[0:02:04]** You have your prompt right here. You can send bash commands by adding one little exclamation mark, and this will be added to context. If you do not want your bash command to be added to context, you just add two exclamation marks, and then whatever you send right here will not be added to context.

**[0:02:23]** On the sidebar, you have right here the name of the session, which is automatically named actually. So if I just say hello right here, you will get an automatic name right here, as you will see in just a moment. And there you go, greeting session. Here you have the activity, how many agent turns it has had, and how many tool calls it has done.

**[0:02:42]** You also have the cumulative usage because it is not the same as the context. So the cumulative usage is essentially the total number of tokens that are used during the session, including how many tokens in, how many tokens out, and how much this is costing you if you're using this over an API.

**[0:03:00]** Right here, you have compaction information. So at one point, compaction will automatically be executed. And then right here, you have a clear view on what files are in your context. So of course, you have the system prompt, which is the base system prompt of Tau and of Pi, which is basically the same system prompt.

**[0:03:17]** I have copied it from Pi. And right here, you have which files are added to context. So in my case, I have my agents.md from my home folder and also from my project folder. Then you have the tools that are loaded. This can change depending on what extensions you have loaded, just like in Pi.

**[0:03:35]** In Tau, just by default, you have four tools, just like in Pi. So you have read, write, edit, and bash. Read can read pretty much any file, and same thing about the others. And right here, you have the skills that are loaded. And this is very useful because you can always have a nice view on how many skills are loaded by default.

**[0:03:54]** Then you have the prompts, the custom prompts, which are basically the slash commands, custom prompt/slash commands that you have created. In my case, I have created a few slash commands that are pretty useful. And then you have the extensions that are loaded, and here you have the version that is currently loaded for Tau.

**[0:04:11]** That is a very quick tour of the graphical user interface. Now, let's take a look at some commands. Alright, so let's start off with what you probably want to do when you just install Tau or Pi for that matter. I'll just zoom in a little bit. So when you just install Tau, you're going to go right here and do login to actually connect to your LLM provider.

**[0:04:31]** In this case, I have subscription, API, and custom provider supported. So for subscription, you have Codex, Anthropic, and GitHub Copilot. I'm adding a few more over the next few days, but those are the default ones for now. You can also connect via an API key using OpenAI, Anthropic, Gemini, etc.

**[0:04:48]** And you can also search right here. I actually recommend you install the Hugging Face one because you will have access to all the inference providers by Hugging Face via a single router. Or you can also add your own custom provider right here. And it's pretty useful. Alright, so once you have logged in, what you're probably going to want to do is select a model.

**[0:05:08]** So you can do slash model just like in Pi. And here you have a list of all the models that are available for you depending on which accounts you have logged into. In my case, I'm going to, for example, use models from Hugging Face. So I want to use, for example, GLM 5.2.

**[0:05:22]** I can just either press enter or click on it. And now I have selected my GLM 5.2. And then I can ask it something like read the README and summarize it for me. And as you can see, right here, we have a nice little spinner saying that it's going to do something.

**[0:05:39]** Every single action as it happens, or every single tool call first appears yellow as it is being executed and then green when it was finished. And here we have the answer. So the answer is, of course, rendered in Markdown and it is streamed as it is delivered to the agent.

**[0:05:57]** And if you want to expand the thinking tokens, just like in Pi, you do Ctrl T and here you have the thinking tokens. And if you want to expand the tool results, you do Ctrl O and here you have the results from the tool calls. And here, right here, of course, if it's a read call, then you have only preview because it's a lot more lines.

**[0:06:17]** And if you want to copy something, that's also pretty useful. You can just select it and it will be copied automatically in Markdown, which is something that terminal user interfaces sometimes get wrong because when you copy something, sometimes it appears with multiple mistakes and errors. Right here, it is all copied in nice markdown.

**[0:06:36]** Now, as I mentioned before, your session is named automatically when you send the first message, and that is done using the same model that you have selected. However, you can also rename the session manually. So you can do slash name hello world and your session is going to be renamed hello world.

**[0:06:54]** And that's actually applied to the title of the tab in your terminal as well. So, as you can see, that right here. And just for the record, that is a slight deviation from the standard Pi design. Pi does not automatically rename your session whenever you send the first message.

**[0:07:12]** That's specific to Tau because it's, of course, a little bit more specific to this terminal user interface. But that also becomes very useful when you use the Pi-like command resume to check out the previous sessions in this working directory. Because, just like in Pi and in pretty much any other harness, your sessions are attached to the working directory where that session was executed.

**[0:07:36]** So, in this case, here are all the sessions that I have executed in this particular directory or in this repository. And they have, of course, the last time when they were opened, the last model that was used in that session, and then right here, you have the name of the session.

**[0:07:53]** So, in this case, I can, for example, take a look at the SDLC skill that I was working on, and here are the three sessions that are related to that particular feature that I'm working on. So, there you go. That is how you can resume sessions. And if you want to use one, for example, I can just use this one right here.

**[0:08:11]** And as you can see, here I have my session. And as you can see, the scroll is also pretty smooth. It's also pretty cool about this terminal user interface. All right, so now let's take a look at how sessions are designed here in Tau, which is basically exactly the same way as they are made in Pi.

**[0:08:27]** In case you're not familiar with how sessions work in Pi, sessions are stored in the format of trees, not in the format of lists. In other words, every single message that you see right here has a property called parent. And each property points to that particular message precedent message.

**[0:08:45]** So each message is pointing to the previous one. That actually makes it very easy so that whenever you want to fork a conversation, you can just take this message right here and create a new leaf that points whose parent is this one right here. And then that basically creates a fork in the same history of the entire session.

**[0:09:05]** And in order to inspect that and to navigate your tree, just like in Pi, you do /tree and you go right here and you see a list of all your messages right here. You can also show or not show the tool calls. That makes it more much more easy to navigate.

**[0:09:22]** And here you can go to any single part right here and continue your conversation from it. So for example, I can go right here and click right here. And now I have essentially just forked the conversation from this particular place. And if I want to continue navigating my tree, I can just continue like this.

**[0:09:38]** I can just click right here, for example, and say hello. And this will create, oops, I'm not using Claude Opus 5 anymore. Let's use Sol, for example, and I can say hello. And as you can see, it will create a new message in my tree. Let me just show you tree.

**[0:09:55]** And as you can see, here I have my new hello message that has created a new branch in my tree. And all of this is stored in a single file, just like in Pi. So let me just show you how that works. If we go right here to my .tau folder right here, and I go into my sessions file, here I'm right here inside my .tau sessions, and then exactly my path into the working directory of Tau.

**[0:10:22]** Here you can see all the sessions that are right here. And as you can see, every single session is a JSONL object. JSONL file, sorry. And in case you're not familiar with it, a JSONL file is a file where every single line is a JSON object, which makes it very, very easy to keep transcripts like JSONL files because then every single message can be just one new line because every single message is going to be a new JSON object.

**[0:10:49]** Here we can, for example, open this one right here. And as you can see, actually, let me just open it like this. And as you can see here, every line is an ID. It points to its parent ID, the timestamp, the type of the message, and then the contents of the message.

**[0:11:08]** And that's essentially how Pi works. Just like before, inside Pi, agent sessions, and then just like in Tau, you have the path to that working directory. And in here, we have a bunch of different sessions. Let's just open the last one. And just like in Tau, it just shows you how it works.

**[0:11:28]** And just like in Tau, you have the parent ID, the ID of this message, the type of message, and then you also have the contents of the message. As you can see, it's exactly the same thing as with Pi. And it's also very easy to navigate, as you can see right here.

**[0:11:45]** All right, so now let's take a look at how to export your sessions. This is one of my favorite features of Pi and of course of Tau as well, because it allows you to take a session that you had with an agent, especially if you were testing, for example, a skill or some tool usage or something like that, and then analyze it with another agent and then improve, of course, your skills and your tools.

**[0:12:07]** So in order to export a session, you do exactly what you would do in Pi. So you do slash export. And if you just do slash export, that's going to export the session with the ID of the session, which is, of course, not very easy to identify. But another way is that you can do export, export like this, and then add a path and a name to your file.

**[0:12:30]** So, for example, I can say that this one is going to be inside my home directory, inside temp, and we're going to call it test.html. And now it's been saved right here. I can just copy this like this, and I can do double like this, and I just open it.

**[0:12:47]** And there you go, here's my session. And as you can see right here on the left, I have all the tool calls and everything that happened. So, for example, the session info is the first one, then I had a model change, and then I had all the tool calls.

**[0:13:00]** For example, here was that bash tool call, here was another read tool call, etc. I can just also filter right here what I want to be shown. If I don't want to show all the tools, I can just remove them from the list right here. Also, the events such as session change, such as session name, model change, thinking level change as well.

**[0:13:18]** I can remove it as well. And that way, I just have my session like that. And here, I also can visualize the tree of the entire conversation, so all the forks and everything that happened. And if I want to download it as a JSONL file, I can just click right here and it's downloaded as a JSONL file.

**[0:13:35]** And of course, if you don't want to just go through all of this to export it to a JSONL, you can either do slash session, and this will actually give you all the information about the session, so where it is stored, which how many tools and skills it has, and here you have this session ID and the name.

**[0:13:54]** You can also use this to retrieve it in the sessions directory that I showed you before. And of course, by just selecting this, it's automatically copied into your clipboard, and you can just ask your agent to analyze this. So, for example, if I want another Tau agent to analyze this one, I can just go right here and say analyze this session for me.

**[0:14:17]** And it will identify, and that Tau saves the sessions in.tau/slash sessions and identify this session and analyze it and give me feedback about it. Let me just pause the video right now, and I'll be back as soon as it finishes analyzing the session. And there you go, it gave me some important feedback.

**[0:14:44]** As you can see, I also got a notification. This is very useful when you have a bunch of different agents running side by side. I like to use Cmux for that. I'm going to show you a little bit more about that in a moment. But this is how you would usually analyze the session.

**[0:14:58]** It's very useful if you're, for example, testing a new skill or a new set of tools, a new MCP server, or something like that. This is very, very useful. You just have another agent analyze the session, and you just have to pass in the session ID, and it will automatically know where to look for it and how to read it.

**[0:15:15]** And by the way, before I forget, there's also the possibility of exporting the session straight into JSONL file. So, just to name it whatever you want, or give it a path as well, and just name it JSONL directly right here, and that will directly name it JSONL file.

**[0:15:32]** And now it is, of course, a JSONL object. Oops, it's not supposed to be like this. Let's just open it with code like that. There you go. So, here we have the JSONL file that we just exported. Okay, so there you go. All right, let's talk very quickly about skills now.

**[0:15:49]** And skills in Tau work pretty much the same way as they would work in any other coding agent, and exactly the same way as they work in Pi. So, skills, in case you're not familiar with them, they're procedural instructions saved in markdown files that can be accompanied by scripts or other templates, and they are stored in usually two different directories, either in your home directory or in your project root directory.

**[0:16:13]** For your home directory, tau by default reads skills inside your.agents directory and also skills inside your.tau directory and your .pi directory. Any of those stored either in your project root directory or in your home directory. And in order to explicitly call a skill, you can do slash skill colon, just like in Pi.

**[0:16:38]** And then here you have your autocomplete box right here, which describes the skills that you have available. In this case, let's suppose that I want to use the explore project skill. I can add additional context right here, but in this case, I'm just going to run it like that.

**[0:16:52]** And when you run a skill, it immediately appears like that. Now, in this case, of course, I ran or explicitly called a skill with slash skill, but your agent can also choose to call a skill by itself because within its context, by default, it comes with the descriptions of the skills that it has loaded.

**[0:17:12]** So, if by after receiving your task or whatever you requested, it deems it necessary that it has to use a skill to complete a task, it will automatically invoke it. And you will see it in your transcript like this, just like in Pi. This works exactly the same as in Pi.

**[0:17:30]** If you want to stop the running, you just click escape, just like I did, and it stops mid-turn. Now, something important about this is that here's a tiny difference with Pi. It's basically just a new command because in Pi, since we're using a different terminal user interface, it's a built-in terminal user interface, they list the skills available right at the start of the session at the header of the session, right?

**[0:17:55]** And since Tau does not have the same terminal user interface, you have this nice little sidebar with the skills listed. But I can at least all the skills right here that would make the sidebar very crowded. So what we did is we added, just like in Claude Code, actually, a command called skills.

**[0:18:13]** And right here, you can just open it and you have a list of all the skills that are loaded into your context. It's very useful because right here you can immediately either press F1 to open the description to see what this skill actually does, escape to go back.

**[0:18:28]** And right here, you can also read the skill directly within your terminal user interface. So you just press Ctrl Enter and it opens the skill like this. And says, for example, in this case, we're opening the skill Firecrawl Parse. And this, by the way, is not added to context.

**[0:18:47]** And here you can read the exact contents of the skill that you have available. It's very good for debugging as well your existing skills. You can just open them directly in your TUI. And of course, within that same menu, you can also add them just by clicking enter.

**[0:19:04]** And that basically just adds the skill like this, and you can call it like that. Okay? Press enter. And just like in Pi, you can also do Ctrl D to close Tau. And just like in Tau, in Pi, sorry, you also have the possibility of creating custom prompts, which are essentially just slash commands that whenever they are sent to your terminal user interface or to your coding agent, they are replaced with a longer prompt.

**[0:19:27]** So, for example, in my case, I have created a prompt, a custom prompt called issue that essentially just opens an issue on GitHub. And here I can just ask it, create a test issue. And as you can see, I'm just going to click escape. But as you can see, it was replaced by the full prompt right here.

**[0:19:45]** And skills and prompts are a little bit different in the sense that skills are much more procedural, and they can also be called directly by the agent. And also, the description of the skills are added directly into the context from the agent from the start, into the system prompt.

**[0:20:02]** And that is different from prompts because prompts are at a much, much further abstraction from the coding agent. Like, actually, the coding agent has no idea that it has custom prompts available unless it reads its own documentation. Skills, it knows about them because they are in its system prompt, but the prompts, they are replaced by this custom prompt at the front-end level.

**[0:20:26]** So the agent just receives the full prompt, and that's all. Also, we'll make a further video later about extensions because that's a little bit more delicate and there is a lot of development going on on that side. But we have already pretty cool extensions. Actually, shout out to Ryan, who created this beautiful extension that allows you to run sub-agents inside Tau and it is just great.

**[0:20:49]** I'm going to be making a further video about this, but it's really cool. And actually, all extensions are completely compatible with Pi. You just have to port them because they use the same events. And just like in Pi, by the way, remember that when you create a new skill or a new custom prompt and Tau or Pi is already open, it does not automatically know about them.

**[0:21:11]** You have to do reload like this. And now it basically just rereads the skills and custom prompts that it has available and also its custom extensions and themes. Oh, and by the way, before I forget, you also have the possibility of selecting a different theme. So there is tau-light, there is high contrast, and you can also create your own themes.

**[0:21:33]** Just ask Tau to create its own theme. It knows how to do it. For example, in this case, I created a new Catppuccin Mocha theme, which is like this. And basically, Tau, just like Pi, knows how to create its own skills and its own custom prompts and its own themes.

**[0:21:47]** And the themes in particular, they were just recently made customizable via simple JSON files. So you can just create a bunch of them and have them locally for your own session of Tau, for your own version of Tau. And finally, let's talk very quickly about the differences between Tau and Pi, because of course they are supposed to be the same thing.

**[0:22:08]** Tau is intended to be a port of Pi, but in Python. But there are slight differences, of course, because the fact that these are different languages also comes with some differences. The most important difference is, of course, the terminal user interface. In this case, with Tau, you have this nice user interface that is a little bit inspired by OpenCode and other agents that I have found in the internet.

**[0:22:32]** And it is, of course, built with Textual, whereas the terminal user interface of Pi is built completely from scratch with TypeScript within Pi. So that is a little bit of a difference. But that also comes with other differences that come after that and because of that. Notably, as I mentioned before, the skills command, which allows you to list all the skills, the prompts command, which also allows you to list all the prompts just like this skills does and finally also since we were already on that side I also added a slash tools command allows you to show all the tools and also their origin so for example if I do if I just come right here and run tau with this extension right here which is the extension that I showed you before the extension from Ryan about subagents right here you can see that I have more tools which are create subagents tier subagent etc and as you can see right here if I go to tools I will see that I have my four tools that are built in and these three additional tools that were added by my extension something else that is a little bit different is the notifications so when I ask Tau to do something so for example count to 10 and I go to another place right here it I will see the name of the session right here and I will see a notification once it's done as you can see right here and same thing right here if I go like count to 10 and then I go back right here it will get a notification once it's done and that way this way in this case I am using Cmux which is pretty nice a very nice terminal especially for working with agents because you have this nice little vertical sidebar it reminds me a little bit of herder and I also of course I run it with Claude Code and with Pi and whatever agent that I'm using it's just very nice to have my agents right here and whenever they're done they just give you a notification which is something that Pi does not do by default also but you can also have it do it with an extension, there is no problem with that.

**[0:24:30]** Just added it by default on Tau because it goes well with the terminal user interface edition. There you go, that is basically all there is about Tau. I told you a little bit about the architecture, a little bit about the features, a tiny little bit about the divergence.

**[0:24:43]** I will be posting more videos about each part of the architecture to give you an idea of how this works so that you can also build your own coding agents and learn a lot about how these things work because it is very, very fun. And thank you very much for watching.

**[0:24:59]** So, thanks a lot for watching, and I will see you in the next one.

