# Contributing to BloodConnect

Thank you for your interest in contributing to BloodConnect — a platform built to save lives by connecting blood donors, seekers, and hospitals during emergencies. Every contribution, big or small, matters.

---

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [How to Contribute](#how-to-contribute)
- [Development Workflow](#development-workflow)
- [Coding Standards](#coding-standards)
- [Commit Message Guidelines](#commit-message-guidelines)
- [Pull Request Process](#pull-request-process)
- [Reporting Bugs](#reporting-bugs)
- [Suggesting Features](#suggesting-features)
- [Testing](#testing)
- [Community](#community)

---

## Code of Conduct

By participating in this project, you agree to uphold a respectful and inclusive environment. We expect all contributors to:

- Use welcoming and inclusive language
- Respect differing viewpoints and experiences
- Gracefully accept constructive feedback
- Prioritize what is best for the community and the people this platform serves

Unacceptable behavior should be reported to the project maintainers.

---

## Getting Started

### Prerequisites

- Python 3.10+
- pip
- Git

### Local Setup

1. **Fork** the repository on GitHub, then clone your fork:

   ```bash
   git clone https://github.com/YOUR_USERNAME/bloodconnect.git
   cd bloodconnect
   ```

2. **Set up a virtual environment:**

   ```bash
   python -m venv venv
   source venv/bin/activate  # Windows: venv\Scripts\activate
   pip install -r requirements.txt
   ```

3. **Configure your environment:**

   ```bash
   cp .env.example .env
   # Edit .env and set SECRET_KEY and other variables
   ```

4. **Run migrations and start the server:**

   ```bash
   python manage.py makemigrations users donors seekers hospitals blood_requests
   python manage.py migrate
   python manage.py createsuperuser
   python manage.py runserver
   ```

5. **Add the upstream remote** so you can keep your fork in sync:

   ```bash
   git remote add upstream https://github.com/ORIGINAL_OWNER/bloodconnect.git
   ```

---

## How to Contribute

There are many ways to contribute beyond writing code:

- **Bug reports** — Found something broken? [Open an issue](#reporting-bugs).
- **Feature suggestions** — Have an idea? [Start a discussion](#suggesting-features).
- **Documentation** — Improve README, docstrings, or inline comments.
- **UI/UX improvements** — Better accessibility, responsiveness, or design.
- **Translations** — Help localize the platform for different regions.
- **Testing** — Write or improve test coverage.
- **Code contributions** — Fix bugs or implement new features.

---

## Development Workflow

1. **Sync your fork** with the latest upstream changes:

   ```bash
   git fetch upstream
   git checkout main
   git merge upstream/main
   ```

2. **Create a feature branch** from `main`:

   ```bash
   git checkout -b feat/your-feature-name
   # or for bug fixes:
   git checkout -b fix/issue-description
   ```

3. **Make your changes**, keeping commits focused and logical.

4. **Run tests** before pushing (see [Testing](#testing)).

5. **Push your branch** and open a Pull Request.

---

## Coding Standards

### Python / Django

- Follow [PEP 8](https://peps.python.org/pep-0008/) style conventions.
- Use meaningful variable and function names.
- Add docstrings to all models, views, and utility functions.
- Keep views thin — move business logic into services or model methods.
- Use Django's ORM; avoid raw SQL unless absolutely necessary.
- All new models must include `__str__` methods.

### HTML / Templates

- Use Bootstrap 5 utility classes; avoid inline styles.
- Maintain the existing color scheme: `#d62828` (Blood Red) and `#34c1c6` (Teal).
- All templates must be mobile-responsive.
- Use `{% url %}` tags instead of hardcoded paths.

### JavaScript

- Keep JS minimal and purposeful.
- Leaflet.js is the approved mapping library — do not introduce alternatives.
- Avoid CDN dependencies beyond those already in use.

### General

- No commented-out dead code in PRs.
- No `print()` statements in production code — use Django's logging framework.
- Secrets and credentials must never be committed. Use `.env` variables.

---

## Commit Message Guidelines

Use clear, imperative commit messages following this format:

```
<type>(<scope>): <short summary>

[optional body]
[optional footer]
```

**Types:**

| Type | When to use |
|------|-------------|
| `feat` | A new feature |
| `fix` | A bug fix |
| `docs` | Documentation changes only |
| `style` | Formatting, missing semicolons, etc. (no logic change) |
| `refactor` | Code restructuring without feature or bug change |
| `test` | Adding or updating tests |
| `chore` | Build process, dependency updates, etc. |

**Examples:**

```
feat(hospitals): add blood stock low-level alert system
fix(donors): correct availability toggle not saving state
docs(readme): update deployment instructions for PostgreSQL
```

---

## Pull Request Process

1. Ensure your branch is up to date with `main` before opening a PR.
2. Fill in the PR template completely, including:
   - What the PR does
   - Which issue(s) it closes (use `Closes #123`)
   - Screenshots for UI changes
   - Steps to test
3. All PRs require at least **one approving review** before merge.
4. All CI checks (linting, tests) must pass.
5. Keep PRs focused — one concern per PR. Large PRs are harder to review and slower to merge.
6. Be responsive to review feedback. PRs inactive for 14 days may be closed.

---

## Reporting Bugs

Before opening a bug report, please search existing issues to avoid duplicates.

When filing a bug, include:

- **Django version** and **Python version**
- **Steps to reproduce** the issue
- **Expected behavior** vs. **actual behavior**
- **Relevant logs or error messages** (redact any personal data)
- **Screenshots** if the issue is visual

Use the **Bug Report** issue template on GitHub.

---

## Suggesting Features

We welcome feature ideas, especially those that improve outcomes for blood seekers, donors, or hospitals.

When suggesting a feature:

- Describe the **problem** it solves, not just the solution.
- Explain who benefits (donor, seeker, hospital, admin).
- Consider edge cases and accessibility.
- Note any privacy or safety implications, given the sensitive health data involved.

Use the **Feature Request** issue template on GitHub.

---

## Testing

BloodConnect uses Django's built-in test framework.

**Run the full test suite:**

```bash
python manage.py test
```

**Run tests for a specific app:**

```bash
python manage.py test donors
python manage.py test hospitals
```

**Guidelines for writing tests:**

- Every new view should have at least one test for authenticated and unauthenticated access.
- Test model `__str__` methods and any custom model logic.
- Use `TestCase` for database tests and `SimpleTestCase` for logic without DB access.
- Use `Client` for simulating requests; avoid testing Django internals.
- Aim for meaningful coverage over high coverage numbers.

---

## Community

This project is built for communities in need. If you have questions, feel free to open a GitHub Discussion or reach out via the contact form on the platform. We appreciate every contribution that helps connect people when it matters most.
