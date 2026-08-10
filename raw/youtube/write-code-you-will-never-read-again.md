---
title: "Write Code You Will Never Read Again"
type: raw-transcript
created: 2026-08-10
video_id: 434cG4g5KLE
url: https://youtu.be/434cG4g5KLE
duration: 00:24:13
language: en
auto_generated: true
source_api: yt-dlp
segments: 741
---

# Write Code You Will Never Read Again

> Duration: 00:24:13 | Segments: 741 | Language: en | Source: yt-dlp (auto-generated)

## Transcript

[00:00] Every once in a while when I decide I Every once in a while when I decide I want to film a video, I realize that there's almost no benefit to me because the video is going to frustrate both sides of the argument. This is definitely one of those cases because I want to talk about how much of your code you should be reading. I'm so scared to you should be reading. I'm so scared to say this next part that I'm just going say this next part that I'm just going to put it on the screen instead. I think this is the case for the vast vast

[00:22] majority of engineers. Whether you're building slop side projects or you're building really important infrastructure that powers the world or medical devices that keep people alive, I think that the percentage of code you're reading is

[00:35] probably too high. It could also be too low, but for the most part, I think a lot of people, especially those who think their code is so so important, are reading way too much. But there's another side here that I think's even more important. You're not generating enough code yet. In many ways, that's actually the more important piece and I think you'll understand after we talk about this for a bit. This video concept came from a tweet I made a few days ago.

[00:59] How much better do the models have to get before you'll stop reading the code?

[01:02] And the answer to this should not be as direct as many people seem to think.

[01:06] This is going to be quite a video and if I do it right, you should be able to come out of it with a better idea of how to get as much value as possible out of AI-generated code. But first, a quick break for today's sponsor. Setting up off for your users is pretty easy as long as your users aren't agents or enterprises. Then it gets hairy fast.

[01:22] Thankfully, we have WorkOS to smooth out those rough edges. They really understand what enterprises need for off, but that doesn't mean they're compromising on the developer experience. They still have all of the things you would expect from a modern off service through off kit, their package system for getting the UI and connections necessary to set up off across every different framework you would possibly want to use. Your agents will be able to figure it out just fine.

[01:40] Their enterprise offerings are where they've historically shined because they have everything a business needs up for your service and businesses have a lot of very specific needs, including the admin portal which makes it trivial for you to send a link to the IT team at the company you're trying to get to sign up for your service so they can onboard themselves. If you want a company's agents to be able to register for your service, The new standard Auth MD, which has been pioneered by WorkOS, is

[02:01] probably your best bet. It's the first standard I've seen that actually makes sense for agents and will make it possible for your users to sign up without actually having to do it themselves. This is why companies like Cloudflare, Firecall, Resend, Monday, and more have already adopted this new standard. Get your app ready for users, standard. Get your app ready for users, agents, and businesses at agents, and businesses at swade.link/workos.

[02:18] Now that my first statement has absolutely destroyed the comment section, let's start talking about what's actually up here. Going to start with my favorite aspect This is meant to be the importance of code spectrum.

[02:32] And on one side here we have like slop website with one viewer. And on the other side we have, I don't know, I'll say firmware for pacemaker. How about that? No one can argue that the firmware on your pacemaker, the thing that keeps your heart beating, isn't really important and that a mistake on that would be really bad because it would literally kill people. All software kind of fits somewhere on this spectrum and I don't want to pretend otherwise. I feel like the narrative around AI code and whether or not you should read it and

[03:00] verify and all of this is kind of plagued by this spectrum and people thinking it matters more than it does because everybody is somewhere on here and if you want to be real, the code I write is not particularly far down it.

[03:12] Like I likely am in this range depending on how you want to define it, if it's exponential or not, whatever. I don't care. I will gladly say Theo's code is nowhere near as important as the firmware on a pacemaker. What I've noticed is that a lot of people seem to think they're further along the line than they are but more importantly, they think really, really negatively about everything below them on this line. So whenever somebody makes a statement about AI generated code, the developer who hears it will think about where they

[03:38] who hears it will think about where they are on the spectrum and if they like the are on the spectrum and if they like the statement, they'll assume that the statement, they'll assume that the person who's talking is where they are person who's talking is where they are or better. And if they don't like the statement, they'll assume the person is where they are or below, probably below.

[03:50] This makes the conversation here nearly impossible because everybody is thinking too highly of themselves and more importantly thinks that the code they

[03:59] write matters way more than it does. I could sit here and argue all day with the people who are arguing against me because they think their code is so important. And what would I know? I generate wrappers for LLMs. Doesn't matter that I built video infrastructure for years before that. But that aside, I don't want to have these arguments. I'm going to do a thing that I don't need to do. I'm going to presume the important code people are correct. I don't have to do this. I think these people are [&nbsp;__&nbsp;] full of themselves and most of

[04:26] them are stupid, but I don't want to argue that because that's not the point I want to make. We are presuming the people who are saying every line of code is important are being real. So let's dig into that statement. Every line of code is important in my work. We'll assume this is true. Every line of code that is being written, being put into a PR, being committed, being compiled, being shipped is so important that people could literally die if you get it wrong. I'll even go that far.

[04:55] If one line is wrong, people could die.

[04:58] In this case, I agree. If this is the case for the projects you're working on, you absolutely should be reading every line of code that gets shipped. But here's the other harsh reality I want you to consider. Code is useful for things other than shipping. If your code is so important that you need to be sure every single line is correct, maybe you could use more code that is less important to verify the important code.

[05:24] There is no person alive where 100% of the code they write is mission critical because if it is, they're not good at their job because you need to work on other things to hone your skills, to keep learning, to hone your skills, to keep learning, to keep growing. But also, you can write keep growing. But also, you can write code that is a little less important to verify the code that is so important.

[05:45] Let's talk about how things used to be.

[05:46] I'm just going to make up numbers for this, but the percentages are going to line up with what my experience was like in the past. Let's say in a given day back when I worked at Twitch, I would back when I worked at Twitch, I would read, I don't know, let's say a thousand read, I don't know, let's say a thousand read, I don't know, let's say a thousand lines of code a day. This is a thousand lines of code a day. This is a thousand lines of code I would read. Let's say I would write, I don't know, let's say I

[06:05] would write 200 or so. So I would write 200 lines of code and then through reading that, referencing other code, doing code reviews and everything else, I would read a thousand lines and I would write 200. This made a lot of sense in the world, but writing code was expensive and all the code we merged was important. So we had to take the time to read a ton of code others were writing cuz every time somebody wrote a line of code in hopes of getting it into prod, at least two people should read it before we give it a thumbs up and merge

[06:31] it or everything's going to slowly fall apart. This has changed. Whether or not we want to admit it, this has changed.

[06:39] If it hasn't changed for you yet, you are not very good at your job right now.

[06:42] are not very good at your job right now. Period. Hear me out. Let's add one more Period. Hear me out. Let's add one more metric here. We'll say that I read a thousand lines, I wrote 200, and let's say out of that 200, only 100 of them were good enough to merge. This ratio of two to one for my written code is not too bad. 200 lines written for every 100 worth merging, acceptable-ish.

[07:06] Let's presume you are similar to me and you're working on projects that are somewhat important, but like if they break, you can just revert and it's fine. This has changed a lot in that case. Nowadays, I find myself reading maybe roughly the same amount of code, but I have to move this to the side now but I have to move this to the side now because it's more complex. I might read because it's more complex. I might read a thousand lines of code a day, but I'm generating 2000 plus. So I might be reading a good bit, but I'm generating

[07:35] way more. And what I merge has increased. It's increased a meaningful amount. We'll say it's 500 lines of code being merged a day. We're kind of in danger because there's a lot of code here that is being generated that is not being read, and that's that is not being read, and that's scary.

[07:50] scary.

[07:51] Except for the fact that most of this code that is being generated is not being put up for code review. It's not being merged. It's not being used for much of anything other than testing ideas. This did not used to make sense because back in my day when I learned how to code by hand, every line of code took time. So, writing a shitload of code to do one quick thing never made sense. If you wanted to get an answer to sense. If you wanted to get an answer to a question about your users, a question about your users,

[08:17] you might have to write 100 lines of code that you would stuff into the product to do new analytics. And then you have to write another 20 or 30 lines of SQL in order to get the answer later on.

[08:26] That was obnoxious, but if the question was important enough, you could do it. The reality is that code is way cheaper now.

[08:34] You should be generating more code than you were before because code is so goddamn cheap. And now we get into the stupid pushback that a lot of people have. If you're able to slop this amount of code, your product isn't that important, and that's okay. I'm sorry, important, and that's okay. I'm sorry, layer. You are incredibly wrong here.

[08:50] layer. You are incredibly wrong here.

[08:52] Nev asked, "I write code for a financial ERP system. Mistakes in this code could be converted to huge losses for my companies who use this ERP system. Is my code important?" Probably, but you should be writing way more code.

[09:07] Hear me out. If your code is so goddamn important that every single line needs to be verified because it could bankrupt businesses, it could get people killed, it could stop people's hearts, it could drive cars into walls, if your code is drive cars into walls, if your code is that important, you should be writing an that important, you should be writing an unbelievable amount of slop. Not to put in your product, but to verify your product. Every line of code that goes in should have 100 lines of slop verifying

[09:34] it. Every line that goes in should have 10,000 lines of code of slop that you can use to verify the system. You should be building custom debuggers. You should be building custom runtimes that you can run your stuff in to verify them. You should be building the tools to should be building the tools to guarantee the thing you care so much guarantee the thing you care so much about keeps working. You cannot convince me that your code is so important that AI can't touch it, but it's not important enough to build verification

[10:00] systems. Oh, but our verification systems are so important. Those need to have every line of code checked, too.

[10:06] Okay, abstract one layer higher then. If the core of your code is too important for AI to touch and the layer around it is too important for AI to touch, then build one more on top of that. Build tools that introspect all of the runs tools that introspect all of the runs that all of your testing tools are that all of your testing tools are using. If you don't have a custom debugger for your software yet, you're not slopping hard enough. If you don't have custom logging systems tracking all the things that matter on top of the

[10:29] vetted ones you already wrote by hand, you're not slopping hard enough. If your code is so goddamn important that you talk [&nbsp;__&nbsp;] on people for only reading 10 to 20% of the code they generate in a given day, you're not doing your job well.

[10:42] Period.

[10:44] And I know this because I know a lot of the people working on these types of systems. Imagine how many lives will be ruined by doing this. Please elaborate, Kotek. I'm listening.

[10:54] How does building sloppy debugging tools to use on top of the existing processes you already have hurt anyone? Please explain. Shoutout says 80% of the code he generates goes into test harnesses and guardrails and the LM has access to the results of the harnesses, which results in way less mistakes. That's a great way to do it.

[11:10] And if you write your code by hand or you review that code by hand, even if you review the test by hand, awesome.

[11:15] I'm cool with all of that. My argument isn't that you shouldn't read all the isn't that you shouldn't read all the code you write. Let's Let's flip this a code you write. Let's Let's flip this a bit to be a little clearer about what I mean here. Let's say you're one of the people who writes this really important code and we'll say that you write, review, and verify 100 LLC a day. This code is super, super important and this is all you wrote. You wrote the 100 lines, you verified the 100 lines, these

[11:34] 100 lines can get people killed, they're really, really important. So, you didn't write any additional code, you didn't read or review any additional code. This was your whole day. I am not saying that you should reduce the number of lines of code you read. If you write 100 lines of code and you also read, let's say you read a bit more. Let's say previously you would write 100 lines and you would read 200 lines. 100 lines of code written and verified and 200 lines of code were read. If this is the case for

[11:59] code were read. If this is the case for you, this should not change. What I you, this should not change. What I would say is today you should still write and hand verify 100 lines of code.

[12:09] Like, don't change that. This is code is really important. Keep doing that. Maybe it even goes down a little bit. Maybe you only have time in the day to do 80 lines of code written and verified now.

[12:18] It's unfortunate, but I know that's the case for a lot of people. Let's say you're Sadly, you can only write 80% of the code you wrote before, but now you review 400 lines of code instead, but you generate and this is the difference. This is the thing I really difference. This is the thing I really need you guys to understand.

[12:31] need you guys to understand.

[12:33] You now generate

[12:36] 800 lines of code. And this 800 lines of code is absolute [&nbsp;__&nbsp;] slop and you don't touch this in your actual product.

[12:44] This 800 lines of code sits in another repo or at the very least some other [&nbsp;__&nbsp;] up directory in your important repos. And all it does is explores. It verifies assumptions. It tests those 80

[12:57] lines before in all of the crazy ways that were never worth it before because writing a thousand lines of code to make sure one line of code works how it's expected to never made sense before. I am not telling people that they need to change how they verify the code that goes into prod. I'm not saying you need to merge slop into your projects. I am saying that if you are of the belief that your code is so god damn important that your code is so god damn important that every line needs to be read or

[13:20] that every line needs to be read or people will die, then you're not writing enough code yet. You're just not generating enough. That's all I am generating enough. That's all I am saying. If you put a little time into saying. If you put a little time into realizing that code is useful for things other than merging, it's useful for things other than updating your product, code can do so much [&nbsp;__&nbsp;] I have like 10,000 lines of JavaScript I wrote on my Windows computer just for organizing files. That never made sense before.

[13:46] Writing 10,000 lines of code for a couple bespoke asset movements. Like I think I maybe moved like 100 assets with it. 10,000 lines of code to organize 100 files is mental illness until the code is free to generate. Then all of a sudden it's totally reasonable. And this is the thing people haven't internalized yet. They are correct that AI code is cheap. They are correct that AI code is bad. They are correct that AI code could get people killed if it's not verified properly. They are incorrect that code

[14:14] is expensive still. And if you can't find ways to use this cheap infinite code generating system to verify your existing systems, you are not a very creative engineer. The fact that code

[14:26] can be generated on a whim for any theory, for any question, for any problem you want to verify, for any step you want to debug, for anything, is a magical thing. And it absolutely sucks

[14:39] that we don't get to experience this as end users because most people are letting AI code just generate slop and they're too lazy to check it. And I agree that's a problem. And I am with you that should be stopped. Important systems need more care and craft with the code going into them. But now we have a tool we can use to verify it harder, to check it more. I never used to write custom lint rules. I wrote maybe one or two in my life. Now I generate custom lint rules all the time.

[15:08] I didn't used to make custom debug tools. I would rely on the ones that existed in the browser. Now I will build my own one-off debuggers and systems and compiler hooks that add a bunch of stuff into my react code to try and see how it performs better. When I was testing how much load my systems could handle, I would have to hit up friends and like put together these complex tests. Now I just tell Codex, "Hey, you have access to AWS. Go spin up a bunch of [&nbsp;__&nbsp;] and stress test the system so I can get a

[15:32] better idea of how much traffic I can handle." The power of unlimited code is hard [snorts] to fathom. And if you're still thinking of code in that small box that is so important, I get why you're struggling to make this jump. But you

[15:48] need to realize important code is only part of your day-to-day. And if 90% of the code that you're touching in a given day is that important, you're just not touching enough code yet. And when your life had to be that way because every line of code was so important people could die, that got so ingrained in your head, as it should, because if it doesn't people die. That era is over now.

[16:09] I'm not saying move faster. I'm not saying merge slop. All I am saying is that everybody can benefit some amount from the infinite code generator that we now all have access to on bedrock, on

[16:22] it, Codex, on Claude code, on whatever.

[16:24] I'm not telling you to make your code cheaper.

[16:27] I'm telling you to make more cheap code.

[16:28] And that's a huge difference. And there are a ton of creative ways you can do this. If you're still reading all of the code, you're not generating enough of code, you're not generating enough of it. I'm not saying you should verify it. I'm not saying you should verify less code than you used to. I'm saying you should write more code than you used to. And you should be writing so much code that a lot of it isn't worth human attention. Reading code takes energy and effort and time, and it should. We

[16:49] shouldn't try to find ways to read code faster. We shouldn't try to find ways to merge unsafe things quicker. We should be finding ways to make the code we care the most about better, faster. Let's think about code in a funnel, top to bottom, where the majority of code is at bottom, where the majority of code is at the top and a very small percentage is the top and a very small percentage is at the bottom. This is meant to be a funnel of importance. There will always be more code that is garbage than code

[17:11] that is super important, and this gap is going to widen rapidly over time. The top here I will clearly label as such, I would rather die than have anyone read this code. It's code that doesn't matter. It's code like the 10,000 lines I wrote to organize assets on my computer. I accidentally read 10 lines of it once. It's such slop that I'm offended that I did. No one should have to read the slop. Nobody should have to read the [&nbsp;__&nbsp;] people are generating on lovable for their like pet stores.

[17:36] And I agree there. And if you don't perceive a difference between that and the code inside of a pacemaker, you are making my job harder here because I'm trying to be realistic here. But there's stops between and these stops are valuable. It's I'll frame these as I'd like for this to work in the middle tier and I might and I'll get fired if it doesn't work for the tier below. I think this roughly represents the tiers I think of code in. At the very top, there's slop. At the very bottom, there's slop. At the very bottom, there's death. And in between is where

[18:05] there's death. And in between is where most of us actually live. The vast majority of people watching, their code falls between this range. Where the code's important enough that like you'd code's important enough that like you'd be upset if it didn't work or the code's be upset if it didn't work or the code's important enough that bad business things will happen, you'll get fired, etc. We need to think about these types of code differently. And I would also argue, and this is probably the most important part, nobody spends all of

[18:29] their time in just one of these sections. Okay. Slop coders that just use stuff like lovable spend most of their time here and probably don't go very far down. But people who write a lot of code at the very least hop between one through three. Some touch on this fourth layer, the really scary stuff. But all of us should be writing stuff. But all of us should be writing code in all of these ranges. The thing code in all of these ranges. The thing that changed, if we were to label these as tiers A, B, C, and D, the thing that

[18:59] changed is that tier D code was so expensive that spending any time in the other tiers just didn't seem valuable because it's just as much work to write bad code as it is to write good code if you're writing it by hand and you you how to write good code because it's hard to turn off your brain and all the demons that are like, you have to make demons that are like, you have to make sure every line is safe. But, sure every line is safe. But, realistically, we should all be doing more in these other ranges. Let's say

[19:23] you're one of these engineers working on pacemakers, and in the average day, the pacemakers, and in the average day, the vast majority of the code you wrote was vast majority of the code you wrote was that D tier. It was the death tier.

[19:32] People could die otherwise. I'm not saying write less.

[19:36] I am simply saying you should draw a line between that code and everything else. And you should be writing a whole lot more of the everything else. But, let's take a look at stuff that I do.

[19:48] Let's take a look at how I think of T3 code changes, or how I think of Lakebed changes. Some of that code is more important than other parts. Dax touches on this a little bit in his post here.

[19:59] Lately, after a big diff change, instead of reading the diff, I asked the agent for a summary of what it did in every for a summary of what it did in every file. Anything weird will stick out file. Anything weird will stick out immediately, and one to two prompts later, it's completely how he wants it.

[20:08] Files and function signatures he needs to know, but he cares less about the to know, but he cares less about the function body. Very much agree here. The function body. Very much agree here. The way I would think of this is the engineers who wrote mostly tier C and B code should find the right ways to get the pieces that matter out of the parts that are in this tier, so they can ignore more of the parts here. And figuring out figuring out where to cut these layers is important.

[20:31] where to cut these layers is important.

[20:33] I read all of the function signatures and all of the API definitions in Lakebed, every single one. It is very important to me that the API and SDK are super solid. Because if they're not, it makes maintaining the project over time way harder, it makes future updates way harder, it massively increases the risk of future releases. So, I want to make

[20:55] sure those layers are as solid as possible. And part of how I do that is writing a shitload of slop to test it.

[21:01] One of my favorite things to do when I define a new API or SDK is to put it in a package that works on my machine, and then go spin up 10 agents with much dumber models to build things on top of it. I absolutely love using Grok models, not for actually writing code that matters, but for writing absolute slop, and to see if they, despite being dumb, are still capable of using these APIs.

[21:24] If they are, awesome. I ship it. If they aren't, interesting. I fix it. So, to go back to the pacemaker people, let's say back to the pacemaker people, let's say you are mostly writing code here in tier you are mostly writing code here in tier D, but you also have a bunch of tests D, but you also have a bunch of tests and harnesses and wrappers and things and harnesses and wrappers and things verifying your work in tier C. I would agree, those things are really important. If you have a test suite that

[21:45] you've slaved over to make sure your super important code continues to work as expected, awesome. I salute you. You should still be reading a lot of that, and you should also be building systems to verify it, too. But maybe, just maybe, you could write more code up here. If you've built all these systems and you have all these tests, and then somebody puts up a PR that you're really not sure about, maybe have an agent do some slop to try out three different theories and test all of them. Maybe have the agent write a new set of tests

[22:13] that don't exist yet for your one-off theory. Maybe you're curious if the code will be faster in Rust than it is in Go, so you have an agent slop port the whole thing over and then run the test suite against it. You know it's not all going to pass. You know it's not all going to merge, but it's going to answer important questions you might have. If you're not finding these opportunities, you're not finding these opportunities, then you're not curious enough. You're then you're not curious enough. You're

[22:31] not creative enough. You're not taking advantage of this once-in-a-lifetime opportunity we have here, the ability to generate infinite code. It's crazy how

[22:42] cool it can be. And I understand why you might feel differently. It's cuz the people in this top A tier are the most obnoxious people on Earth. The people who are shipping endless [&nbsp;__&nbsp;] code for services nobody [&nbsp;__&nbsp;] uses. They are the ones who are the loudest on are the ones who are the loudest on social media, and they are the ones that social media, and they are the ones that represent this new slop AI vibe coding movement. I hate them, too.

[23:05] I need you to understand that we are on the same side even if I rage bait on Twitter a little bit. That doesn't mean there isn't value here. There's also the fact that you should probably have AI reading your code too. That's a whole separate rant I don't want to bog this down with but in generally speaking I down with but in generally speaking I don't bother my team with code until don't bother my team with code until multiple agents have verified it deeply.

[23:22] Use AI to review your code. It's actually really cool for that. I think this is the diagram that best summarizes my thoughts here on this type of important code. I will be clear, there are lots of other types of code where this level of hand review is not that important and you're probably merging a important and you're probably merging a lot more too. But if your code is so lot more too. But if your code is so important that you need this pipeline, please be realistic. Most importantly, I want you to be more creative. Find more

[23:46] ways to use code to verify the things ways to use code to verify the things that matter. If your code's important, that matter. If your code's important, you should write code that isn't to verify the code that is. You'd be amazed what you can come up with when you start treating code as more disposable, as throwaway for one idea, one theory, one question, one thing. Be creative. And you'll make awesome things happen. I've said all I have to on this one and it seems like I pissed everyone off which

[24:07] means mission accomplished. Go be more creative, write more slop, and until next time,
