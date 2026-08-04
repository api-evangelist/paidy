# Paidy

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
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Paidy is a Japanese buy now, pay later (BNPL) and digital payment service that enables consumers in Japan to make purchases and pay later through monthly consolidated billing or installment plans. Acquired by PayPal in 2021, Paidy serves as a leading BNPL provider in Japan, allowing consumers to shop without a credit card and pay via bank transfer, convenience store, or direct debit.

## APIs

### Paidy Checkout

JavaScript-based integration embedded in the merchant checkout page. Handles the consumer-facing authentication flow: the consumer enters their mobile number, receives an SMS verification code, and confirms the payment or creates a recurring payment token. Passes the resulting payment ID or token ID back to the merchant frontend via callback.

- **Integration**: `<script src="https://apps.paidy.com/"></script>`
- **Documentation**: https://paidy.com/docs/en/paidycheckout.html

### Paidy Payments API

REST API for managing the full payment lifecycle. Base URL: `https://api.paidy.com`

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/payments` | POST | Create a payment using a stored token |
| `/payments/{id}/captures` | POST | Capture an authorized payment |
| `/payments/{id}/refunds` | POST | Refund a captured payment (partial or full) |
| `/payments/{id}` | GET | Retrieve payment details |
| `/payments/{id}` | PUT | Update order reference, description, or metadata |
| `/payments/{id}/close` | POST | Close an authorized but uncaptured payment |

### Paidy Tokens API

REST API for managing stored payment tokens for recurring billing.

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/tokens/{id}/suspend` | POST | Suspend an active token |
| `/tokens/{id}/resume` | POST | Resume a suspended token |
| `/tokens/{id}/delete` | POST | Permanently delete a token |
| `/tokens/{id}` | GET | Retrieve a specific token |
| `/tokens/` | GET | Retrieve all active and suspended tokens |

## Authentication

Merchants receive four API keys via the Merchant Dashboard (`https://merchant.paidy.com/`):
- **Secret keys** (`sk_` prefix): Used in `Authorization: Bearer <sk_key>` headers for API requests
- **Public keys** (`pk_` prefix): Used in Paidy Checkout JavaScript configuration
- Separate test and production keys are provided

All requests require HTTPS and `Paidy-Version: 2018-04-10` header.

## Webhooks

Paidy sends HTTP POST notifications to registered merchant endpoints for the following events:

**Payment events**: `authorize_success`, `capture_success`, `close_success`, `refund_success`, `update_success`

**Token events**: `activate_success`, `suspend_success`, `resume_success`, `delete_success`

Endpoints must respond within 10 seconds with HTTP 200. Failed deliveries are retried with exponential backoff up to approximately 5 hours.

Whitelist Paidy IP addresses: `13.114.134.35`, `13.113.94.100`, `18.182.135.232`, `52.199.50.20`, `52.199.62.26`

## Testing

Use test API keys with these test credentials in Paidy Checkout:
- **Successful payment**: email `successful.payment@paidy.com`, phone `08000000001`, auth code `8888`
- **Failed payment**: email `rejected.payment@paidy.com`, phone `08000000001`, auth code `8888`

## Key Constraints

- Currency: **JPY only**
- Encoding: **UTF-8** (required for Japanese characters)
- TLS: **1.2 or higher** required
- Metadata: **20 key-value pairs** maximum per object
- API Version: **2018-04-10** (must be specified in request header)

## Links

- **Documentation**: https://paidy.com/docs/en/
- **API Reference**: https://paidy.com/docs/api/en/
- **Merchant Dashboard**: https://merchant.paidy.com/
- **Webhooks Guide**: https://paidy.com/docs/en/webhook.html
- **API Changelog**: https://paidy.com/docs/en/updates.html
- **Testing Guide**: https://paidy.com/docs/en/testing.html
