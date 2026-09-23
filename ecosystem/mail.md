---
description: Send email from ColdBox applications with cbmailservices — protocol-based, testable, and environment-aware.
icon: envelope
---

# Mail

ColdBox sends email through the **cbmailservices** module: an object-oriented mailer with pluggable protocols, so your code never changes between environments.

```bash
box install cbmailservices
```

📖 **Full documentation:** [cbmailservices.ortusbooks.com](https://cbmailservices.ortusbooks.com)

## Quick Example

```javascript
property name="mailService" inject="MailService@cbmailservices";

var mail = mailService.newMail(
    to      : user.getEmail(),
    from    : "no-reply@myapp.com",
    subject : "Welcome!"
)

mail.setBody( renderView( "emails/welcome", { user : user } ) )
mailService.send( mail )
```

## Protocols

Switch delivery per environment without touching application code:

| Protocol | Use |
| --- | --- |
| `CFMail` | Traditional engine mail sending |
| `File` | Write emails to disk — perfect for development |
| `Postmark` | Via the `PostBox` protocol module |
| `SendGrid` | Via the `send-grid-protocol` module |

```javascript
// config/ColdBox.bx — development
mailservices : {
    protocol : "file",
    filePath : "/runtime/mail"
}
```

You can build your own protocol by extending the base protocol class — the interface is small and documented in the module book.

## When to Use It

- Transactional email (welcome, password reset, receipts)
- Rendering email bodies with ColdBox views and layouts
- Testing mail flows locally without an SMTP server

## See Also

- [Queues](queues.md) — queue mail sending with `cbq` for non-blocking delivery
- [Views](../the-basics/layouts-and-views/views/README.md) — rendering email templates
