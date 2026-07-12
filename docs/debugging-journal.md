# Debugging Journal

This document records the most significant technical issues encountered while building the platform.

---

# Issue 1: Oracle Blocks Port 25

## Symptom

Direct outbound email delivery never succeeded.

## Investigation

Local mail submission worked correctly.

However, messages never reached external recipient servers.

## Root Cause

Oracle Cloud blocks outbound TCP port 25 by default.

## Resolution

Oracle Email Delivery was introduced as an authenticated relay.

```text
Postfix
    ↓
Oracle Email Delivery
    ↓
Recipient
```

## Lesson Learned

Infrastructure constraints imposed by cloud providers can significantly influence system architecture.

---

# Issue 2: Gmail Authentication Failure

## Symptom

Gmail Send As could not authenticate successfully.

## Investigation

Postfix accepted connections but authentication attempts failed.

## Root Cause

Postfix did not have a functioning SASL authentication backend.

## Resolution

Dovecot was integrated as a SASL authentication provider for Postfix.

Authentication Flow:

```text
Gmail
    ↓
SMTP AUTH
    ↓
Postfix
    ↓
Dovecot Authentication
```

Mail Flow:

```text
Gmail
    ↓
Postfix
    ↓
Oracle Email Delivery
    ↓
Recipient
```

## Lesson Learned

SMTP transport and user authentication are separate concerns.

---

# Issue 3: Oracle SMTP Authentication Failure

## Symptom

```text
535 Authentication required
```

## Investigation

Oracle Email Delivery rejected relay attempts.

## Root Cause

Postfix was not correctly using the configured SMTP credentials.

## Resolution

Configured:

```ini
smtp_sasl_password_maps
```

Then rebuilt the credential database:

```bash
postmap /etc/postfix/sasl_passwd
```

## Lesson Learned

Always verify that Postfix is using the compiled credential database rather than the source file.

---

# Issue 4: Oracle Authorization Failure

## Symptom

```text
535 Authorization failed
Envelope From address not authorized
```

## Investigation

Authentication succeeded, but Oracle still rejected messages.

## Root Cause

The sender identity had not been authorized within Oracle Email Delivery.

## Resolution

Configured approved sender identities and aligned sender mappings.

## Lesson Learned

Authentication and authorization are different problems.

Successful login does not imply permission to use arbitrary sender addresses.

---

# Issue 5: SMTP TLS Certificate Validation

## Symptom

SMTP clients reported certificate trust warnings during connection attempts.

## Investigation

The SMTP service was operational, but clients could not verify the certificate chain successfully.

This reduced trust and could impact SMTP client compatibility.

## Root Cause

Postfix was initially using a self-signed certificate rather than a publicly trusted certificate.

## Resolution

Configured Postfix to use a valid Let's Encrypt TLS certificate.

This allowed SMTP clients to establish encrypted connections while successfully validating the certificate chain.

## Validation

```bash
openssl s_client \
-starttls smtp \
-connect mail.example.com:587
```

Result:

```text
subject=CN = mail.example.com
```

The certificate was now trusted and presented correctly during SMTP submission.

## Lesson Learned

Encryption alone is not enough.

Clients must also trust the certificate being presented.

---

# Issue 6: Multiple Alias Support

## Symptom

Authenticated SMTP submission worked, but Oracle Email Delivery rejected messages sent from certain aliases.

## Investigation

Authentication was successful.

However, Oracle validated the envelope sender independently from the authenticated account.

## Root Cause

Oracle Email Delivery requires sender identities to be authorized before they can be used.

Successful authentication alone was not sufficient.

## Resolution

Configured approved sender identities and verified that all required aliases were authorized.

Examples:

- contact@example.com
- help@example.com
- notifications@example.com
- postmaster@example.com

All aliases were then able to send mail through the same authenticated SMTP account.

## Lesson Learned

Modern email systems separate:

- Authentication
- Authorization
- Identity validation

Each layer must be configured correctly for successful delivery.