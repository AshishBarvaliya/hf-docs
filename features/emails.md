# Emails

**Routes:** `(dashboard)/emails`, job/candidate email tabs  
**Domain:** `emails`  
**Roadmap step:** 10

## Product behavior

Recruiters view **templates**, send messages to candidates, and read **send history** / threads tied to jobs and profiles. Workflow stages can trigger emails (configured in workflow builder; sent by backend—client configures and monitors).

## Plan

1. **API** — Template list, render preview, send, thread/history pagination.
2. **Phase A** — Templates library (read) + send composer on candidate profile.
3. **Phase B** — Job-level email tab; bulk send with permission gates.
4. **Phase C** — `/emails` hub: templates CRUD (if API allows), recent sends, failed delivery queue.
5. **Dependencies** — Email service integration; workflow notification flags from step 7.

## Dev

### Contract

| Area | Details |
|------|---------|
| Send | `POST /api/v1/emails/send` — template id, merge fields, candidate/job context |
| Threads | `GET /api/v1/emails/threads?candidateId=` |
| Templates | `GET/POST/PATCH /api/v1/email-templates` when hub ships |
| Permissions | `emails.send`, `emails.templates.manage` (catalog) |

### Data

`email_messages`, `email_templates` (or integration-owned ids + local cache) — workspace-scoped; delivery status from provider webhooks.

### Server

- `server/src/domains/emails/` — send + template CRUD; integration client

### Client

- `src/domains/emails/` — `MessageComposer`, template picker, `useSendEmail`, `useEmailThread`
- RHF + Zod for merge fields; i18n for UI chrome

### Tests

- **Server:** send validation, 403 without `emails.send`
- **Client unit:** composer schema, template variable map
- **Client e2e:** send from profile tab when API ready

## Acceptance criteria

- [ ] Send from candidate profile and job tab
- [ ] History visible on Emails tab of profile
- [ ] Template management per permissions
- [ ] Errors from email service shown with retry where safe

## Design mockups

| File | Notes |
|------|--------|
| `designs/screens/EmailTemplates.html` | Template list |
| `designs/screens/EmailTemplateEdit.html` | Template editor (assessment invite) |
| `designs/screens/EmailCompose.html` | Composer on candidate profile |
| `designs/screens/EmailSent.html` | Sent and failed deliveries |
| `designs/screens/ProfileEmails.html` | Email history tab on profile |

## References

- [external-integrations.md](./external-integrations.md)
- [automations.md](./automations.md)
- [product-design-mockups.md](../architecture/product-design-mockups.md)
