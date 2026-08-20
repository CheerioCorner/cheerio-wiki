---
title: "OpenWiki & OKF — know.2nth.ai"
type: raw-web
created: 2026-08-20
url: "https://know.2nth.ai/explainers/agents/openwiki"
---

# OpenWiki & OKF — know.2nth.ai

OpenWiki & OKF — know.2nth.ai

know.2nth.ai

Agents

OpenWiki & OKF

agents · OpenWiki + OKF · Format Leaf

A directory of markdown,  with just enough metadata to find it.

The  Open Knowledge Format (OKF)  is Google Cloud's vendor-neutral spec — published 12 June 2026 — that formalises the "LLM-wiki" pattern into a portable format: a folder of markdown concept files, each with a little YAML frontmatter.  OpenWiki  is LangChain's CLI that reads a codebase and writes an OKF bundle a coding agent can navigate; version 0.2 (16 July 2026) is the release that adopted OKF. The spec's required surface is deliberately tiny — exactly one field.

Live spec · v0.1

Google Cloud origin

Markdown + YAML

One required field

npm i -g openwiki

01 · What it is

A knowledge format, and the tool that writes it.

Two things share this leaf because they shipped together, but they are not the same thing.  OKF  is the format: an open, vendor-neutral specification from Google Cloud for representing curated knowledge as a directory of markdown files, each carrying a small block of YAML frontmatter.  OpenWiki  is a  producer  of that format: a CLI from LangChain that scans a codebase and emits — and keeps updating — an OKF bundle that coding agents read. OKF is the durable thing; OpenWiki is one shipped implementation of it, the way  anthropics/skills  is one implementation of the Agent Skills spec.

This isn't a new runtime.  It's a directory of markdown files, with a convention for the frontmatter and two reserved filenames.  Google published OKF on 12 June 2026 and called it, in its own words, "a starting point, not a finished standard." LangChain's OpenWiki 0.2, on 16 July 2026, was the first widely-noted tool to adopt it — every wiki OpenWiki now generates or updates is written as an OKF bundle.

The reason a format like this earns a name at all: agents are only as good as the context they're handed. Point a coding agent at a large repository with no map and it discovers structure by open-ended file-system scraping — slow and token-expensive. OKF's bet is that a small, consistent layer of metadata — written once, kept in git next to the code — lets an agent find the right document without reading the whole tree first.

The one required field

OKF v0.1 requires exactly  one  frontmatter field:  type , a non-empty short string the spec describes as identifying "the kind of concept — consumers use this for routing, filtering, and presentation." Everything else —  title ,  description ,  resource ,  tags ,  timestamp  — is  optional  (the spec calls them recommended). Producers may add their own keys, and consumers are told to preserve unknown keys and not reject documents that carry them. So minimal conformance is low: a directory of  .md  files, each with a non-empty  type . Any characterisation of OKF as demanding five mandatory fields, or a rigid three-part structure, overstates what the spec actually asks for.

02 · How it works

Concept files, six standard fields, two reserved documents.

An OKF bundle is a directory. Most files are  concept documents  — ordinary markdown with a frontmatter block. Two filenames are reserved with special meaning. The frontmatter is where the retrieval leverage lives: one required field, five recommended ones, and room for producer extensions.

The six standard frontmatter fields  — only the first is required:

Field  Status  What it carries

type    Required  (non-empty)  The kind of concept — used for routing, filtering, presentation.

title   Optional  Human-readable name for the concept.

description   Optional  One-line summary — the field an agent scans to decide relevance.

resource   Optional  A URL or identifier the concept points at (a table, an endpoint, a doc).

tags   Optional  Free-form labels for filtering.

timestamp   Optional  When the concept was last curated.

A minimal OKF concept document  — the required field, a few recommended ones, then markdown:

---

type : table                         # REQUIRED — the only mandatory field

title : orders

description : One row per customer order; grain is order_id.

resource : bigquery://sales.prod.orders

tags : [billing, revenue]

timestamp :  "2026-07-16"

---

# orders

The canonical orders fact table. Join to `customers` on

`customer_id`. `status` is one of draft / paid / refunded.

Revenue reporting reads `total_ex_vat`, never `total`.

The directory shape.  Concept files sit alongside two reserved documents — a directory listing and an update history:

sales-wiki/

├── index.md             # reserved: directory listing (spec §6)

├── log.md               # reserved: update history (spec §7)

├── orders.md            # concept document

├── customers.md         # concept document

└── billing/

└── invoices.md      # concept document

A naming discrepancy worth knowing

OKF defines  two  reserved documents — not three structural elements. The change-log filename is where the sources disagree: the Google OKF  SPEC.md  names it  log.md  (singular), while LangChain's OpenWiki 0.2 blog post writes  logs.md  (plural) in its prose. It's a small inconsistency in the announcement, but a real one — if you're writing a validator or a reader, handle both spellings rather than trusting either source alone. The listing file,  index.md , is consistent across sources.

The retrieval shift — foundation, not yet shipped tooling

The point of the metadata is deterministic filtering. Instead of an agent scraping the whole tree, structured frontmatter lets a reader filter — LangChain's example is "every doc tagged  billing , or every doc in the  BigQuery tables  category" — rather than relying entirely on open-ended agentic search.  Read the tense honestly:  LangChain frames deterministic search as something the metadata  enables  for "future OpenWiki tooling," not a feature that ships in 0.2. What 0.2 delivers is the structured foundation; the fast deterministic reader on top of it is stated as forthcoming. Don't buy the retrieval speed-up as already built.

03 · The ecosystem

One spec, a visualizer, and OpenWiki as producer.

OKF is young — a v0.1 spec published in June 2026 — but it already has a reference implementation, a bundled viewer, and a real producer in OpenWiki. The origin use-case was data catalogs: describing BigQuery tables, metrics, and join paths so agents query the right thing.

Piece  What it is  Where

OKF spec   The  SPEC.md  +  README.md  defining the format, with sample bundles.   GoogleCloudPlatform/knowledge-catalog

OKF visualizer   An open-source static-HTML graph viewer for an OKF bundle, bundled in the reference repo.  same repo

OpenWiki   LangChain's CLI that generates and maintains an OKF bundle for a codebase, in code or personal mode.   langchain-ai/openwiki

Agent surface   OpenWiki exposes the wiki to agents by writing  AGENTS.md  /  CLAUDE.md  entry points.  generated in-repo

Getting an OKF bundle out of a repo  is two commands — install the CLI, initialise it in the project:

# install the CLI globally

npm install -g openwiki@latest

# scan the current repo and write an OKF bundle + AGENTS.md / CLAUDE.md

openwiki --init

Where it came from, and where it's going

OKF grew out of Google Cloud's data-sharing work — the problem of handing an agent a warehouse it can actually reason about. Google is explicit that v0.1 is "a starting point, not a finished standard," which is the right frame for anyone adopting it: the field semantics may move. The wider ecosystem is early — a handful of community viewers and conformance checkers — and worth watching rather than betting a production pipeline on today.

04 · OKF vs Skills vs MCP

Knowledge, capability, access — three different layers.

OKF sits in the same open-standards layer as Agent Skills and MCP, and it's easy to blur them. They solve different problems, and the difference is load-bearing once you're assembling a real agent system:

Concern  OKF  Agent Skills  MCP

Packages   Curated  knowledge  / concepts  Reusable  capabilities   Live  access  to tools & data

Format   Folder of  .md  + YAML frontmatter  Folder +  SKILL.md   JSON-RPC server

Lifetime   Static; in git next to the code  Static; in git  Long-running process

Required surface   One field ( type )  Two fields ( name ,  description )  Handshake + capability list

Best fit   Giving an agent a map of a codebase or a warehouse  Repeatable procedures, brand / policy adherence  Reading and writing live external systems

How they compose

They stack rather than compete. An OKF bundle tells the agent  what exists and where  — the  orders  table lives in BigQuery, joins to  customers . An MCP server gives it  live access  to actually run the query. A skill frames the  procedure  — how this team writes a revenue report.  OKF is the map, MCP is the road, Skills is the route you always take.

✗ Skip OKF when…

— Your knowledge is small enough that letting the agent read the raw files is cheaper than maintaining frontmatter discipline. The format earns its keep at scale, not on a five-file repo.

— You need  freshness guarantees . OKF v0.1 has no staleness enforcement — a concept doc can happily describe code that has since changed. Nothing in the format stops the map drifting from the territory.

— You're buying it for the deterministic-search speed-up today. That's a stated foundation in OpenWiki 0.2, not shipped tooling.

— You want to hard-code against field semantics. Google calls v0.1 "a starting point, not a finished standard" — treat the spec as movable.

05 · South African context

Institutional knowledge as a git-versioned, POPIA-safe bundle.

The consultancy unlock.  A delivery team's real asset is knowing how a client's systems actually fit together — which ERP module is the source of truth, how the chart of accounts maps to SARS categories, why the BBBEE scorecard pulls from two places. That knowledge usually lives in someone's head or a stale Confluence page. As an OKF bundle it becomes a folder of markdown in the client's git repo: agent-readable, human-editable, versioned alongside the code it describes. No new platform, no vendor lock-in, no FX exposure — it's a directory of text files.

POPIA and the data boundary.  An OKF bundle is static curated context — descriptions, join paths, table meanings. On its own it never reaches out to a database or an API, so it's safe to keep in a shared repo the way documentation is. The bits that touch personal information stay behind the MCP servers the bundle merely  points at  (via the  resource  field), and those servers stay on SA-hosted infrastructure. The map can be shared; the territory stays put.

The honest caveat for SA teams.  The value here is real but early. OKF is a v0.1 spec and OpenWiki's deterministic-search payoff isn't shipped yet, so the near-term win is modest: a consistent, agent-friendly documentation format you can adopt cheaply and grow into — not a finished retrieval engine. Adopt it for the discipline and the portability, not for a speed number that doesn't exist yet.

06 · Connections

Where OpenWiki & OKF link in the tree.

agents

Agents hub

OKF sits in the Protocols / open-standards band alongside MCP and Agent Skills — a format, not a runtime.

agents/skills

Agent Skills

The sibling format. Skills package  capabilities ; OKF packages  knowledge . Both are folder + markdown + YAML with a tiny required surface.

agents/mcp

Model Context Protocol

The complement. OKF is the static map of what exists; MCP is the live access that reads and writes it. The  resource  field often points at an MCP-served system.

agents/claude-code

Claude Code

A direct consumer — OpenWiki writes the  CLAUDE.md  /  AGENTS.md  entry points that coding CLI agents read on start-up.

agents/coding-cli-agents

Coding CLI agents

The band that benefits: an OKF bundle gives any codebase-navigating agent a map instead of a blind file-system crawl.

data

Data domain

OKF's origin use-case — describing warehouse tables, metrics, and join paths so an agent queries the right thing.

07 · Resources

Spec, repos, and the announcements.

Spec

OKF SPEC.md · the authoritative format

github.com/GoogleCloudPlatform/knowledge-catalog/…/okf/SPEC.md

Source

knowledge-catalog/okf · README, samples, visualizer

github.com/GoogleCloudPlatform/knowledge-catalog/tree/main/okf

Announcement · 12 Jun 2026

How the Open Knowledge Format can improve data sharing

cloud.google.com/blog/…/open-knowledge-format…

Source

langchain-ai/openwiki · the CLI

github.com/langchain-ai/openwiki

Announcement · 16 Jul 2026

OpenWiki 0.2 brings OKF to codebase documentation

langchain.com/blog/openwiki-0-2-adds-okf-support
