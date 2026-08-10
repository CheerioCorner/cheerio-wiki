---
title: "Uncle Bob Stopped Reading AI-Generated Code"
type: raw-transcript
created: 2026-08-10
video_id: sClTAvkQDOU
url: https://www.youtube.com/watch?v=sClTAvkQDOU
duration: 00:08:21
language: en
auto_generated: true
source_api: yt-dlp
segments: 25
---

# Uncle Bob Stopped Reading AI-Generated Code

> Duration: 00:08:21 | Segments: 25 | Language: en | Source: yt-dlp (auto-generated)

## Transcript

[00:00] In this video, I want to talk about a In this video, I want to talk about a very hot topic in the tech space and that is a viral tweet from Uncle Bob, the writer of Clean Code and Clean Architecture, where he claims that he is no longer reading his AI-generated code.

[00:15] Now, I want to unpack what's behind this, where this makes sense, and I also want to share my approach to using agents and making sure that the code I produce works and also doing this efficiently. Here's the viral tweet from

[00:30] Uncle Bob, now racking up close to 3 million views, and I'm going to first just read through it. So, he says, "I'm significantly older than you." And this is actually a reply to the above comment, where Ori wrote, "I'm trying to use Cloud Code to help me write something, but I just don't feel comfortable letting it edit my files.

[00:46] Does anybody else feel the same? If I'm responsible for code, I need to understand it psychologically, if for no other reason. Started programming in 1983. Am I old?" So, to this, Uncle Bob replies, "I'm significantly older than you. I started coding in the late '60s.

[00:59] My current strategy, and this is actually what causes this post to go viral, is because he says, "His current strategy is to not read any of the code written by my agents. That's the only way I can take advantage of their productivity. What I do instead is to surround the agents with extreme constraints, unit tests, Gherkin tests, QA procedures, quality metrics, mutation testing, test coverage, and a plethora of others. In the end, I have very high confidence in the code that they produce because they've had to run the gauntlet

[01:25] of all of my constraints and tests. So, a lot of things to unpack here and discuss, and I know this is going to discuss, and I know this is going to hurt a bunch of feelings, but my opinion hurt a bunch of feelings, but my opinion is that this is where we are directionally going as software engineers, and for some developers, this isn't the future that they were hoping for. So, let's unpack this by writing out the core premise, which basically says that you should not be reading AI-generated code. The reason this ends

[01:49] up being so controversial is partly because Uncle Bob said it. A lot of folks have pretty strong feelings for or against his views for one or more reasons. Now, I'm not going to go into this. It's completely irrelevant for the discussion I want to have. And that is if this argument even makes sense to begin with. Now, there's another popular YouTuber called Fio. He produced an excellent video discussing this, and he basically ranks all code on this spectrum, where at the lower end we've

[02:18] got, let's say, a web app with one user, and on the high end we've got something like people die if the code is not working. And the argument is basically how much of the code should you be reading depending on where you are on the spectrum. So, if you are somewhere here, then you should definitely be here, then you should definitely be reading all of your code regardless of reading all of your code regardless of if it's human written or AI generated.

[02:37] Whereas, if you are somewhere here, then it probably doesn't even matter because if mistakes were made, the impacts are going to be pretty minimal. And by the way, I don't intend to cannibalize his entire video. I'm going to actually entire video. I'm going to actually leave a link to it in the description leave a link to it in the description below, and I encourage you to check it out because there are some very interesting points there. Now, where I would place my own code is somewhere on the lower end of the spectrum. I am

[02:57] running a SaaS right now called Catabenche, which is a learning platform for .NET developers. And although I consider the software mission-critical because people are paying money to use some parts of the platform, and I don't exactly have one user. I've actually got plenty of users, and the platform is growing daily. But still, nobody is actually going to get hurt if the code in the platform is AI-generated and God forbid not read before being pushed to production. Now, how do you actually do this? How do you produce tons of code

[03:27] using AI without producing slop? So, the first argument we're going to hear against this is something along the lines of all AI-generated code is slop.

[03:36] Now, the reason I don't agree with this is because that all of the popular AI models that we are using right now were trained on human code. Go figure. So, from this we could extrapolate that all human code is also slop, right? Because the AI doesn't really have an ability to produce new code. It only generates things it already saw and that were part of its training. So, I don't really agree with this, but we can definitely say that some human-written code is slop say that some human-written code is slop and that might also imply that some

[04:03] and that might also imply that some AI-generated code is slop. How much that matters is again going to depend which type of application you are building.

[04:12] Let's say that you are open-minded and that you actually want to explore this idea. How do you take AI-generated code, regardless of how it was produced, let's say cloud code or codex or any other agent that you could be using, how do you take this code and validate that it is correct without having to actually read it? You have to do some sort of verification and fundamentally these are things that we've always done to make sure our code works correctly, right?

[04:38] So, we've got things like manual QA. You open up the app, you test it and you validate that it works correctly. In fact, we've got a role called a QA engineer whose sole job is to validate that the code that you produce works correctly or in other words, the application works as was intended when requirements were defined. Then another thing you can do is write tests and you can write a lot of this. So, we've got unit tests, we've got integration tests, unit tests, we've got integration tests, we've got architecture tests, which are

[05:03] we've got architecture tests, which are really proving valuable with AI generating a lot of the code. It's now harder for the agents to break the architecture that you put in place because you can encode it behind a set of automated tests. And then on top of this, you can do a lot of interesting things. For example, you can have skills that tell the agent how to verify the code that it produces. You can build custom debuggers. You can add harnesses to ensure that whatever code you produce works correctly. You can write tools to

[05:31] help you validate the functionality of your application. You can do mutation testing. You can also use code coverage.

[05:37] Although it's not a metric for code quality or correctness, it still helps you measure how much of your application is actually covered by your tests and that gives you potential areas where you can focus your agents on to write more tests. Now, the really interesting thing is that, let's say you generate a given number of code measured in lines of code, however many this is. What you now have the ability to do with AI and agents is generate a lot of additional

[06:05] code that validates the application code. So, basically, you write more code to validate the application code that you generated, and this could be any combination of the things that I just outlined here. You can tell it to do manual QA. You can tell it to write more tests. You can generate skills that are going to verify that your code is working correctly. You can add harnesses around the entire process of producing code that can help you verify the code as it's being generated. When you think about it, this is essentially how

[06:32] software engineering as an industry has functioned for many years, except instead of using AI to generate code, we were using humans to generate the code, and then the verification process was more or less the same. There were QA engineers, we would write tests ourselves to verify the code. And after all of that is said and done, humans still produce slop. So, now the equation has shifted, and you as an engineer may no longer be responsible for writing, or let's say generating, the majority of the code in production code bases. And a

[07:00] lot of people have a problem with that because they've centered their identity around writing code, where the code itself is only an artifact that gets you from point A to point B. I'm not saying that code isn't important, but from a business perspective, code is only one artifact that produces value. It's the combination of things, and essentially, combination of things, and essentially, whatever is the functionality that a whatever is the functionality that a given business provides that produces

[07:23] the actual value. Code is there to facilitate the value creation, and you can still do the value creation part even without handwriting all of the code and using AI agents to produce the code.

[07:34] I'm going to wrap it up here, and I'd love to hear from you in the comments how you feel about all of this, and where you see our industry headed in the coming years. And I promise to make a separate video discussing more in-depth about what's actually still important if we assume that agents are going to generate the majority of the code going forward. And that begs the question, what our role is going to be as developers. And my overall opinion is that future is very bright if you are a software engineer, where I'm stressing

[08:00] the engineer part. If you want to see what my AI agent workflow looks like, I talked about it in-depth in this video here, which you can take a look next.

[08:08] And then see how you could apply that to your own code base, whether that being some side project that you're working on, or maybe even in your production code base. Nonetheless, if you enjoyed this video, go ahead and smash the like this video, go ahead and smash the like button. Thanks a lot for watching, and button. Thanks a lot for watching, and
