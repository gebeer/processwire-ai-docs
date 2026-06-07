# Mailpit

DDEV provides Mailpit for local email inspection.

```bash
ddev mailpit
```

## WireMailSmtp Runtime Override

`WireMailSmtp` sends directly to its configured SMTP server, so Mailpit only catches it when SMTP settings point to DDEV's local SMTP endpoint.

Inject before the first `wireMail()` call:

```php
<?php namespace ProcessWire;
include(__DIR__ . '/../index.php');

$config->wiremailsmtp = [
    'smtp_host' => '127.0.0.1',
    'smtp_port' => 1025,
    'smtp_ssl' => 0,
    'smtp_start_tls' => 0,
    'allow_without_authentication' => 1,
    'smtp_user' => '',
    'smtp_password' => '',
];

$mail = wireMail();
$mail->to('user@example.com')->subject('Test')->body('Mailpit test')->send();
```

## Notes

- Use only in DDEV/local CLI scripts.
- Inject before creating the `wireMail()` instance.