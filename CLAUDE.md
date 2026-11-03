# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Context

This is the **frontend** half of the JP project (repo: `Krauszie/jp-fe-vue`). It lives in `frontend/` next to a separate `backend/` directory, which has its own repo. The backend's API conventions aren't set yet; don't invent endpoints or response shapes.

Stack: Vue 3 (Composition API, `<script setup lang="ts">`), TypeScript, Vite. Scaffolded with `create-vue` and TypeScript only. Pinia handles state (registered in `src/main.ts`) and Tailwind CSS v4 handles styling (via `@tailwindcss/vite`, imported at the top of `src/assets/main.css`; there's no `tailwind.config` file, so configure it in CSS with `@theme`). There's no router, test runner, ESLint or Prettier yet.

## Commands

Run from `frontend/`:

- `npm run dev`: Vite dev server with HMR (Vue DevTools plugin enabled)
- `npm run build`: runs `type-check` and `build-only` in parallel (via `npm-run-all2`); output goes to `dist/`
- `npm run type-check`: `vue-tsc --build`. This is the only static check available; run it after changes
- `npm run preview`: serve the production build locally

Node `^22.22.2 || >=24.15.0` is required (see `engines` in `package.json`).

## Structure & conventions

- `@/` aliases to `src/` (configured in both `vite.config.ts` and `tsconfig.app.json`; keep them in sync if you change it).
- TS config is split: `tsconfig.app.json` covers browser code in `src/`, `tsconfig.node.json` covers Vite/tooling config files, and `tsconfig.json` just references both.
- `noUncheckedIndexedAccess` is on, so indexed array/record lookups are typed `T | undefined` and need narrowing.
- Files under `src/**/__tests__/` are excluded from the app tsconfig (reserved for future tests).
- Global styles come in through `src/assets/main.css` (imported in `src/main.ts`).
- `HelloWorld.vue`, `TheWelcome.vue`, `WelcomeItem.vue` and `components/icons/*` are scaffold demo files. Delete or replace them freely.

## Spec-Driven Development (spec-kit)

This project uses [GitHub spec-kit](https://github.com/github/spec-kit) (v1.0.14.dev0, Claude integration, PowerShell scripts). Build features through its workflow, not ad hoc:

1. `/speckit-constitution`: set or amend project principles in `.specify/memory/constitution.md`.
2. `/speckit-specify <feature description>`: write `specs/NNN-<feature>/spec.md` (the *what* and *why*, with no tech choices).
3. `/speckit-clarify` (optional): resolve ambiguities in the spec. Run it before planning.
4. `/speckit-plan`: write `plan.md`, plus `research.md`, `data-model.md`, `contracts/` and `quickstart.md` as needed (the *how*).
5. `/speckit-tasks`: write `tasks.md`, ordered by dependency and grouped by user story.
6. `/speckit-analyze` (optional): check spec, plan and tasks for consistency before implementing.
7. `/speckit-implement`: carry out `tasks.md`, ticking off tasks as they finish.

Optional extras: `/speckit-checklist` (requirement-quality checklists), `/speckit-converge` (append unbuilt work as tasks after implementing), `/speckit-taskstoissues` (turn tasks into GitHub issues).

Rules:

- **Specs come first.** For any non-trivial feature, put a spec in `specs/` before writing code. Small fixes and chores can skip the workflow.
- **The constitution wins.** Specs, plans, tasks and implementation must follow `.specify/memory/constitution.md`. Change a principle with an explicit `/speckit-constitution` amendment, never by quietly ignoring it. The constitution is still an unfilled template, so fill it in before the first `/speckit-plan`.
- **Keep the constitution and this file in sync.** Encode the stack and the "ask before adding major dependencies" rule below as constitution principles too. A plan must not add a router, HTTP client, UI library or test framework without the user's approval.
- **Don't invent the backend contract.** Plans may draft API shapes in `contracts/`, but mark them as proposals until the backend repo confirms them (see Context).
- **Tests are opt-in.** No test runner exists yet. `/speckit-tasks` generates test tasks only when the spec asks for them, and adding a test framework needs approval.
- **Verify every task.** After each implementation task, run `npm run type-check`.
- **The active feature lives in `.specify/feature.json`.** `/speckit-specify` writes it and downstream commands read it. Spec directory names are independent of git branch names.
- **Leave generated tooling alone.** Don't hand-edit `.specify/scripts/`, `.specify/templates/` or `.claude/skills/speckit-*`. They belong to spec-kit and are replaced on upgrade (`specify init --here --force --integration claude --script ps`). Edit the constitution and `specs/` freely.

## Local rules

- Use `<script setup lang="ts">` in all new SFCs; type props/emits with `defineProps<...>()` / `defineEmits<...>()`.
- Use the `@/` alias for imports from `src/` rather than deep relative paths.
- Ask before adding major dependencies (router, state management, UI library, HTTP client, test framework), because the project's choices aren't made yet.
- Personal, uncommitted notes belong in `CLAUDE.local.md` (listed in `.gitignore`).
