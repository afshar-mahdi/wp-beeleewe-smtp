# Beeleewe SMTP

Send WordPress email through your own SMTP server using PHPMailer, with encrypted password storage, From-address control, a setup wizard and a built-in test tool.

![Version](https://img.shields.io/badge/version-1.1.0-blue)
![WordPress](https://img.shields.io/badge/WordPress-5.5%2B-21759b)
![PHP](https://img.shields.io/badge/PHP-7.4%2B-777bb4)
![License](https://img.shields.io/badge/license-GPL--2.0--or--later-green)

## Features

- **Setup wizard** on first activation.
- **Safe password storage**: authenticated encryption (libsodium `secretbox`, or AES-256-GCM as a fallback) with a key derived from your `wp-config.php` salts. Or keep the password out of the database entirely with a constant.
- **From Email / From Name** settings, plus **Force From**, which moves the original sender to Reply-To (ideal for contact forms).
- **TLS 587, SSL 465 or none 25**, with an optional custom port and opportunistic-TLS switch.
- **Connection timeout** (default 15 s) so a down mail server cannot freeze checkout or form submissions.
- Username can be **any string** (API keys, account IDs) or empty for open relays.
- **Test email** button, an **admin notice** for the last mail error, and optional debug logging.
- Every setting can be fixed in `wp-config.php`.
- Removes all of its data on uninstall.

## Requirements

| | Minimum |
|---|---|
| WordPress | 5.5 |
| PHP | 7.4 |

## Installation

1. Download the latest zip from the [Releases](../../releases) page (or zip the `beeleewe-smtp/` folder).
2. In WordPress go to **Plugins → Add New → Upload Plugin**, upload the zip and activate it.
3. Follow the setup wizard, or open **Settings → Beeleewe SMTP**.
4. Click **Send Test** to verify.

Manual install: copy the `beeleewe-smtp/` folder to `wp-content/plugins/`.

## Password security options

| Method | Where the password lives | Notes |
|---|---|---|
| **Constant** (most secure) | `wp-config.php` | `define('BWSMTP_PASSWORD', 'your_password');` |
| **Encrypted** | Database | Key is derived from `AUTH_KEY` / `AUTH_SALT`. A database leak alone does not expose it. |
| **Plain text** | Database | Only when the server cannot encrypt. The settings page warns you. |

> **Moving a site or rotating salts:** the encryption key comes from your salts. If they change, or the database is copied to a site with different salts, enter the password again. An admin notice tells you when this happens.

The password field is always shown empty. Leave it empty to keep the saved password, type a new one to change it, or tick **Remove the saved password** to clear it.

## wp-config.php constants

All constants are optional. A defined constant overrides the database value and locks that field in the UI.

```php
define('BWSMTP_HOST', 'smtp.example.com');
define('BWSMTP_USER', 'apikey');
define('BWSMTP_PASSWORD', 'your_password');
define('BWSMTP_CONNECTION_TYPE', 'tls_587');   // tls_587 | ssl_465 | none_25
define('BWSMTP_PORT', 2525);                   // overrides the connection type's port
define('BWSMTP_CHARSET', 'UTF-8');             // UTF-8 | ISO-8859-1
define('BWSMTP_FROM_EMAIL', 'no-reply@example.com');
define('BWSMTP_FROM_NAME', 'Example Shop');
define('BWSMTP_FORCE_FROM', true);
define('BWSMTP_TIMEOUT', 15);                  // 5-120 seconds
define('BWSMTP_DEBUG_LOG', true);
```

## FAQ

**Emails are accepted by the SMTP server but never arrive.**
Set a From Email that belongs to your authenticated account or domain, and make sure SPF/DKIM are configured for that domain. Many servers reject or spam-flag the default `wordpress@yourdomain` address.

**Why does a contact form email show my From Email instead of the visitor's?**
That is *Force From*. The visitor's address is added as Reply-To, so replying still reaches them. Turn the option off to let plugins set their own From.

**I see "The saved SMTP password could not be decrypted".**
The salts in `wp-config.php` changed, or the database was moved to a site with different salts. Enter the password again and save.

**Where is the debug log?**
Enable *Debug Logging* in the plugin and add `define('WP_DEBUG_LOG', true);` to `wp-config.php`. Messages go to `wp-content/debug.log`. Recipient addresses and subjects are never logged.

**Does it support OAuth (Gmail / Microsoft 365)?**
Not yet. Use an app password or your provider's SMTP relay credentials.

## Project structure

```
beeleewe-smtp/
├── beeleewe-smtp.php        # Main plugin file
├── uninstall.php            # Removes all plugin data on delete
├── readme.txt               # WordPress.org style readme
├── assets/
│   ├── admin.js             # Test-email AJAX
│   └── admin.css
└── languages/
    └── beeleewe-smtp.pot    # Translation template
```

## Upgrading from 1.0.0

Updating is automatic. Passwords stored by 1.0.0 (encrypted with a key in the database, or plain text) are re-encrypted in the new format on the first request after the update, and the old key is removed. Version 1.1.0 also fixes two bugs from 1.0.0 that could lose the saved password, so update before relying on the old version.

See [CHANGELOG.md](CHANGELOG.md) for the full list of changes.

## Translations

The text domain is `beeleewe-smtp`. Use `languages/beeleewe-smtp.pot` as the template for `.po` / `.mo` files named `beeleewe-smtp-<locale>.po` / `.mo`.

## Contributing

Issues and pull requests are welcome. Before opening a PR:

- Keep code compatible with PHP 7.4 and WordPress 5.5.
- Escape all output and unslash/sanitize all input.
- Update `CHANGELOG.md` and `readme.txt`, and regenerate the `.pot` if you add or change strings.

## License

GPL v2 or later. See <https://www.gnu.org/licenses/gpl-2.0.html>.

## Author

[Beeleewe](https://beeleewe.com)
