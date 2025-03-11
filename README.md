# Simple 402: A Minimalist Payment Required Standard

**A lightweight, HTTP-native approach to adding payment capabilities to the web using the 402 status code and standard headers.**

## Key Features

- **HTTP-native implementation**: Uses the HTTP 402 status code with standard headers rather than complex JSON payloads
- **Location-based redirects**: Simplifies payment flows with the `Location` header pointing to payment endpoints
- **Universal price indication**: Optional `X-Price` header works with any status code to indicate pricing
- **Payment-method agnostic**: Compatible with any payment system that can be linked via URLs
- **Minimal integration effort**: Requires minimal changes to existing HTTP servers and clients

## Introduction

The HTTP 402 "Payment Required" status code has existed since the early days of the web but has never been standardized. Simple 402 proposes a lightweight approach to implementing this status code in a way that's consistent with HTTP standards and requires minimal effort to adopt.

Rather than defining complex JSON structures, Simple 402 relies on HTTP headers to indicate payment requirements, making it easier to implement across different platforms and programming languages.

## Protocol Specification

### 402 Response

When a resource requires payment, the server responds with:

- HTTP status code `402 Payment Required`
- A `Location` header pointing to a payment endpoint
- An optional `X-Price` header indicating the price

Example:

```http
HTTP/1.1 402 Payment Required
Location: https://api.example.com/payments/checkout?item=resource123
X-Price: 0.50 USD