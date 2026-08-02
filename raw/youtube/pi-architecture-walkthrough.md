---
title: "Pi Architecture Walkthrough — Full Agent Core + Interactive Layer Explained"
type: transcript
created: 2026-08-06
source: https://www.youtube.com/watch?v=gTeujlv8qK0
video_id: gTeujlv8qK0
language: en
segments: 488
duration: ~39:00
tags: [youtube, transcript, pi, architecture, agent-core, sessions, tools, extensions, skills, compaction, tui]
---

# Pi Architecture Walkthrough — Full Agent Core + Interactive Layer Explained

> YouTube transcript. 488 segments, ~39 minutes. English.
> Video URL: https://www.youtube.com/watch?v=gTeujlv8qK0

## Segments

[0.7] Good morning, everyone. How's it going today?
[2.7] Today I bring you a new format right here.
[5.3] I'm going to be recording with my iPad,
[7.1] and we're going to be going over the entire architecture of Pi.
[13.0] Pi, of course, has been taking the world by storm,
[15.7] and it's actually very minimalist,
[17.6] and it's very beautifully architected and designed.
[21.1] So we're going to be taking a look at how it works, actually,
[24.8] so that you can think about probably creating your own.
[27.7] It's a very educational project if you want to go about it.
[32.1] Or if you're just interested in understanding what's really happening behind the scenes and why Pi is so well designed, this video is also for you.
[41.3] So we're going to be talking about mainly two things.
[44.7] So we're going to be talking, first of all, about the agent core, which is basically just the agentic loop that runs behind the scenes.
[53.1] And this one right here can also be called via RPC or in a programmatic way via the SDK.
[59.8] And then we're going to be taking a look at the PyInteractive way, which is the actual
[64.4] functionalities that are added via the terminal user interface.
[69.2] Okay.
[69.8] So without any further ado, let's actually get started with the PyCore.
[75.6] All right.
[76.3] So let's start off with PyCore.
[79.2] And in order to start with PyCore, I think that it is very, very important to understand
[83.5] that the main part of Py as a design is just its agent core.
[92.5] Okay?
[93.1] In other words, it's agent loop.
[95.5] Let's just call it loop instead.
[97.1] Agent loop.
[98.8] And this is going to be very straightforward.
[100.4] It is essentially all the steps that are going to happen every time that you start a conversation
[105.8] with Py.
[107.4] So let's suppose that you start a conversation with Pi and you're going to go right here.
[111.3] And the first step that's going to happen after you open Pi for the first time and send the first message is it's going to initialize its context.
[121.2] Okay.
[122.2] Now, what does this mean?
[123.9] This means that it's going to put together a bunch of different things.
[127.3] First, it's going to put together its system prompt.
[131.6] And this system prompt is hardcoded into Pi.
[134.7] You can, of course, update it by creating your own system.md in your workspace.
[140.3] But in this case more often it is going to be loading the pre system prompt I probably leave a link in the description if you want to take a look at it It is very very minimalist Don try to make it more minimalist
[156.2] It's already like a few lines of code, a few lines of instructions. After that, it's going to append
[163.4] all the agents.md files that you have, both in home and also in your current working directory.
[173.2] Okay, so of course, be sure to not add too many .agents.md files, because that will just
[183.1] bloat your system prompt. Actually, I think I made a mistake. This is not .agents, just
[187.8] agents.md file. After that, it is going to append all the skills descriptions.
[196.6] Okay, description. So all the skills that you have loaded into your agent, it is going to load
[201.4] the descriptions and it is going to do the same thing with the tools. So all the tool descriptions
[206.2] are also going to go into the initialized context. Okay. And there you go. And then after that,
[212.3] it is going to append your message history like this and your current message right here. So this,
[225.2] if this is a new conversation, then there is not going to be any message history. If this is an
[230.9] ongoing conversation, there is going to be a message history. And if the conversation was
[235.4] compacted, this can be replaced by the summary of your previous message history. Okay, so there you
[245.0] go. That is the initialization context. The second step that happens every single time is this step
[250.9] called the transformation, the transformation of the context. Okay. And what this means is that it
[257.7] is going to take a look at the context that was just created, and it is going to figure out whether
[262.3] or not it needs to compact that context. If it needs to compact, then it is going to compact it
[269.6] and add it right here instead of the message history. Compacting basically means that it
[275.8] takes all of the messages that are right there in the history and summarize them with the LLM,
[282.0] of course, as well. The third step is going to be actually doing the large language model call. So
[288.8] it's going to call your large language model to whichever provider that you have selected. It can
[293.6] be OpenAI GPT 5 it can be Anthropics models it can be Kimi it can be Minimax whatever you want And then your model is going to return a tool call if it wants to make a tool call For example
[307.5] it wants to update a file, read a file, search the internet, etc. And then your tool is going
[316.2] to naturally return a response to your large language model. And then the large language
[320.1] model may decide to make another tool call and then so back so forth. It can do hundreds of tool
[326.3] calls if you're really doing something very complicated or just a couple if you're just
[330.8] searching the web, for example. And then once the agent decides or your layers language model decides
[337.0] that it does not need a tool call, it will just reply and it will give you a response.
[344.7] And there you go. That is essentially everything that happens whenever you send a message to Pi.
[349.4] And that is kind of the core or the agentic loop.
[353.4] It may sound very easy and like it's just a few things that just a very straightforward
[358.9] diagram.
[361.4] But in reality, this is quite a complicated thing.
[364.3] And in Pi, it is coded from scratch.
[366.6] There is no additional library helping Pi build the whole thing right here.
[372.7] And for the record, there are libraries that do this for you that have this agentic loop
[377.5] preloaded.
[378.3] So you just have to import the agent loop and use it.
[383.3] Some examples are, for example, OpenAI Agents SDK.
[389.7] You also have Versus Sales AI SDK and all of this.
[396.6] But in this case, this one right here is completely custom.
[401.4] So that's with the agentic look of Pi.
[404.5] And that's the first thing.
[406.1] The second thing that we're going to take a look at is the sessions and memory.
[412.3] Okay, and the next thing to understand right here is the memory.
[416.3] Let's just call it like this, memory and sessions.
[422.1] Because this is one of my favorite parts of Pi, actually,
[425.5] which is that it is extremely easy and straightforward to export your sessions,
[431.3] to navigate them, to go to a previous step in the session, to fork it, etc.
[435.5] It is very, very straightforward and very, very well designed.
[439.7] So first of all, where are the sessions stored?
[443.5] The sessions are stored in your home directory inside your directory inside agent and inside sessions
[455.7] And inside here, you're going to see a bunch of different directories.
[460.6] And they're going to be mapped into each one of your working directories.
[465.1] So for example, let's suppose that you were working in an application called dashboard.
[470.0] And it's going to be a dashboard directory.
[472.8] then let's suppose that you were working in an application called weather app.
[478.6] Weather app.
[479.9] There's going to be that directory right here, etc.
[482.6] So here's going to be a list of directories.
[485.1] And then inside each directory, you're going to have each session with their ID, etc.
[492.4] And it's going to be stored in JSON-L.
[496.1] And what this basically means is that it is going to be just a very straightforward,
[500.4] file with the message here like that. It's kind of exactly like a JSON, but instead of having an
[511.2] actual JSON object, it is going to be just a document with a bunch of JSON-like objects and
[517.9] one object in each line. And this, of course, makes it very easy and very straightforward to
[524.0] document this because that means that whenever there is a new message in your conversation,
[529.6] all it has to do is append it in the last line.
[533.7] And these objects, of course, include the role, the message, etc.
[539.1] And there you go.
[540.2] It is extremely straightforward.
[542.0] And as you can see, it stores all of your sessions
[545.3] by the location where you started to work on them.
[549.5] And then every message is just its own JSON object.
[554.4] And this is, of course, easier to update
[556.6] than if you actually had an array,
[560.5] then you would have to update
[561.6] just a single part of the whole thing.
[564.2] JSON-L is just much more convenient.
[566.8] So that is the thing about sessions.
[569.1] Let me actually go into the...
[572.2] I'm going to show you that in just a moment
[573.9] in the actual Py command line
[576.6] to show you how this actually works.
[578.4] But before we do that,
[580.0] let me show you something that is very, very fun,
[583.8] which is the fact that these sessions right here
[586.6] are stored in not in a list.
[589.7] So it's not a list of sessions.
[592.1] So not a list,
[593.7] but it's actually a tree of sessions.
[597.3] In other words,
[598.6] you probably have seen
[599.7] that in order to navigate in Py to a previous command
[603.4] or a previous prompt that you gave, you do slash tree.
[607.7] And the reason for that is that all of these messages right here,
[612.0] they have, of course, the role, the message,
[613.9] and they also have a property called parent.
[617.8] And they also have their ID.
[619.9] So this one right here, the parent will refer the fact
[624.5] that this message bifurcated probably from a previous message.
[630.8] So right here we can have the role, all of its information,
[635.8] and here we have the parent, and this parent will be 111.
[641.8] And this one right here is going to have an ID of 111.
[646.2] So now Pi knows that this message comes before this one,
[650.6] but maybe you bifurcated from this one into another message,
[654.0] So you will have another, you fork the conversation.
[657.1] So here you will have all of your conversation history.
[659.8] And here you will have another parent 111.
[662.5] And that basically just creates a tree structure immediately from a single file like this.
[669.0] So now you have two different messages that come from the same parent message.
[676.0] And that creates two different forked conversations.
[679.9] It is just a beautiful design.
[681.9] And I have seen many AI agents trying to migrate into this new tree design rather than just a simple list, one message after the other system.
[694.8] So that is the thing that you're going to be seeing much more often in the coming agents that are coming out.
[702.3] So now that we have seen this in the actual map right here, let's actually show you what it looks like in the screen.
[711.9] All right, so let's go right here into my command line.
[715.9] And as you can see, I have this very nice session
[720.4] where I just talked to the agent
[722.0] about creating some videos, et cetera.
[724.1] And I had my whole video workflow automated right here.
[728.0] And let me show you what happens
[729.8] when you go right here and you do slash tree.
[732.4] As you can see, you have a bunch of different messages.
[734.7] And here, what we're actually doing
[735.8] is we're going vertically.
[738.5] We're navigating through this list of messages
[741.1] in this JSONL file.
[742.6] As you can see a bunch of messages are actually tool calls And a message can be also a user message an assistant message et cetera what going to happen right here let suppose that I want to go right here to this
[754.9] message right here. I can tell it to summarize the previous part of the conversation. And now
[759.2] this is going to create a new message in my JSONL file. And it is going to set it as a parent or as
[767.2] a child message of the message that was right before this one, before I bifurcated. But the
[773.7] other messages are still in the same directory, so in the same list of JSONL messages.
[785.7] So there you go.
[786.5] Here is, and if I go right back into tree, you can see that here we have a bifurcation
[791.4] and you have the summary and the whole thing that I can just take over.
[795.7] Let me show you what this looks like in the actual Py directory.
[803.7] So as I told you, you go to Py, Agent, and inside of here, you go into Sessions.
[809.0] And let me just show you.
[810.6] Inside of here, you have all the sessions in the different directories that I have run Py.
[816.4] So I have Inside Users, Alejandro, Agent Skills, Video Tool.
[820.0] This is one directory.
[821.5] Of course, this is not the exact name of the directory.
[824.8] It is like the path to that in a more standardized way.
[830.4] And I can access any of this.
[832.4] So for example, I suppose that I go into this one right here.
[837.0] I suppose that I go here.
[839.5] And as you can see, I have two sessions right here.
[841.6] So I can just open, for example, the last one, which is going to be this one.
[845.8] And as you can see, it is just a list of JSONL files with all of my conversation right here.
[852.2] And as you can see, every JSONL file starts and ends with this curly braces and just shows
[859.3] the whole thing of what happened.
[860.8] And actually, let me show this to you in code, in VS Code.
[869.3] There you go.
[870.1] Now I open the same thing on VS Code.
[871.7] And as you can see, every single line is a single message.
[874.5] And as you can see, each one contains the type of message, which can be a message.
[878.0] It's ID.
[879.1] It's parent ID, as I was telling you, to create the tree structure, the timestamp, and the
[883.6] actual message right here.
[885.9] So there you go.
[886.9] That is how sessions work.
[889.1] Now let actually take a look at the next part of the PyCore setting which is the tools All right so let talk now very quickly about the tools that it has and actually the as you probably know pi is a very minimalist agent and the tools is the tool list
[909.7] that it has is very minimal as well so it actually only has four tools the first one is the read tool
[919.2] tool then it has a bash tool and then it has an edit tool and a write tool and that's all it has
[928.9] there is no more than that that's you can of course add additional tools to pi if you want
[936.0] you can ask pi to create a new tool you can install packages to for it to add new tools but just out
[942.0] of the box it comes with these four tools i would myself add web search that's the only tool that i
[949.0] always install when I use Pi. So that would be my real minimalist setup. But just by having this,
[957.3] you already have a great minimalist setup, actually. It does, however, let me just mention
[963.5] something very quickly that it's not often mentioned. The fact that, yes, you have four
[968.8] tools, but there are two additional tools that are grep and find. And these additional tools are
[977.3] essentially the same thing or things that you can already do with bash but this additional tools are
[983.3] by default disabled because they are supposed to be enabled only when you want to use pi on read
[989.4] only mode right so you do not you probably don't want to give it access to bash so if you're going
[995.1] to be running pi with slash slash tools sorry dash dash tools and here you pass as an argument
[1001.4] And what tools you want to use, you can mention that you only want, for example, read, grep, and find.
[1009.9] And that is going to give you a Pi system that will be read-only.
[1016.1] And this is, of course, very useful if you're running Pi programmatically.
[1019.4] So, for example, if you're running Pi through RPC, that is going to be very useful because you probably don't want Pi to edit your files if you're just automating a few workflows.
[1029.8] So that is for the tools, it's very straightforward.
[1034.1] Let's now take a very, very quick look on extensions.
[1040.8] So let talk very quickly about extensions and you probably know already what these are if you have used Pi but in case you haven extensions are these packages or things that you can add to Pi to modify its behavior Because of course it is a very minimalist setup
[1057.4] It comes with only four tools by default.
[1059.7] It does not come with either MCP support or with web search or anything like that.
[1065.0] So extensions are kind of this ready-to-use packages
[1068.8] that you can just install on top of Pi,
[1071.8] and Pi will have all of these features out of the box.
[1074.7] And it's very, very cool.
[1076.7] Let me tell you some of the things that you can do with extensions.
[1081.0] So I'm just going to add here extensions like this.
[1086.3] And some of the things that you can do with extensions are, for example, register new tools.
[1094.3] You can subscribe to events.
[1098.2] And this is very important because that's something that I forgot to mention before
[1101.8] is that every part of this entire workflow
[1106.9] that happens every time that you have a conversation
[1109.0] actually is triggering some events.
[1111.9] And these events can be something like tool call,
[1115.3] like agent response, like user message, etc.
[1119.6] And these are events that happen during the workflow.
[1122.3] So you can subscribe to events so that these extensions
[1124.9] or these packages perform particular actions
[1127.3] at a particular time of the agent loop.
[1130.2] They can also register commands.
[1134.8] They can add keyboard shortcuts, for example.
[1139.3] Add CLI flags as well.
[1142.4] They can also update the system prompt.
[1147.8] Or even render custom messages.
[1152.9] And these are extensions that you can code yourself.
[1155.6] They are coded in TypeScript.
[1157.1] And it's very, very straightforward because Pi naturally allows you to, since it is very modular, you can literally just add an additional extension, plug it to whatever you want to plug it, and it will modify the behavior of Pi.
[1172.7] It's very straightforward.
[1174.1] And of course, if you're interested in extensions, feel free to take a look at all of the extensions in the packages part of the website.
[1181.5] Now, of course, be aware that these packages are naturally loading and executing code in your system.
[1188.4] So you probably don't want to install packages from third-party sources that you do not trust.
[1193.5] Or if you want to use them, at least run them through PyAgents.
[1199.6] have pi explore the code of that particular package to make sure that it is safe so that is
[1206.7] one thing now let's talk about skills and the system prompt in pi all right so let's talk very
[1215.7] very quickly about the system prompt it is very very straightforward so the system prompt it's
[1222.4] very straightforward there is just a very simple i can leave a link in the description if you want
[1227.7] to actually read it, but it's very short. It's about 20 lines long. And basically all that it
[1232.9] does is it tells the agent, you are a helpful assistant, you are a pi, helpful assistant,
[1239.7] then all the appended sections, because you can also append your own system prompt to it just by
[1246.0] creating your own append-system.md file. So you can do append-system.md file in your
[1256.2] .py directory, and this will append this one right here
[1260.8] to right after the urpy part of the prompt.
[1268.8] After that, it lists the skills.
[1271.1] And to list the skills, it uses a very nice markup language like this. So skills and skills like this.
[1279.1] And inside of it, there is the skill with its description,
[1281.9] its name, and what it does, etc.
[1283.6] And this is very useful and it's very important to leave it in markup files because this is actually going to be parsed by the terminal user interface later on.
[1294.6] I'm going to be talking about the TUI in a bit.
[1297.9] But this is very, very useful and this is very, very important.
[1301.9] Right after that, it includes the current date and the current working directory.
[1308.8] And that's basically what the system prompt is in Py.
[1312.3] and something important as well that you should keep in mind
[1316.8] is that you can of course override it
[1319.6] if you create your own system.md file in .py
[1325.1] and same thing you can also override it
[1329.1] if you run pi with the flag dash dash system prompt
[1334.5] and then just pass your system prompt like this
[1338.0] so those are ways to override the system prompt
[1341.5] but this is how it is created by default So that has been basically the Pi core And I hope that with this information you have pretty much all you need to create your own version of Py Let now think about how PyCore
[1360.1] interacts with the interactive part of Py, which is the actual part that we see in the terminal
[1365.7] user interface, and how you can connect it to other user interfaces. So let's take a look at that.
[1371.9] all right so now that we have pretty much covered everything related to pi core of course there is
[1378.1] always a way to go a little bit deeper into this but i figured that this is a good place to stop
[1383.8] we can take a look at pi interactive which is another package completely it's not in the pi
[1391.0] core package and this one right here is the actual let's call it the coding agent right
[1397.8] because this one right here is just an agent in itself, just the agent. And this one right here
[1403.0] is going to be the actual coding agent. So let's talk about the entry point on the CLI. So when you
[1410.6] create a new session, you log into the CLI, what actually happens? Let's call it CLI entry point.
[1419.0] So this happens in two different files. The first one is in client.ts. And the second one is on
[1427.4] main.ts.
[1430.6] Okay?
[1431.3] And on client.ts, what happens,
[1433.6] let me just show this to you,
[1435.5] and on client.ts, what happens
[1437.4] is that it receives the py command,
[1440.7] it does a bunch of other things,
[1442.6] like setting the process title, etc.,
[1445.3] and then it calls main,
[1448.0] like this.
[1449.3] Okay?
[1449.8] And here on main is where the fun part happens,
[1454.3] is the arguments are parsed,
[1457.4] It resolves the configuration, so it figures out where the custom working directory is and
[1464.3] everything like that.
[1465.2] It loads the extensions as well, because it's, of course, remember that this is very modular.
[1470.9] Then after that, it creates the agent session.
[1473.6] Okay, so it's not until now that we actually start the agent session, which initializes
[1479.3] actually the PyCore element or the PyCore component.
[1483.9] And it runs in the selected mode.
[1486.9] So it runs, it can be either interactive, of course.
[1490.9] It can be doing RPC as well And it can be doing just print to STDIO which is if you just run pi like this in the command line and just type your prompt like that Okay So that is the entry point Let talk
[1511.5] a little bit about this, particularly on the terminal user interface, because that is also
[1518.3] a very interesting thing.
[1522.0] All right.
[1522.6] So the terminal user interface
[1524.3] is actually very straightforward.
[1525.9] You have probably already seen it on,
[1529.0] I mean, you can see that it is very, very modular.
[1532.0] You have your input right here.
[1534.2] You have your messages on top.
[1535.7] And then you have a bunch of information
[1537.8] in the nice little bar at the bottom.
[1541.3] And it's pretty useful, actually,
[1543.8] pretty fun and very, very minimalist.
[1545.3] and it does not flicker, which is great.
[1550.4] And yeah, everything works very well in a very minimalist way.
[1554.4] And the reason for it is that it is, first of all,
[1556.9] it is completely custom built.
[1558.6] So it does not use textual or anything like that.
[1560.7] It is completely custom.
[1562.9] And then something else is that it is component-based.
[1566.2] Okay, component-based.
[1569.7] And then about that, you have to consider
[1572.7] that each component basically is responsible for its own rendering, for its own inputs,
[1581.9] and also can be updated dynamically. So yeah, that is something to consider. It can, of course,
[1591.6] subscribe to a bunch of different events that are released by the agent core, but it is completely
[1597.6] custom built and that does not mean that you cannot add your own graphical user interface on
[1603.7] top or your own 2e on top but this one right here the one that comes out of the box is completely
[1609.7] custom for pi now let's talk about the compaction or the way that pi deals with compactions because
[1618.2] i find that that is very interesting all right so now let's talk very quickly about the way that pi
[1625.5] deals with compaction, because many different agents deal with this in different ways.
[1631.4] And I figure that the way that Pi does it is actually not only very minimalist, but also
[1636.7] very simple and very intuitive.
[1639.0] So I have seen some agents for example try to measure how long your context is by taking the number of characters in the entire context and dividing that by four to figure out how many tokens approximately are there Now that of course I seen some agents
[1658.6] do that, especially at the beginning when you don't have a response from the LLM yet,
[1662.5] and that seems to work. However, Pi does not do that at all. It just relies on the feedback that
[1668.9] the response from the LLM gives you. It just assumes that on the start, you're not going to
[1674.6] send a super long message anyways. So what happens is that Pi calls this function called
[1683.4] check compaction, check compaction, like that. And it calls it on two different occasions.
[1693.0] The first one is when an agent ends.
[1697.6] That is to say, when the agent finishes a turn and it gives you the actual response from a tool call or whatever.
[1706.1] And also before the prompt.
[1709.1] So before the prompt.
[1711.2] So if you have, so that is before you actually start sending a message.
[1716.0] That's the other moment when this checks for compaction.
[1720.0] And the reason, of course, naturally, the check for compaction is that you do not want your context to be too long
[1727.5] so that when the agent is going to reply, it is going to just overload the context window.
[1733.5] And you, of course, don't want to overload the context window from the start either.
[1737.8] So what happens right here is that once the agent responds, it measures how many tokens are in your response.
[1745.8] And some agents, some LLM, sorry, some LLM providers actually return to you in the response,
[1752.7] the context.
[1753.8] Okay, so the context tokens.
[1756.3] So if those are present, then it just takes them directly from there.
[1760.4] If they are not present, however, it calculates the context by adding together the following
[1765.9] things.
[1766.6] So usually, whenever an LLM gives you a response, you get a usage, let me just go right here,
[1773.5] a usage parameter that includes the usage input,
[1778.5] that mentions how many tokens you input,
[1781.5] then includes the usage.output,
[1783.5] that mentions how many tokens were generated by the LLM.
[1788.5] And on top of that,
[1790.5] it usually mentions the cache.read and the cache.write.
[1799.5] adding all of this together, let me just say that here, by adding all of this together,
[1805.9] then it calculates the context by naturally every single time that an agent ends a turn
[1814.4] or before the user sends a prompt. So there you go. That is for compaction. And of course,
[1823.9] if you want to take a look at what the compaction actually looks like, it is also very minimalist.
[1828.9] Let me see if I can find the actual code right here, because it is very, very fun.
[1835.2] Let's see.
[1836.1] Oh, here it is.
[1836.8] Let me switch to the computer to show you the actual compaction prompt.
[1841.4] And here we are.
[1842.5] We are inside packages, agent, source, harness, compaction, and inside compactions.ts.
[1849.0] And as you can see here, we have the summarization system prompt.
[1851.7] It says, let me just wrap this right here.
[1854.0] You are a context summarization assistant.
[1856.2] Your task is to read a conversation between a user and an AI assistant, blah, blah, blah.
[1860.6] And something pretty cool is that here you have the complete system prompt that you have.
[1866.7] So the messages above are a conversation to summarize, create a structured context
[1870.3] checkpoint summary that another LLM will use to continue the work.
[1874.8] And here is the exact format.
[1876.1] So you mentioned the goal, the constraints and preferences, the progress, what is done
[1880.4] and what is in progress, what is blocked, the key decisions that the agent has made,
[1884.2] the next steps and the critical context. Keep each section concise, preserve exact file paths,
[1891.5] function names, and error messages. And it has a slightly different prompt for updating an existing
[1898.1] already context summary. So as you can see, it is very, very straightforward. And let me see if I can
[1905.8] show you something fun right here. Let's see if I can just open this like this. And let's just go
[1913.7] back into a working repository.
[1917.7] And here I'm in a working repository.
[1920.7] I'm just going to resume one of this.
[1924.7] Let's see if this works.
[1926.7] And there we go.
[1927.7] Something that I can do right here is just ask it to compact the whole thing And so we going to see the exact compaction that it generates right here in just a moment And there you go Here we have the compaction
[1940.3] And if you want to take a look at it, just do Ctrl-O to expand.
[1944.6] And as you can see, we have the exact compaction that follows the prompt that we just saw.
[1949.7] So the goal is this.
[1951.1] The constraints and preferences are this.
[1952.9] The progress, what is done, what is in progress to be done, and what is blocked,
[1957.0] the key decisions it has done, the next steps, the critical context, original request, early
[1961.9] progress, etc. So there you go. That is how compaction works. Now let's take a look, last
[1969.9] but not least, at how the PyInteractiveAgent deals with skills. I think that this is very, very fun.
[1980.8] All right, now something that I wanted to mention precisely about this, and it is very interesting,
[1986.2] is how Pi deals with scales and with custom prompts.
[1993.6] So custom prompts.
[1996.6] These are two different things,
[1998.3] and they are both dealt in a very similar way.
[2001.3] Now, in case you're not familiar with scales,
[2002.9] scales are these MD files, MD markdown files
[2007.5] that contain a lot of very clear, detailed instructions.
[2010.8] And at the header, they have a name and a description
[2014.3] that is loaded into the system prompt.
[2017.7] And when it comes to custom prompts,
[2019.7] they're basically just custom slash commands.
[2022.3] So you can do just like your slash command.
[2025.2] And this is going to be replaced with your system prompt
[2028.4] at the PI interactive layer.
[2031.3] So this is never going to reach the actual PI core.
[2036.0] And that is very important.
[2037.9] Now for custom prompts, it's very, very straightforward.
[2040.4] Whenever you send a custom slash command like this, the CLI is going to read it and it is going to turn it into the actual prompt that you had stored in your custom prompts.
[2053.4] So that is very straightforward.
[2054.8] The part that I find most interesting is how skills are managed.
[2059.6] So remember we mentioned before in the system prompt that here there is a section with all the skills available And that is of course the first part of the skills workflow So in the system prompt let just go again mention the system prompt
[2075.5] There is a lot of things, and then at some point, there is the list of skills available,
[2082.8] as I mentioned before.
[2084.5] Okay, just like that.
[2086.1] And so now your agent, Pi, and your LLM knows that it has skills to work with.
[2093.6] Okay, so it is actually aware that it has skills.
[2097.4] It is not aware that it has custom slash commands
[2099.5] because they just reach pi completely rendered.
[2103.0] But for skills, they don't reach pi completely rendered, actually.
[2106.8] What happens is that let's suppose that you...
[2110.1] So here's the system prompt.
[2111.4] And then the user, you, send your slash skill colon.
[2117.4] And then you mention the skill that you want.
[2119.0] Let's suppose that your, I don't know, your custom workflow.
[2123.6] Now, this right here is going to be intercepted by the interactive layer.
[2131.1] So your agent core will not see this command.
[2135.3] It has no idea that you call a scale like doing slash scale colon.
[2140.0] You could very well use another CLI or another TUI that uses the dollar sign like codex or just a slash command like clotcode, etc.
[2154.0] okay so what happens is that when this command reaches the the interactive layer of your agent
[2163.4] of your cli this is going to be replaced by the skill like this skill with markup tags which will
[2175.9] contain its name, it will contain its description, and it will notably contain its location.
[2185.2] And the location right here is basically just telling your agent where this skill is located.
[2192.3] So for example it can be located in say pi agent skills It can be for example located in dot agents slash skills And this can be either in the current working directory or in your home directory And this is going to be very important
[2213.7] because this data is going to be sent in the message. Okay, so the AI, your LLM does see this,
[2221.8] But there is a custom instruction right here in the prompt saying that if a skill is invoked, use the read tool to read it.
[2240.5] And what happens is that it will basically just after receiving the skill, it will just call the tool read and it will read this location and then get the response.
[2251.5] and continue all the work.
[2254.0] So the skills, at least in Pi,
[2255.9] are not automatically replaced at the interactive layer.
[2263.0] Some other agents that I have seen take this command right here
[2266.7] and immediately paste the contents of the skill directly from here.
[2273.7] But what Pi does, at least in this interactive layer,
[2276.7] of course you can do this differently
[2278.0] because this is done outside of the core that we saw before.
[2283.2] What it does is it just sends the skill that was called
[2285.9] with the name, the description, and its location
[2288.6] so that Pi manually opens it with a tool call
[2292.2] and gets the results and then just continues with the rest.
[2296.4] So there you go.
[2297.3] I mean, I think we have covered pretty much everything
[2299.4] related to how Pi is built.
[2301.1] I feel like you should be ready to at least start with Pi
[2305.4] and maybe even create your own version of Pi.
[2308.3] I find it very educational.
[2310.0] I have been working on things like that before
[2311.8] and over the past few weeks, and it's very fun.
[2315.7] So I hope that this has been interesting.
[2317.7] This is just a kind of a side research project
[2321.2] that I was working on.
[2322.8] If you're interested in similar videos like this,
[2325.1] feel free to let me know.
[2326.1] And of course, if you have any questions,
[2327.9] post them right here in the comments.
[2329.6] I'll be very happy to talk about this.
[2332.2] so thanks a lot and I will see you in the next one
