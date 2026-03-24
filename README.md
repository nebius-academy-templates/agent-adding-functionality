# Practice: Claude Subagent + Playwright

In this practice you will extend a Claude Code agent with a **custom subagent** that generates Playwright tests, and a **memory file** that prevents duplicate test cases across sessions.

---

## Repository layout

```
agent-subagent-playwright-practice/
├── .claude/
│   └── agents/
│       └── e2e_test_generator.md.template   ← copy → e2e_test_generator.md and fill in
├── app/
│   └── index.html                           ← Observability Dashboard (app under test)
├── data/
│   └── tickets/
│       └── 3.json                           ← golden ticket
├── memory/
│   └── test_cases.md.template               ← copy → test_cases.md and fill in
├── skills/
│   ├── get_ticket/get_ticket.py
│   └── post_comment/post_comment.py
├── tests/
│   └── e2e/                                 ← generated spec goes here
├── CLAUDE.md                                ← orchestrator agent (already complete)
├── playwright.config.ts
└── package.json
```

---

## Step 0 — Setup

```bash
npm install
npx playwright install chromium
```

Verify the skills work:

```bash
python3 skills/get_ticket/get_ticket.py "#3"
python3 skills/post_comment/post_comment.py "#3" "hello"
```

---

## Step 1 — Create the subagent

Copy the template and fill in every `[TODO]`:

```bash
cp .claude/agents/e2e_test_generator.md.template \
   .claude/agents/e2e_test_generator.md
```

Fill in:
| Section | What to write |
|---|---|
| **Your role** | One-sentence identity |
| **Purpose** | What file it writes and where |
| **What to generate** | Test count, structure, selectors to use |
| **Output format** | TypeScript example |
| **Guardrails** | No-duplicate rule + file path rule |

---

## Step 2 — Create the memory file

```bash
cp memory/test_cases.md.template memory/test_cases.md
```

Fill in the `[TODO]` rule: the agent must never generate a test whose ID already appears in the table.

---

## Step 3 — Run the agent

Start Claude Code in this directory:

```bash
claude
```

Send the ticket ID to trigger the workflow:

```
#3
```

Confirm the agent:
- fetches ticket #3
- checks memory for existing test IDs
- invokes the `e2e_test_generator` subagent
- writes `tests/e2e/generated.spec.ts` with ≥ 5 tests
- updates `memory/test_cases.md`
- presents the summary and **waits** — does not call `post_comment`

---

## Step 4 — Human review loop + publish gate

1. Request a change, e.g. "Add a test for when the filter input is cleared."
2. Confirm the agent updates the spec without publishing.
3. Say "publish".
4. Confirm it posts a summary comment via `post_comment`.

---

## Step 5 — Run the tests

```bash
npx playwright test
```

All tests must pass (the app is served automatically by `playwright.config.ts`).

---

## Submission checklist

- [ ] `.claude/agents/e2e_test_generator.md` exists with all `[TODO]` sections filled in
- [ ] `tests/e2e/generated.spec.ts` exists and contains at least 5 `test()` cases
- [ ] `memory/test_cases.md` exists and includes a "no duplicate test cases" rule
- [ ] `npx playwright test` exits green
- [ ] Changes committed and pushed to `main`
