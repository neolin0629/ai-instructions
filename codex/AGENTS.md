# Global Codex Working Agreements

Personal defaults that apply across repositories. Keep repository-specific architecture, language, domain, data, and verification rules in the nearest repository `AGENTS.md`; more specific instructions override this file.

## Communication

- Respond in the user's language unless they request otherwise.
- Lead with the outcome, keep explanations proportional to the task, and state material uncertainty directly. Prefer plain prose; use lists and tables when they help.
- When explaining complex material, use short sentences, one idea per sentence, active voice, and one term per concept throughout.
- Use a diagram (Mermaid) or table when it shows relationships, flows, states, or data comparisons more clearly than prose. When the user wants to understand a complex system or dataset rather than change it, you may offer a disposable HTML page; do not generate one by default.
- Comments, commit messages, and document prose default to Chinese; follow an explicit language request or the repository's established convention instead when applicable.
- Keep identifiers, log messages, config keys, schema names, and file names in English; preserve existing technical names.

## Scope and Authorization

- Make the smallest safe change that completes the requested outcome, including necessary dependencies and affected documentation. File names mentioned in a request are not an exhaustive allowlist unless the user makes them one. Avoid speculative abstractions, unrelated refactoring, and drive-by cleanup.
- Treat existing workspace changes as user-owned. Do not revert, stash, or overwrite them to make your changes apply cleanly.
- Resolve routine implementation choices from the request and repository. Ask only when a missing decision materially affects scope, compatibility, data safety, or external side effects and cannot be inferred; explain material changes.
- Carry authorized work through the requested outcome, not just a first implementation. Resolve failures caused by the change and complete applicable verification before handing back. State low-risk assumptions and continue without repeating authorization requests; while awaiting a necessary decision, continue independent work.
- Review-only, diagnostic, and explanation requests stay read-only unless changes are also requested.
- Protect secrets. Never expose or commit credentials, tokens, private keys, or real values from local environment files.
- Do not commit, push, publish, deploy, open pull requests, or send external messages unless explicitly requested. When a commit is requested, use a concise Conventional Commit message unless the repository specifies otherwise.

## Verification and Delivery

- Use the repository's required checks and verification proportionate to risk. Passing checks completes verification, not any remaining requested deliverables. Repeat or broaden checks only for new changes, failures, or unresolved risks; report unrelated failures without automatically fixing them.
- Do not suppress errors or weaken checks to obtain a pass. Add tests for meaningful behavior or regressions, not merely to mirror a reversible, low-impact edit.
- Report actual verification commands and results. If verification cannot complete, explain why, what was checked manually, the remaining risk, and the exact command the user can run next.
- Return explanations and proposals in chat by default. Create or update files when they are a requested deliverable or necessary to complete the task; choose a conventional path when none is supplied. Avoid unsolicited planning documents.
- After changes, summarize modified files, behavioral impact, verification, and residual risk. When a non-trivial change alters a call chain or data flow, add a before/after diagram or table so it can be reviewed quickly.

## Context and Skills

- Load only skills and supporting references whose scope matches the current task. Use `architect` for design deliverables, `product-manager` for requirement briefs, `pro-writer` for professional documents, and `quant-guardrails` for quant behavior and its storage decisions. Ordinary implementation does not require a design or requirements skill; everyday writing needs no writing skill. Loading a skill does not require a subagent.
- Read project documentation relevant to the change: architecture for service boundaries, data documentation for schema changes, deployment guidance for releases. Do not require a full repository map or unrelated documents before a small edit.
- Use relevant skills within the user's authorized scope. User instructions take precedence over skill guidelines, subject to higher-priority instructions and enforced permissions. If a skill blocks progress, cite the exact file and instruction and explain the conflict.
- For Python dependency management, use `uv` unless the repository already uses another tool. Keep project-specific stack, lint, typing, and coverage rules in the project configuration or its `AGENTS.md`.

## Subagents

- The main agent handles tasks by default; there is no mandatory routing table or multi-agent pipeline.
- Use subagents when requested or when a bounded investigation or independent review would materially improve speed, quality, or context isolation. Keep decisions that need back-and-forth with the user in the main conversation; do not require a review agent after every change.
- Give each subagent a bounded task, relevant context, completion criteria, and clear file ownership. Use `code-review` for independent review. Keep parallel write scopes disjoint; integrate dependent results sequentially and identify their source.
