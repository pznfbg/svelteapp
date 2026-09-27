AGENTS.md

Project

SvelteKit application using Better Auth with SQLite for authentication.

Goal

Implement a complete authentication system from scratch using:

- SvelteKit
- Better Auth
- SQLite
- "better-sqlite3"

The initial implementation should remain simple and avoid introducing an ORM such as Drizzle or Prisma unless there is a later reason to do so.

---

Progress So Far

Stack

SvelteKit
   │
   ├── Svelte UI
   │
   ├── SvelteKit server
   │
   └── Better Auth
          │
          └── SQLite
               └── database.sqlite

Decisions

- Use SQLite as the initial database.
- Use Better Auth for authentication and session management.
- Use "better-sqlite3" as the SQLite driver.
- Do not implement password hashing manually.
- Do not implement session management manually.
- Do not store authentication/session data in "localStorage".
- Keep authentication configuration server-side.
- Use SvelteKit server-side functionality for protecting authenticated routes.
- Start without an ORM to keep the implementation understandable.

Dependencies planned

better-auth
better-sqlite3

Environment variables

Create a ".env" file containing:

BETTER_AUTH_SECRET=<strong-random-secret>
BETTER_AUTH_URL=http://localhost:5173

The Better Auth secret must be a strong, high-entropy secret and should not be committed to version control.

---

Current Status

The project has been planned around the following sequence:

1. Create SvelteKit project          ← started/planned
2. Install dependencies              ← planned
3. Configure environment variables  ← planned
4. Create Better Auth configuration ← NEXT
5. Connect Better Auth to SQLite
6. Run Better Auth database migration
7. Verify SQLite database
8. Configure SvelteKit server hook
9. Create signup page
10. Create login page
11. Expose session/user through locals
12. Protect dashboard
13. Implement logout
14. Add email verification
15. Add password reset
16. Add OAuth providers if needed
17. Add roles/permissions if needed
18. Consider 2FA/passkeys later

Do not skip ahead unnecessarily. The immediate goal is to establish a working Better Auth + SQLite backend before building the UI.

---

Next Task

Create "src/lib/auth.ts"

Configure Better Auth with:

- SQLite database
- "better-sqlite3"
- Email/password authentication
- Environment-based secret
- Environment-based base URL

Keep the Better Auth instance server-only.

The expected conceptual structure is:

src/
├── lib/
│   ├── auth.ts
│   └── auth-client.ts       # later
│
├── routes/
│   ├── login/               # later
│   ├── signup/              # later
│   └── ...
│
└── hooks.server.ts          # later

Do not create the client authentication code until the server-side Better Auth configuration is working.

---

Authentication Architecture

The intended request flow is:

Browser
   │
   │ login/signup
   ▼
SvelteKit
   │
   ▼
Better Auth
   │
   ├── Verify credentials
   ├── Hash/verify passwords
   ├── Create sessions
   └── Manage authentication cookies
   │
   ▼
SQLite

After login:

User logs in
     │
     ▼
Better Auth validates credentials
     │
     ▼
Session is created
     │
     ▼
Secure cookie is sent to browser
     │
     ▼
Browser sends cookie with future requests
     │
     ▼
SvelteKit/Better Auth identifies user

---

Security Rules

Never

- Store plaintext passwords.
- Implement password hashing manually.
- Put database credentials in client-side code.
- Put "BETTER_AUTH_SECRET" in client-side code.
- Store authentication tokens in "localStorage" unnecessarily.
- Trust client-side checks as authorization.
- Put Better Auth server configuration into browser code.
- Commit ".env" or other secrets.

Remember

Authentication answers:

«Who is this user?»

Authorization answers:

«Is this user allowed to perform this action?»

Better Auth primarily handles authentication/session management.

Application-specific authorization belongs in the application's server-side code.

---

Route Protection

Protected pages should be protected on the server.

For example:

/dashboard

should conceptually work like:

Request
   │
   ▼
SvelteKit server
   │
   ▼
Check session
   │
   ├── authenticated ──→ dashboard
   │
   └── unauthenticated → /login

Do not rely solely on:

{#if user}
    <Dashboard />
{/if}

A hidden UI element is not an authorization boundary.

---

Planned SvelteKit Session Integration

Use SvelteKit's server hooks and "event.locals" to make the authenticated user/session available to server-side application code.

Conceptually:

Request
   ↓
hooks.server.ts
   ↓
Better Auth session lookup
   ↓
event.locals.user
event.locals.session
   ↓
+page.server.ts / server endpoints

This should be implemented after the basic Better Auth + SQLite configuration has been verified.

---

Database

Initial database:

database.sqlite

Better Auth should manage the authentication schema/migrations.

Do not manually create authentication tables unless Better Auth documentation specifically requires it.

Expected authentication-related concepts include:

user
session
account
verification

The exact schema should be generated using the Better Auth tooling rather than manually guessed.

---

Development Principles

1. Make one authentication component work before adding another.
2. Verify database connectivity before building UI.
3. Keep server and client code clearly separated.
4. Prefer Better Auth's built-in functionality over custom authentication logic.
5. Keep the initial implementation minimal.
6. Explain why each authentication component exists.
7. Test authentication from the server's perspective, not just the browser UI.
8. Add additional authentication features only after basic login/logout works.

---

Immediate Milestone

The first milestone is:

SvelteKit
   +
Better Auth
   +
SQLite
   ↓
Successful Better Auth initialization
   ↓
SQLite database created/migrated
   ↓
Email/password authentication enabled

Only after this milestone is confirmed should login/signup UI be implemented.

---

Future Milestones

Milestone 1 — Backend

- [ ] Install Better Auth
- [ ] Install "better-sqlite3"
- [ ] Configure environment variables
- [ ] Create "src/lib/auth.ts"
- [ ] Connect SQLite
- [ ] Enable email/password authentication
- [ ] Run migrations
- [ ] Verify database

Milestone 2 — SvelteKit Integration

- [ ] Create Better Auth SvelteKit integration
- [ ] Configure "hooks.server.ts"
- [ ] Populate "event.locals"
- [ ] Verify authenticated sessions server-side

Milestone 3 — Authentication UI

- [ ] Signup page
- [ ] Login page
- [ ] Logout
- [ ] Error handling
- [ ] Loading states
- [ ] Form validation

Milestone 4 — Protected Application

- [ ] Protected dashboard
- [ ] Server-side authorization checks
- [ ] Display current user
- [ ] Handle expired/invalid sessions

Milestone 5 — Account Recovery

- [ ] Email verification
- [ ] Password reset
- [ ] Appropriate email provider

Milestone 6 — Optional Authentication

Only if needed:

- [ ] Google OAuth
- [ ] GitHub OAuth
- [ ] Passkeys
- [ ] Two-factor authentication
- [ ] Account linking

---

Important Context

This project is being built from scratch for learning and correctness, rather than copying a complete authentication starter template.

When making future changes, prefer explaining:

- what the file does,
- why it is needed,
- what Better Auth is responsible for,
- what SvelteKit is responsible for,
- and where the security boundary is.

Keep the implementation understandable before optimizing or adding abstractions.