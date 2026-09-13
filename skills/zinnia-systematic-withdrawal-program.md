---
name: zinnia-systematic-withdrawal-program
description: Set up, quote and update a systematic withdrawal program (SWP) on a Zinnia-administered annuity policy. Systematic Withdrawal Program Setup and Systematic Withdrawal Program Update are two of the four in-force transaction APIs the Insured Retirement Institute approved on Zinnia's Enterprise API framework.
api: Zinnia Policy Transactions API
generated: '2026-09-13'
method: generated
source: openapi/zinnia-policy-transactions-openapi.yml, openapi/zinnia-policy-service-openapi.yml
operations:
  - Systematic Programs
  - systematicProgramQuote
  - systematicProgrameligibilitycheck1
  - systematicProgramValidation1
  - systematicProgram1
  - systematicProgramUpdateeligibilitychecks
  - systematicProgramUpdateValidations
  - systematicProgramUpdates
---

# Systematic withdrawal program (SWP)

A systematic program is an arrangement on a policy, addressed by `arrangementId`. The policy
itself is addressed by `planCode` + `policyNumber`.

## Read what already exists first

`GET /policy/v1/policies/{planCode}/{policyNumber}/systematicPrograms` (`Systematic Programs`,
Policy Service) lists the programs currently on the policy, with their `arrangementId`s. There
is also `Systematic Programs for External Transaction Id` when you are holding your own
reference rather than Zinnia's.

## Setting up a new program

1. **Quote it.** `POST /policy/v1/transactions/{planCode}/{policyNumber}/systematicprograms/quote`
   (`systematicProgramQuote`) returns the calculated figures for the program you are describing
   without creating anything.
2. **Eligibility.** `POST .../systematicprograms/eligibilitycheck` (`systematicProgrameligibilitycheck1`).
3. **Validation.** `POST .../systematicprograms/validation` (`systematicProgramValidation1`) —
   the rehearsal of the exact payload you are about to submit.
4. **Submit.** `POST .../systematicprograms` (`systematicProgram1`).

## Updating an existing program

The update path takes the `arrangementId` you read in step 0 and mirrors the same three stages:

1. `POST .../systematicprograms/{arrangementId}/eligibilitycheck` (`systematicProgramUpdateeligibilitychecks`)
2. `POST .../systematicprograms/{arrangementId}/validation` (`systematicProgramUpdateValidations`)
3. `PUT .../systematicprograms/{arrangementId}` (`systematicProgramUpdates`)

## Rules that apply to both

- **No idempotency key exists on this API.** A replayed setup call can create a second program.
  Re-read `Systematic Programs` before any retry rather than resending.
- Never skip the validation stage. It is the only dry run Zinnia offers and it is free.
- A submitted program can be cancelled in flight with `cancelTransaction1`; there is no published
  window for reversing a program that has already begun paying.
- Error envelope and retry policy: `errors/zinnia-problem-types.yml`.
