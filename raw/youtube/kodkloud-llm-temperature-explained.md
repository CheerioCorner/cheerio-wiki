---
title: "LLM Temperature Explained — KodeKloud"
type: raw-transcript
created: 2026-09-07
video_id: IWSutLJbURU
url: https://www.youtube.com/watch?v=IWSutLJbURU
duration: 00:05:05
language: en
auto_generated: true
source_api: youtube_transcript_api
segments: 23
timestamp_format: "[MM:SS]"
---

# LLM Temperature Explained — KodeKloud

> Duration: 00:05:05 | Segments: 23 | Language: en | Source: API (auto-generated)

## Transcript

[00:00] Hello, and welcome back. If you've been using the AI models in your agents or in your normal day-to-day, you might have heard this word called as temperature.

[00:10] What exactly is temperature? Well, in this whiteboarding session, I'm going to explain that for you.

[00:17] >> [music]

[00:21] >> The concept that we want to understand here is temperature.

[00:25] Of course, you might have heard about it. You might have also used it. But, what exactly is temperature? So, let's take the model itself. So, we have been using an LLM model. We We have an agent that is calling this model.

[00:41] And it gets an output. And this output can be displayed to users. Simple as this.

[00:48] So, the users give input here. So, we have an input, we have an agent, we have a model, we give an output. That's it.

[00:53] And what does this agent do? So, this agent is basically a flight scanner for us. And based on your input, it flies, it goes, it gives you an output to you find you the best flights. Now, the question is, where does temperature come in? The temperature parameter basically defines how creativity or randomness the model can have. So, two words, remember what I said. How randomness and how creative the model can be.

[01:25] That is the two parameters that temperature defines. Now, the temperature itself has a range. So, the range can be between zero to one. But, in some cases, it can go to 1.5 also.

[01:39] What does it mean? It means the creativity is low when you have it to zero. So, this is low.

[01:49] And the creativity is high when it is at one or 1.5, for example. Now, what happens when the creativity is low and randomness is low? It means that the model is going to be a very decisive. It is going to be very decisive. It is going to focus on the task and give it the output. It doesn't venture out to see what can it do better. So, every token generation, every next token generation here will have some kind of steadiness based on the temperature.

[02:18] Now, again, if you open hugging face and if you look into like top 10 models randomly, a standard here is 0.7, okay?

[02:26] But, of course, you get to tune it as I mentioned.

[02:29] We'll go back to the example of our flight scanner. So, the input would be like, "Hey, I would like to fly to Paris." Now, why would you need a high randomness here? Like, if you were to fly to Paris, let's say, from Dubai. So, you want to fly to Dubai to Paris, okay?

[02:46] If this is your journey, why would you need a high randomness? Like, would you like to go from Dubai to Sydney and fly back to Paris? No, right? So, the randomness or creativity can be very less here.

[02:58] So, in these cases, let's say, the randomness can be 0.3, for example, or even 0.1, doesn't matter.

[03:06] But, let's take the other example. Let's say you say that to create an image for a trip that you're taking, okay? Or it needs to generate an image. For that, the creativity has to be on the higher side. So, it has to be 0.9 or even one.

[03:26] Because every token generation that it does will need to be smarter, will need to have some creativity or randomness to it, so it can generate a nice image for you. So, I hope you are able to understand how and when to tune this temperature parameter for your agents.

[03:44] Going back to this example of the flight scanner, it is pretty much falling in this area, correct? So, when you get these inputs, you basically set your temperature to 0.3.

[03:54] But, let's say let's say for some reason you also allow them to create image for the trip, then you set a better temperature over here. So, temperature itself as a parameter has very big value in your agent and agent flow. And I've seen in companies people don't really think a lot about it. They don't even do experiments with it. Like they try to set this to 0.5 randomly and just leave it. Well, why it is 0.5, you need to question it. Why is it not 0.3? You need to experiment all of this in your agent

[04:27] flow to make sure your agent is always proper.

[04:31] When people say to me that because of agents the job market is going to be low or high, it doesn't matter. For me, what is very interesting is with these kind of technologies and parameters, it makes engineers to spend more time understanding the depth of it. Every change you make makes things much better for your agent flow completely. Well, that is what temperature is. And to check out more of these concepts regarding AI and ML, do look into the course catalog in codeclod.com. Thank you.

[05:03] >> [music]
