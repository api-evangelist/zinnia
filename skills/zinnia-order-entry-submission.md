---
name: zinnia-order-entry-submission
description: Drive a life insurance or annuity application end to end through the Zinnia Market Connect Order Entry API - create a transaction from an ACORD-aligned prefill, render and progressively save the blueprint, attach documents, run e-signature, and submit.
api: Zinnia Market Connect Order Entry API
generated: '2026-09-13'
method: generated
source: openapi/zinnia-market-connect-order-entry-openapi.yml
operations:
  - createTransaction
  - getTransaction
  - updateTransactionEnhancedBlueprint
  - getDataset
  - getProductPricing
  - ingestAttachmentDocument
  - getAttachment
  - submitEsignature
  - getEsignatureSignerStatus
  - updateEsignSignerDetails
  - cancelEsignatureCeremony
  - retriggerEsignatureEmailNotification
  - reviewerAction
  - submitTransaction
---

# Headless order entry

Market Connect Order Entry is the headless application surface: you own the UI, Zinnia owns the
product rules, forms, validation and downstream submission. The unit of work is a
**transaction**, identified by `transactionId`.

## Steps

1. **Create the transaction.** `POST /market-connect/order-entry/v1/transaction`
   (`createTransaction`). As of 1.0.3 the request accepts an optional **ACORD-aligned `prefill`
   node** that pre-populates party and product fields, plus optional `illustrationId`,
   `accountNumber` and `externalTransactionId`. Pass `externalTransactionId` — it is the only
   handle that lets you reconcile your own record with Zinnia's if a call fails, because this
   API has no idempotency key.
2. **Read the blueprint.** `GET /market-connect/order-entry/v1/transaction/{transactionId}`
   (`getTransaction`) returns the order entry blueprint — the sections, controls, conditional
   visibility rules and any answers already saved. Render from this; do not hardcode a form.
3. **Resolve dependent controls.** `POST /market-connect/order-entry/v1/dataset` (`getDataset`)
   returns the valid values for a dependent control given the answers so far.
4. **Save progressively.** `PUT /market-connect/order-entry/v1/transaction/{transactionId}`
   (`updateTransactionEnhancedBlueprint`) supports save-and-resume. Validation errors come back
   as `ValidationErrors` carrying `fieldId`, `answerNodeId`, `sectionName` and `message` — map
   them onto your own controls rather than showing raw text.
5. **Pricing.** `GET /market-connect/order-entry/v1/productpricing/{transactionId}`
   (`getProductPricing`).
6. **Attachments.** `ingestAttachmentDocument` registers an EDS document against the transaction;
   `getAttachment` returns the attachment payload including required-document state.
7. **E-signature.** `submitEsignature` sends the case to DocuSign. Then:
   `getEsignatureSignerStatus` to poll per-recipient status, `updateEsignSignerDetails` to
   correct a signer's email or phone mid-ceremony, `retriggerEsignatureEmailNotification` to
   resend, and `cancelEsignatureCeremony` to void the envelope and return the case to Pending.
8. **Reviewer action.** `reviewerAction` executes forward, approve, decline or terminate.
9. **Submit.** `POST /market-connect/order-entry/v1/transaction/{transactionId}/submit`
   (`submitTransaction`).

## Errors

This API has the most explicit error contract Zinnia publishes: an `ErrorResponse` envelope
carrying `transactionId` plus an `errors[]` array whose `code` is one of 23 enumerated values
(`VALIDATION_ERROR`, `INVALID_TRANSACTION_ID`, `TRANSACTION_ID_NOT_FOUND`, `UNAUTHORIZED`,
`FORBIDDEN`, `NOT_FOUND`, `RATE_LIMIT_EXCEEDED`, `UPSTREAM_SERVICE_ERROR`, `UPSTREAM_TIMEOUT`,
`SERVICE_UNAVAILABLE`, …). Since 1.0.2 business-validation failures return `422`, distinct from
schema failures at `400`. `429` means `RATE_LIMIT_EXCEEDED` — honour `Retry-After`.
Full catalog: `errors/zinnia-problem-types.yml`.

## Reversibility

`cancelEsignatureCeremony` voids an in-flight signing ceremony and returns the case to Pending.
`deleteComment` removes a comment. There is no published reversal for a submitted transaction
and no stated window on any of them.
