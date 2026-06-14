# Paidy

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
