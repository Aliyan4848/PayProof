PayProof Team Contract

1. Purpose

This is the shared engineering contract for the PayProof hackathon team. Every teammate and every coding AI must read this file before making changes.

Core idea:

Don’t pay for the work. Pay for the verified result.

PayProof is an outcome-based payment platform. A buyer defines a contract with a clear deliverable, success criteria, and payment amount. PayPal authorization protects the buyer’s payment while the contracted outcome is being verified. A worker submits evidence or a deliverable. PayProof verifies it using deterministic checks plus AI reasoning where appropriate. Only the backend payment policy can allow PayPal capture after successful verification.

Do not describe PayProof as PayPal putting money into our AI escrow. For the hackathon MVP, describe it as:

PayProof uses PayPal authorization and capture to protect the buyer’s payment until the contracted outcome is verified.

⸻

2. Team Ownership

Team Lead — Backend / PayPal / Integration

Owner: Aliyan Ahmed

Responsibilities:

* Overall architecture and integration decisions
* Backend API and contract state machine
* PayPal Sandbox integration
* Payment authorization and capture policy
* Security and server-side payment rules
* Final integration and code review

Must not:

* Put PayPal secrets in the frontend
* Allow AI to directly call PayPal
* Capture payment merely because an AI says PASS

Frontend

Owner: Team Member 2

Responsibilities:

* Buyer contract creation
* Acceptance criteria UI
* Worker/freelancer dashboard
* Submission UI
* Verification and payment status
* Audit/timeline UI
* API client
* Loading and error states

Must not:

* Implement payment capture logic
* Treat frontend state as proof of payment or verification
* Hardcode production business state

AI / Verification

Owner: Team Member 3

Responsibilities:

* Requirement and acceptance-criteria interpretation
* Deterministic verification checks
* AI evaluation
* Structured verification verdicts
* Evidence extraction
* PASS/FAIL reasoning
* Correction feedback

Must not:

* Call PayPal directly
* Decide payment capture
* Bypass deterministic checks when they are available

Database / Auth / Storage

Owner: Team Member 4

Responsibilities:

* Supabase schema and migrations
* Authentication
* Authorization/RLS
* Private submission storage
* Contract/submission/verification/audit data model
* Indexes
* Constraints
* Timestamps
* Ownership relationships

QA / Testing / Demo

Owner: Team Member 5

Responsibilities:

* Unit testing
* Integration testing
* API testing
* UI testing
* Security and edge-case testing
* End-to-end demo verification
* Bug reports
* Demo script
* Documentation support

⸻

3. Repository Rules

Repository: Aliyan4848/PayProof

Default branch: main

Rules:

1. Do not push directly to main during normal development.
2. Every teammate works from a feature branch.
3. Open a Pull Request before merging.
4. Team Lead reviews integration-sensitive changes.
5. Keep commits small and descriptive.
6. Never commit .env files containing secrets.
7. Never commit PayPal secrets, Supabase service-role keys, AI API keys, or other credentials.
8. Do not rewrite or delete another teammate’s work without coordination.
9. Cross-module changes require communication.
10. Test changes before requesting review.
11. Every coding AI must read this file before coding.

Recommended branches:

feature/backend-paypal
feature/frontend
feature/verification
feature/database-auth
feature/testing-demo

⸻

4. Shared Architecture

                    PayProof
                       |
                  Frontend UI
                       |
                       v
                Backend API
                 /    |    \
                /     |     \
               v      v      v
          Supabase  PayPal  Verification
          Database  Sandbox    Engine
          Auth      Payment       |
          Storage                / \
                               /   \
                    Deterministic  AI
                       Checks    Reasoning

Core principle:

AI verifies. Backend policy decides. PayPal settles.

The AI must never have authority to capture, refund, or otherwise move money.

⸻

5. Core Product Flow

The MVP vertical slice is:

1. Buyer creates a contract.
2. Buyer defines the deliverable.
3. Buyer defines acceptance criteria.
4. Buyer defines the payment amount.
5. Backend creates a PayPal Sandbox order using the authorization flow.
6. Buyer approves the PayPal payment.
7. Backend confirms and records the authorization.
8. Contract becomes AUTHORIZED.
9. Worker submits the deliverable/evidence.
10. Verification starts.
11. Deterministic checks run where applicable.
12. AI evaluates the available evidence.
13. AI returns a structured verification result.
14. If verification fails, payment remains protected.
15. Worker receives correction feedback.
16. Worker submits a correction.
17. Verification runs again.
18. Verification passes.
19. Backend payment policy checks all capture conditions.
20. Backend captures the PayPal payment.
21. Contract becomes CAPTURED.
22. Audit trail records the complete sequence.

Demo Scenario

Use a simple C++ Fibonacci task.

Example:

Buyer
  |
  | Create $10 contract
  v
PayPal Authorization
  |
  v
Worker submits fib.cpp
  |
  v
Verification
  |
  +---- FAIL
  |       |
  |       v
  |   Payment remains protected
  |       |
  |       v
  |   Worker corrects code
  |
  v
Verification PASS
  |
  v
Backend payment policy
  |
  v
PayPal Capture
  |
  v
Contract Completed

⸻

6. Contract State Machine

The agreed state model is:

DRAFT
   |
   v
PENDING_PAYMENT
   |
   v
AUTHORIZED
   |
   v
AWAITING_SUBMISSION
   |
   v
VERIFYING
   |
   +----> VERIFICATION_FAILED
   |              |
   |              v
   |      AWAITING_SUBMISSION
   |
   v
VERIFIED
   |
   v
CAPTURE_PENDING
   |
   v
CAPTURED

CANCELLED may be supported where appropriate.

Important Rules

* Never mark AUTHORIZED merely because a PayPal order was created.
* Never mark VERIFIED without recorded verification evidence.
* Never capture unless backend policy confirms the required conditions.
* State transitions should be centralized.
* Important state transitions should create audit events.
* Do not allow random API routes to directly manipulate sensitive states without validation.

⸻

7. Payment Rules

PayPal Sandbox is the payment integration for the hackathon.

Required payment flow:

Create PayPal Order
        ↓
Buyer Approval
        ↓
Backend Authorization Confirmation
        ↓
Contract AUTHORIZED
        ↓
Work Submitted
        ↓
Verification
        ↓
PASS
        ↓
Backend Capture Policy
        ↓
PayPal Capture

Before capture, the backend must verify as applicable:

* Contract belongs to the authorized buyer/account.
* Contract is in the correct state.
* A valid PayPal authorization exists.
* PayPal order/authorization belongs to this contract.
* Latest relevant verification passed.
* All mandatory acceptance criteria passed.
* Capture has not already happened.
* Request is authorized.
* Operation is idempotent.

Never trust:

* Frontend status
* Client-supplied verified=true
* AI output alone
* Client-supplied PayPal identifiers without server-side validation

Critical Rule

AI can recommend PASS. AI cannot capture the payment.

The backend makes the final payment decision.

⸻

8. Verification Rules

Verification should be modular.

Initial structure:

Verification Engine
│
├── CodeVerifier
├── DocumentVerifier
├── DesignVerifier
└── GenericEvidenceVerifier

For the hackathon MVP, start with:

CodeVerifier

Code Verification

For uploaded code:

1. Validate file type.
2. Validate file size.
3. Store the submission privately.
4. Create a checksum/version.
5. Run deterministic tests.
6. Use an isolated execution environment.
7. Apply timeout/resource/network restrictions.
8. Record test output as evidence.
9. Use AI to interpret requirements and evidence where useful.
10. Return a structured verification result.

Example:

{
  "verdict": "PASS",
  "confidence": 0.97,
  "criteria": [
    {
      "id": "fib-tests",
      "status": "PASS",
      "evidence": "All required Fibonacci tests passed."
    }
  ],
  "summary": "The submitted deliverable satisfies all mandatory criteria.",
  "correction": null
}

The exact schema may evolve.

The principle must remain:

AI produces evidence and reasoning. Backend policy decides whether payment may proceed.

Security

Never execute arbitrary uploaded code directly inside the public API process.

Uploaded code must run in an isolated environment with appropriate:

* Timeout
* CPU limits
* Memory limits
* Process limits
* Network restrictions
* Filesystem restrictions

⸻

9. Database Principles

The database should support at minimum:

Users / Profiles
Contracts
Contract Criteria
Payments
PayPal Orders
PayPal Authorizations
Submissions
Submission Versions
Verification Runs
Verification Criteria Results
Audit Events

Database requirements:

* Foreign keys where appropriate
* Useful indexes
* Created/updated timestamps
* Ownership relationships
* Constraints for valid states
* Private storage for submitted files
* RLS where appropriate
* Server-side authorization for sensitive operations

The database state model and application state model must remain consistent.

⸻

10. API Conventions

Example API responsibilities:

POST   /contracts
GET    /contracts/:id
POST   /contracts/:id/payment/create
POST   /contracts/:id/payment/authorize
POST   /contracts/:id/submissions
GET    /contracts/:id/submissions
POST   /contracts/:id/verify
GET    /contracts/:id/verification
POST   /contracts/:id/capture
GET    /contracts/:id/audit

Exact routes may change during implementation, but responsibilities must remain clear.

Use:

* Request validation
* Consistent error responses
* Authentication
* Ownership checks
* Idempotency for payment-changing operations
* Server-side logging
* Audit events

⸻

11. Frontend Integration Contract

The frontend must consume the real backend instead of maintaining fake payment and verification state.

Use a centralized API configuration such as:

VITE_API_URL

The frontend should display real backend states:

Contract Status
Payment Status
Submission Status
Verification Status
Capture Status
Audit Events

Never put PayPal secrets in frontend environment variables.

⸻

12. Security Rules

Never commit:

PayPal client secrets
Supabase service-role keys
AI provider API keys
Database passwords
Private tokens
.env files containing secrets

Sensitive operations must be server-side.

Uploaded files must be treated as untrusted input.

AI output must be treated as untrusted data and validated before use.

Uploaded code must never execute with unrestricted host access.

⸻

13. AI Coding Workflow

Every teammate should give their coding AI:

1. The shared PayProof Master Context/architecture prompt.
2. This docs/TEAM_CONTRACT.md.
3. Their role-specific prompt.
4. The current task.
5. The current repository state.

Recommended context:

MASTER PROMPT
      +
TEAM CONTRACT
      +
ROLE PROMPT
      +
CURRENT TASK
      +
REPOSITORY CONTEXT

Every AI must:

1. Read the repository before editing.
2. Understand the existing architecture.
3. Identify interfaces used by other teammates.
4. Work only within its assigned responsibility unless integration requires otherwise.
5. Avoid rebuilding unrelated modules.
6. Preserve agreed interfaces.
7. Run tests/type checks/builds relevant to its changes.
8. Report all changes clearly.

After completing work, the AI should report:

Files changed:
APIs changed:
Database changes:
Environment variables added:
Dependencies added:
Tests run:
Known issues:
Integration requirements:

⸻

14. Integration Protocol

When one teammate needs another team’s functionality:

Step 1

Define the required interface.

Step 2

Agree on request/response structure.

Step 3

Implement against the agreed interface.

Step 4

Open a Pull Request.

Step 5

Team Lead reviews cross-module impact.

Step 6

QA tests the integrated flow.

Do not silently create competing implementations of the same business rule.

⸻

15. Definition of Done

A feature is not complete simply because it works on one developer’s machine.

Before merge:

* Code compiles.
* Type checking passes.
* Relevant tests pass.
* No secrets are committed.
* Error states are handled.
* Authentication/ownership checks exist where required.
* Database changes are documented.
* API contracts are documented.
* Frontend uses the real backend where applicable.
* Payment-changing operations are protected.
* Payment-changing operations are idempotent.
* Important actions create audit events.
* No unrelated files were unnecessarily changed.

⸻

16. Hackathon MVP Priority

Must Have

* Contract creation
* Acceptance criteria
* PayPal Sandbox authorization
* Worker submission
* Verification
* Failed verification keeps payment protected
* Correction submission
* Successful verification
* Backend-controlled PayPal capture
* Audit trail
* Clean demo UI

Should Have

* Authentication
* Private storage
* Realtime/polling status updates
* Multiple verifier types
* Stronger evidence reporting

Cut Until the Core Loop Works

* Full messaging system
* Reviews/reputation
* Crypto
* Mobile app
* Complex dispute marketplace
* Large social features
* Unnecessary dashboards

Priority

Build one reliable end-to-end outcome-based payment flow before expanding the product.

⸻

17. Demo Narrative

The entire demo should communicate one idea:

PayProof makes payment conditional on proof of the promised outcome.

Demo sequence:

PROMISE
   ↓
PAYPAL AUTHORIZATION
   ↓
WORKER SUBMISSION
   ↓
VERIFICATION FAILS
   ↓
PAYMENT STAYS PROTECTED
   ↓
CORRECTION
   ↓
VERIFICATION PASSES
   ↓
PAYPAL CAPTURE
   ↓
AUDIT PROOF

The audience should clearly see:

1. PayPal is meaningfully involved.
2. AI is meaningfully involved.
3. The backend—not the AI or frontend—controls payment settlement.
4. A failed deliverable does not release the buyer’s payment.
5. A successful deliverable causes the authorized payment to be captured.

⸻

18. Change Control

If a teammate believes the architecture needs to change:

1. Explain the problem.
2. Propose the alternative.
3. Identify affected modules.
4. Discuss the change with the Team Lead.
5. Agree on the new interface before implementing cross-cutting changes.

Security, payment, database, and state-machine changes require particular care.

This document is the shared source of truth unless the team explicitly updates it through a reviewed change.
