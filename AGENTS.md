# finance-assistant-planning — Agent Guide

## Project Identity

Personal finance assistant. User types an expense in natural language → LLM parses → editable draft → user confirms intent. Firefly III is final ledger; app stores assistant workflow/context, not a ledger replacement.

MVP tracer bullet: capture one already-paid THB expense through local draft → confirm intent. No auth, no OpenAPI, no Firefly write yet.

## Repo Layout

```
finance-assistant-planning/          # Root orchestration repo (no src/)
  AGENTS.md                          # This file
  CONTEXT.md                         # Glossary & domain terms
  README.md                          # Placeholder
  .gitmodules                        # Defines 2 submodules
  finance-assistant-api/             # Backend submodule (Bun + Elysia)
    src/index.ts                     # Entrypoint
    bun run dev                      # --watch mode on :3000
  finance-assistant-web/             # Frontend submodule (React 19 + Vite)
    src/main.tsx                     # Entrypoint
    src/test/smoke.test.tsx          # Vitest unit tests
    e2e/mvp.spec.ts                  # Playwright E2E tests
    bun run test / bun run e2e
```

Both submodules track `main` (see `.gitmodules`). Each has its own `package.json`, `bun.lock`, `.gitignore`.

## Commands

### Root
- `git status --short` — check overall state
- `git submodule status` — verify submodule commits
- `gh issue list --state open` — see active issues
- `gh issue view <number>` — read issue body

### finance-assistant-api/
- `bun install`
- `bun run dev`

### finance-assistant-web/
- `bun install`
- `bun run dev`
- `bun run build`
- `bun run typecheck`
- `bun run lint`
- `bun run test`
- `bun run e2e`

## Definition of Done

A task is done when:
1. Changes work end-to-end (manual validation or passing tests).
2. Relevant tests pass (`bun run test` in web, e2e if applicable).
3. No secrets, tokens, or credentials in code.
4. Submodule pointers committed at root if submodule changes are part of the task.
5. `git status --short` is clean (no unexpected dirty files).

## Hard Constraints

- **No new tooling/deps without asking.** No adding new npm packages, language servers, CI systems, or infrastructure without explicit user approval. Includes playwright plugins, vite plugins, eslint plugins, etc.
- **Firefly token backend-only.** Never expose token to frontend. Never let LLM write directly to Firefly.
- **Draft never written to Firefly in MVP.** Local-only confirmation proving intent.
- **LLM parses; backend validates.** Do not skip backend validation for parsed output.
- **Firefly III is system of record.** Do not model app as competing ledger.
- **No auth in MVP.** No user login, no session, no secure shell yet.
- **MVP scope:** already-paid THB asset-account expenses only. Exclude income, transfers, credit cards, debts, installments, foreign cash.

## Git & Submodule Rules

- **Prefer scoped edits.** Change files in one submodule per commit when possible. Cross-submodule edits are allowed when the task demands it (e.g. wiring a new feature through API → web).
- **Check status in every touched submodule.** Before committing, run `git status --short` inside each modified submodule and at root. Unexpected dirty files mean something is wrong.
- **Submodule pointers must be committed** at root when submodule changes are part of the task. `git status --short` shows `M` for modified submodules; these need `git add` + commit in the superproject.
- **Never force-push, amend, or rebase** shared branches unless explicitly asked.
- **Stage only intended files.** Inspect `git diff` before staging. Do not commit generated files (dist/, node_modules/, .tanstack/, playwright-report/).
- **Commit messages:** Conventional Commits (`feat|fix|refactor|build|ci|chore|docs|style|perf|test`).

## Security & Secrets

- `.env` files in submodules are gitignored but may exist locally. Never read, write, or expose their contents.
- No API keys, tokens, or credentials in source code, commit messages, or file contents.
- If you encounter a secret, stop and report it immediately.

## MVP Boundaries (from CONTEXT.md)

- **Core-value tracer bullet:** smallest end-to-end slice proving product value before auth, OpenAPI, Firefly cache, or ledger sync hardening.
- **Confirmed draft:** locally accepted draft proving user intent. No Firefly account/category selection. No Firefly transaction write.
- **Local MVP draft store:** browser-owned (localStorage/local state). No backend persistence/auth/security until core capture→draft→confirm is proven.
- **MVP parser:** LLM-backed one-shot parse into editable draft. Manual field editing handles wrong AI output.
- **Draft correction path** (multi-turn edit) is future-only; not part of current MVP.

## Escalation

If you cannot determine the right course of action from this file, repo files, or standard tooling:
1. Open a question with specific options and tradeoffs.
2. If blocked by ambiguity in MVP scope, reference issue list (`gh issue list --state open`) and this file.
3. If you suspect a security concern, stop and report immediately — do not attempt to fix silently.
