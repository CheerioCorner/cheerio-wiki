---
title: "Memory Harnesses for Long-Running Research Agents"
type: raw-transcript
created: 2026-08-20
video_id: R3-anFK1YM8
url: https://www.youtube.com/watch?v=R3-anFK1YM8
duration: 00:12:45
language: English (auto-generated)
auto_generated: true
source_api: youtube_transcript_api
segments: 310
timestamp_format: "[MM:SS]"
---

# Memory Harnesses for Long-Running Research Agents

> Duration: 00:12:45 | Segments: 310 | Language: English (auto-generated) | Source: API (auto-generated)

## Transcript

[00:01] [music]
[00:12] >> Hello. Welcome. Uh this is a big room, so you're if you're in the back, don't hesitate to come closer. Um My name is Stefania Druga. I'm a research scientist at Sakana AI in Tokyo. Uh I used to be based here and AI engineering uh is home community for me before being the hyperloop. So, it's very good to be back.
[00:34] And today I'm going to talk to you about memory harnesses for long-running research agents on device.
[00:42] So, if you work with long horizon tasks, you probably run into this issue of context blow. Right? Like when the model starts contradicting itself, or it has to redo the work because it forgot it did that task in the first place, or it starts to drift from your questions because it forgot them. And this this matters now more than ever because from this recent projections from Meter, we see that the trend is to solve longer and longer uh horizon tasks, and also that we're getting fewer and fewer model
[01:18] releases. So, at some point later this year, we're going to have this convergence, right? Where we'll get many more long-term horizon tasks and fewer model releases.
[01:29] So, that makes this issue of dealing with context rot a priority.
[01:36] And why did I wanted to to tackle this problem on local models and with a local harness?
[01:42] Uh maybe some of you have seen this tweet. It's only 2 days old. Uh the CEO of Coinbase actually shared how their company managed to reduce their AI spent while actually increasing uh the AI usage. And the way they did that was by transitioning to use many more local models, but also having better practices, like using better routing, better caching, keeping the context clean, and then having better visibility for what people are using it for what.
[02:15] Uh what kind of task.
[02:17] So, we are seeing the local models like crossing the line, right? Like GLM is on everyone's minds, like especially with Fable going away. Uh Deep Seek V4 Flash can now be run on uh M3 Ultra.
[02:33] And there's still a bottleneck for RAM.
[02:35] It's tricky.
[02:37] But these local models are starting to be useful for agentic tasks and for tool use.
[02:43] So, I wanted to show you what has been my setup for the experiments I'm going to share with you today.
[02:48] Uh This This is my Mac. It's still running evaluations right now uh back in my desk in Tokyo, and I'm controlling it from my phone. Um and after running evals non-stop for a couple of days, it started to get hot. So, I had my husband put fans around it. Um we're running out of fans, but the the machine is still running and the evals are still giving results. Um on this M3 Ultra with 96 GB and 28 core CPUs, I'm using two models.
[03:21] I'm using the Qwen 27B quantized at 4-bit and the Deep Seek V4 Flash.
[03:29] And before I show you how I build the memory harness on this machine, I wanted to tell you what this little What is this an example of, right? Like memory, when we design a harness for memory, this is the mental model I want you to have in mind. Um you can think of memory as a write-manage-read loop.
[03:48] So, it's not just a database store. It's actually this control loop around the model.
[03:55] More concretely, how did I take that loop and customize it? So, this is my harness design. Like, I started with research agents that are the small agents because they have zero durable memory, and I wanted all the memory to come from the harness.
[04:08] And then, um in the middle, I have a core, which is always shown to to the agent um of traces. And then, I have a recall block where I'm testing different modes.
[04:21] And then, an archival block where I'm kee- keeping track of information across different um sessions.
[04:28] And in that recall block, I'm actually going through a ladder of modes that I'm testing.
[04:34] The baseline is like not to use memory at all, no recall at all. So, I'm I'm testing for that.
[04:40] Uh next is to use a rag vector, vector rag, um just to see whatever like the harness would pull in terms of similarity.
[04:50] Then is to use a decisions uh ledger where I actually keep track of what decisions are being made for every turn, and then I can prioritize them.
[05:00] And last but not least, and this piece is very important, I have uh what I call an oracle, but basically this is the ground truth. So, this is like telling the harness for every loop what the correct memory that needs to be retrieved is.
[05:18] And the model is fixed across all the different tasks. So, the only things that I'm changing is like these different variables in the recall block.
[05:27] And I wanted to to give you an example of a first task that I tested. So, I wanted to see if I give the agent a task of doing literature review, and I'm including a lot of papers in the corpus where there was a big scientific claim. Like, this is actually a nature paper where they said they discovered 742,000 promising materials. Like, it was a very big claim, which got retracted later.
[05:57] But, the retraction to it's a much smaller like haystack needle in that corpus than the headlines and the citations.
[06:05] So, I wanted to see if if the system can retrieve the right answer for these type of questions.
[06:14] And what I found was because like for these tasks, all the papers and all the information fit into the context, the memory actually didn't add more capability. It was the same performance with memory and without memory, and it only added more cost. So, when your task fits in context, the harness doesn't add much.
[06:39] However, if I start to run tasks that are longer term horizon, and the entire task and the relevant context doesn't uh fit, then having a good memory harness really starts to pay off.
[06:55] So, this is another example of a task that I ran. This is actually from an established benchmark for long horizon uh tasks memory. It's called X-Bench.
[07:04] And this is an example of a question, right? So, I'm asking a question, and then like the right answer is in a like step 124, but the moment when I ask the question, I'm asking it like at step 500. So, it's completely outside of the context window, and the model needs to use the memory harness to retrieve the specific answer from the right step.
[07:33] So, I'm testing this by uh changing the different policy ladder that I explained before with memory off uh by deploying recall different types of recall and by using the Oracle as a reference.
[07:49] And what I found was that with the ranked recall the model gets the right answer more frequently than without.
[07:59] And here is a breakdown of the decomposition of performance on this X bench tasks.
[08:05] So I ran over 68 questions and for each of these questions there were like multiple cells and lots of different seeds.
[08:17] And what I found was that the rank only ledger performed the best.
[08:24] And it performed better than like just gating the harness by saying do you need to use memory or do you not need to use memory.
[08:32] And you're probably going to ask like why is the Oracle not hitting like the max? And I'm going to explain that too.
[08:39] So the Oracle what it does, it provides the right information, the right memory to the model but it doesn't force it to use it. So the model can get the right memory but still retrieve the wrong information or choose to ignore it or be confused. So that's why the Oracle in this case doesn't hit the max performance.
[08:59] And I've done lots of ablations on these tasks to see like what happens if I give arbitrary um examples. What happens if I give it the wrong step? What happens if I give it the most recent step? And I still found that the best performing condition was the one with the ranked policy for recall.
[09:22] And this actually works on several models, not only on the Qwen 27B but also on the DS4 flash and it also works across different benchmarks. I also tried it on the Spider V2 benchmark.
[09:36] And it's not just that it gives you better recall, it actually costs less.
[09:41] So, maybe a good heuristic to have here is that bad memory is expensive because it spends more token and it can send the agent the wrong way. But having like a good structural policy for recall can save you a lot of tokens and uh budget.
[10:02] So, one thing that I want to encourage you from this experiment is to consider the recall policy as a first-class metric and to start to think about how you might use it in your systems. Like, what are the type of memories that you want to store?
[10:20] What How do you rank them? Like, how do you design your recall function?
[10:25] And then um what are the type What survives when you run this over and over and over and um multiple sessions, multiple runs?
[10:35] And this is just a simple first kind of experiment but the memory technique landscape is very rich. Um so, there's over 30 runnable cookbooks that are shared in this open-source repository from um Diamond and memory is complex. We have short-term, long-term, different cognitive tech- techniques. Uh we can use start to use evaluation results as well.
[11:02] Um and right now, there's actually a a pretty broad landscape of solutions, right? So, going from simple file system retrieval to training memory models um there's there's a wide spectrum of solutions from less structural to completely structured. Um so, I think there's a lot of research we're going to see in this space. Uh it's important um it becomes more and more relevant. And for me, it's been super fun to to test this on local models um because I got to control everything.
[11:38] I got to control the data I was using, the entire traces of compute and evaluations, and um yeah, I I see that as an example of sovereignty.
[11:49] And it comes at a cost. Uh I didn't tell you that these local models I can only what run them in serial, like they don't support batch querying for the deep seed 4 flash. So, that's why I am still running evaluations back on my computer in Tokyo or I I was doing it on the flight on my way here because it takes a long time. Um but I still think it's very powerful, and it's a very good test for what memory can do when you can control every single step of the pipeline.
[12:20] And this sovereign capability is part of a bigger ecosystem that is very important for us at Sakana AI in Japan.
[12:27] Um we believe in the importance of sovereign AI today more than ever. And we are also hiring. So, if you're interested and want to hear more about this, and if you want to come join us in Japan, come talk to me.
[12:40] Uh thank you very much.
[12:43] >> [applause]