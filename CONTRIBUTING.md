# Contributing to MeritPay Backend

Thanks for your interest in contributing! This service is the persistence API for
[MeritPay](https://github.com/Samuel1505/MeritPay), a zero-knowledge, performance-linked
payroll system on Stellar Soroban. It stores employer employee-config, claim bundles, and
claim status that would otherwise live only in browser `localStorage`.

Please also read the [Code of Conduct](CODE_OF_CONDUCT.md) and, for anything
security-sensitive, [SECURITY.md](SECURITY.md).

---

## Table of contents

- [Ways to contribute](#ways-to-contribute)
- [Project scope & non-goals](#project-scope--non-goals)
- [Development setup](#development-setup)
- [Project layout](#project-layout)
- [Making a change](#making-a-change)
- [Database & Prisma](#database--prisma)
- [Coding standards](#coding-standards)
- [Commit messages](#commit-messages)
- [Tests](#tests)
- [Opening a pull request](#opening-a-pull-request)
- [Finding something to work on](#finding-something-to-work-on)

---

## Ways to contribute

- **Pick up an issue** from the
  [GitHub issue tracker](https://github.com/MeritPay/MeritPay-Backend/issues) —
  look for the `good first issue` and `help wanted` labels.
- **Report a bug** using the bug report template.
- **Propose a feature** using the feature request template — but check
  [project scope](#project-scope--non-goals) first.
- **Improve docs** — the README, this file, or inline comments.

For anything larger than a bug fix, please open an issue to discuss the approach before
writing code. It saves everyone a wasted PR.

## Project scope & non-goals

This backend deliberately stays small. It **persists state the browsers would otherwise
lose** and nothing more. Soroban remains the source of truth for proof validity and
nullifier state.

**In scope:** employer employee-config, payroll epochs + claim bundles, claim-status
mirror, the auth/validation/observability/deployment concerns needed to run that safely.

**Out of scope:**

- Storing raw KPI inputs (hours worked, sales figures). These never leave the employee's
  browser by design — persisting them here defeats the purpose of the ZK system.
- Generating or verifying Groth16 proofs. That is the frontend's and the Soroban
  contracts' job.
- Being a security boundary for whether a nullifier is spent — that check is always
  on-chain (`payroll.is_nullifier_spent`).
- Circuits and smart contracts — those live in the
  [main MeritPay repo](https://github.com/Samuel1505/MeritPay).

## Development setup

**Requirements:** Node.js 20.x and npm. (A `.nvmrc` / `engines` field may be present —
if so, `nvm use`.)

```bash
git clone https://github.com/MeritPay/MeritPay-Backend.git
cd MeritPay-Backend
npm install
cp .env.example .env
npm run db:migrate      # applies prisma/migrations to a local SQLite db
npm run dev             # http://localhost:4000
```

Verify it's up:

```bash
curl -s http://localhost:4000/health   # -> {"status":"ok"}
```

### Environment variables

| Var | Default | Purpose |
|---|---|---|
| `PORT` | `4000` | HTTP port |
| `DATABASE_URL` | `file:./dev.db` | Prisma connection string (SQLite locally) |
| `CORS_ORIGIN` | `*` | Allowed browser origin(s) |

Never commit `.env` or `prisma/dev.db` — both are gitignored.

## Project layout

```
src/
  index.ts               Express app bootstrap + app.listen
  db.ts                  PrismaClient singleton
  middleware/
    errorHandler.ts      Central error → HTTP status mapping (Zod / Prisma / HttpError)
  lib/
    validation.ts        zod request schemas — the single source of input truth
  routes/
    health.ts            GET /health
    employees.ts         employer employee-config CRUD
    epochs.ts            POST /epochs + claim-bundle reads
    claims.ts            claim-status mirror
prisma/
  schema.prisma          data model
  migrations/            committed migration history
```

## Making a change

1. **Fork** the repo (external contributors) or create a branch (maintainers).
2. Branch from `main` using a descriptive name:
   `feat/list-epochs-endpoint`, `fix/malformed-json-500`, `docs/contributing`.
3. Keep the change focused — one logical change per PR. If you find yourself writing "and
   also…", that's a second PR.
4. Follow the [CLAUDE.md](CLAUDE.md) principles that guide this codebase:
   **simplicity first, minimal impact, root-cause fixes, no temporary hacks.**
5. Update docs (`README.md`, `.env.example`, this file) in the same PR as the code they
   describe.

## Database & Prisma

- **Schema changes** go through Prisma migrations — never hand-edit the database.
  ```bash
  # edit prisma/schema.prisma, then:
  npm run db:migrate -- --name short_description_of_change
  ```
  Commit the generated folder under `prisma/migrations/`.
- Run `npm run db:generate` after pulling a schema change from someone else.
- `prisma/migrations/migration_lock.toml` is committed and pins the provider. Changing
  the database provider is **not** a one-line change — it requires regenerating the
  migration history against the new provider.
- Money/stroop values (`totalPayroll`, `amountStroops`) are stored as `String` on purpose
  to avoid JS number-precision loss. Keep them strings.

## Coding standards

- **TypeScript, `strict` mode.** No `any` without a comment justifying it.
- **All request input is validated with `zod`** in `src/lib/validation.ts`. Routes call
  `schema.parse(...)` and let errors propagate to `errorHandler` — don't hand-roll
  validation in a route.
- **Errors:** throw `HttpError(status, message)` for expected failures; let the central
  `errorHandler` map `ZodError` and Prisma errors. Don't `res.status(500)` in a route.
- **Async routes** must `try/catch` and call `next(err)` (the current pattern) — or use a
  wrapper if one is introduced.
- Match the surrounding style: 2-space indent, double quotes, semicolons, named exports
  for routers.
- A linter/formatter (ESLint + Prettier) is planned. Once present, `npm run lint` and
  `npm run format:check` must pass.

## Commit messages

This repo uses [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>: <short imperative summary>

<optional body explaining what and why>
```

Types in use: `feat`, `fix`, `docs`, `chore`, `test`, `refactor`, `perf`, `ci`.

Examples from history:

```
feat: add employee configuration routes
fix: return 400 for malformed JSON bodies
docs: add contributing guide
```

Do **not** add AI/tool attribution lines or `Co-authored-by` for tooling.

## Tests

A test suite (Vitest + supertest) is being introduced. Once it lands:

```bash
npm test            # run once
npm run test:watch  # watch mode
```

Guidelines:

- Every new route or behavior change needs at least one happy-path and one error-path
  test.
- Tests use a throwaway SQLite database — never point them at `prisma/dev.db`.
- A bug fix should come with a regression test that fails before the fix.

Until the suite exists, describe your manual verification steps in the PR description
(the exact `curl` commands and their output).

## Opening a pull request

1. Rebase on the latest `main`.
2. Make sure the build is clean: `npm run build` (and `npm run lint` / `npm test` once
   available).
3. Push and open a PR against `main`. Fill in the PR template completely.
4. Link the issue you're resolving (`Closes #123`).
5. A maintainer will review. Address feedback with additional commits (don't force-push
   mid-review unless asked); the branch is squash-merged.

**PR checklist:**

- [ ] Focused, single-purpose change
- [ ] `npm run build` passes
- [ ] Tests added/updated (or manual verification steps documented)
- [ ] Docs updated (`README.md`, `.env.example`, `CONTRIBUTING.md` as needed)
- [ ] Conventional Commit messages
- [ ] No secrets, `.env`, or `dev.db` committed

## Finding something to work on

Browse the [issue tracker](https://github.com/MeritPay/MeritPay-Backend/issues):

- **`good first issue`** — small, self-contained, well-specified.
- **`help wanted`** — larger pieces the maintainers would welcome help with.

Before starting non-trivial work, comment on the issue so effort isn't duplicated and the
approach can be sanity-checked. If there's no issue for what you want to do, open one
first.

---

By contributing, you agree that your contributions are licensed under the
[MIT License](LICENSE).
