# WebApp Studio — Execution group

The team of OpenCode agents that implements web applications from an already
approved contract. The binding rules live in `docs/shared-rules.md`: **read it
before acting**. They are not duplicated here so they can't diverge.

---

## Startup rule

No agent in this group starts working until `scripts/verify-spec-hash.sh` has
returned 0 in this session. If it fails, the contract isn't sealed or the
specs changed after being approved: stop and tell the user to go back to
`/define` in Claude Code.

---

## Organization

- **`execution-lead`** (primary) — the sole orchestrator. Breaks the sprint
  into tasks, assigns specialist and task, enforces the cap of 3 concurrent
  agents (R2), receives the verify-loop verdict, and requeues what's rejected.
  Nothing gets dispatched without going through it.
- **7 specialists** — each with its own file scope. They don't write outside
  their scope: if a task requires it, they return control to the lead.
- **5 cross-cutting agents** — serving all groups: `code-reviewer`,
  `verification-agent`, `documentation-writer`, `refactoring-specialist`,
  `closure-auditor`.
- **`domain-specialist`** — only if the user chose an agent specialist in
  `/start`.

There is no lead-per-area layer. With a cap of 3 concurrent agents, an
intermediate layer would burn context and quota without executing anything.

---

## Commands

- `/execute` — plans the sprint and dispatches work
- `/verify-loop` — plan → execute → verify → reject → requeue loop
- `/preview` — spins up the browsable app for the user's semantic feedback

`/start`, `/define`, `/specialist-gate`, and `/closure-audit` live in Claude
Code, not here. If the user invokes them in OpenCode, tell them to switch
terminals.

---

## Code-writing rules

1. **R1 (simplicity) applies to every file you write.** Justify in the commit
   message why the new code was necessary.
2. **Follow the `specs/tech-spec.md` API contract literally.** If you think the
   contract is wrong, don't change it: return control to the lead with the
   objection. Changing the contract is definition-group work.
3. **Never invent requirements.** If the spec doesn't say what happens in an
   edge case, that's a gap in the spec, not an invitation to improvise. Report
   it.
4. **No secrets in the repo.** Not in code, not in tests, not in fixtures.
5. **Atomic commits** referencing the sprint task.

---

## Reporting

Every agent, when finished, reports back: what it did, which files it
touched, which decisions it made that should go to the ledger, and what was
left out. Without that report the lead can't close the task.
