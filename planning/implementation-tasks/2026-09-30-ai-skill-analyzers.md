# AI skill analyzers — catalog, assignment, interview reports

**Status:** proposed  
**Created:** 2026-09-30  
**Feature spec:** [docs/features/ai-skill-analyzers.md](../../features/ai-skill-analyzers.md)

## Summary

Hyreefy default analyzers (tech × seniority), manager-editable workspace copies, job skills with **priority + contribution**, server **assignment** to candidate-job, recording-driven **detailed reports** on candidate profile, optional pre-interview profile mode.

## Out of scope (this task)

- In-app video playback hosting (use meeting product URLs)
- Auto-hire/reject from analyzer score

## Client

- [ ] Settings analyzers gallery + editor
- [ ] JD skill priority/contribution UI
- [ ] Job analyzer override; workflow interview round picker
- [ ] Profile Analysis tab + interview status badges
- Paths: `client/src/domains/analyzers/`, extend `jobs`, `meetings`, `candidates`

## Server

- [ ] Analyzer schema + default seed
- [ ] Assignment service; skill matrix on job publish
- [ ] Analysis worker + report persistence
- [ ] Meetings webhook → enqueue analysis
- Paths: `server/src/domains/analyzers/`

## Contract / API

- Analyzer publish JSON; report DTO with evidence timestamps
- `GET/PATCH` job skill matrix; `POST` assign analyzer; `GET` reports by candidate

## Acceptance criteria

- [ ] Manager updates analyzer; new runs use new version
- [ ] Report weights match job contribution; P0 gaps surfaced
- [ ] Recording completes → report within SLA (async)

## Promotion to sprint

After meetings integration (step 9) or parallel server worker with mocks; align step 12 focus.
