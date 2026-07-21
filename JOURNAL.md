## Week 7 — Issue selection

**Issue link:** (https://github.com/ascherj/pathreview/issues/66)

**Issue title:** Safety monitoring doesn't emit metrics when the content filter is bypassed by a multi-turn conversation
 

**Tier:** [ ] Tier 1  [ ] Tier 2  [x] Tier 3

**Problem summary:**

Issue number 66 talks about dealing with flaged conversation as a whole, and not one prmopt. It asks us to create SafetyMonitor.get_event_count tool that goes along the conversation and collect the flagged words and see if it should kick the user or the materils that is being requested is safe. 

One way to solve it as I can see is to implement window_hours currently, which means it is getting all the events rather than within the last 1 hour.

The codebase shows a hardcoded review with no AI generation integrated even and the clear solution is adding the safety monitor and providing multi-turn support.

**Branch name:** fix/66-safety-monitor-multi-turn-metrics

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [x] Issue added to cohort ledger