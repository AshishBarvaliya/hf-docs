# Permissions and roles

Multi-tenant hiring requires **permission-based** UI within the **current tenant workspace** (resolved from subdomain at login), not ad hoc role string checks scattered in components. See [multi-tenancy.md](./multi-tenancy.md).

## Role hierarchy (typical)

```text
Company Admin
    ↓
Recruiter
    ↓
Hiring Manager
    ↓
Interviewer
```

Exact role names come from the auth/workspace API; the client maps them to a permission set.

## Permission examples

```text
jobs.read
jobs.create
jobs.edit
candidates.read
candidates.edit
candidates.reject
assessments.assign
interviews.schedule
analytics.read
billing.manage
settings.manage
```

## Frontend pattern

```tsx
<Can permission="candidates.reject">
  <RejectCandidateButton />
</Can>
```

Implementation target: `src/shared/permissions/` (or `src/lib/permissions/`) with:

- `usePermissions()` — from session/workspace context
- `<Can permission="…">` — render children or fallback
- Optional `requirePermission()` for server actions and route guards

**Do not** use `if (user.role === "admin")` in feature code; extend the permission map when roles change.

## Navigation and routes

- Sidebar: omit or disable items without `*.read` permissions.
- Destructive actions: gate with explicit permissions (`candidates.reject`, not generic `candidates.edit`).
- Job-scoped actions may further check job ownership via API; client gates are not a substitute for server authorization.
