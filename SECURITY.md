# Security Policy

## Supported versions

This project is pre-1.0 and under active development. Security fixes are applied to the
`main` branch only. There are no maintained release branches yet.

| Version | Supported |
|---|---|
| `main` (latest) | ✅ |
| tagged pre-releases | ❌ |

## Reporting a vulnerability

**Please do not open a public issue for security vulnerabilities.**

Report privately through GitHub:

1. Go to the repository's **Security** tab.
2. Click **Report a vulnerability** (Private Vulnerability Reporting).
3. Include:
   - A description of the issue and its impact.
   - Steps to reproduce (a minimal request sequence, `curl` commands, or a script).
   - The affected endpoint(s), file(s), or component(s).
   - Any suggested remediation.

If you cannot use GitHub's form, contact a maintainer listed in
[`.github/CODEOWNERS`](.github/CODEOWNERS) directly and ask for a private channel.

### What to expect

- **Acknowledgement:** within 3 business days.
- **Assessment & triage:** within 7 business days, including a severity rating and an
  intended fix timeline.
- **Disclosure:** coordinated. We will agree on a disclosure date with you and credit you
  in the fix notes unless you prefer to remain anonymous.

## Scope

This repository is **only the persistence API**. It stores:

- Employer-set employee configuration (names, base salaries, thresholds, bonus rates).
- Payroll epochs and claim bundles (nullifiers, Groth16 proofs, public signals, amounts
  in stroops) — data that becomes public on-chain.
- A claim-status mirror.

The following are **explicitly out of scope for this repo** (report them against the
[main MeritPay repository](https://github.com/Samuel1505/MeritPay) instead):

- The Circom circuits and their trusted setup.
- The Soroban `groth16_verifier`, `payroll`, and `claim` contracts.
- Frontend proof generation and wallet signing.
- The soundness of the ZK scheme itself.

### In scope for this repo

Examples of issues we want to hear about:

- Authentication/authorization bypass (e.g. reading or modifying another
  `employerWallet`'s data). Note: the MVP currently has **no auth** — this is a known
  limitation (see the issue tracker), not a vulnerability report. Reports that go beyond
  those known gaps are welcome.
- Injection (SQL via Prisma raw queries, etc.).
- Denial of service through unvalidated input or unbounded work per request.
- Sensitive data exposure in logs or error responses.
- Dependency vulnerabilities with a practical exploit path in this codebase.

## Known limitations (not vulnerabilities)

The README's "Limitations (MVP scope)" section documents accepted trade-offs for the
current hackathon-stage build. These are tracked in the issue tracker and do not need
a security report:

- No wallet-signature authentication yet.
- No rate limiting or security headers yet.
- SQLite by default.
