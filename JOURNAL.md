## Week 7 — Issue selection

**Issue link:** (https://github.com/ascherj/pathreview/issues/66)

**Issue title:** Safety monitoring doesn't emit metrics when the content filter is bypassed by a multi-turn conversation
 

**Tier:** [ ] Tier 1  [ ] Tier 2  [x] Tier 3

**Problem summary:**

Issue number 66 talks about dealing with flaged conversation as a whole, and not one prmopt. It asks us to create SafetyMonitor.get_event_count tool that goes along the conversation and collect the flagged words and see if it should kick the user or the materils that is being requested is safe. 

One way to solve it as I can see is to implement window_hours currently, which means it is getting all the events rather than within the last 1 hour.

The codebase shows a hardcoded review with no AI generation integrated even and the clear solution is adding the safety monitor and providing multi-turn support.

**Branch name:** fix/66-safety-monitor-multi-turn-metrics
https://github.com/aliabbaka/pathreview/blob/fix/66-safety-monitor-multi-turn-metrics/JOURNAL.md
**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [x] Issue added to cohort ledger


## Week 8 — Reproduction & solution planning

**Reproduction commit link:** https://github.com/aliabbaka/pathreview/commit/3e041c75750ed1bcc1236005ed38f7c139b4ad7a

**Reproduction summary:**Added a failing unit test (test_monitoring.py::test_window_hours_is_ignored_reproduces_66) that records 5 lifetime content_filtered events with only 2 inside the last hour, then calls get_event_count("content_filtered", window_hours=1). It returns 5 instead of 2 (assert 5 == 2 fails), confirming window_hours is ignored so events across a multi-turn conversation are never counted within a rolling window.
**PLAN.md link:** https://github.com/aliabbaka/pathreview/blob/fix/66-safety-monitor-multi-turn-metrics/PLAN.md

**Walkthrough video (recommended):** https://drive.google.com/file/d/1S-X-YzFoySNWFty719cXwbFFI_7OrP8A/view?usp=sharing
**Blockers or open questions:**
If there could be a bigger time frame, more than two hours but that will not increase the latency of the answers.


## Week 9 — Implementation & review

**Fix commit link:** https://github.com/aliabbaka/pathreview/commit/02bd06f

**What I built:** Switched `SafetyMonitor` from a cumulative `INCR` counter to a
Redis sorted set of timestamped events. `get_event_count` now enforces `window_hours`
via `zremrangebyscore` + `zcard`, mirroring `RateLimiter`. The reproduction test
(`test_window_hours_is_ignored_reproduces_66`) now passes; added 3 tests (timestamped
write, windowed count, error path) and hardened the unknown-type test. `ruff`/`black`/
`mypy` clean on changed files; 5/5 monitoring tests pass.

**Draft PR:** https://github.com/ascherj/pathreview/pull/1022

**Blockers or open questions:** Whether wiring `get_event_count` into the content-filter
path (PLAN §3.3) should be part of this issue or a separate follow-up. `SafetyMonitor`
currently has no callers.