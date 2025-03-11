# Internet-Draft: L402 - A Simplified Payment Protocol for the Web

### Status: Experimental

## Abstract

This document specifies L402, a simplified HTTP-based payment protocol that leverages the HTTP 402 status code to enable machine-friendly transactions. L402 provides a minimalist approach to web payments, designed specifically for AI agents and automated systems to handle payments without human intervention. This specification defines the usage of HTTP headers for payment indication and redirection, removing the need for complex JSON payloads found in previous approaches.

## Status of This Document

This Internet-Draft is submitted as an experimental specification to stimulate discussion and development within the IETF community. Comments are requested and should be directed to the authors.

Internet-Drafts are working documents of the Internet Engineering Task Force (IETF). Note that other groups may also distribute working documents as Internet-Drafts. The list of current Internet-Drafts is at https://datatracker.ietf.org/drafts/current/.

## Introduction

The original design of the internet largely overlooked payments, despite HTTP reserving the 402 "Payment Required" status code in RFC 7231. Payment solutions have typically centered around human interactions and checkout flows, which worked for traditional web browsing but present challenges for autonomous systems and AI agents that operate independently through APIs.

L402 addresses this gap by creating a machine-friendly payment protocol. By using HTTP 402 status codes with a simple Location header redirecting to payment endpoints, L402 enables AI agents and automated systems to handle payments programmatically without requiring human intervention.

This specification decouples the payment flow from the response and moves it to the location specified in the Location header. This approach embraces an ecosystem of different payment solutions while maintaining a standardized way to indicate that payment is required.

## Terminology

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 [RFC2119] [RFC8174] when, and only when, they appear in all capitals, as shown here.

## Protocol Specification

### 1. Overview

L402 uses standard HTTP mechanisms to indicate that payment is required for a resource and to direct clients to the appropriate payment endpoint. The protocol is designed to be simple, extensible, and compatible with existing HTTP infrastructure.

The basic flow is as follows:

1. Client requests an HTTP resource
2. If payment is required, server responds with HTTP 402
3. Response includes a Location header pointing to the payment endpoint
4. Client follows the Location header to complete payment
5. After payment, the client requests the original resource again
6. Server verifies payment and provides access to the resource

### 2. HTTP Status Code Usage

#### 2.1 402 Payment Required

When a server requires payment for a resource, it MUST respond with the HTTP 402 status code. According to RFC 7231, this status code is "reserved for future use" - L402 defines this usage.

When returning a 402 status code, the server MUST include a Location header (as defined in Section 3.1). The response MAY include an X-Price header (as defined in Section 3.2) to indicate the payment amount.

### 3. Header Specifications

#### 3.1 Location Header

The Location header is REQUIRED in responses with 402 status code and points to the payment endpoint URL. This header follows the standard definition in RFC 7231, Section 7.1.2.

Example:

```
Location: https://api.example.com/pay/item-123
```

The payment endpoint URL MAY include query parameters to provide additional context to the payment system.

#### 3.2 X-Price Header

The X-Price header is OPTIONAL and can be included with any status code (not just 402). It provides information about the price of the resource.

Format:

```
X-Price: <amount> [<currency>]
```

Where:

- `<amount>` is a numerical value
- `<currency>` is an optional currency code (default is USD if not specified)

Examples:

```
X-Price: 0.01
X-Price: 0.01 USD
X-Price: 10 EUR
X-Price: 0.0001 BTC
```

The X-Price header is informational only. The actual payment amount and currency are determined by the payment endpoint.

### 4. Example Interactions

#### 4.1 Basic Flow

**Initial Request:**

```
GET /premium-content HTTP/1.1
Host: api.example.com
```

**Server Response (Payment Required):**

```
HTTP/1.1 402 Payment Required
Location: https://api.example.com/pay/premium-content-123
X-Price: 0.50 USD
```

**Client makes payment at the Location URL and then retries:**

```
GET /premium-content HTTP/1.1
Host: api.example.com
Authorization: Bearer payment_token_xyz
```

**Server Response (After Payment):**

```
HTTP/1.1 200 OK
Content-Type: application/json

{
  "premium_content": "Here is the content you paid for."
}
```

#### 4.2 Price Discovery

Clients can discover the price of a resource without attempting to access it:

**Price Discovery Request:**

```
HEAD /premium-content HTTP/1.1
Host: api.example.com
```

**Server Response:**

```
HTTP/1.1 200 OK
X-Price: 0.50 USD
```

### 5. Payment Verification

The protocol does not define how payment verification should be implemented. Servers may use various mechanisms such as:

1. Session cookies
2. Bearer tokens
3. API keys
4. Digital signatures
5. Other authentication mechanisms

The specific mechanism used is out of scope for this specification and left to the implementation.

### 6. Security Considerations

#### 6.1 Transport Security

All implementations of L402 SHOULD use HTTPS (HTTP over TLS) as specified in [RFC2818] to protect the confidentiality and integrity of the payment transaction.

#### 6.2 Payment Endpoint Security

The payment endpoint specified in the Location header SHOULD implement appropriate security measures to prevent fraud, ensure confidentiality, and validate payment completion.

#### 6.3 Cross-Origin Resource Sharing

Servers implementing L402 should consider Cross-Origin Resource Sharing (CORS) implications if the payment endpoint is on a different origin than the original resource.

## Rationale and Comparisons

### Previous Approaches

Previous approaches to HTTP-based payment protocols often relied on complex JSON payloads containing payment details, which required custom parsing logic and specific client implementations. By leveraging standard HTTP mechanisms (status codes and headers), L402 reduces the implementation complexity and improves interoperability.

### Design Choices

The key design choice in L402 is the decoupling of the payment flow from the original request, moving it to the endpoint specified in the Location header. This approach:

1. Allows innovation in payment processing without changing the core protocol
2. Leverages existing HTTP redirect mechanisms familiar to developers
3. Enables a diverse ecosystem of payment solutions to coexist
4. Simplifies client implementation requirements

## References

### Normative References

[RFC2119] Bradner, S., "Key words for use in RFCs to Indicate Requirement Levels", BCP 14, RFC 2119, DOI 10.17487/RFC2119, March 1997, <https://www.rfc-editor.org/info/rfc2119>.

[RFC7231] Fielding, R., Ed. and J. Reschke, Ed., "Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content", RFC 7231, DOI 10.17487/RFC7231, June 2014, <https://www.rfc-editor.org/info/rfc7231>.

[RFC8174] Leiba, B., "Ambiguity of Uppercase vs Lowercase in RFC 2119 Key Words", BCP 14, RFC 8174, DOI 10.17487/RFC8174, May 2017, <https://www.rfc-editor.org/info/rfc8174>.

### Informative References

[RFC2818] Rescorla, E., "HTTP Over TLS", RFC 2818, DOI 10.17487/RFC2818, May 2000, <https://www.rfc-editor.org/info/rfc2818>.

## Acknowledgements

The authors would like to thank the contributors and reviewers who provided valuable feedback on this specification.

## Authors' Addresses

Jan Wilmake
jan@wilmake.com
