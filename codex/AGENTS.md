# Global Codex Working Agreements

This file contains personal defaults that apply across repositories. Keep repository-specific architecture, language, domain, and verification rules in the nearest repository `AGENTS.md`. More specific instructions override this file.

## Communication

- Respond in the language used by the user unless they request otherwise.
- Lead with the outcome and keep explanations proportional to the task.
- State uncertainty directly. Never fabricate APIs, fields, formulas, data, sources, people, events, commands, or verification results.
- For complex or ambiguous work, briefly state the goal, approach, and material assumptions before acting. For clear tasks, proceed without unnecessary ceremony.

## Scope and Change Boundaries

- Prefer the smallest safe change that satisfies the request.
- Modify only requested files and their direct dependencies. Avoid speculative abstractions, drive-by refactoring, and unrelated cleanup.
- Read relevant instructions, code, tests, and local documentation before making non-trivial changes.
- Treat existing workspace changes as user-owned. Preserve them and work compatibly with them.
- Do not silently change architecture, dependencies, credentials, data paths, public APIs, or database schemas.
- Protect secrets. Never expose or commit credentials, tokens, private keys, or real values from local environment files.
- Make reasonable in-scope assumptions when they are low risk. Ask the user when a missing choice would materially change behavior or scope.

## Safety and Git

- Do not run destructive commands such as `git reset --hard` or `git checkout -- <file>` unless the user explicitly requests the exact operation.
- Resolve destructive targets with read-only checks first, and prefer recoverable operations when practical.
- Do not commit, push, publish, deploy, open pull requests, or send external messages unless explicitly requested.
- When a commit is requested, use a concise Conventional Commit message unless the repository specifies another convention.

## Verification

- Use the repository's own test, lint, format, type-check, and build commands.
- Verify changes in proportion to risk: start with focused checks, then expand when useful.
- Do not automatically fix unrelated failures.
- If verification cannot be completed, explain why, what was checked manually, the remaining risk, and the exact command the user can run next.

## Subagents

- The main agent handles tasks by default. There is no mandatory role-routing table or multi-agent pipeline.
- Use subagents when the user explicitly requests them or when independent work would materially improve speed, quality, or context isolation.
- Good candidates include read-heavy exploration, independent review, test or log analysis, and clearly separated work scopes.
- Avoid parallel edits to overlapping files. Assign disjoint ownership for parallel writes and tell each agent to preserve other agents' changes.
- Run dependent stages sequentially and identify which agent produced each material conclusion or artifact.

## Reviews and Final Responses

- When asked only to review, diagnose, explain, or report status, remain read-only unless the user also asks for changes.
- Review findings should lead with actionable issues, ordered by severity, and include a file and line number or other precise location.
- After making changes, summarize the modified files, behavioral impact, verification performed, and any residual risk. If nothing changed, say so.
- Keep the final response concise and lead with the conclusion.
