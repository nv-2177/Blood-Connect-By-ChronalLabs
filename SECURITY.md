# Security Policy

BloodConnect handles sensitive personal and medical information — blood types, health conditions, location data, and emergency contacts. We take the security and privacy of our users extremely seriously. This document explains how to responsibly report vulnerabilities and outlines our security practices.

---

## Table of Contents

- [Supported Versions](#supported-versions)
- [Reporting a Vulnerability](#reporting-a-vulnerability)
- [Our Commitment to Reporters](#our-commitment-to-reporters)
- [Disclosure Process](#disclosure-process)
- [Security Best Practices for Deployment](#security-best-practices-for-deployment)
- [Known Sensitive Data Areas](#known-sensitive-data-areas)
- [Out of Scope](#out-of-scope)

---

## Supported Versions

We provide security updates for the following versions:

| Version | Supported |
|---------|-----------|
| Latest `main` branch | ✅ Active support |
| Previous major release | ⚠️ Critical fixes only |
| Older releases | ❌ No longer supported |

We strongly recommend always running the latest version in production.

---

## Reporting a Vulnerability

**Please do not report security vulnerabilities through public GitHub Issues.**

Public disclosure before a fix is available puts users at risk — particularly in an emergency medical context where user data is sensitive.

### How to Report

Send your report via **email** to:

```
security@bloodconnect.example.com
```

> Replace the above with your actual security contact address before publishing.

### What to Include

To help us triage and reproduce the issue quickly, please provide:

- **Type of vulnerability** (e.g., SQL injection, XSS, CSRF, broken authentication, IDOR, data exposure)
- **Affected component** (URL, view name, model, or feature area)
- **Steps to reproduce** — as detailed as possible
- **Proof of concept** — screenshots, HTTP request/response logs, or a minimal reproduction script
- **Potential impact** — what data or functionality could be affected
- **Your suggested fix** (optional but appreciated)

If the report involves personal data exposure, please **do not include real user data** in your report. Fabricated or anonymized examples are sufficient.

### Encryption

If you'd like to encrypt your report, please request our PGP public key in your initial email and we will provide it promptly.

---

## Our Commitment to Reporters

We are committed to working collaboratively with security researchers and the community. Upon receiving a report, we will:

- **Acknowledge receipt** within 48 hours
- **Provide an initial assessment** (confirmed, not reproducible, or needs more info) within 5 business days
- **Keep you informed** of our progress toward a fix
- **Credit you** in our release notes and security advisories (unless you prefer to remain anonymous)
- **Not pursue legal action** against researchers who act in good faith and follow this policy

We ask that reporters:

- Allow us reasonable time to patch before public disclosure
- Avoid accessing, modifying, or deleting user data during testing
- Do not perform denial-of-service attacks or social engineering against our users

---

## Disclosure Process

1. Reporter submits vulnerability privately.
2. Maintainers acknowledge and begin investigation.
3. A fix is developed and tested.
4. A patched release is published.
5. A security advisory is posted (GitHub Security Advisories or release notes).
6. Public disclosure occurs, with credit to the reporter if desired.

We target a **90-day disclosure timeline** from receipt of report to public disclosure, though critical vulnerabilities affecting user safety may be patched and disclosed faster.

---

## Security Best Practices for Deployment

If you are self-hosting BloodConnect, follow these guidelines to maintain a secure environment.

### Environment Variables

Never hardcode secrets. Always use environment variables:

```env
SECRET_KEY=<long-random-string-minimum-50-chars>
DEBUG=False
ALLOWED_HOSTS=yourdomain.com,www.yourdomain.com
DATABASE_URL=postgres://user:password@host:5432/bloodconnect
```

- Generate `SECRET_KEY` using `python -c "import secrets; print(secrets.token_urlsafe(60))"`.
- Never set `DEBUG=True` in production. This exposes stack traces and internal settings.
- Restrict `ALLOWED_HOSTS` to only your domain(s).

### Database

- Use **PostgreSQL** in production — never SQLite.
- Use a dedicated database user with minimal privileges (SELECT, INSERT, UPDATE, DELETE only — no DROP or CREATE).
- Enable SSL for all database connections.
- Schedule regular encrypted database backups.

### HTTPS

- Serve all traffic over HTTPS using a valid TLS certificate (e.g., Let's Encrypt).
- Redirect all HTTP traffic to HTTPS at the web server level.
- Enable **HSTS** in Django settings:

  ```python
  SECURE_HSTS_SECONDS = 31536000
  SECURE_HSTS_INCLUDE_SUBDOMAINS = True
  SECURE_HSTS_PRELOAD = True
  SECURE_SSL_REDIRECT = True
  ```

### Django Security Settings (Production Checklist)

Enable the following in `settings.py` for production:

```python
SESSION_COOKIE_SECURE = True
SESSION_COOKIE_HTTPONLY = True
CSRF_COOKIE_SECURE = True
CSRF_COOKIE_HTTPONLY = True
X_FRAME_OPTIONS = 'DENY'
SECURE_CONTENT_TYPE_NOSNIFF = True
SECURE_BROWSER_XSS_FILTER = True
```

Run Django's built-in deployment check before going live:

```bash
python manage.py check --deploy
```

### Authentication

- Enforce strong passwords in production — configure Django's `AUTH_PASSWORD_VALIDATORS`.
- Limit login attempts to prevent brute-force attacks (consider `django-axes` or similar).
- Admin panel (`/admin/`) should be protected by IP allowlist or moved to a non-default URL.
- Superuser accounts should use unique, strong passwords and ideally two-factor authentication.

### Static and Media Files

- Serve static files via **WhiteNoise** (already configured) or a reverse proxy like Nginx.
- Never serve user-uploaded files from the same origin without validation.
- Validate all uploaded content for file type, size, and content.

### Dependencies

- Regularly audit dependencies with:

  ```bash
  pip audit
  ```

- Keep Django, Pillow, and all other packages up to date.
- Subscribe to [Django security release announcements](https://www.djangoproject.com/weblog/).

### Google Sheets Integration

- If using the Google Apps Script contact form integration, restrict the Apps Script Web App to authorized users only where possible.
- Treat the Web App URL as a secret — do not expose it in client-side code or public repositories.

---

## Known Sensitive Data Areas

The following areas of the application handle particularly sensitive information and should receive extra scrutiny in security reviews:

| Area | Sensitivity |
|------|-------------|
| `DonorProfile` | Blood type, health conditions, RH factor |
| `EmergencyContact` | Personal contact details |
| `BloodRequest` | Medical emergency details, location |
| `HospitalProfile` | Blood stock levels, verified status |
| `BloodDonationHistory` | Medical history and donation records |
| User authentication | Passwords, session tokens |
| Contact form | User messages sent to Google Sheets |

All views accessing these models should verify authentication and authorization. Users must only be able to access their own data unless they have an explicit elevated role (hospital staff, admin).

---

## Out of Scope

The following are considered out of scope for security reports:

- Vulnerabilities in the local development environment (SQLite, `DEBUG=True`) that do not apply to production
- Missing security headers on non-sensitive, publicly accessible static pages
- Self-XSS that requires the attacker to already have account access
- Rate limiting on non-sensitive endpoints
- Social engineering attacks targeting users outside the platform
- Theoretical vulnerabilities without a demonstrated impact
- Issues in third-party dependencies that are already publicly known and tracked upstream

---

## Attribution

This security policy is adapted from responsible disclosure best practices. We thank the security community for helping keep BloodConnect and its users safe.
