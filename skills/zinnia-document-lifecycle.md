---
name: zinnia-document-lifecycle
description: Upload, retrieve, search, update and download documents and tax forms in the Zinnia Enterprise Document Service (EDS), the system of record for documents across Zinnia's life insurance and annuity platforms.
api: Zinnia Enterprise Documents API
generated: '2026-09-13'
method: generated
source: openapi/zinnia-documents-openapi.yml
operations:
  - UploadDocuments
  - getMetadataByDocumentId
  - updateDocuments
  - searchDocuments
  - downloadDocument-v3
  - getTaxForms-v3
  - downloadTaxForm-v3
---

# Enterprise Document Service

A document in EDS has two parts: **content** (the file) and **metadata** (structured business
information describing what the file is and what it relates to). Every document gets a
`documentId` at upload. Zinnia's own guidance: treat it as an opaque handle, store it, and pass
it back on every subsequent call for that document.

## Steps

1. **Upload.** `POST /document/v3/documents` (`UploadDocuments`) stores the file with its
   business metadata and returns the `documentId`. Persist that id immediately — there is no
   idempotency key on this API, so a retried upload creates a second document rather than
   returning the first one.
2. **Read metadata.** `GET /document/v3/documents/{documentId}` (`getMetadataByDocumentId`),
   optionally including version history.
3. **Update.** `PUT /document/v3/documents/{documentId}` (`updateDocuments`) changes content or
   metadata.
4. **Search.** `POST /document/v3/documents/search` (`searchDocuments`) finds documents by
   business metadata across the retention period.
5. **Download.** `GET /document/v3/documents/{documentId}/download` (`downloadDocument-v3`)
   returns the binary.

## Tax forms are a separate collection

Tax forms are managed apart from the general document collection. They are **never** returned by
`searchDocuments` and are reachable only through `getTaxForms-v3`
(`GET /document/v3/tax-forms`) and `downloadTaxForm-v3`
(`GET /document/v3/tax-forms/{id}/download`). If you are looking for a 1099 and searching
documents, you will find nothing and the absence is not an error.

## Version note

The same contract still publishes the older `/document/v2/...` operations
(`getDocuments`, `getDocument`, `downloadDocument-v2`, `getTaxForms-v2`, `downloadTaxForm-v2`).
They are not marked `deprecated: true` and Zinnia publishes no deprecation policy, but v3 is the
current release. Build against `/document/v3/`.

## Authentication

`Authorization: Bearer {token}`. The Bearer prefix is case-sensitive and the trailing space is
required. Tokens are Auth0 machine-to-machine tokens valid for 24 hours and reusable for any
number of requests in that window.
