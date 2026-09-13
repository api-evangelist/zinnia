---
name: zinnia-one-time-withdrawal
description: Take a one-time partial withdrawal from an in-force Zinnia-administered annuity or life policy, using the eligibility -> validation -> submit sequence the Zinnia Policy Transactions API requires. This is one of the four in-force transactions the Insured Retirement Institute standardized on Zinnia's Enterprise API framework.
api: Zinnia Policy Transactions API
generated: '2026-09-13'
method: generated
source: openapi/zinnia-policy-transactions-openapi.yml, openapi/zinnia-policy-service-openapi.yml
operations:
  - getEligibleTransactions
  - partialWithdrawalOneTimeEligibilityCheck1
  - partialWithdrawalOneTimeValidation1
  - partialWithdrawalOneTime1
  - Transaction By Id
---

# One-time partial withdrawal

A policy on the Zinnia platform is addressed by a composite key: `planCode` (the product plan
code) and `policyNumber`. You need both before you can do anything here.

## Before you act

- **There is no idempotency key on this API.** Nothing in the Zinnia contract deduplicates a
  replayed write, and this operation moves money. Do not retry `partialWithdrawalOneTime1`
  blind. If a submit times out, confirm the outcome with `Transaction By Id`
  (`GET /policy/v1/policies/{planCode}/{policyNumber}/transactions/{transactionId}`) or
  `Transaction by External Transaction Id` before sending anything again.
- Authenticate with an Auth0 machine-to-machine bearer token whose audience is the environment
  API host. See `authentication/zinnia-authentication.yml`.

## Steps

1. **Find out what the policy may do.** `GET /bpm/v1/policies/{cusip}/{policyNumber}/eligibleTransactions`
   (`getEligibleTransactions`) returns the transaction types this policy is eligible for. If a
   one-time partial withdrawal is not in that list, stop — the later calls will fail for a
   reason the policy already knows.
2. **Check eligibility for this specific transaction.**
   `POST /policy/v1/transactions/{planCode}/{policyNumber}/partialwithdrawalonetime/eligibilitycheck`
   (`partialWithdrawalOneTimeEligibilityCheck1`). This asks whether the policy is in a state that
   permits the withdrawal at all, independent of the amount.
3. **Validate the concrete request.**
   `POST /policy/v1/transactions/{planCode}/{policyNumber}/partialwithdrawalonetime/validation`
   (`partialWithdrawalOneTimeValidation1`) with the payload you intend to submit. This is the
   rehearsal step — it returns the same validation shape as the submit without performing it.
   Treat a non-empty `validationResult` array with `severity: ERROR` as a hard stop.
4. **Submit.** `POST /policy/v1/transactions/{planCode}/{policyNumber}/partialwithdrawalonetime`
   (`partialWithdrawalOneTime1`). Record the returned transaction identifier.
5. **Confirm.** Read the transaction back with `Transaction By Id` before reporting success.

## Errors

Zinnia does not use RFC 9457. Policy Transactions returns the `validationResult` envelope:

```json
{"status":"failure","validationResult":[{"code":"ERROR_001","message":"Invalid request parameters","severity":"ERROR","details":{"field":"premium.amount","constraint":"...","providedValue":5000}}]}
```

Retry policy published by Zinnia: do **not** retry on `400`, `401` or `403` unless the request
has been corrected. You **may** retry on `429`, `500`, `502`, `503` and `504` with exponential
backoff — and on a write, only after confirming the transaction did not already post.
`429` carries `Retry-After`. Full catalog: `errors/zinnia-problem-types.yml`.

## Reversing it

There is no published refund or reversal window for a completed withdrawal. The reversal paths
that do exist are `cancelTransaction1` (cancel an in-flight transaction) and `reverserecreate1`
(reverse and recreate a posted transaction). Neither has a stated window. See
`conventions/zinnia-conventions.yml` → `reversibility`.
