# Glossary

This document explains the technical terms used throughout the project.

---

# DNS (Domain Name System)

The system that translates domain names into IP addresses.

Example:

```text
mail.example.com
        ↓
203.0.113.10
```

Without DNS, email systems would not know where to connect.

---

# MX Record (Mail Exchange Record)

A DNS record that tells the internet where email for a domain should be delivered.

Example:

```text
example.com
    ↓
MX Record
    ↓
mail.example.com
```

---

# SMTP (Simple Mail Transfer Protocol)

The protocol used to send email between systems.

SMTP is responsible for moving messages from one server to another.

Think of it as the delivery truck of email.

---

# SMTP Submission

A secure version of SMTP used by users and applications.

Typically runs on:

```text
Port 587
```

In this project:

```text
Gmail
    ↓
Postfix
```

uses SMTP Submission.

---

# Postfix

A Mail Transfer Agent (MTA).

Responsible for:

- Receiving email submissions
- Routing messages
- Relaying outbound mail

Postfix is the core outbound email component of this project.

---

# Dovecot

An email server commonly used for:

- IMAP
- POP3
- Authentication

In this project, Dovecot is used only for SMTP authentication.

No mailboxes are hosted locally.

---

# SASL (Simple Authentication and Security Layer)

A framework used for authentication.

In this project:

```text
Gmail
    ↓
Postfix
    ↓
Dovecot
```

SASL allows Gmail to prove its identity before Postfix accepts mail.

---

# TLS (Transport Layer Security)

Encryption used to secure network traffic.

TLS prevents attackers from reading email credentials while they are being transmitted.

---

# Let's Encrypt

A certificate authority that provides free TLS certificates.

These certificates allow clients to verify the identity of a server.

---

# Oracle Email Delivery

Oracle Cloud's managed email delivery service.

Acts as an SMTP relay.

Used because Oracle blocks direct outbound SMTP on port 25.

---

# SMTP Relay

A trusted service that forwards email on behalf of another system.

In this project:

```text
Postfix
    ↓
Oracle Email Delivery
    ↓
Recipient
```

Oracle acts as the relay.

---

# Envelope Sender

The sender identity used during SMTP delivery.

This may differ from the visible "From" address shown to users.

Many email providers validate the envelope sender separately.

---

# SPF (Sender Policy Framework)

A DNS-based mechanism that tells receiving mail servers:

> Which servers are allowed to send mail for this domain?

Example:

```text
example.com
    ↓
SPF Record
    ↓
Allowed Senders
```

---

# DKIM (DomainKeys Identified Mail)

A cryptographic signature attached to outgoing email.

Allows recipients to verify:

- The message was not modified
- The sender is legitimate

---

# DMARC (Domain-based Message Authentication, Reporting, and Conformance)

A policy layer built on top of SPF and DKIM.

DMARC tells receiving servers what to do when authentication fails.

Examples:

```text
none
quarantine
reject
```

---

# Gmail Send As

A Gmail feature that allows sending mail through an external SMTP server.

In this project Gmail sends mail through:

```text
mail.example.com:587
```

rather than Google's outbound servers.

---

# Cloudflare Email Routing

A service that forwards incoming email.

Used to deliver inbound mail directly to Gmail without hosting mailboxes.

Flow:

```text
Internet
    ↓
Cloudflare Email Routing
    ↓
Gmail
```

---

# Mailbox Hosting

Running and maintaining email storage on a server.

This typically requires:

- IMAP
- Mail storage
- Backups
- User management

This project intentionally avoids mailbox hosting.

---

# IMAP (Internet Message Access Protocol)

A protocol used to access stored email.

Commonly used by:

- Outlook
- Thunderbird
- Mobile Mail Apps

This project does not use IMAP.

---

# POP3 (Post Office Protocol v3)

An older protocol for downloading email.

This project does not use POP3.

---

# Deliverability

The likelihood that an email successfully reaches the recipient's inbox.

Deliverability depends on:

- SPF
- DKIM
- DMARC
- Reputation
- Authentication

Sending email is easy.

Reaching the inbox is hard.

---

# MTA (Mail Transfer Agent)

Software responsible for transferring email.

Examples:

- Postfix
- Exim
- Sendmail

In this project:

```text
Postfix
```

is the MTA.

---

# MUA (Mail User Agent)

The application used by humans to read and send email.

Examples:

- Gmail
- Outlook
- Thunderbird

In this project:

```text
Gmail
```

is the MUA.