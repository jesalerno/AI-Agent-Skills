# AI Coding Agent Skills & Instructions

A curated collection of specification documents that define the operating behavior, coding standards, and definition of done for AI-powered software development agents. These documents are designed to be used as system-level instructions (custom agent modes, skills, or rules) in AI coding assistants and similar tools.

## Documents

### Core Programming

| Document | Version | Description |
|---|---|---|
| [Programming Agent Skills (Generalist)](Programming-Generalist-Agent-Skills.md) | 1.2 | **Parent document.** Language-agnostic operating principles, coding standards, and definition of done for an AI programming agent targeting C/C++, Python, Swift, JavaScript/TypeScript, Go, and Rust. All language-specific documents inherit from this one. |
| [Planning Agent Skills](Planning-Agent-Skills.md) | 1.5 | Requirements analysis and specification agent. Decomposes requirements into a three-level hierarchy (Epics, Stories, Tasks), produces validated JSON specifications, performs STRIDE threat modeling, captures NFRs, and builds a traceable risk register. |
| [Evaluator Agent Skills](Evaluator-Agent-Skills.md) | — | Evaluation and benchmarking agent. Defines KPIs, scoring rubrics, and structured evaluation workflows for assessing AI agent outputs. |
| [Tech Doc Writer Agent Skills](Tech-Doc-Writer-Agent-Skills.md) | 1.2 | Technical documentation agent. Produces API reference docs, READMEs, runbooks, architecture guides, and onboarding docs from source code or verbal descriptions. Outputs as Markdown, HTML, PDF, or Word Doc. |

### Language- and Domain-Specific

| Document | Version | Scope |
|---|---|---|
| [Programming — Python](Programming-Python-Agent-Skills.md) | 1.2 | Python applications, libraries, CLIs, and APIs. Covers type annotations (`mypy --strict`), `uv` package management, `pytest`, `ruff`, project layout, and CI templates. |
| [Programming — C and C++](Programming-C-Cpp-Agent-Skills.md) | 1.1 | General-purpose, embedded, systems, and safety-critical C/C++ development. Includes cross-standard guidance for MISRA C++:2023, AUTOSAR C++14, and project-specific deviation processes. |
| [Programming — Full-Stack Web](Programming-Full-Stack-Web-Agent-Skills.md) | 1.1 | Full-stack web applications. Frontend (TypeScript, React, Next.js, Vite), backend (Go, Python, Node.js), data layer (PostgreSQL, Redis, Drizzle/Prisma), infrastructure (Docker, OpenTelemetry), and monorepo project layouts. |
| [Programming — Swift](Programming-Swift-Agent-Skills.md) | 1.1 | Swift on Apple platforms (iOS, iPadOS, macOS, tvOS, watchOS, visionOS). Covers SwiftUI, Approachable Concurrency (Swift 6.2), Swift Testing, SwiftLint, and HIG compliance. |
| [Swift HMI / UIControl Guidelines](Swift-HMI-UIContols-Guidelines.md) | — | Guidance for developing custom `UIControl`-based components in UIKit. Covers color management, control states, accessibility (VoiceOver, Dynamic Type, Reduce Motion), Dark Mode, Liquid Glass, pointer interactions, RTL layout, and SF Symbols. |

### Claude Skills (`claude-skills/`)

Installable skill packages for Claude (Cowork and Claude Code). Each skill folder contains a `SKILL.md` with normative operating instructions and a `references/` folder for supplementary content.

| Skill | Description |
|---|---|
| [evaluator-agent](claude-skills/evaluator-agent/) | Evaluation and benchmarking agent. Defines KPIs, scoring methodology, and structured evaluation workflows. |
| [software-planner](claude-skills/software-planner/) | Requirements analysis and planning agent. Produces JSON specifications with Epics, Stories, Tasks, STRIDE threat model, NFR register, and risk register. |
| [tech-doc-agent](claude-skills/tech-doc-agent/) | Technical documentation agent. Produces API reference docs, READMEs, runbooks, architecture guides, and onboarding docs. Supports Markdown, HTML, PDF, and Word Doc output. |

## Document Hierarchy

```
Programming Agent Skills (Generalist) v1.2       ← base document
├── Planning Agent Skills v1.5                    ← requirements & specification
├── Evaluator Agent Skills                        ← evaluation & benchmarking
├── Tech Doc Writer Agent Skills v1.2             ← technical documentation
├── Programming — Python v1.2                     ← extends generalist for Python
├── Programming — C and C++ v1.1                  ← extends generalist for C/C++
├── Programming — Full-Stack Web v1.1             ← extends generalist for web
├── Programming — Swift v1.1                      ← extends generalist for Swift
│   └── Swift HMI / UIControl Guidelines          ← supplementary reference
```

Where a language-specific document conflicts with the generalist, the language-specific document takes precedence within its defined scope.

## Key Design Principles

All documents share these foundational principles:

- **Primary Source Discipline** — agents must fetch and cite authoritative documentation rather than rely on training data alone.
- **RFC 2119 Keyword Semantics** — normative requirements use MUST, SHOULD, MAY per [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119).
- **Minimal-Diff Discipline** — changes should be the smallest correct modification that resolves the stated issue.
- **Security as a First-Class Concern** — security is treated as always-present, not as an optional addition.
- **Definition of Done** — each document specifies concrete, verifiable completion criteria.

## Usage

These documents can be used as:

- **Claude Skills** — install the `.skill` packages from `claude-skills/` directly into Claude (Cowork or Claude Code).
- **Cursor Rules** — place in `.cursor/rules/` or reference as agent skills.
- **GitHub Copilot Custom Instructions** — use as custom agent mode definitions.
- **System Prompts** — embed in any LLM-based coding assistant's system prompt or instruction set.

Select the generalist document as a baseline, then layer the appropriate language-specific document(s) on top for your project's stack.

## License

This project is licensed under the [MIT License](LICENSE).

Copyright (c) 2026 John Salerno
