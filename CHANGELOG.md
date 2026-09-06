# Changelog

All notable changes to this project are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- Open-source project docs: `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, `SECURITY.md`,
  `SUPPORT.md`, `LICENSE` (MIT), issue/PR templates, `CODEOWNERS`, Dependabot config.

## [0.1.0] - unreleased

Initial development version (`package.json` `version` is `0.1.0`; no git tag or
published release yet).

### Added
- Express + TypeScript API bootstrap (`src/index.ts`).
- Prisma schema and initial SQLite migration for `Employee`, `PayrollEpoch`,
  `ClaimEntry`.
- `GET /health` liveness endpoint.
- Employee configuration routes: `POST /employees` (upsert), `GET /employees`,
  `DELETE /employees/:id`.
- Payroll epoch + claim bundle routes: `POST /epochs`, `GET /epochs/:epoch`,
  `GET /epochs/:epoch/claims/:employeeId`.
- Claim status routes: `POST /claims/:nullifier/complete`, `GET /claims/:nullifier`.
- `zod` request validation schemas (`src/lib/validation.ts`).
- Centralized error handling middleware mapping `ZodError` / Prisma errors / `HttpError`
  to HTTP responses.

[Unreleased]: https://github.com/MeritPay/MeritPay-Backend/commits/main
