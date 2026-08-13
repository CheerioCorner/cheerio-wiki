---
title: "How To Use Claude Design To Build Beautiful Sites"
type: raw-transcript
created: 2026-08-13
video_id: bBlY5YOsKN8
url: https://www.youtube.com/watch?v=bBlY5YOsKN8
duration: 18:33
language: en
auto_generated: true
source_api: pytubefix
segments: 53
---

# How To Use Claude Design To Build Beautiful Sites

> Duration: 18:33 | Segments: 53 | Language: en | Source: pytubefix (auto-generated caption)

## Transcript

[00:00] A good design really matters because if you want customers, your site's visuals are the first thing they judge. There are a lot of tools that help with this and Claw Design is one of the most powerful ones out right now. But a good design doesn't come from just prompting the tool. So, in this video, we're going to go through a five-stage process that you can run with Claw Design yourself.

[00:19] Whether you've never opened Claw Design or you've been using it for months, there's something here for you. If you've never opened it before, that's fine because we start from what the tool even is. And if you've already been designing in it, we go into how we integrate Claude design with Claude code to make it a lot more powerful. So use the timestamps below to jump straight there. Now, if you're new to the channel, then welcome. We're actually a software company and we've built a full system for you around claw design based

[00:44] on our internal processes. So without wasting any more time, let's get into the video. This [snorts] section is only for those who don't know what Claude design is. So if you already know it, you can skip to the next one. It first came out as a research preview when Claude launched their Opus 4.7 model.

[01:00] It's basically a design tool with a really easy to use interface that lets anyone, even people who aren't technical, create good designs with just simple prompting, and it's available across all the paid plans. It doesn't stop at websites either. You can also design your slides, documents, and even create animations with it. Also, Claude Design is available on both Claude desktop and the Claude website, so you don't necessarily need to install the desktop app. If you want to use it on the app, you just download it, run the

[01:25] installation, and log into your account.

[01:27] Then you open the sidebar, click on design, and its interface opens up where you can start using it. Claude Design gives you all the Claude models to choose from, even Fable, which is their most capable model, though you only get limited access to it. Their flagship for general use is still Opus, and it's still the best for designing, but the problem with these Opus models is that they just burn through a lot of tokens.

[01:47] Haiku on the other hand doesn't use nearly as many but might need more refining to get a good design out of it.

[01:53] And on top of that you can set the effort level for each model too. An effort level basically controls how hard the model thinks before doing anything.

[02:00] We use Opus 5, the latest model as of now on medium effort for our design tasks because it lets us design easily without wasting too many tokens. It also lets you bring in all your MCPs and skills which makes it way easier to use your own system right inside it. For those who don't know, MCPs are basically what let an agent connect to things outside of it, like other tools and services. And skills are sets of instructions that teach the agent how to do something better with step-by-step guidance and references it can follow.

[02:29] Now, before you start, there's one thing to keep in mind. There's a limit to how many designs you can create. Claude Design runs on a 5-hour limit, but it isn't just for your designs. Everything else you do on Claude counts toward the same one. Within that 5h hour window, there's a cap on how many tokens you can spend. And once it runs out, you have to wait for those 5 hours to finish before you can use it again. And even though it's gotten so much better than when it first came out, Claw Design is still

[02:54] very token hungry. So if you're using it heavily, expect your limits to run out faster. So in order to counter the extensive token usage, you need to take some steps before you jump in straight to prompting. These steps are important because they make sure that you get the design you want without wasting much tokens. The first thing you need to do is create the design.md file. This is a format that Google started and it holds all the details of your brand's design.

[03:18] Your AI coding agent reads that file before it writes anything. So every screen it gives you comes out on brand.

[03:24] It never has to guess so it never goes off brand. Now Claude design has a feature that lets you create design systems. A design system is basically a set of style choices that show up across your site. It contains your fonts, your colors, and all the other details that make up your design. And it lets you use those to build more designs. So, all the designs built using a design system will follow the same style and look like part of the same site. But the main reason you want to start with a predefined

[03:49] design file is that all these models output designs that look identical. If you let Claude Design build the design system on its own, it just falls back to the generic colors and styles that the Opus models use everywhere. And because of that, your site ends up looking like every other AI built site out there. But if you hand it a design.md to base the design system on, that file tells it exactly what you want. So it's way more controlled and doesn't fall back to those defaults. And as we already

[04:16] mentioned, the other reason is consistency. Since the whole design system is based on that one file, every page follows the exact same style, which is what keeps your multi-page site looking consistent. So for that, we made a skill that plans out the design for you. Now to use this skill, you run it inside Claude Code. And Claude Code is basically another tool by Anthropic. It is basically an AI coding tool you run in your terminal that can build entire apps for you. To install it, you just head to their official site, copy the

[04:44] install command, and run that command in your terminal. Once that's done, you type Claude in the terminal, and you can start using it by typing in your prompt.

[04:52] Since we were going to build the app in Claude Code, we just dropped this skill into the skills folder inside the Claude folder, which is basically where all of Claude Codes settings and configs live.

[05:02] You probably won't have this folder, but you can just tell Claude Code in the prompt box that you need to install skills in the project, and it's going to set it up for you and even put the skill in there by itself. The Claude folder won't be visible when you open it in Finder. That's because its name starts with a dot, and files that start with a dot get treated as system files. So, Finder hides them by default. So, to get in there, you need a code editor, which is basically an app that lets you see

[05:27] all your project files in one organized place. The one we use is VS Code, which is Microsoft's, and it's the one most builders use just because of how easy it is to work with. To install it, you head to their official site, download the installer for whatever system you're on, and run the installation. Then, you just open your project folder inside it, and you'll be able to see everything, including the hidden ones. Then whenever you want to build an app, you just invoke this skill with its slash

[05:51] command, which is basically a shortcut you type with a slash to trigger it and give it a prompt telling it what you want to build. It interviews you and plans out the product's visual identity with you in detail. And once it's got everything, it writes out the whole design.md. And it also validates that against Google standards by running the scripts. This skill also holds anti-slop references, which are basically all the common patterns that give a site away as AI built. So planning out a design.md

[06:18] first is really the best option. Also, if you're wondering where to get this skill, don't worry. We'll tell you later in the video. Now, the app we're building in this video is a project management app where you manage all your coding projects in one place. So, to get started, first you give Claude Code a prompt with the /ash command for the skill, telling it about the app you want to build in broad terms and what it's going to be used for. And it starts by analyzing the visual identity of your idea. Once it's got that, it starts

[06:44] asking you questions like whether you've got a color in mind for your website. If you've already decided on your brand colors, you can hand them over or you can let it choose on its own. Because you're using the skill, it won't default to slop colors. So, you don't have to worry about that generic look, but it's still better to have them decided beforehand. What we usually do is use Coolers, which is the site that generates and visualizes color palettes, so that you can see how the colors look together. Once you find a color

[07:09] combination that you like, you can click on export, then choose the code option, and it's going to give you this text that you can just copy and paste into claude code. Claude Code is going to put this inside your design MD files for you. After that, it's going to create the design MD file and run some verification steps. And the design file comes out exactly the way Google laid it out. Now, that Google format is easy for the AI model to understand, but it's hard for you to actually picture the designs that are going to be generated

[07:36] from this. So, you can't tell if it's the design that you want to finalize or if it needs to be changed. But don't test it inside Claw Design. You generate your designs there. And let's suppose you don't like them. Now, you have to go back into the design MD, change it, and generate everything again. Every one of those rounds costs you tokens. There are a lot of tools that will just visualize a design MD for you. And one that we used is design MD.space. Any one of them shows you how your design file looks and

[08:02] how all the colors it picked for different areas work together. It visualizes all the elements too, which makes them way easier to decide on. Now, this is just one way of creating a design.md, but there are others. If you don't want to make one on your own, you can just copy the design of a popular website as a sample. And honestly, copying one as a reference is what a lot of people already do. You take it in as a starting point and make changes to make it your own. Just like how a lot of people have taken notion simple look and

[08:28] worked it into their own designs after a few tweaks. For that, there's a site called getdesign.md which lists a lot of brands and all their styles as design. MD files that you can go and get yourself. Since we already have a design.md of our own, we can move ahead with claw design and create a new design system from it. When you create a new one, it asks about the product details, but since you've got a design MD, you just upload that as an asset. And then it starts generating the new system by reading the file, creating

[08:57] to-dos, and working through them one by one. After a bit, Claude design creates the design system and visualizes everything for you, building out the different elements that'll be part of the app it's used in. Now, for you to follow along with this workflow, you'll need this skill. We've put it up on GitHub and linked it in the description so you can get it for free. So, to use it, just give this link to Claude Code and tell it to set it up for you, and it will install it and place it in the do.claude folder without you having to

[09:22] do anything. Now, once [snorts] the design system is locked and it's time to actually use it, there's another really important step before you start building the site, and that's creating wireframes of what you want to build. A wireframe is basically a rough sketch that lets you see where all the elements go and whether they're placed right. Now, you could just straight up build the site instead, but that causes problems. You'd have to wait a long time because claw design is slow to create designs. And if

[09:46] after all that, the design isn't what you need, you'd have to redo the whole thing. So to do that in claw design, select the design system you just made, select the wireframe option and give it a prompt with the details of how many screens you want. After that, it also lets you create multiple options for each screen. So Claude can give you a few different styles of each screen to pick from. It won't take long to generate them and then you can look at the structure and go through all the variations it made for the same page.

[10:12] It's also got a commenting feature. So to change any one thing, you just drop a comment on it. A comment basically sends the exact details of that element along with your prompt. So Claude knows exactly which area needs changing and how. That makes it way better than just prompting and letting Claude figure out which area you mean on its own. And to add one, you just select the comment feature, click on the exact thing you want to change and type in the change you need. And you can add as many comments as you need. You could send

[10:38] them one by one, but it's better to group them and send them all at once so it resolves them together and fixes everything in one go. that keeps your wait time to a minimum. So, just keep tweaking it until you're happy with the structure. And once you're done here, you can move on. And if you're enjoying the video so far, subscribe to the channel and hit the hype button. This small gesture of support goes a long way for us. Now, once you've designed the wireframe, it's time to design the actual website on top of the structure

[11:04] you just built. Since you usually ask Claude to make a few variations of the same design, you first pick which variation you want out of the ones you just made. Each of these pages is identified by its code number. So you just select it by that. After that you give it a prompt telling it you want these screens turned into the actual full detail design and you tell it to keep the layout and structure from the wireframe with clean spacing and hierarchy. Claude design then reads all the elements it made while building the

[11:30] design system and using those it builds out the whole site so it comes out exactly according to the design system and the wireframe just how we need it.

[11:38] Just like on the wireframe, you can comment and fix things here, too. And you'll probably need a lot of changes because it tends to not pick button colors properly. Some buttons need to grab attention and should be in a bold color to stand out. So, you can ask it to fix those. And because it builds on top of the wireframe, the site comes out a little blank on its own with just the basic parts added in over the rough sketch. So, you can ask it to add textures and elements for a fuller look.

[12:02] You can also apply edits right on the design. So, if you've got a minor change, like deleting some element, you can do it directly on the design without prompting Claude for it. That way, the change gets applied without you waiting around and wasting tokens on something that small. And once you're done making changes, you can ask Claude Design to polish the overall look and it makes these subtle, meaningful changes to the design. So, the first version of the app is done now, but there's something

[12:26] missing and that's the animations. Now, they're important because they make a site feel better to use and guide the eyes toward what actually matters.

[12:34] things like scrolling into view or any other small touches make the site feel more alive. So for that we gave Claude Design a prompt to add animations across all the screens like things scrolling into view or any other small touches that make the site feel more alive. And we told it to keep them subtle and not make anything too springy since that can feel off. Once claw design was done, you could see the scroll into view and text pop-up animations that make the whole site feel so much more interactive. And

[13:00] once you're happy with the design, you can move to the next step, which is the handoff. So the design in Claw Design is finalized now and it's time to actually give it life by turning it into the real product we're going to deliver in the end. Claude Design didn't have this before, but now you can export your designs directly into Claude Code. It uses the Claude Design MCP, which is basically what lets Claude Design and Claude Code talk to each other. And it doesn't just work one way, it works both

[13:25] ways. So you can move your designs from claw design into claude code and bring them back over the same way too. You open claude code again and paste in this prompt that you got from claude design.

[13:35] It starts by reading the metadata which is basically the summary info about the design and then it lists out the project files. It reads the design system first because that's where the design is defined and then it reads all the elements one by one. Once it has the full design, it builds it out in your project with all the animations that are in claw design. But we had to doublech checkck this conversion to make sure it was done right. Visually, everything looked good. But there could still be internal issues when the raw code from

[14:01] claw design gets turned into a real app.

[14:03] So, we asked it for a deep review and it started checking the design.md against what it actually built to see if the two matched up. And this review caught multiple issues like responsiveness problems, which just means the app wasn't looking right on different screen sizes such as mobiles or tablets. What claw design builds tends to have this problem because it's still an early version. So whatever it designs comes out best for the exact thing it was made for. A design made for a website will look great there but not always on other

[14:30] screen sizes. So we ran the fixes and after that the visual part of the app was working and responsive. Now to actually finish this app and make it work, we needed to connect it to something called a database. So the problem right now is that even though the app is fully functional and if we click on buttons it will work and add details on a new project. But if we open the app again, all the progress that we made in the app will be gone. This is why we need a database so that all that data is stored inside the app and people

[14:57] can actually use it. So for that, we used Superbase. It's one of the most popular back-end providers out there and one of the best ways to manage users and all your data. Now, Superbase isn't new to us. We use it in almost every app we build, no matter who we're building for, and even a lot of our internal tools run their entire setup on it. That's because it takes a lot of the work off our hands that we'd otherwise have to do. It's also one of the most widely used databases just because of how easy it is

[15:22] to connect with AI agents like Claude Code. Superbase also has an agent skill which holds instructions, scripts, and resources the agent can use to work with Superbase more reliably. That way, it knows everything it needs to get the most out of Superbase's tools. To install it, you just copy the install command and give it to the agent. And inside that bundle, there are two skills. One is the superbase skill which is basically a guide on how to use it and the other is the best practices skill which holds the best ways to

[15:50] optimize your app's performance. Once the installation is done, you'll see both new skills added to your project.

[15:56] The superbase skill triggers whenever there's any database or login task and the best practices skill kicks in if when you want claude code to make your app faster by mentioning the words optimize performance in your prompt. All of that's backed by a huge library of references for all kinds of issues. Once these skills are installed, you can give Claude Code a prompt to build the backend and connect the existing design to it so the whole app actually works.

[16:19] Now, because we didn't want the agent working on the structure of how the app's data is organized, we'd already pre-made it ourselves and added it into the project's main folder. Since the skill is just a bundle of instructions, it can't reach your project on Superbase's servers and make changes there. It's just text telling the agent what to do. So, it doesn't come with any tools that let it work on things outside the agent. That's why the Superbase skill asks you to connect either their CLI, which is the tool you run from the

[16:46] terminal, or the MCP. Either one gives the agent the tools to work on your Superbase project directly, so you're not doing anything manually. We went with the MCP because it's easier to set up and we wouldn't have to deal with the keys that let the CLI log into your account. To connect it, you head to their official site, copy the install command for the agent you're using, and run it in the terminal that's open in your project folder. After that, you run the /mcp command inside claude code and you'll see it connected, but you won't

[17:14] be able to use it right away because it's not logged in. So once you select authenticate, you'll be routed to the browser where you log in and then the MCP will be connected. Once the MCP is connected, Claude Code then loaded the skill and got to work on the app. It made a lot of changes and ran tests against those database requests to make sure it built the best version. It also made sure it locked down all the areas that shouldn't be accessible to every user. All thanks to those guidelines in the skill. And just like that, Superbase

[17:40] handles the entire backend for you all through the skill. So now the whole app is built and actually working. There's just one step left which is deployment.

[17:48] Basically putting it online so anyone out there can use it. But we're not going to get into that here. So keep an eye out for the upcoming videos where we go over it in detail. Now the skill we gave is actually a part of a bigger design system that we actually use internally in our projects. So if you want access to that system, you can get it inside AIABS Pro, which is our community. That's also where you'll find other skills and resources that we show you in our videos along with a place to interact with our team. So if you found

[18:15] value in what we do and want to support the channel, this is the best way to do it. The links in the description. That brings us to the end of this video. If you'd like to support the channel and help us keep making videos like this, you can do so by using the super thanks button below. As always, thank you for watching and I'll see you in the next one.

