# Architecture Decision Records (ADR)

This document captures the major architectural decisions made during the project and the reasoning behind them.

---

# ADR-001: Cloudflare Email Routing

## Decision

Use Cloudflare Email Routing for inbound email delivery.

## Context

The original plan involved hosting mailboxes directly on the server.

This would require:

- IMAP
- mailbox storage
- spam filtering
- backups
- mailbox administration

## Reasoning

Cloudflare Email Routing forwards incoming email directly to Gmail.

This eliminated the need to host mailboxes entirely.

## Outcome

```text
Internet
    ↓
Cloudflare Email Routing
    ↓
Gmail Inbox
```

## Impact

Benefits:

- No mailbox hosting
- No IMAP administration
- No storage management
- Reduced operational overhead

---

# ADR-002: Gmail as the Primary Mail Interface

## Decision

Use Gmail as the only user-facing mailbox interface.

## Context

The goal was to avoid managing additional email clients and mailbox infrastructure.

## Reasoning

Gmail already provides:

- Search
- Spam filtering
- Mobile applications
- Reliability
- Familiar user experience

## Outcome

All inbound mail arrives in Gmail.

All outbound mail is sent through Gmail aliases.

---

# ADR-003: Oracle Email Delivery as SMTP Relay

## Decision

Relay outbound email through Oracle Email Delivery.

## Context

Direct SMTP delivery was originally planned.

## Problem

Oracle Cloud blocks outbound TCP port 25.

This prevented direct delivery to recipient mail servers.

## Reasoning

Oracle Email Delivery provides an authenticated SMTP relay service.

## Outcome

```text
Postfix
    ↓
Oracle Email Delivery
    ↓
Recipient
```

---

# ADR-004: Dovecot for SMTP Authentication

## Decision

Use Dovecot exclusively as a SASL authentication backend.

## Context

Gmail Send As requires SMTP authentication.

Postfix alone was not sufficient for the desired authentication workflow.

## Reasoning

Dovecot integrates cleanly with Postfix and provides reliable SASL authentication.

## Outcome

```text
Gmail
    ↓
Postfix
    ↓
Dovecot Authentication
```

## Note

Dovecot is not used for:

- IMAP
- POP3
- Mailbox storage

Only authentication.

---

# ADR-005: Postfix Instead of Stalwart

## Decision

Adopt Postfix as the SMTP server.

## Context

Stalwart Mail Server was evaluated during the design phase.

## Reasoning

While Stalwart appeared promising, deployment and operational challenges introduced unnecessary complexity.

Postfix offered:

- Maturity
- Stability
- Extensive documentation
- Predictable behavior

## Outcome

Postfix became the foundation of the outbound email architecture.

## Lesson

Sometimes the most reliable solution is the least exciting one.