---
title: "What Is Chunkless RAG? How Docling & AI Agents Navigate Documents"
type: raw-transcript
created: 2026-08-10
video_id: vRZNJWw78BQ
url: https://www.youtube.com/watch?v=vRZNJWw78BQ
duration: 00:07:00
language: en
auto_generated: false
source_api: youtube_transcript_api (pytubefix fallback)
segments: 98
---

# What Is Chunkless RAG? How Docling & AI Agents Navigate Documents

> Duration: 00:07:00 | Segments: 98 | Language: en | Source: pytubefix SRT

## Transcript

[00:00] Say you hand a model a 200-page annual report and ask it one specific question,
[00:05] something like, what changed in the revenue recognition policy this year, and where does the report explain why?
[00:11] That's a normal question.
[00:12] A person who knows the document could answer it in about two minutes.
[00:15] They'd flip to the right section and read it.
[00:17] But watch what the machine does to answer the same thing,
[00:20] because the usual approach throws away the one thing that we would use to make the question easy.
[00:25] The usual way to do this is retrieval augmented generation, or RAG.
[00:29] You take the document and you cut it into chunks.
[00:31] Maybe every 500 words, maybe by paragraph.
[00:35] You then turn each chunk into a vector.
[00:38] Store all of them, and when a question comes in, you turn that question into a vector as well,
[00:43] and you pull back the handful of chunks that look the most similar.
[00:46] Those go into the model's context, and it answers from them.
[00:50] This is useful, and it's cheap, and for a lot of questions, it's all you really need.
[00:55] The strain shows up when one big structured document shows up.
[00:59] The moment you cut it into chunks, you've discarded how it was put together.
[01:02] A heading gets separated from the paragraphs it was introducing.
[01:05] A table gets split from the sentences that say what the table means.
[01:08] And if the answer lives across multiple different sections,
[01:11] similarity search has no idea those sections belong together because it was only ever comparing small blobs of text.
[01:18] You get fragments back and the model has to guess how they can relate.
[01:21] But that document was never a pile of text to begin with.
[01:25] Somebody wrote it as a structure.
[01:27] There's a title,
[01:29] headers,
[01:33] sections, subsections, there's paragraphs, tables, images, all of which are underneath this tree structure.
[01:40] The author already organized the information for you.
[01:43] Chunking takes that tree and flattens it so that we can do a similarity search over those flat sections,
[01:48] but we end up destroying the structure and then spend a lot more effort trying
[01:53] to recover the relationships that were sitting right there the whole time.
[01:57] So let's think about another way to find the right material.
[02:00] Don't flatten the document, keep the tree.
[02:02] And let the model reason its way to the right part instead of matching by similarity.
[02:07] Think about how you'd actually answer that revenue question for yourself.
[02:11] You wouldn't read all 200 papers of the document.
[02:14] You'd open the table of contents, maybe find the section on accounting policies, flip to it, and then read just that section.
[02:22] If it pointed to a footnote, you may follow the reference.
[02:24] You'd navigate through the document, and that's the model that an agent can do as well.
[02:35] It starts with an outline of the tree where each section comes with a short summary
[02:39] so that it can see the shape of the document without reading the body.
[02:43] It reasons about which section is most likely to hold the answer, opens just that one and reads it.
[02:49] If that's enough, it answers the question.
[02:51] If not, it can pick the next section and it keeps going a few steps at a time until it has what it needs.
[02:57] Two things fall out of this that are hard to get with just flat chunks.
[03:02] First, context comes along for free.
[03:04] When the agent is reading a paragraph, it still knows which section that paragraph lives in
[03:09] and which subsection because it walked the entire tree.
[03:13] The headings above it are part of that path.
[03:15] A chunk pulled by similarity search has no idea where it came from.
[03:20] Second, you can answer questions that span the whole document.
[03:23] If the policy is defined in one section and reasoning is three sections later,
[03:27] the agent can still hold its place, go read the other branch and come back.
[03:31] It's moving around the map.
[03:33] Similarity search just hand you the pieces that happen to look similar to your query.
[03:37] There's another benefit as well.
[03:39] The agent isn't doing less total work.
[03:41] Walking the tree means several passes and that adds up.
[03:45] What changes is what ends up in front of the model before it finally answers.
[03:49] Instead of the entire document or just a handful of chunks that have lost their place in the document,
[03:54] the model gets one relevant section with its heading still on it.
[03:58] It's reasoning over the right material and its right shape.
[04:01] And tends to mean cleaner answers with fewer of those moments where it
[04:06] invents something from disconnected fragments.
[04:09] Everything I just described depends on actually having the tree.
[04:13] And that's the hard part because most documents show up as PDFs, which
[04:17] are basically just instructions for placing characters and figures onto a page.
[04:22] There's no clean structure in there to walk.
[04:25] This is the job that Docling does.
[04:27] You give it a PDF.
[04:32] And you get back a Docling document.
[04:42] Which is gonna be that structured tree, real sections and headings, reading
[04:46] order being preserved, tables that are still tables.
[04:50] The PDF format buries the hierarchy that the author has put in and Docling reconstructs it.
[04:56] Once you have that object, the Docling agent.
[05:05] It can work on that structure directly.
[05:08] It can write, edit, extract fields, and enrich sections.
[05:12] It includes an agent that does exactly the navigation that I just walked through.
[05:16] The project calls this Chunkless RAG.
[05:26] Which is a good description, same goal as ordinary retrieval, find the right material and ground the answer in it.
[05:33] But it gets there by reasoning over the document structure instead of chopping it up into chunks and matching by similarity.
[05:40] This is still retrieval.
[05:41] It just keeps the document whole while you're doing it.
[05:44] It isn't free though.
[05:46] You need a good tree before any of it works and parsing real world documents into clean structure is a hard problem.
[05:53] That's most of the engineering.
[05:55] Reasoning through the structure also takes more back and forth with the model than a single vector lookup does.
[06:01] So there's a lot more latency and more calls.
[06:04] The chunk-based retrieval is still the right tool a lot of the time.
[06:08] If you've got millions of documents and a fuzzy find me anything about X kind of question, similarity search is hard to beat.
[06:15] The structure approach earns its keep on long organized documents where
[06:19] precision matters and the connections between parts are what you're after.
[06:24] In a lot of real world system, you'd use both, similarity search to find the right document, structure to navigate inside of it.
[06:31] So that's the real choice.
[06:33] Both of these are retrieval.
[06:35] The question is whether you chop the document into pieces and match by
[06:39] similarity, or keep it whole and reason through its structure.
[06:43] The author already drew the map, document gives it back to you, and an agent can now follow it.
