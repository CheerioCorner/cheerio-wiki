---
title: "Herdr: The Runtime Your Coding Agents Live On"
type: raw-transcript
created: 2026-08-25
video_id: Ct-mtWqV3Ro
url: https://www.youtube.com/watch?v=Ct-mtWqV3Ro
duration: 00:16:41
language: en
auto_generated: false
source_api: youtube_transcript_api
segments: 173
timestamp_format: "[MM:SS]"
---

# Herdr: The Runtime Your Coding Agents Live On

> Duration: 00:16:41 | Segments: 173 | Language: en | Source: API

## Transcript

[00:00] Good morning, everyone.
[00:01] How's it going today?
[00:02] Today, we're going to be taking a very quick look at this tool that you see right here, which is Herder.
[00:07] Now, Herder allows you to run multiple agents at the same time and keep track of everything that they are doing.
[00:13] It has multiple workspaces, it allows you to create new tabs, and it automatically shows the agents that are open right here in the bottom.
[00:22] It is also a terminal multiplexer, which means that when you close your computer or close the application, your agents actually keep running because they are background processes.
[00:32] And it works great with VPSs, with remote servers, so that you can connect your Herder instance to a VPS and have your agents running that VPS and control them only from your computer.
[00:44] So, you can just turn off your computer and continue working with your agents on, say, your phone, for example.
[00:50] So, today we're going to be taking a look at everything about it.
[00:54] First of all, we're going to see a demo, then we're going to see very quickly what is a multiplexer and how it works.
[00:59] Then, we're going to see how to set this up for local and remote work with a VPS.
[01:03] And finally, I'm going to show you how to use an agent to control the Herder API itself and have an agent create new sessions, open new tabs, etc.
[01:14] So, without any further ado, let's actually get right into it.
[01:21] All right, so let's actually take a look at what Herder is and how to install it and how it works.
[01:26] Kind of a very quick tour, so kind of a TLDR if you want to just get right into using it right away.
[01:33] And actually, to be completely frank, the first time I used Herder was about a month and a half ago, and I didn't like it, didn't think it was for me.
[01:40] I was already using Cmux and Tmux, so didn't really feel like Herder was adding anything to my workflow.
[01:47] But then I tried it again about a week and a half or two weeks ago, and I haven't been able to stop using it.
[01:53] It is great, it allows me to keep track of all my agents and to keep them running as background processes, and I can also have my agents run in the cloud, which I'm going to show you how to do in just a moment.
[01:59] To give you the tour, you go to herder.dev, you click right here on copy and you paste that into your terminal and you install it.
[02:12] And what that actually does is that it opens this thing right here.
[02:15] Let me just detach right here.
[02:17] So you install it like this, and then you just run herder in order to open the herder workspace, work the application.
[02:25] And what it does is that it allows you to run multiple applications inside of a single terminal window.
[02:32] So as you can see, this is still one single ghosty terminal window, and there are a bunch of different workspaces right here.
[02:39] Now, each workspace is kind of like a window tab, okay?
[02:44] And each, I mean, each workspace has also multiple tabs that you can open within it.
[02:48] So for example, here is one workspace, and here is two tabs that I'm running.
[02:55] Ideally, you want to have one workspace per project that you're dealing with, and one tab per process, or different agents that are running different things.
[03:02] For example, you can have an implementer right here, a reviewer right here, then a server, your npm run dev right here, etc.
[03:10] And also, herder is very mouse-friendly, so when you don't know the shortcuts, you can just click around and just right-click to rename new tab, etc.
[03:21] But of course, I prefer to use the keyboard, so I just do CtrlB.
[03:26] And for example, to create a new tab, I do C, and then I just name it new test.
[03:31] And then right here, I am in my new terminal right here, and I can just run an agent right here.
[03:37] Okay, and something useful about this is that when you run an agent right here, for example, let's say I use Pi, it automatically detects that this is an agent and it lists it right here in my system right here.
[03:51] As you can see, it just created a new system right here.
[03:54] I can just say hello, and here Pi, well, I don't have Haiku configured, you just say hello, and here Pi responds, and this is the agent right here.
[04:05] You can also create panes, so for example, you can split right or down, or you can also do Ctrl B to detach and to open a new pane like here, and you can also run multiple panes like that.
[04:17] Okay, so in other words, there are workspaces that are divided into that you can divide split into tabs, and each tab you can split into multiple panes, be it vertical or horizontal, like this, okay?
[04:32] And it works great.
[04:33] And something cool about this is that when you create a new workspace or a new tab, and you just open a new agent, so for example, here I am going to run tau, which is which is the agent that I am developing right here at Hugginface.
[04:49] You can just open tau right here, and you can ask it to sleep for 10 seconds.
[04:55] And we can just go back to another session right here.
[04:57] Here are all the agents across different tabs and across different workspaces.
[05:02] And this one right here, since it's working, it's shown right here in yellow.
[05:07] And when it finishes working, it will change color to blue and send me a notification.
[05:14] Let's see what it does.
[05:16] And there you go, it's blue.
[05:17] And here we have my notification.
[05:19] I can just go right here and also get the nice little blue thing right here on the workspace.
[05:25] So you can open the workspace or click right here directly in the agent, and you can see that the agent finished.
[05:29] It's very, very, very useful.
[05:30] So you can keep track of a bunch of agents working at the same time.
[05:37] And something useful again is that let's, for example, ask it to sleep again for other 10 seconds, is you can detach from it, you can close this application, you can go back to go to have some food or go for lunch, then come back, run Herder again.
[05:51] And as you can see, the process is still running.
[05:55] And so your agents will continue running in the background even if you close the application or leave your computer.
[06:02] And this is even more useful if you connect it to a VPS, which is what I'm going to show you in a moment how to set it up.
[06:07] Okay, so that is essentially the very quick overview of Herder and how it works.
[06:13] Let's now see very quickly what is a multiplexer and how to set this up.
[06:20] Alright, so very quickly let's talk about what is a multiplexer.
[06:23] I don't want to go very much into detail with it.
[06:25] I just want to give you exactly what you need to understand what's actually going on and how to use Herder.
[06:31] So a multiplexer essentially is this layer or this application that sits in between your terminal and a bunch of different processes.
[06:39] Okay, so as you saw right here, this is a single ghosty tab, a single ghosty window.
[06:45] And when I open Herder, there are a bunch of different things happening inside this individual window.
[06:52] So I have a bunch of different workspaces, a bunch of different tabs, a bunch of different agents.
[06:56] And each one of this is an individual process that is running in the background.
[07:03] And that is why I can then close this window and it's going to continue running those processes.
[07:09] So the multiplexer is this layer in between that handles and manages all these sub-processes that they can run in the background.
[07:16] And if there is ever an error or some or I shut down the application or something like that, these sub-processes are going to continue running.
[07:25] And that is the good part about this whole thing.
[07:28] Now, before I told you, for example, that in order to open a new tab, you do CtrlB and then you do C and then just name it whatever you want.
[07:37] Now you may be wondering, why don't I just do Ctrl T or Comment T?
[07:42] Well, the reason for that is that it is not very straightforward to access the multiplexer with shortcuts.
[07:50] This happens, of course, by the way, with Tmux as well.
[07:52] If you want to do a new tab and you wanted to do command T, that's going to open a new tab in Ghosting.
[07:59] If you wanted to do, for example, some shortcut in your coding agent, how do you know that Ctrl D is going to go into your coding agent and not into your multiplexer?
[08:13] Well, this is the reason why you need a prefix.
[08:16] And that is how multiplexers usually work.
[08:19] You add a prefix and whatever comes after the prefix is a shortcut for your multiplexer.
[08:25] And if you don't use a prefix, then it targets the application or the processes that is running underneath the multiplexer.
[08:32] So if right here I do control D, then that is going to go into my coding agent and it's going to close my coding agent.
[08:41] If I do control B, I enable the prefix and whatever comes after is going to control herder.
[08:48] So in this case, I can just do question mark to open the shortcuts available.
[08:55] And here I can see, for example, that prefix plus S opens the settings.
[08:59] So I can just do CtrlB again, which is my prefix, and then S to open my settings.
[09:05] And as you can see right here, I have my themes that I can run.
[09:09] I have my indicator.
[09:10] So for example, here I can use this or this one right here.
[09:13] The sound, if I want my notifications to enable any sound.
[09:17] Here I have if I want my notifications to open toast right here in the corner of herder, or if I want them to be notifications of the terminal or notifications via system, I can set it up right here.
[09:30] I think by default it is off.
[09:31] So if you want notifications, do enable this right here.
[09:36] For pain labels, if you want the panes to have different labels.
[09:40] For integrations, in this case, these are all the agents that are natively supported.
[09:45] And yeah, that's basically how it works.
[09:48] If you want to use tau, for example, what you're going to want to do is you're going to want to install the tau extension, so tau install, and you're going to want to use tau herder right here by Ryan Dolphin.
[10:00] And then you're going to be able to use Tau within Herder.
[10:07] And there you go.
[10:09] Essentially, that is what is going on right here.
[10:11] And that's what is a multiplexer.
[10:13] I do encourage you to use the mouse at first and to identify what you're more comfortable with doing.
[10:19] And then whenever you want to do something, just take a look at the key bindings and slowly get used to this shortcut so that you can actually start using Herder without using the mouse because, because of course, it's nicer to just use the keyboard.
[10:34] Now, now in order to detach from a herder session that is currently open, all you want to do is CtrlB and then Q.
[10:42] And now all your agents will continue running and everything that you started right there, your processes, your servers, everything is going to continue running.
[10:51] And then when you go back to Herder, it's going to be right where you left off.
[10:56] So that is essentially how to set up Herder for your local machine.
[10:59] Right here, everything is local.
[11:01] Let's take a look at how to do the same thing, but on a VPS so that you can even turn off your computer and your agents keep running on the VPS and you can control them with Herder.
[11:12] Okay, let's do that.
[11:14] Alright, so let's take a look at how to run your agents remotely in your VPS.
[11:18] It's very straightforward actually.
[11:20] Well, of course, the first thing that you can do is to just connect to your VPS via SSH and then inside of here install herder like we did before and then just run herder like that.
[11:32] And that's going to work.
[11:33] That's going to open the herder interface in your VPS but it's going to add a lot of latency because herder the herder interface is going to be running in your server and then streaming that interface back into your terminal.
[11:46] So that's going to add a lot of latency that you don't need.
[11:49] What we're going to do instead is we're going to disconnect from it and we're going to run herder like this dash dash remote and we're going to connect to the same SSH identity that we have right here.
[12:03] Connect.
[12:04] If this is the first time that you're connecting then it's going to ask you to install herder first and you just click enter to validate it.
[12:12] It's going to install herder and right here we are running herder within our computer, but it is controlling our VPS.
[12:19] So, as you can see, there is no latency, everything's running fine.
[12:22] But once I click enter, every sub-process or every process is going to be run directly in the VPS.
[12:29] So, I can just run this right here, turn off my computer, and that's going to keep working on my VPS.
[12:35] Okay, so right here, I can just open my agents, I can just create and install my GitHub credentials, my Git credentials, clone my repositories, install my agents, have them all work right here, and then detach from it.
[12:49] I can just do Ctrl B Q to detach, then I can just do herder session list to see which sessions are open.
[12:56] And as you can see, it is my default session right here, which is open in this machine, and the other one is in the server.
[13:03] So, in order to reconnect to it, I just do herder remote VPS, and I am back where I left off, as you can see.
[13:09] Okay, so there you go.
[13:11] Not going to go much further about this.
[13:13] Let's take a look at how to have your agents control your herder session.
[13:18] All right, so very quickly, I'm just going to show you how to have your agents control herder.
[13:24] Now, herder, it's actually very straightforward because herder exposes an API through a CLI.
[13:30] So, you can just teach your agents to use that CLI in order to take control over herder and have them initialize new agents, in new workspaces, in new tabs, in new panes, etc.
[13:41] So, there is no better way to do this than with a skill, and they have an official skill actually.
[13:48] So, the best way to find it is to go right here to herder documentation.
[13:51] You go to the agent skill file, and right here you will find it.
[13:55] Here it is.
[13:57] And you can, of course, install it within PX skills, or you can just copy this directly into your skills directory.
[14:06] And then, once that is done, you will have it right here.
[14:09] I do encourage you to use TAO.
[14:11] It is the agent that we've been building right here.
[14:13] It is really fun, it comes with native support for local models, so you can just go right here and run it with Lama Cpp and select your models.
[14:22] And it is actually built on top of Py, or more like using the exact same architecture of Py, you can think about it as a port, a Python port of Py.
[14:32] It is exactly as minimalist, and it scores pretty much exactly the same way in most benchmarks because it is pretty much the same thing, but in Python.
[14:40] You can also extend it with extensions, and the extensions can also modify the terminal user interface and add new widgets right here to your sidebar.
[14:48] So, that is pretty cool.
[14:49] And as you can see right here, I already have loaded herder, as you can see.
[14:54] So, let me just start a new session right here.
[14:57] Open tau and ask it, create a new herder session, run pi inside of it, and ask it to say hello world.
[15:08] And let's see, just like in Py, we can just enable to see the thinking traces with Ctrl T.
[15:19] And there you go.
[15:20] As you can see, we have higher Py agent saying hello world.
[15:25] And as you can see, this can be very powerful because now you can design your own skills and your own workflows to spawn up sub-agents or different sessions and talk to them between each other with this herder skill, which is pretty, pretty fun.
[15:41] I do encourage you to experiment with it.
[15:43] I have not finished experimenting with this particular part, but it looks really, really fun and with a lot of potential.
[15:49] So, definitely let me know what you recommend that I try about this.
[15:54] So, there you go, that is essentially everything that there is to know to kickstart with Herder.
[15:59] So, big shout out to the creators.
[16:00] I have been very much enjoying it.
[16:06] Thanks a lot to you for watching.
[16:08] Do give a try to Tau.
[16:09] I've been putting a lot of effort to it.
[16:12] Especially if you're familiar with Py or you really like Pi and you want a Python port of it or you want a user interface that looks a little bit more like open code.
[16:23] And there you go.
[16:24] Well, thanks a lot for watching.
[16:26] It's been a great pleasure as usual.
[16:28] Do definitely give a try to Herder.
[16:30] I completely recommend it.
[16:31] And it works extremely well with Tau.
[16:34] So I do recommend that you give a try to that one too.
[16:37] Thanks a lot.
[16:37] It has been an absolute pleasure.
[16:39] And I will see you in the next one.
