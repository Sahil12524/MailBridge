# Lessons Learned

This project started as a simple goal:

> Use professional email addresses without paying for mailbox hosting.

It ended up teaching far more about email infrastructure than expected.

---

# Email Is Mostly DNS

Before this project, I assumed email infrastructure was primarily about SMTP servers.

The reality was very different.

The most important components became:

- SPF
- DKIM
- DMARC
- Domain reputation
- Trust relationships

SMTP was only one piece of a much larger system.

---

# Receiving Email Is Easier Than Sending Email

Inbound email was solved surprisingly quickly using Cloudflare Email Routing.

Outbound email required significantly more work because every message must establish trust before recipients accept it.

---

# Authentication and Authorization Are Different Problems

One of the most valuable lessons from this project.

Authentication answers:

> Who are you?

Authorization answers:

> What are you allowed to do?

The Oracle Email Delivery issues demonstrated this distinction clearly.

---

# Boring Software Wins

Stalwart Mail Server was modern and attractive.

Postfix was older and less exciting.

In practice, Postfix proved easier to troubleshoot and more predictable under real-world conditions.

---

# Constraints Create Architecture

The final design was not planned from the beginning.

It emerged as a response to constraints:

- Oracle Cloud port restrictions
- Gmail integration requirements
- Deliverability requirements
- Operational simplicity

Every major component exists because a previous approach failed.

---

# Simplicity Has Long-Term Value

The final architecture avoids:

- mailbox hosting
- IMAP administration
- local email storage

while still providing professional email capabilities.

Reducing operational complexity often delivers more value than adding features.

---

# Understanding Systems Matters More Than Memorizing Commands

The most important outcome of this project was not learning Postfix commands.

It was learning how:

- SMTP
- DNS
- TLS
- Authentication
- Deliverability

interact to form a complete email system.

That understanding transfers to many other areas of infrastructure engineering.