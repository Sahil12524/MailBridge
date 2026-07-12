# Deployment Guide

This document summarizes the high-level deployment process used to build the platform.

---

# 1. Install Postfix

```bash
sudo apt update
sudo apt install postfix
```

---

# 2. Install Dovecot

```bash
sudo apt install dovecot-core
```

---

# 3. Enable SMTP Submission

Edit:

```bash
sudo nano /etc/postfix/master.cf
```

Enable the submission service:

```ini
submission inet n - y - - smtpd
```

---

# 4. Configure Oracle Email Delivery

Create the credential file:

```bash
sudo nano /etc/postfix/sasl_passwd
```

Example:

```text
[smtp.email.<region>.oraclecloud.com]:587 SMTP_USERNAME:SMTP_PASSWORD
```

Generate the Postfix lookup database:

```bash
sudo postmap /etc/postfix/sasl_passwd
```

---

# 5. Restart Services

```bash
sudo systemctl restart postfix
sudo systemctl restart dovecot
```

---

# 6. Verify SMTP Submission

```bash
sudo ss -tulpn | grep :587
```

Expected result:

```text
LISTEN 0 100 0.0.0.0:587
```

---

# 7. Verify TLS Certificate

```bash
openssl s_client \
-starttls smtp \
-connect localhost:587
```

Expected result:

```text
subject=CN = mail.example.com
```

---

# 8. Verify End-to-End Delivery

Send a test message through Gmail Send As and confirm successful delivery using Postfix logs.

Example:

```text
status=sent (250 Ok)
```