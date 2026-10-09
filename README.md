# WebApp Studio

Agentic workflow for building web applications, organized into **three
groups** across **two runtimes**.

| Group | Runtime | Configuration | Models |
|---|---|---|---|
| Definition | Claude Code in VS Code | `CLAUDE.md`, `.claude/` | Claude |
| Specialist | You, or `domain-specialist` | — | Grok 4.6 if it's an agent |
| Execution | OpenCode | `AGENTS.md`, `opencode.jsonc`, `.opencode/` | OpenCode Go |

The binding rules live in **`docs/shared-rules.md`**, imported by both rule
files. Edit them there, never duplicated.

---

## Setup

### 1. Mandatory manual step — model identifiers

Connect OpenCode Go (`/connect` in the TUI) and run `/models`. Compare the
real ids against the ones in `.opencode/agent/*.md` (`opencode-go/...`) and
fix them if they don't match. This is the only manual step in the repo.

### 2. Confirm the contract blocks execution

```bash
./scripts/verify-spec-hash.sh
```

It must **fail** in a freshly cloned repo. That's correct: it means the
execution group can't start without approved and sealed specs.

### 3. If the specialist is an agent

Fill in the `[DOMAIN PROFILE]` block in
`.opencode/agent/domain-specialist.md` with the real profile
(metallurgist, project manager, thermodynamics expert…).

---

## Workflow

```
[Claude Code]  /start            sprint 0: domain, specialist, stack, checklist
                  ↓
[Claude Code]  /define           product-spec + tech-spec + checklist
                  ↓
[Claude Code]  /specialist-gate  BLOCKING · approves domain and seals hashes
                  ↓
               ── switch to the OpenCode terminal ──
                  ↓
[OpenCode]     /execute          verifies hash, plans, dispatches ≤3 agents
[OpenCode]     /verify-loop      BLOCKING · automated checklist
[OpenCode]     /preview          BLOCKING · browsable app + live feedback
                  ↓
               ── switch back to Claude Code ──
                  ↓
[Claude Code]  /closure-audit    BLOCKING · closure vs. accumulated ledger
```

There's no automatic bridge between the two runtimes: the handoff is you
switching terminals, and the coupling is the repo's files plus the hash
sealed in the ledger.

---

## The three verification layers

None replaces the other two:

| Question | Mechanism |
|---|---|
| Is it correct right now? | `/verify-loop` against `specs/verify-checklist.md` |
| Does it make sense to a human? | `/preview` with live semantic feedback |
| Is it still consistent with what's decided? | `/closure-audit` against the ledger |

---

## Structure

```
CLAUDE.md                      rules for the definition group
AGENTS.md                      rules for the execution group
opencode.jsonc                 global OpenCode config
docs/shared-rules.md           R1–R9, single source of truth
docs/decisions-ledger.yaml     binding decisions + sealed hashes
docs/sprint-current.md         plan, status, feedback, escalations
docs/agent-roster.md           roster and quota allocation
specs/product-spec.md          what's being built
specs/tech-spec.md             how, with what contracts
specs/verify-checklist.md      what work is verified against
scripts/verify-spec-hash.sh    the check that blocks /execute
.claude/agents/                3 definition subagents
.claude/commands/               start, define, specialist-gate, closure-audit
.opencode/agent/               14 execution agents
.opencode/command/             execute, verify-loop, preview
.opencode/plugins/             automatic session memory
```

---

## The 11 practices and where they live

| # | Practice | Where |
|---|---|---|
| 1 | Domain-shaped architecture, not copied | Three groups instead of the generic hierarchy |
| 2 | Verify-loop as a loop | `/verify-loop` + `specs/verify-checklist.md` |
| 3 | Anti-over-engineering | R1, enforced by `code-reviewer` and `refactoring-specialist` |
| 4 | Cross-cutting layer | 5 agents serving all three groups |
| 5 | Cap of 3 concurrent agents | R2, enforced by `execution-lead` |
| 6 | Visual/interactive environment | The web app itself is the environment; `/preview` spins it up |
| 7 | Interactive semantic feedback | `/preview`, blocking gate |
| 8 | Research with interim report | `docs/research/` before committing to spec |
| 9 | Decisions → automated check | `verify-spec-hash.sh` + maturation rule |
| 10 | Machine-readable ledger | `docs/decisions-ledger.yaml`, single writer |
| 11 | Blocking closure audit | `/closure-audit` |
