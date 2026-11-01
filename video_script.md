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

## Q2. What did you find in the logs, and what did you change? (about 1.5 min)

- Version 1 finished all 48 months with a quantitative score of 57.38, but only reached L4. Reviews were C four times, then B four times.
- In the replay, Health and Dignity were at 9 or 10 almost the whole game. We were over-protecting and leaving buffer unused.
- The manager's half-year feedback kept saying "show clearer results", and Output ended at 26 while Skill was 73. Reviews see Output, and we were neglecting it.
- The HR partner warned "the risks have piled up" 13 times, but the agent used Defuse legacy risk only 3 times. It ignored the narrative signals.
- We also found that the agent loaded an empty template Skill we had forgotten to delete.
- Changes in version 2: read the narrative feedback as evidence; treat full Health and Dignity as spendable buffer; don't recover a stat that's already capped; defuse risk after warnings; push Output when it lags or the manager asks for results; and remove the template Skill.

## Q3. Why no scripts or reference files? (about 45 s)

- Every decision is a judgement about free-text options, and there's no calculation a script could do better.
- The only facts the agent needs repeatedly, the effect directions of the quarterly actions, fit in a few lines inside `quarterly-planning`.
- A reference file would cost an extra tool call and more tokens each quarter, with no new information. We decided simplicity was worth more.

## Q4. How general and robust are the Skills? (about 1 min)

- No hard-coded thresholds, event scripts or engine internals, which the rules forbid and which wouldn't survive a hidden dataset anyway.
- Every rule is relative: "near the top of its range", "lagging behind the other two", "a repeated warning". If ranges, events or effect sizes change, the rules still apply.
- The agent reads the handbook at the start of every run, so if published actions change, it sees the new ones.
- The weak point is calibration: in a much harsher environment, spending buffer could be too aggressive. The assessment re-checks every turn, though, so a falling Health switches the priority back to recover.

## Q5. Agents and Skills: what do Skills add, and where do they fall short? (about 1 min)

- Skills give the agent a consistent method. Without them the model reasons from scratch on each event and drifts; with them it applies the same priorities every time.
- They also make behaviour inspectable. We could read the log, see which rule produced a choice, and fix that rule.
- But Skills are only as good as the model's reading of them. The model sometimes followed the letter (protect Health) and missed the intent (don't waste Energy on full Health).
- Skills can't give the agent information it doesn't have. With hidden thresholds, a Skill can only encode a reasonable attitude to risk, not the answer.

**Close (10 s)**
"Thanks for watching. Our full reasoning is in skills_design.md and decision_report.md."
