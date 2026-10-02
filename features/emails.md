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

| Piece | Implementation |
|-------|----------------|
| Domain | `src/domains/emails/` |
| Composer | RHF + template variables; Zod for required merge fields |
| UI | `MessageComposer`, thread list, template picker |
| Hooks | `useEmailTemplates`, `useSendEmail`, `useEmailThread` |

- **Permissions:** Distinct from generic messaging—align with backend (`emails.send`, etc.).
- **i18n:** Template content may be locale-specific server-side; UI chrome translated client-side.

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
