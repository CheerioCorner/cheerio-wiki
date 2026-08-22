---
title: "AI Agent 長期記憶架構 — 以 Mem0 為例"
type: raw-transcript
created: 2026-08-22
video_id: aYfZN8t6AQs
url: https://www.youtube.com/watch?v=aYfZN8t6AQs
duration: 28:32
language: en
auto_generated: false
source_api: youtube_transcript_api
segments: 246
timestamp_format: "[MM:SS]"
---

# AI Agent 長期記憶架構 — 以 Mem0 為例

> Duration: 28:32 | Segments: 246 | Language: en | Source: API

## Transcript

[00:00] Good morning everyone.
[00:02] If you want your agent to remember things across conversations about yourself, about itself and what it learns about the world, you're going to need an agent memory system.
[00:12] And today that's what we're going to be explaining.
[00:14] First of all, we're going to see what agent memory systems are.
[00:17] And then we're going to be taking a deep dive into one existing very popular memory system called Mem0.
[00:24] We're going to explore what stores it uses to create its full long-term memory.
[00:29] We're going to explore the workflows for ingestion and retrieval of the memories that it creates.
[00:34] And we're going to take a look at what it does when it just wants to delete one memory.
[00:38] And finally, I'm going to explain to you how you can build this yourself if you want to build it your own, and how you can run the whole thing with your own local models and some recommendations of local models that will be useful on every step of the whole thing.
[00:52] So without any further ado, let's get right into it.
[00:56] All right, so the first thing to talk about is what actually is long-form or long-term memory for agents.
[01:03] And something to keep in mind is that this is not the same thing as conversational memory.
[01:09] So remember that LLMs are stateless machines.
[01:13] In other words, when you send a prompt to your LLM, your LLM is going to process it and give you a completion.
[01:19] If you send another prompt to it, after that, it will not remember anything about the previous prompt or the previous completion.
[01:27] So if you want your agent or your assistant to be able to remember what it said before, you're going to have to send over the entire history of the conversation with your prompt so that it knows what it talked about before.
[01:41] And that is essentially conversational memory.
[01:44] This is what happens actually when you create an agent.
[01:47] So an agent scaffold or an agent harness will handle that context for you.
[01:52] In other words, whenever you send a message to your agent, your agent will process it with an LLM, then we'll get the response back from the LLM and will append the response, both your message and the response to the history that it keeps in its state.
[02:08] And then, whenever there is a new message, it will add the new message right here and send the whole thing back again to the LLM.
[02:16] That's the way it will remember things about its conversation.
[02:19] And this way, when you mention something right here, the agent will remember it right here in the conversation.
[02:26] But this is conversational memory.
[02:29] If you only have this, and you, for example, you start a new conversation and you send the new message, your agent will not remember anything from this other conversation because it is in another context.
[02:41] So, what we do when we want to create long-form memory is we create another service right here, which is going to be the long-term memory.
[02:49] And this one right here is going to store things from every single conversation you have.
[02:54] And on every turn of your agent, it is going to check its long-term memory to be able to respond with facts that it knows about you and about itself, which will be stored in the long-form memory, which will be independent from the conversational memory.
[03:10] Okay, and what we're going to be doing today is we're going to be taking a look at how this long-form memory works in Mem0, which is one very popular example of long-term memory implementations.
[03:22] And we're going to take a look at the entire architecture and how it works.
[03:26] So, let's get right into that.
[03:28] And before I forget, just remember that for long-form memory to work, you're going to have to make it external to your conversation.
[03:35] So, it should not be within the same file or the same store as the session itself.
[03:41] It should be persistent across sessions, in other words, it should be at a different place from where you store your sessions.
[03:49] And this also means that you can also use the same memory for multiple agents if you're going to have it centered on the user.
[03:56] So you have the same memory for one user, like for example myself, and my tastes and my current projects that I'm working on, and have that memory be shared among different agents.
[04:06] So for example, with ChatGPT, with Claude, with Pi, etc.
[04:11] And then ideally, you want to give your agent the tools to explore its own memory, or at least you want to do ingestion and retrieval automatically on every agent turn, okay?
[04:24] Or at least after each conversation.
[04:27] All right, so with that out of the way, let's take a look at which stores Mem0 uses.
[04:32] And let's just store it like this: let's say stores.
[04:35] And it uses three main stores.
[04:38] Okay, so the first one is going to be the main memory.
[04:43] And this one is going to be the main place where all the memories are going to be stored.
[04:48] So the memories themselves, the short paragraph or short sentences that contain the memory itself, are going to be stored right here.
[04:56] So the memories plus their metadata.
[04:59] A memory can be something like, for example, the user prefers vegan restaurants.
[05:05] And that memory is going to contain a bunch of metadata associated to it, and it's going to be in this vector store as well.
[05:12] The metadata can include things such as the date when the memory was created, whether the memory belongs to the user or the agent.
[05:22] So, if it's information, for example, about the user, as we mentioned, about the user preferring vegan restaurants, for example, or about the agent, for example, learning about current events or about a new way to perform some procedure, etc.
[05:36] It's also going to contain the hash of the memory itself, and this is used strictly for the duplication.
[05:43] We're also going to have the lemmatized version of this, and this one right here is used mainly for keyword search, which we're going to be explaining in a bit.
[05:54] And after that, it also includes the dates, such as when it was the dates when it was created and updated, and it also creates mentions the expiration date.
[06:05] And it will only have the attribution of who created this memory.
[06:11] So, for example, if you're using multiple agents, which agent created this memory, and that's the main memory store, the vector store.
[06:19] And on top of that, we have another store called the entity store or the entity memory.
[06:25] And what's going to happen is that Mem0 is going to extract the entities from the main memory, so the places, the proper names, the people, etc., and it's going to save them in this separate vector store.
[06:39] So, this one right here is also a vector database, but every point in that vector database is going to be an entity.
[06:47] And each entity we're going to have in its dead metadata that is linked to one or more main memories.
[06:56] So, for example, if my memory is that my favorite neighborhood in Paris is Montmartre, then the entity is going to be Montmartre in Paris.
[07:06] And whenever I am asking a question about Paris, it will retrieve the entity about Paris and it will see all the memories linked to that entity and among which there is going to be my memory about my favorite neighborhood in Paris.
[07:23] Okay, that is the second one.
[07:25] And the third one is a SQLite database, which is used for two things.
[07:31] First of all, basically a logging system to keep track of all the history of all the changes in your vector stores.
[07:40] And second, it's going to keep the latest 10 messages that were sent to the pipeline so that it has access to those most recent messages to figure out what the user is talking about when creating a new memory.
[07:56] I'm going to explain to you a little bit more about this in a moment.
[07:59] So those are the three different stores that we have.
[08:02] Let's take a look at how Mem0 uses them in ingestion and in retrieval.
[08:07] All right, so now we have a much better understanding, first of all, of what is long-term memory for agents, and also in this particular example, which are the stores that are used by our agent.
[08:19] Now, let's talk about the ingestion part, which is the part that is going to run when the agent wants to save new memories.
[08:25] Okay, so the ingestion part is going to be executed or it's going to be run after every agent turn.
[08:33] So when you ask the agent to do something, it will continue the task, finish it, or when it finishes one turn, it will take those messages produced by that turn and they will be sent to this pipeline.
[08:45] Okay, so the input in this case is going to be messages.
[08:49] And just for reference, there are three main ways in which those messages can be turned into memories in your database.
[08:58] The first one is going to be procedural memory.
[09:04] And this way of ingesting the memory is going to take a list of messages and summarize the process or the actions that the agent took so that in the future it is able to retrieve this memory and reproduce that exact procedure.
[09:20] This is useful if you want to, if you finished the agenda, if the agent finished doing something and you want it to remember exactly what it did with which tool calls it made and which results it got, this is the one that you probably want to use.
[09:35] But in general, I don't really feel like it's very used anymore.
[09:40] Precisely because you can just extract a skill from a conversation transcript, and that works much better.
[09:47] The second way is to set the infer parameter to false.
[09:52] And what this is going to do is that it's going to take all these input messages that you're taking, sending into the pipeline, embed them, and put them straight into your database, just like that, just the messages themselves.
[10:04] Okay, and the third way is to set infer equal to true.
[10:09] And this one right here is going to trigger the extraction with the LLM pipeline, which is the more sophisticated and more advanced way to do memory for long-term agents.
[10:21] And this is the one that we're going to be focusing on today.
[10:25] Okay, so let's suppose that you have set infer to true and you send the input messages to the ingestion pipeline.
[10:32] What is going to happen after that?
[10:34] So, the second step right here is going to be to load recent context.
[10:42] And this includes a bunch of things because this extraction is going to happen with an LLM.
[10:47] So, what's going to happen is that you're going to send all these messages to an LLM, and the LLM is going to expand a structured JSON with all the memories that it extracted from it.
[11:02] Okay, and in order to do that, the LLM is going to have a very specific prompt and a very specific context about the user and about what they are talking about so that it is able to extract relevant memories from those messages.
[11:17] And this context includes a bunch of different things.
[11:21] So, let me tell you.
[11:23] First of all, let me just give you a tour on this prompt.
[11:26] The prompt includes just the role of the LLM, which is going to be memory extractor, then also includes a summary of the user, so user summary.
[11:38] It is also going to include the messages, of course, that we are adding right here as input.
[11:43] It is also going to include the recent memories that were saved to the database so that we know what we're talking about and maybe some relevant memories that come into play right here.
[11:55] It's going to include relevant memories, so that means that it is going to look into the database to find relevant memories to these messages.
[12:03] And this one right here is actually done by just flattening the messages, so turning the messages into a single string, embedding that list of messages, and searching the existing database for relevant memories with this particular that are related to that message.
[12:23] And those relevant messages are also going to be appended to context, and it is also going to include the last n number of messages.
[12:28] Ideally, these are the 10 latest messages, so last 10 messages.
[12:36] And these ones right here are going to come from the SQLite database that we mentioned before.
[12:40] So every time this pipeline is triggered, it is going to save that message into the SQLite database and it's going to keep only the 10 latest messages.
[12:52] And this one right here is very useful to identify and to figure out what pronouns mean, for example, in that particular new message.
[13:00] So for example, it's supposed that we send a new input messages, a new input message that includes things like it is great or he's really good at this.
[13:11] Those it and he don't really have context about what they mean unless you send the past few messages so that the this LLM knows what you're talking about.
[13:20] So this one right here comes from SQLite and this one right here comes from the main vector DB.
[13:27] Then on top of that, it lists the conversation date and the current date.
[13:32] And this prompt is going to export this very nice JSON, which is going to be the memory that we're going to be importing into our system.
[13:41] Okay, so let's suppose that we now have passed this, and as a result, we got something like the user prefers vegan restaurants.
[13:53] And this is going to be the memory of the user.
[13:55] Okay, that's just an example.
[13:57] Now, what's going to happen is that this memory is going to be saved into our main memory store.
[14:04] It's going to be added, it's going to get the date where it was added or updated, whether it's from the user or the agent, as we mentioned before.
[14:12] It's going to be hashed and deduplicated against the existing database.
[14:18] And of course, since we're using hash deduplication in this particular case, it only works for memories that are the exact same wording.
[14:27] So probably not the most useful deduplication system that we have, but that's what we have right here.
[14:32] And it's also going to contain its lemmatized version.
[14:36] So this sentence right here is going to be lemmatized and it's going to be saved alongside the memory for keyword search later on.
[14:44] And that is how it's going to be stored into the main main vector db and also into our SQLite database, as we mentioned before, to keep track of the latest messages that were added to the pipeline.
[15:01] Okay, and that's essentially how the system is going to store the new memories.
[15:08] And the most important part is the LLM-based extraction.
[15:12] And this right here can be done with a very small LLM.
[15:15] If I'm not mistaken, Mem0 does it with GPT-5 Mini, but you can use a very small model, open source, and run this for free in your machine because it is very straightforward and very simple.
[15:28] Gonna be giving you some recommendations of models in a bit.
[15:32] All right, so we have effectively understood pretty well what is long-term memory, the stores that come into play with Mem0, and how the ingestion flow works.
[15:42] Now let's talk a little bit about retrieval.
[15:44] And this is a very important part.
[15:46] And there are essentially two main parts or two main moments where you are going to want retrieval to work.
[15:53] Retrieval essentially means getting relevant memories to whatever topic you're talking about.
[15:59] So, you want to retrieve those relevant memories in two different scenarios.
[16:03] The first one is when your agent wants to explicitly search its memory, and ideally, this is exposed to the agent via an agent tool.
[16:13] So, for example, a tool called search memory database or something like that.
[16:18] That way, your agent is going to be able to call the tool, search it with a query, and find relevant memories related to that particular query.
[16:26] That is explicit memory searching.
[16:29] And another way of searching is to have it be automatic on every agent turn.
[16:35] So, when you send a message to the agent, the memory is going to trigger automatically, it's going to find relevant memories related to your query, append them to the context of your agent, and send that to your LLM so your LLM has context about what your user is talking about and some previous conversations.
[16:56] Okay, and this retrieval, the pipeline for the retrieval is the same for both the tool and automatic retrieval.
[17:03] And what's going to happen is the following: so, first of all, we're going to take a few parameters as input.
[17:09] We're going to take, first of all, the query, of course, so whatever we're talking about or what we're using.
[17:15] If it is the user message, then the user message becomes the query itself.
[17:19] There might be some query rewriting at this step, and that can be pretty useful in your pipeline.
[17:25] As far as I'm not aware, Mem0 does not do that by default, so you would have to implement that on the harness side.
[17:32] But just keep that in mind.
[17:33] The query is the first part.
[17:35] The top K memories that you want, so how many memories do you want by retrieval search?
[17:42] So, for example, let's say that you want 10 memories, a threshold of the minimum score of memories to be added to the list of related memories, and finally, the identity of who is looking for these memories.
[17:55] Okay, so not the ID, the identity.
[17:58] So, is this memory related to the agent, a memory related to the user, or to the run itself?
[18:05] Okay, and this is very important because agent memory can be things about what it learned about the world or some procedures about how to do some different stuff.
[18:15] And about the user, it will be related to the user preferences, the user's previous experience, etc.
[18:23] And with that input, what we're going to do is we're going to retrieve relevant memories for that conversation.
[18:28] So, the first step right here is to embed the query.
[18:32] And in order to do that, we're going to use the same embedding system, the same embedding model as we used to create the vector database.
[18:40] And in order to do that, we can use some open source models.
[18:44] And here on the screen is a list of some of the most popular embedding models that you can use for this particular task.
[18:50] And you can run them locally, of course, in your machine.
[18:53] Mem0, by default, uses closed models.
[18:56] Just know that you can also do this locally completely with your own open source embedding models.
[19:02] Once the query is embedded, we're going to use it to retrieve semantically similar memories in our main vector store.
[19:12] Okay, so right here we're going to search vector DB semantically, and this is done with ANN.
[19:19] Okay, okay?
[19:19] So, nearest neighbors.
[19:22] And actually, here is where your top K value comes into play.
[19:28] Because remember that top K is the number of memories that you want to be added to your context of your agent.
[19:29] And let's suppose that you said that you want the top K of 10.
[19:37] So you want 10 related memories to enrich your context of your user about your agent.
[19:45] And what's going to happen is that this vector search is not going to look for only 10 messages.
[19:51] It's going to look for many more because it's going to do some re-ranking.
[19:54] And re-ranking works better, of course, if you have a larger pool of results.
[19:59] And then it's going to select the top 10 from those, that bigger pool.
[20:04] And in order to do that, what's going to happen is that it's going to either multiply your top K times 4 or select 60 as the minimum number of messages, memories that are queried.
[20:21] So if you set a top K of 10, you're going to actually get 60 messages from this vector search.
[20:28] And this is very important because on top of these 60 messages, there are going to be a couple of extra steps to re-rank and to boost some memories over others.
[20:42] And here is where you have the keyword matching score.
[20:46] And on the other side, you have the entity boost.
[20:51] Now, this one right here is essentially going to search for overlaps in words between your query and the memories within the that were returned from here.
[21:04] So remember that the memories returned from here are the base pool on which there is going to be the re-ranking.
[21:11] None of these two steps can add additional memories to what was retrieved right here.
[21:14] So what's going to happen is that both the query is going to be lemmatized and actually remember that all the memories already already contain their own lemma in their metadata as we mentioned before.
[21:16] So the first part right here is keyword matching score.
[21:32] So we're going to compare these two and we're going to give it a score depending on how similar the words are.
[21:39] So word.
[21:40] So in other words, it's measuring the word overlap and it is doing that in case you're curious about it with BM25.
[21:47] Okay.
[21:48] And by the end, we're going to translate that into a score between 0 and 1.
[21:55] Okay.
[21:56] So that's going to be this score right here of keyword matching.
[21:59] And for entity boost, what's going to happen is that it is going to first extract the entities from the query.
[22:07] Then it is going to search the entity database for this query.
[22:13] And the resulting entities contain memories that are linked to them, right?
[22:19] As we mentioned before, that is how the entity memory works.
[22:22] So every entry in this vector database is an entity.
[22:26] So for example, Paris, restaurant, etc.
[22:30] And every single one of those entities is linked to one or more memories, okay?
[22:36] And what's going to happen is that whatever entities we get from that vector search, we're going to take the memories associated to those entities and we're going to only select the ones that are in our initial 60 or our top K initial pool.
[22:55] And in order to do that, we're going to select those and we're going to give them an additional score.
[23:00] And this score right here is between 0 and 5.
[23:04] If you're interested in the exact formula of how they get this specific score, you can take a look at the screen.
[23:11] I will be showing it right now.
[23:14] But to keep things simple, just remember that whichever entity, whichever memories were retrieved that were retrieved from this entity database, are going to get a score, and the score is going to be higher if there are less memories associated to that particular entity.
[23:31] Okay, so see, for example, if for example, if my entity is Paris and I have a thousand memories associated to that one, then the score is going to be much lower.
[23:40] If my entity is Paris and I have just two memories associated to that one, then it is more likely that those memories are related to whatever I am trying to retrieve right here.
[23:51] So that's going to give us a larger score between 0.5, closer to 0.5.
[23:58] In the end, all these three scores are going to be added.
[24:02] The vector search also is translated into a score between 0 and 1.
[24:06] So we have from 0 to 1 here, from 0 to 1 here, and from 0 to 0.5 here.
[24:12] That gives us a total of 2.5 potential score.
[24:17] So what's going to happen for each one of the messages in our initial pool is we're going to add both three scores.
[24:24] So this one, this one plus this one plus this one, and we're going to divide them over 2.5.
[24:31] And that is going to give us the final score.
[24:35] And then, given that score, we're going to select the actual top K.
[24:42] And those are the ones that are going to be returned to the user or to your agent.
[24:44] Okay, and that's essentially how retrieval works.
[24:51] As you can see, it's a little bit more complex than the ingestion part.
[24:54] There are three different scores that are added together in order to actually rank the most relevant memories for that particular query.
[25:03] And something else, remember, I mean, I already mentioned it, but remember, it's very important to remember that the query right here is anything, it can be anything, and it's usually whatever you send to this pipeline.
[25:16] But this pipeline does not necessarily have a query rewriting system.
[25:21] If you want the retrieval to work better, I would recommend that you add a query rewriting system right here on the side of your harness.
[25:28] So whenever the user asks about something, you ideally have a small LLM, translate that into a better query so that you can get better results right here in the retrieval.
[25:41] And there you go.
[25:42] This is essentially everything that you need to know about how Mem0 works.
[25:46] And let's take a look very quickly at some models that you may want to use in each one of these stages if you want to do this with completely local setups with open source models.
[25:57] So if you go to Hugging Face, we have right here, if you go into models, you're going to see a bunch of different models.
[26:04] I would recommend that if you want to use an open source model for the extraction part, which is one of the most important parts, I would recommend that you choose a small model because extraction is a relatively simple task.
[26:18] So between 1 billion and 12 billion parameters should be enough.
[26:22] I would not go beyond 1 billion unless you fine-tune the model in order to make it more accurate.
[26:28] But in this ballpark, you already have good suggestions.
[26:32] And if you click on text generation, it's going to filter only for text generation.
[26:37] So, for example, right here, you can see that you can use some Llama models, you can use Qwen.
[26:43] Probably you could, I would recommend something like Qwen3 8B.
[26:47] I think that 8 billion parameters is pretty good.
[26:49] You can choose the latest version of the model at the time of watching.
[26:55] For embeddings, if you want to take a look at them as well, I recommend that you go also right here to models and in tasks, you're going to go right here and filter by feature extraction.
[27:07] And that way, you will see mostly embedding models, and you can select the one that you like the best.
[27:13] And if you want to see a ranking of the embedding models, you can take a look at it right here.
[27:19] Here, which is one of the most reputable benchmarks for a bunch of different embedding models for multilingual, for English, for different languages, etc.
[27:29] And here you have the benchmarks exactly, depending on which one you're trying to figure out.
[27:33] So, for example, it's supposed that you want something related specifically to medical.
[27:38] Well, here you have the benchmark for embedding models that perform better with medical contexts.
[27:45] And of course, there is always the possibility of fine-tuning your own small models.
[27:49] I would actually recommend that if you're going to be doing this in a long term, you fine-tune your models for this to work even better and to be able to run this locally.
[27:58] And there you go, that is essentially, a very quick overview of long-form or long-term memory for agents with a very nice use case like Mem0.
[28:09] I will be dissecting other memory libraries such as SuperMemory, for example, etc., to show you a little bit more of different ways of dealing with long-form memory.
[28:20] But I hope that this was educational and do let me know if there are other things that you would like to see in this format.
[28:26] And thanks a lot for watching, it has been a great pleasure.
[28:29] I'll see you in the next one.
