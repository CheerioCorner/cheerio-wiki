---
title: "Make your website agent ready with WebMCP"
type: raw-transcript
created: 2026-08-24
video_id: FARxSG_EY98
url: https://www.youtube.com/watch?v=FARxSG_EY98
duration: 00:05:21
language: en
auto_generated: true
source_api: youtube_transcript_api
segments: 147
timestamp_format: "[MM:SS]"
---

# Make your website agent ready with WebMCP

> Duration: 00:05:21 | Segments: 147 | Language: en | Source: API (auto-generated)

## Transcript

[00:02] Hi, I'm Ugo, a Google developer expert in AI, and I'll talk to you about Web MCPs, [music] a technology that's reshaping how AI agents interact with the web.

[00:13] Let's start with an example. [music] Imagine you run an e-commerce site. More and more customers are delegating tasks to AI agents to do things for them. Find a product, compare options, or [music] place an order. To stay relevant, your site can't just be human-readable. It has to be machine-accessible.

[00:31] You want to be a website that agents can [music] actually use reliably at scale.

[00:36] And this is where Web MCPs comes in. Web MCP lets you declare [music] agent-ready interfaces directly inside your web page. So, when your website is loaded, a couple of tools are loaded and exposed together with it to the AI agents. And it behaves like a standard MCP, but instead of running on a separate server, it lives [music] in the page itself, exposed through a simple browser API.

[01:03] With Web MCP, when an agent lands in a page, it doesn't have to scrape the HTML or guess. It sees a clean set of predefined tools that they can use to [music] manipulate the website.

[01:15] For our e-commerce example, uh >> [music] >> the homepage could expose uh tools such as search products, get categories, and filter, while the product page could expose add to cart or get similar products. And the agent calls them like any MCP tool, but the tools are contextual to the page [music] the agent's navigating. And there is no need to connect to anything beyond [music] uh the web page.

[01:41] And having this on your website makes a huge difference because navigation becomes much faster and much cheaper in terms of tokens. So, let me show you how this looks like in practice. To demonstrate this, I built a small example called Happy Coffee, a fake internal data developer portal for a coffee warehouse. It's the kind of tool most companies have for managing their data pipelines, quality checks, and cost monitoring all in one place.

[02:08] This is a react app [music] and it's fully web MCP enabled using the imperative API. In this example, I'm using a Chrome extension to ask Gemini to search for a data set, >> [music] >> analyze the quality checks, and give me a summary of what's going on.

[02:23] This is a sequence of actions that requires a contextual understanding of the website [music] and what to look for. And notice how fast Gemini does it and does it properly.

[02:33] >> [music] >> Uh and all of this using a tiny fraction of the tokens a screenshot based agent would burn in the process.

[02:41] In this [music] case, it used uh a few MCP tools I defined, but let me zoom in in the search global catalog so you can understand how it works.

[02:50] To create a web MCP tool, you basically need to register it, describe what it does, declare its inputs and outputs, and mark required fields. And and that's it. The agent now has a precise typed contract for that capability on our website.

[03:07] And all the tools are contextual to the page. The agent has everything they need on the page they are loading.

[03:15] There is one more thing I'm really excited about and it's the reason I chose the developer portal for this [music] demo. Web MCP isn't just for remote agents hitting APIs and interacting with your website. [music] It also unlocks local agents working with web wise, which enables some very powerful workflows.

[03:33] For example, on a typical data development flow, before you merge a pipeline to production, you run a staging execution to make sure the data set materializes correctly. And what we are seeing [music] is that developers are calling agents on CLIs to do the whole development for them.

[03:48] But once the data set is ready, they want to have a visual feedback on the execution going on. The logs, the lineage, [music] a sample data, and etc.

[03:57] And web UIs are the best format to provide such feedback. So, I implemented a way to open my local terminal inside my web portal, and I will ask Gemini to develop a data transformation for me.

[04:10] However, I used web MCP Q exposed the developer portal to the Gemini [music] model I'm interacting. So, the code is being developed by Gemini in my local machine, and once the code is ready, Gemini is going to trigger a staging execution for the data set, and they will navigate to the pipeline [music] page to enable me to follow that execution, and then I can expect the results myself. I can see the logs, I can see a [music] sample of the data, and so on.

[04:37] So, notice that after my commands, uh the model developed the data set, and it's now controlling my browsers to check the execution. And it's the type of solution that really accelerates development workflows. In my case, I'm monitoring the execution, but in practice, you can just expose the web MCP tools and let I to flow autonomously. If you want, you can just have the the visual feedback. And yeah, that's web MCP. It makes [music] agentic uh driven web navigation the fast and cost-efficient, and it gives you a

[05:06] builder, a real control of how agents experience your product. The full source code for this demo and an article on web MCPs are linked [music] in the description below, and thanks for watching, and I see you next time.
