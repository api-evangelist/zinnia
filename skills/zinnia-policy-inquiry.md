---
name: zinnia-policy-inquiry
description: Read the full in-force picture of a life or annuity policy administered by Zinnia - overview, coverage, riders, parties, fund distribution, systematic programs, transaction history and version history - using the Zinnia Policy Service API. Read-only; nothing here changes a policy.
api: Zinnia Policy Service API
generated: '2026-09-13'
method: generated
source: openapi/zinnia-policy-service-openapi.yml
operations:
  - Policy Search
  - Policy Overview
  - Coverage
  - Riders
  - Parties
  - Distribution of Investments
  - Features
  - Systematic Programs
  - Transactions
  - Transaction By Id
  - Policy Versions
  - Policy By Version
  - Metrics
---

# Policy inquiry

Base: `https://api.zinnia.io`. Every policy is addressed by `planCode` + `policyNumber`.

## Finding the policy

`POST /policy/v1/policies/search` (`Policy Search`) when you do not already hold the composite
key. If you do hold it, go straight to the overview.

## The core read

`GET /policy/v1/policies/{planCode}/{policyNumber}` (`Policy Overview`) is the single call that
answers "what is this policy". Everything below hangs off the same path and is safe to fan out
in parallel:

| What you need | Operation | Path suffix |
|---|---|---|
| Coverage amounts | `Coverage` | `/coverage` |
| Riders on the contract | `Riders` | `/riders` |
| Owner, insured, beneficiaries, payor | `Parties` | `/parties` |
| Fund allocation | `Distribution of Investments` | `/distribution` |
| Product features | `Features` | `/features` |
| Active SWPs | `Systematic Programs` | `/systematicPrograms` |
| Transaction history | `Transactions` | `/transactions` |
| Illustration input payload | `Policy Illustration Information` | `/lpspayload` |

## History

`GET .../versions` (`Policy Versions`) returns the version history and accepts `limit` and
`transactionType`. `GET .../versions/{version}` (`Policy By Version`) returns the policy as it
stood at one version. This is the audit trail — use it rather than reconstructing state from
transactions.

## Reconciling your own reference

If you hold your own identifier rather than Zinnia's, `Transaction by External Transaction Id`
(`/transactions/{externalIdType}/{externalId}`) and the matching systematic-programs variant let
you resolve it without a search.

## Notes

- Read-only. Idempotency and reversibility do not apply to this skill.
- Authorization accepts tokens from the Zinnia Auth0 tenant and, on this API only, from
  `https://login.mypolicyview.com/` — the consumer-facing MyPolicyView tenant.
- Pagination is `limit` only; there is no cursor and no total count.
