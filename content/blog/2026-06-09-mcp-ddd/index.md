---
title: "Your Agent Skill Is Not an Anti-Corruption Layer"
subtitle: "Why MCP pollutes your Bounded Contex"
slug: "mcp-bounded-context"
date: 2026-06-08T22:00:00+02:00
description: "MCP is marketed as the USB-C of AI integrations — but plugging raw upstream schemas directly into an agent's context window is a textbook DDD anti-pattern. Here's why, and what to do instead."
tags: ["ai", "mcp", "ddd", "agents", "architecture", "platform-engineering"]
categories: ["engineering"]
---

You have likely heard the narrative that the Model Context Protocol (MCP) is the "USB-C port" for integrating external systems with AI applications. It's a compelling metaphor — but it has led many to treat MCP as a universal adapter for all AI architecture, resulting in widespread overuse.

To stretch that analogy to its logical conclusion: USB-C is a fantastic protocol for connecting external peripherals to your laptop, but you wouldn't use raw consumer cables to wire up the core routing infrastructure of an enterprise data center. Similarly, while MCP is essential for supercharging local coding assistants, exposing unmediated upstream schemas directly to an LLM's system prompt introduces massive semantic risk. It was never designed to serve as the primary architecture for a complex, production-grade enterprise agent network.

While others have noted that MCP is still catching up to the rigorous technical robustness required for distributed systems, this post looks at the issue through a different lens. Using core concepts from Domain-Driven Design (DDD), we will explore how using MCP servers as a universal plug-and-play adapter inevitably leads to tight coupling, muddy boundaries, and poorly designed enterprise AI systems.

## Where MCP Shines: Fast Adoption

Without a doubt, MCP's biggest strength is its frictionless adoption. Adding an MCP server is often just pasting a few lines into a local configuration file — or connecting it with a single click. It is actually so simple that it gives enterprise security officers literal nightmares about employees integrating untrusted servers.

For individual developers, however, it is a superpower. Most of us have supercharged our local coding agents with one or more MCP tools. A prime example is the Jira MCP server, which I use daily. The value proposition is obvious: the coding agent seamlessly retrieves the context of the user story I am working on, allowing it to factor acceptance criteria directly into its implementation plan. By simply adding a short configuration and credentials to an `mcp.json` file, my agent can suddenly read and modify tickets in my name.

It feels like magic. But when we try to scale this "local coding agent" pattern to enterprise-grade AI applications, we run straight into architectural debt. To understand why, we need to revisit a few foundational DDD concepts.

## A Quick DDD Refresher: Bounded Contexts

In Domain-Driven Design, a large system is divided into **Bounded Contexts**. A Bounded Context defines an explicit boundary within which a specific domain model applies. Inside this boundary, all terms, definitions, and rules form a **Ubiquitous Language** — a shared vocabulary unique to that specific business domain.

> "A Bounded Context delimits the applicability of a particular model so that team members have a clear and shared understanding of what must be consistent and how it relates to other contexts."
> — Eric Evans

Let's look back at the Jira example through this lens. Within your project, you might use Jira to track your work, but your team has a highly specific definition of what a *user story* actually is, focused entirely on shipping great software. Within your team, everyone shares the same understanding: stories are written to prove a business hypothesis, they follow a clear narrative structure, they specify acceptance criteria, and they must meet a team-wide Definition of Done.

In contrast, Jira is an external, general-purpose tool built to track *anything* for *anyone* — from a massive software migration to a marketing campaign or facilities requests. What your team defines as a rich, rule-bound User Story is not natively the same thing as a generic Jira Issue. They are two entirely separate models built for two completely different purposes.

## The Conformist Agent

When we plug a raw Jira MCP server into an AI agent, we are attempting to integrate two entirely different Bounded Contexts. To understand why this is dangerous, we need to look at two distinct Context Mapping patterns from DDD:

**The Conformist Pattern** — The downstream system completely conforms to the upstream system's domain model. It accepts the upstream team's schema, vocabulary, and logic as-is, sacrificing its own model autonomy. If the upstream system changes, the downstream system breaks or is forced to change with it.

**The Anti-Corruption Layer (ACL)** — A translating layer created between two contexts. The downstream system refuses to be polluted by the upstream model. Instead, the ACL translates data back and forth, ensuring both systems maintain their architectural integrity and leaving each side free to evolve independently.

Plugging an off-the-shelf MCP server with an upstream-defined schema directly into an agent is a textbook example of the **Conformist pattern**. You are injecting an externally defined interface straight into the agent's system prompt — the absolute core of its Bounded Context.

When tool definitions are exposed naively, an upstream API change can instantly alter the tool schemas passed to your agent. Your application code won't catch this breaking change. Instead, it will pass silently into the agent's context window until the LLM unexpectedly hallucinates or fails.

To be fair, the culprit isn't MCP's underlying protocol, but the architectural malpractice it facilitates. It makes it dangerously simple to skip proper system design and let raw, uncurated upstream data structures dictate your agent's cognitive model.

Beyond architectural fragility, this conformist approach introduces immediate operational inefficiencies. An off-the-shelf Jira MCP server typically exposes dozens of generic tools. Your specific coding agent might only ever need two or three of them — yet the entire uncurated toolset remains in the payload, unnecessarily bloating the agent's context window and driving up token costs.

You can mitigate this by filtering the allowed tool list. But how do you enforce your internal project conventions? If you want the agent to create a new user story, how do you ensure it follows your team's specific template and engineering criteria?

Today, the industry's default band-aid is to cram these business rules into the system prompt — either directly into an `agent.md` file or encoded as a prompt-based "agent skill." While this might work for a local coding assistant where a developer is watching every output, the calculation changes completely for an enterprise agent. When you scale to autonomous, production-grade workflows, your prompt has become your Anti-Corruption Layer.

And in an enterprise environment, do you really want your critical architectural boundaries governed by probabilistic prose?

## The Enterprise Challenge: Context Translation

In an enterprise environment, an AI agent rarely lives in a vacuum. Its primary value is acting as a bridge between multiple systems — intelligent coordinators that span distinct Bounded Contexts, translating and orchestrating workflows that historically required manual human intervention.

But because these systems represent completely separate Bounded Contexts, connecting them forces the agent into a linguistic trap. Consider a corporate travel agent tasked with coordinating three distinct domains: a flight booking system, a hotel reservation platform, and an internal expense compliance engine.

The word *"reservation"* exists in all three systems, but it means entirely different things, follows different lifecycles, and triggers completely different error states:

- **Flight system** — A reservation is a volatile, temporary hold pending ticket issuance.
- **Hotel platform** — A guaranteed room night subject to strict, legally binding cancellation policies.
- **Expense engine** — A pre-allocated budget line item requiring multi-level manager approval.

This semantic mismatch is exactly where your architectural choices will make or break the system. Translating between these contexts is the agent's core purpose — but how you handle that translation matters enormously.

## The Better Way: Domain-Specific Tools as Your ACL

Achieving a well-defined boundary is surprisingly simple. You don't need heavy middleware or a fragile 4,000-token system prompt. Instead, you can build an effective Anti-Corruption Layer directly inside your application using domain-specific tools, leveraging the native tool-calling capabilities of modern agent frameworks like PydanticAI.

Instead of piping externally defined tool definitions directly into the agent's context, you define custom, tightly specified tools that match the **Ubiquitous Language of your agent**. The tool interface exposed to the LLM becomes a clean, unified gateway — such as a single `book_trip()` function — while traditional software engineering code underneath handles all data transformation.

By defining explicit, typed schemas for your tools, your agent's tools naturally become the ACL. This doesn't make the agent deterministic — the core engine remains probabilistic — but shifting this boundary into code gives you three immediate advantages:

1. **Ubiquitous Language Enforcement** — Model tool interfaces entirely in the agent's internal vocabulary. The ACL translates those clean concepts into whatever legacy data structures upstream systems require, while eliminating the "tool bloat" of general-purpose MCP servers.

2. **Built-In Input Validation** — Decorate interfaces with rich metadata, clear descriptions, and strict validation rules matching your bounded context. This drastically reduces the risk of the model hallucinating parameters or sending malformed payloads.

3. **Production-Grade Integration** — Bypass MCP's distributed-system limitations entirely. Your native agent tools communicate directly with enterprise systems using established, production-tested protocols like REST, GraphQL, or gRPC.

Shifting translation and validation into code ensures your agent operates within a single Ubiquitous Language — combining the semantic reasoning of an LLM with the deterministic safeguards of traditional software engineering.

## Should We Remove MCP from Our Toolbox?

No, absolutely not. But as with any technology, there is no silver bullet. Software architecture is — and always will be — about understanding trade-offs.

MCP itself is not the issue. The issue is its overuse as an architectural shortcut to blindly inject upstream Bounded Contexts into your agent. In fact, you *can* use MCP to build the exact domain-specific tools we just discussed. In our Jira example, instead of exposing a raw, generic third-party server, you could build a **custom internal MCP server** to act as your ACL — translating your team's specific definition of a User Story into Jira's schema behind the scenes, while using the MCP protocol simply to expose that single, curated tool to your coding assistant.

Even from a DDD perspective, the Conformist pattern isn't an anti-pattern to be avoided at all costs. Conforming can be the most pragmatic integration choice, especially when the upstream context is highly stable, universally understood, or when building a custom translation layer adds unnecessary overhead.

MCP's undisputed superpower is **speed of adoption and unparalleled variety**. Because you can plug in almost any service instantly, it provides an incredible playground for rapid agent experimentation. When you are in the early, exploratory phases of building an AI application, you want to test ideas quickly without getting bogged down in integration code. Prompt-based boundaries and quick configuration files are perfect for this phase.

However, things change dramatically with scale.

Whether "scale" means running your local workflows a hundred times a day, or rolling an autonomous agent out to thousands of enterprise users, flexibility eventually stops being your primary goal — **reliability becomes your bottleneck**.

At that tipping point, your priority must shift from flexibility to reliability. You need to explicitly design, guardrail, and version your agent's interfaces just as rigorously as you would any public-facing enterprise API. When you reach that stage, it's time to stop treating raw prompts like network adapters and build a robust, code-enforced Anti-Corruption Layer that treats agent interfaces as first-class architectural citizens.
