# PayProof Development Guide

## 1. Purpose

This document explains how the PayProof team develops, tests, reviews, and merges code.

Read this together with:

```text
docs/TEAM_CONTRACT.md
```

The Team Contract defines **who owns what and the architecture**.

This document defines **how we work together in GitHub**.

---

## 2. Repository

GitHub repository:

```text
https://github.com/Aliyan4848/PayProof
```

Default branch:

```text
main
```

The `main` branch should always contain code that is safe to integrate into the current project.

Do not use `main` as your personal development branch.

---

## 3. Team Workflow

The standard workflow is:

```text
Pull latest main
       ↓
Create feature branch
       ↓
Write code
       ↓
Test locally
       ↓
Commit changes
       ↓
Push branch
       ↓
Create Pull Request
       ↓
Code Review
       ↓
QA / Testing
       ↓
Merge into main
```

No teammate should directly push normal feature work to `main`.

---

## 4. First-Time Setup

Clone the repository:

```bash
git clone https://github.com/Aliyan4848/PayProof.git
```

Enter the project:

```bash
cd PayProof
```

Check the current branch:

```bash
git branch
```

Switch to `main`:

```bash
git checkout main
```

Pull the latest version:

```bash
git pull origin main
```

Install dependencies according to the project instructions in the relevant package directories.

---

## 5. Before Starting Any Task

Before coding, every teammate must:

1. Pull the latest `main`.
2. Read `README.md`.
3. Read `docs/TEAM_CONTRACT.md`.
4. Read this `DEVELOPMENT.md`.
5. Read their role-specific instructions.
6. Inspect the existing code.
7. Identify files/modules owned by other teammates.
8. Understand the API/database interfaces they will use.

Do not immediately start rewriting files.

---

## 6. Branching Strategy

Each task should have its own branch.

Recommended branches:

```text
feature/backend-paypal
feature/frontend
feature/verification
feature/database-auth
feature/testing-demo
```

For smaller tasks, use a more specific name:

```text
feature/paypal-authorization
feature/contract-api
feature/fibonacci-verifier
feature/supabase-schema
feature/audit-dashboard
```

Create a branch from the latest `main`:

```bash
git checkout main
git pull origin main
git checkout -b feature/your-feature-name
```

---

## 7. Working on a Feature

While working:

```bash
git status
```

Use this regularly to see what changed.

Before committing:

```bash
git diff
```

Review your changes and make sure you did not accidentally modify another teammate's work.

---

## 8. Commit Rules

Use clear commit messages.

Good:

```text
feat: add contract creation API
feat: add PayPal authorization flow
feat: add Fibonacci verifier
fix: handle failed verification state
fix: prevent duplicate payment capture
docs: update API documentation
test: add contract API tests
```

Avoid:

```text
update
changes
final
final final
test
stuff
```

Keep commits focused on one logical change whenever possible.

---

## 9. Push Your Branch

After testing:

```bash
git add .
git commit -m "feat: describe your change"
git push -u origin feature/your-feature-name
```

Then open a Pull Request on GitHub.

---

## 10. Pull Requests

Every Pull Request should explain:

### What changed?

Describe the feature or fix.

### Why?

Explain the problem being solved.

### Files/modules changed

List the important files.

### Testing

Explain what you tested.

Example:

```text
Tests:
- npm run build
- npm run typecheck
- Contract API tested locally
- PayPal Sandbox authorization tested
```

### Integration requirements

If another teammate needs to change something, explain it.

Example:

```text
Frontend needs to call:
POST /contracts/:id/payment/create
```

---

## 11. Code Review

Before merging, reviewers should check:

* Does the code match the Team Contract?
* Does it stay within the owner's responsibility?
* Does it break another module?
* Are secrets exposed?
* Are API inputs validated?
* Are authentication/ownership checks present?
* Are payment operations protected?
* Are state transitions valid?
* Are tests included?
* Does the project still build?

Payment, authentication, database, and state-machine changes require extra review.

---

## 12. Keeping Your Branch Updated

If `main` has changed while you are working:

```bash
git checkout main
git pull origin main
```

Then return to your branch:

```bash
git checkout feature/your-feature-name
```

Update your branch using the team's agreed Git strategy.

For a simple team workflow, merging the latest `main` into your feature branch is acceptable:

```bash
git merge main
```

Resolve conflicts carefully and test again.

---

## 13. Merge Conflicts

If Git reports a conflict:

1. Do not randomly delete code.
2. Identify which teammate owns the conflicting module.
3. Understand both changes.
4. Keep the correct combined behavior.
5. Test the result.
6. Ask the Team Lead if the conflict affects architecture, payment, database, or state management.

After resolving:

```bash
git add .
git commit
```

Then push the branch again.

---

## 14. AI Coding Rules

Every teammate may use an AI coding assistant.

However, AI does not automatically understand the team's architecture.

Before asking AI to code, provide:

```text
1. PayProof Master Context
2. docs/TEAM_CONTRACT.md
3. docs/DEVELOPMENT.md
4. Your role prompt
5. Your specific task
```

The AI must:

* Inspect the repository first.
* Understand existing code.
* Follow the Team Contract.
* Stay within its assigned responsibility.
* Avoid unnecessary rewrites.
* Avoid changing unrelated files.
* Preserve existing interfaces.
* Test its changes.
* Explain what it changed.

Recommended instruction:

```text
Do not rebuild the project from scratch.

First inspect the repository and existing implementation.

Follow docs/TEAM_CONTRACT.md and docs/DEVELOPMENT.md.

Only modify files necessary for the assigned task.

Do not modify another team's module unless integration requires it.

Before finishing, run the relevant tests/build/type checks and report all changes.
```

---

## 15. AI Handoff Protocol

When an AI finishes a task, it must provide:

```text
TASK COMPLETED

Files changed:
- ...

Files created:
- ...

APIs changed:
- ...

Database changes:
- ...

Environment variables:
- ...

Dependencies:
- ...

Tests run:
- ...

Known issues:
- ...

Integration needed from other teammates:
- ...
```

This makes it easier for the next teammate or AI to continue the work.

---

## 16. Environment Variables

Never commit secrets.

Use local environment files such as:

```text
.env.local
```

or the appropriate environment file for the backend.

Examples of secrets that must remain private:

```text
PAYPAL_CLIENT_SECRET
SUPABASE_SERVICE_ROLE_KEY
AI_API_KEY
DATABASE_PASSWORD
```

Only commit safe example files such as:

```text
.env.example
```

Example:

```env
VITE_API_URL=http://localhost:3000
PAYPAL_CLIENT_ID=your_client_id_here
PAYPAL_CLIENT_SECRET=your_secret_here
```

Never put real credentials in `.env.example`.

---

## 17. Module Ownership

The current ownership model is:

| Area                           | Owner         |
| ------------------------------ | ------------- |
| Backend / PayPal / Integration | Aliyan        |
| Frontend                       | Team Member 2 |
| AI / Verification              | Team Member 3 |
| Database / Auth / Storage      | Team Member 4 |
| QA / Testing / Demo            | Team Member 5 |

If you need to modify another owner's module, communicate with that teammate first.

---

## 18. Shared Interfaces

Teams should agree on interfaces before building dependent features.

For example:

```text
Frontend
   ↓
Backend API
   ↓
Verification Service
   ↓
Verification Result
   ↓
Backend Payment Policy
   ↓
PayPal Capture
```

Do not create separate competing versions of the same API or business rule.

---

## 19. Testing Requirements

At minimum, test:

### Backend

* Contract creation
* Invalid contract input
* Payment creation
* Payment authorization
* Unauthorized requests
* Submission creation
* Verification
* Failed verification
* Successful verification
* Capture
* Duplicate capture attempt
* Invalid state transitions

### Verification

* Valid submission
* Invalid submission
* Failed test
* Passing test
* Malformed file
* Oversized file
* Timeout
* Unexpected execution error

### Frontend

* Contract creation
* Loading states
* Error states
* Submission
* Verification status
* Payment status
* Audit timeline

---

## 20. Critical Payment Test

The following scenario must work before the final demo:

```text
Buyer creates contract
        ↓
PayPal authorization
        ↓
Worker submits incorrect work
        ↓
Verification FAIL
        ↓
Payment remains protected
        ↓
Worker submits correction
        ↓
Verification PASS
        ↓
Backend validates capture conditions
        ↓
PayPal capture
        ↓
Contract CAPTURED
```

This is the most important end-to-end test in the project.

---

## 21. What Not to Build Yet

Do not spend development time on:

* Full messaging platform
* Social network
* Mobile application
* Cryptocurrency
* Complex reputation system
* Large admin systems
* Unnecessary animations
* Features unrelated to outcome verification and payment

First make the core flow reliable.

---

## 22. Definition of Done

A task is complete only when:

* Code works.
* Relevant tests pass.
* Build/type checks pass.
* No secrets are committed.
* Existing functionality still works.
* The implementation follows `TEAM_CONTRACT.md`.
* Documentation is updated when necessary.
* The Pull Request clearly explains the changes.
* QA can reproduce and verify the feature.

---

## 23. Final Team Principle

The goal is not to build the largest application.

The goal is to build the clearest and most reliable demonstration of:

```text
A buyer makes a promise of payment
            ↓
PayPal protects the payment
            ↓
A worker delivers an outcome
            ↓
AI + deterministic verification checks the outcome
            ↓
Failed result → payment stays protected
            ↓
Successful result → payment is captured
            ↓
Everything is recorded in an audit trail
```

**Build the core loop first. Expand only after the core loop works.**
