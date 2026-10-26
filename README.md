# Zinnia

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Zinnia is an Eldridge-backed insurance technology and third-party administration company that provides the
policy administration, new business, order entry, illustration, document and in-force servicing infrastructure
behind a large share of the US life insurance and annuity market. Formerly SE2, and now the parent of
Policygenius, Ebix's life and annuity software assets, AnnuityNet, VitalQuote, WinFlex, SmartOffice and Zahara.

## What this profile found

Zinnia runs a public Kong developer portal at [developers.zinnia.com](https://developers.zinnia.com/) that
publishes **ten first-party OpenAPI contracts totalling 215 operations** against the enterprise API gateway at
`api.zinnia.io`:

| API | Version | Operations |
|---|---|---|
| Policy Transactions | 1.0.8 | 102 |
| Illustration Generation | 3.5.3 | 21 |
| Market Connect Order Entry | 1.0.3 | 19 |
| Policy Service | 0.0.21 | 16 |
| New Business | 0.9.8 | 14 |
| Enterprise Documents (EDS) | 3.0.0 | 12 |
| Illustration Client Cases | v1 | 12 |
| New Business Annuity | 0.7.0 | 12 |
| Product Service | 0.1.0 | 4 |
| Case Management | 1.0.0 | 3 |

Authentication is Auth0 machine-to-machine OAuth 2.0 with 24-hour JWT bearer tokens, enforced by the Kong
OpenID Connect plugin; the tenant's OIDC discovery document is live and anonymously readable at
`login.zinnia.com`. The contracts declare **ACORD-aligned** prefill and field semantics and carry **DTCC**
Insurance & Retirement Services participant and money-movement identifiers as first-class fields. The four
in-force annuity transaction APIs the **Insured Retirement Institute** approved in April 2026 were built on
the Enterprise API framework Zinnia contributed, and all four shapes are present in the published Policy
Transactions contract.

Gaps this profile recorded, with evidence: no idempotency key on any of the 215 operations (on a surface of
131 mutating operations that move money); no published rate-limit numbers, though `429` and `Retry-After` are
documented; no deprecation policy while superseded `/document/v2/` and `/case/v1/` operations stay published
unmarked; no public status page (the Statuspage tenant that exists is team-only); no pricing; no SDKs in any
registry; no MCP server or agent card; and four documentation links published in the Policy Service support
section point at `docs.zinnia.com`, a host that does not resolve.
