---
categories: tooling api mock testing quality documentation
date: 2026-04-10 00:00:00
layout: post
title: "Chameleon: a mock server built from your schemas"
---

Anyone who has worked on a frontend or mobile app knows this: waiting for a backend to be "ready" is one of the fastest ways to slow everything down.<br>
And even when the backend exists, what you really need for productive development is often missing: realistic responses, reproducible edge cases, coherent data across endpoints, and a simple way to switch between mock and real upstream behavior.

That's where [**Chameleon**](https://github.com/EmanueleMinotto/chameleon) comes in: an open-source mock/faker server that generates credible responses directly from your schemas, with a workflow designed for both local development and deployment (including Vercel). <!--more-->

<img src="https://raw.githubusercontent.com/EmanueleMinotto/chameleon/main/chameleon.png" alt="Chameleon cover" align="right" width="220">

## What is Chameleon?

Chameleon is a server that reads your schemas (OpenAPI, GraphQL SDL, Postman collections, Pact contracts, static JSON) and serves fake but realistic responses without forcing you to modify the schemas themselves.

Full documentation is available in the wiki: [**chameleon wiki**](https://github.com/EmanueleMinotto/chameleon/wiki).

---

### The Real Problem: Hand-Made Mocks Do Not Scale

Manual mocks tend to degrade quickly:

- they become fixed, unrealistic datasets
- they do not evolve with the schemas
- they introduce logical forks (mock mode vs real mode) that are hard to govern
- they do not help when you need variations (pagination, errors, optional fields, combinations)

In short: they work while the system is small, then they turn into noise.

---

## Why Chameleon Is Different

These are the design choices that make it compelling:

### Schema-First, Without Polluting the Schema

Chameleon starts from schemas but keeps annotations external: instead of adding proprietary extensions or inline comments, it uses separate YAML files to map fields to generators ([Faker.js](https://fakerjs.dev/) or custom TypeScript generators).

This detail matters because:

- you do not lock your contract into a single tool
- you reduce friction across teams and consumers
- you can iterate on mocks without touching your source of truth

### Realistic Responses, Not Just Valid Ones

**"Valid" does not mean "credible".**<br>
Chameleon aims to generate data that looks like what you will actually see in production, often inferring sensible generators from field name, type, and format (while still letting you override where needed).

### Proxy Mode: Mock and Upstream on the Same Port

In practice, the most useful split is often not "mock vs real" by environment.<br>
It is "mock vs real" per request.

Chameleon supports a proxy mode where you can choose per request whether to serve a mock response or forward to a real upstream (typically via an HTTP header). This enables:

- targeted debugging
- A/B comparisons between responses
- gradual migrations
- pragmatic fallbacks when part of the system is not stable yet

### Easy Deployment (Including Vercel)

When mocks are needed by multiple people (frontend, QA, partners, demos), distribution becomes the real challenge.<br>
Chameleon is built to be deployable easily, with shared logic across local CLI usage and serverless deployment.

---

## A Practical Workflow (That Actually Works)

A practical way to use Chameleon in a team:

- **Contract**: keep an OpenAPI/GraphQL schema versioned in your repository
- **Annotations**: add a YAML layer for business-like generators (corporate emails, coherent IDs, plausible dates)
- **Overlays**: where determinism is needed, merge static JSON on top (for edge cases or demos)
- **Proxy**: enable upstream for critical endpoints and mock the rest while backend work progresses
- **CI / preview**: run a deploy (even temporary) for demos and manual validation

The result is an environment that does not block other teams and still stays faithful to the contract.

---

## When to Use It (And When Not To)

Chameleon shines when:

- you are building UI before the backend (or in parallel)
- you want QA and manual testing with credible data
- you need to simulate errors, pagination, optional fields, and variants
- you want to keep the contract as source of truth without fragile mocks

It is less suitable if:

- you do not have schemas (or they are unreliable)
- your goal is to test real backend logic (you need an integrated environment, not a faker)

---

## Where to Start

- **Repository**: [github.com/EmanueleMinotto/chameleon](https://github.com/EmanueleMinotto/chameleon)
- **Documentation**: [github.com/EmanueleMinotto/chameleon/wiki](https://github.com/EmanueleMinotto/chameleon/wiki)

The best path is to start with "Getting Started" in the wiki, then read "Project Structure" and "Annotations": these three pages move you from "it works" to "it produces responses that look real".

---

## Conclusion

The difference between a useful mock server and one that creates technical debt is the same difference between "valid data" and "credible data", between "a JSON file" and "a contract".

Chameleon tries to make that difference systemic: schema-first design, external annotations, realistic generation, proxy mode, and easy deployment.

If you often work ahead of the backend, or you want to make contract quality a team asset, Chameleon is worth exploring.
