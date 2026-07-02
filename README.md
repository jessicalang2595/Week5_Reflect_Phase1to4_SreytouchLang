# Week 5 Reflection: Phases I–IV — `Add message queue support for V1 conversations during WebSocket connection`

**Student:** Sreytouch Lang (Jessica)
**Issue:** [OpenHands/OpenHands#12279](https://github.com/OpenHands/OpenHands/issues/12279)
**Pull Request:** [OpenHands/OpenHands#14860](https://github.com/OpenHands/OpenHands/pull/14860)
**Status:** Phase IV Complete — PR open against upstream `main` and awaiting maintainer review

This README is my Week 5 progress journal. Rather than starting a new issue, I carried a single OpenHands issue (`#12279`) through all four phases. This document reflects on that full Phase I → IV journey: what I did each phase, where the contribution stands now, and what I learned.

---

## Contribution at a Glance

| Item | Detail |
| --- | --- |
| **Issue** | [OpenHands/OpenHands#12279](https://github.com/OpenHands/OpenHands/issues/12279) — queue V1 messages sent before the WebSocket is connected |
| **Fork** | [sreytouch/OpenHands](https://github.com/sreytouch/OpenHands) |
| **Branch** | `test/v1-pending-message-queueing` |
| **Commit** | `789fba303da66ea87f0439211ed108fd2c499414` — `test(frontend): add V1 pending message queue coverage` |
| **PR** | [OpenHands/OpenHands#14860](https://github.com/OpenHands/OpenHands/pull/14860) → base `OpenHands/OpenHands:main` |
| **PR state** | Open, ready for review, no human maintainer feedback yet |

---

## Phase-by-Phase Reflection

### Phase I — Issue Selection & Understanding (Week 1)

I selected issue [#12279](https://github.com/OpenHands/OpenHands/issues/12279): V1 conversations in OpenHands did not reliably queue and deliver messages a user sends before the native WebSocket connection is fully established. I chose it because it sits at the intersection of my frontend and full-stack interests — React, TypeScript, WebSocket lifecycle, async state, and a concrete user-facing reliability problem.

I completed the Phase I contributor tasks: commented on the issue from my GitHub account, updated the course issue sheet, forked the repo, and wrote up the issue analysis and a UMPIRE-based contribution plan.

**Honest risk I documented up front:** the issue already had an active related PR ([#14692](https://github.com/OpenHands/OpenHands/pull/14692)), which made it a riskier selection than a fully uncontested issue. I recorded that tradeoff rather than hiding it.

- 📄 [Week 1 README (Phase I)](https://github.com/jessicalang2595/Week1_PhaseI_Issue_Selection_SreytouchLang)

### Phase II — Reproduction & Scope Investigation (Week 2)

I cloned my fork and traced the V1 WebSocket message path on current `main` (HEAD `2a3f06a`). The key finding: the codebase had already gained **server-side pending-message queueing** in commit `238cab4` (March 16, 2026), *after* the January 6, 2026 issue was filed.

That changed the nature of the work. I could no longer honestly claim the original bug reproduced unchanged. Phase II became a **scope investigation**: is `#12279` still a valid target, and if so, what narrower gap remains (reconnect timing, delivery confirmation, or missing test coverage)? My conclusion: the original "queue from scratch" feature largely existed; the real opportunity was validating and covering the current behavior.

- 📄 [Week 2 README (Phase II)](https://github.com/jessicalang2595/Week2_PhaseII_Issue_Selection_SreytouchLang)

### Phase III — Implementation (Week 3)

Given the scope drift, I pivoted from "reimplement the feature" to **strengthening the test coverage** around the existing V1 queue-fallback path. On branch `test/v1-pending-message-queueing`, I added two frontend tests to `frontend/__tests__/conversation-websocket-handler.test.tsx`:

1. When the V1 WebSocket is not connected, `sendMessage` falls back to `PendingMessageService.queueMessage(...)` and returns `{ queued: true }`.
2. When `PendingMessageService.queueMessage(...)` fails, the error is surfaced to the caller **and** written to the frontend error message store.

I reused the suite's existing `renderWithWebSocketContext(...)` helper and its established mocking patterns so the tests read like the rest of the file. I worked through real blockers along the way: a Node version mismatch (repo needs `>=22.12.0`; default was `22.9.0`, resolved with the bundled `24.14.0` runtime), a `poetry`-dependent pre-commit hook, and a non-writable original fork.

- 📄 [Week 3 README (Phase III)](https://github.com/jessicalang2595/Week3_PhaseIII_Issue_Selection_SreytouchLang)

### Phase IV — Pull Request & Submission (Week 4)

I resolved the fork-write blocker by creating a writable fork at [sreytouch/OpenHands](https://github.com/sreytouch/OpenHands), pushing the branch, and opening upstream PR [#14860](https://github.com/OpenHands/OpenHands/pull/14860) against `OpenHands/OpenHands:main`, marked ready for review. I matched the repo's PR template, addressed the automated readiness-bot feedback, used `Closes #12279` with a transparent scope note, and posted a dated review-request comment to surface the PR to maintainers.

Verified test result under the bundled Node `24.14.0` runtime: **`2 passed, 40 skipped`** for the targeted run. I explicitly did *not* claim the full pre-existing suite is green — it has unrelated failures in this environment that predate my change, and I documented that honestly.

- 📄 [Week 4 README (Phase IV)](https://github.com/jessicalang2595/Week4_PhaseIV_Issue_Selection_SreytouchLang)

---

## Current Status

- ✅ Remote branch pushed to a writable fork
- ✅ Upstream PR open against `OpenHands/OpenHands:main`, ready for review (not a draft)
- ✅ Automated readiness-bot feedback addressed
- ✅ PR surfaced to maintainers via a review-request comment (June 24, 2026)
- ⬜ No human maintainer feedback yet — watching for review

**Status vocabulary:** Awaiting review / Iterating / Approved / Merged → currently **Awaiting review**.

---

## Key Learnings Across Phases I–IV

- **An issue's description is a snapshot, not a contract.** Open-source `main` moves fast. By the time I reached implementation, the feature was already largely shipped by someone else's merged commit. Now the first thing I do is diff the issue against current `main` — read the relevant files and `git log` before writing any code.
- **Scoping a contribution to tests is legitimate and mergeable.** When the feature already exists, strengthening test coverage (and saying so honestly in the PR) earns more maintainer trust than overstating a fix.
- **Honest scoping is a feature, not a weakness.** I kept the selection risk, the scope drift, and the "full suite not green" caveat visible in every README rather than hiding them.
- **Real-world contribution mechanics matter.** I learned the fork → branch → upstream-PR flow, the head/base relationship, draft vs. ready-for-review, targeted Vitest runs with `-t`, and how to work around environment blockers (Node version, pre-commit hooks, fork write access).

**What I'd do differently:** validate that an issue is both *unresolved* and *uncontested* before committing four phases to it, and sort out fork write-access on day one instead of at submission time.

---

## Next Steps

This contribution is complete through Phase IV; the remaining work is maintainer-driven:

1. Watch PR [#14860](https://github.com/OpenHands/OpenHands/pull/14860) for maintainer feedback.
2. Respond with follow-up changes if requested.
3. Update the **Status** line (Iterating / Approved / Merged) as the PR progresses.
