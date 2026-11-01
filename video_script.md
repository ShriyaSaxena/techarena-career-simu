# presentation.mp4 script (team KSL)

The video must be English, at most 10 minutes, at normal speed, and answer the five questions **in this order**. One person on camera is enough. Aim for 5 to 7 minutes. Save the recording as `solution/presentation.mp4`.

---

**Intro (15 s)**
"Hi, we're team KSL. This is our Career Survival agent: one JiuwenSwarm agent in single-agent mode, with three Skills."

## Q1. How did you think while designing the Skills? (about 1.5 min)

- We started from the rules: formulas, thresholds and hidden risk are not published, and consequences arrive months later. So we couldn't build an agent that calculates the best move. It has to judge.
- The two goals conflict: survive 48 months, but also get promoted. We asked what a sensible employee would do: first decide what matters most right now, then choose.
- That became our split: `career-state-assessment` decides one priority (recover, protect, reduce-risk, prepare-review, grow-balanced), and `event-decision` and `quarterly-planning` act on it.
- The priorities are in a fixed order, survival before growth, because failing early costs every remaining month.
- We kept each Skill short. The agent makes about 200 decisions, so every extra line is paid for 200 times in tokens.

## Q2. What did you find in the logs, and what did you change? (about 2 min)

- **v1** finished all 48 months with a quantitative score of 57.38 and reached L4. Its replay showed over-caution: Health and Dignity were at 9 or 10 almost all game, and reviews were C four times before turning to B. The agent had also loaded an empty template Skill we'd forgotten to delete.
- So in **v2** we made it bolder: spend the Health buffer, push Output, and switch to review-preparation after any middling review.
- That backfired. **v2 was dismissed at month 24** with two D grades and a score of 22.4. The log showed why: almost every review is middling, so the agent was locked in review-prep mode from month 6. It never ran balanced growth, and Network fell to 0. The manager's feedback then mentioned "friction" and attitude.
- Looking across both logs, we found a shared weakness: in story events the agent kept losing Network by refusing, escalating or "protecting Output". That happened in v1 too, just less severely.
- **v3**, our final version, goes back to v1's priority order, which is what kept it alive for 48 months, and adds two narrow rules: treat a Network loss as a real cost in events, and never spend recovery Energy on a stat that's already at its maximum.
- Our main lesson: change one thing at a time. A single rule that shifts the priority order changes the whole game.

## Q3. Why no scripts or reference files? (about 45 s)

- Every decision is a judgement about free-text options, and there's no calculation a script could do better.
- The only facts the agent needs repeatedly, the effect directions of the quarterly actions, fit in a few lines inside `quarterly-planning`.
- A reference file would cost an extra tool call and more tokens each quarter, with no new information. We decided simplicity was worth more.

## Q4. How general and robust are the Skills? (about 1 min)

- No hard-coded thresholds, event scripts or engine internals, which the rules forbid and which wouldn't survive a hidden dataset anyway.
- Every rule is relative: "near the top of its range", "lagging behind the other two", "a repeated warning". If ranges, events or effect sizes change, the rules still apply.
- The agent reads the handbook at the start of every run, so if published actions change, it sees the new ones.
- The weak point is calibration: we can't see thresholds, so we chose caution. Our v2 experiment showed that a bolder setting can fail badly. The assessment re-checks every turn, so a falling statistic switches the priority straight back.

## Q5. Agents and Skills: what do Skills add, and where do they fall short? (about 1 min)

- Skills give the agent a consistent method. Without them the model reasons from scratch on each event and drifts; with them it applies the same priorities every time.
- They also make behaviour inspectable. We could read the log, see which rule produced a choice, and fix that rule.
- But Skills are only as good as the model's reading of them. Small wording changes have big effects. One extra condition in a priority rule took us from surviving 48 months to dismissal at month 24.
- Skills can't give the agent information it doesn't have. With hidden thresholds, a Skill can only encode a reasonable attitude to risk, not the answer.

**Close (10 s)**
"Thanks for watching. Our full reasoning is in skills_design.md and decision_report.md."
