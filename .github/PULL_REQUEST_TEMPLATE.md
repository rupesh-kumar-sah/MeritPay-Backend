<!--
Thanks for contributing to MeritPay Backend!
Please fill in every section. PRs that leave the template blank may be closed.
-->

## Summary

<!-- What does this PR do, and why? 1–3 sentences. -->

## Related issue

<!-- e.g. Closes #12 -->
Closes #

## Type of change

- [ ] `fix` — bug fix (non-breaking)
- [ ] `feat` — new feature (non-breaking)
- [ ] `feat!` / `fix!` — breaking change (API or DB shape changes)
- [ ] `docs` — documentation only
- [ ] `chore` / `ci` / `refactor` / `test` / `perf`

## What changed

<!-- Bullet the concrete changes. Call out any new env vars, endpoints, or dependencies. -->

-

## Database changes

- [ ] No schema change
- [ ] Includes a Prisma migration (`prisma/migrations/…`) committed in this PR
- [ ] Breaking data change — describe the upgrade path:

## How this was tested

<!--
Automated tests once the suite exists, otherwise the exact manual steps.
Include commands and their output.
-->

```
```

## Checklist

- [ ] Change is focused and single-purpose
- [ ] `npm run build` passes
- [ ] `npm run lint` / `npm test` pass (if present in the repo)
- [ ] New/changed request input is validated in `src/lib/validation.ts`
- [ ] Docs updated (`README.md`, `.env.example`, `CONTRIBUTING.md`) as needed
- [ ] Conventional Commit message(s)
- [ ] No secrets, `.env`, or `prisma/dev.db` committed
- [ ] I have read the [Contributing guide](../CONTRIBUTING.md)
