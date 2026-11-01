# Decision report

## 1. Overall decision approach

We treat the game as sequential decision-making under incomplete information. The agent can't see formulas, failure thresholds or hidden risk, and many consequences arrive months later. Rather than trying to predict exact effects, the agent decides by **priority**. Before every decision it asks what matters most right now, and then picks the option that serves that best, with the least delayed risk.

The priorities, in order of precedence, are:

1. `recover`: Health is low or falling, or there was an overwork warning.
2. `protect`: Dignity is low or falling, or Wealth is turning negative.
3. `reduce-risk`: warnings suggest hidden risk is building.
4. `prepare-review`: survival is safe, and a review is close or the last review asked for clearer results.
5. `grow-balanced`: everything is safe, so build whichever of Skill, Network or Output is lagging.

Survival outranks growth because failing early loses all the remaining months of salary, promotion and growth. Within that ordering we try not to over-protect. Health and Dignity at their maximum are treated as buffer that can be spent on growth, and recovered toward the end of the run, when the final state is scored.

## 2. Public information and in-run feedback relied on

**Public information** (handbook and rules):

- the ten-level structure, the six-month review and promotion cycle, and the salary schedule;
- the meaning of review grades;
- the published effect direction of every Energy action and main action (for example, High-intensity work: Output +, Health -, Dignity -);
- the 0 to 10 range of Health and Dignity;
- the fact that risks can accumulate and settle later.

**In-run feedback:**

- the visible statistics and their trend over recent months;
- the narrative `events` text each month, in particular the HR partner's remark about risk, the manager's half-year feedback, and promotion notices;
- review grades and bonus lines;
- `check_latest_logs`, used occasionally to see the exact effects of recent choices.

The agent doesn't use event-specific answers, unpublished thresholds or engine internals.

## 3. Principal risks

- **Unknown thresholds.** Without visible failure points the agent can be too cautious (slow promotion) or too aggressive (early failure). Our first version erred on the cautious side (see section 5). The current version spends spare buffer, but it could now overshoot in a harsher scenario.
- **Delayed hidden risk.** Choices that look safe can create risk that settles much later. We rely on narrative warnings and on Defuse legacy risk to manage it, but the signal is indirect.
- **Model variance.** Each version is scored over three runs with an LLM that reads free text. The same state can produce different choices, so we keep the rules simple and ordered to reduce variance.
- **Token cost.** About 200 decisions in one long context mean input tokens dominate cost. We keep the Skills short, load each Skill once, and ask for brief reasoning and notes.
- **Untested final changes.** The changes in version 2 come from analysing the version 1 log, but we didn't have time for several new local runs before the deadline.

## 4. Generalisation beyond the development dataset

- Every rule is written in terms of published concepts (statistic ranges, effect directions, review timing) and of feedback categories (risk warnings, review feedback), not specific events or numbers. A new event set with different wording should still map onto the same priorities.
- Judgements are relative ("near the top of its range", "lagging behind the other two", "repeated warning"). If statistic ranges or event effects change, the rules still apply.
- The priority ordering doesn't depend on the dataset. It follows from what the score rewards and from the fact that failure ends the run.
- If action effects or prices changed, only the short list of published options in `quarterly-planning` would need updating, and the agent also reads the handbook at the start of every run.

## 5. Development log

| Version | Change | Local result (dev dataset) | What we learned |
|---|---|---|---|
| Preliminary | Three-Skill design: assessment sets one priority, two choosing Skills act on it. | No run (before the API key). | n/a |
| v1 (27 Oct) | First full Skills, plus a leftover template Skill `skill-name` in `skills/`. | Completed all 48 months. `quantitative_score` 57.38, `competition_partial_score` 40.16. Final level L4, Wealth 28, Health and Dignity 20/20, Skill 75, Network 36, risk control 2. Review grades C, C, C, C, B, B, B, B, with promotions at months 12, 24 and 36. About 62.9M total tokens. | Survival was never in danger: Health and Dignity were at 9 to 10 almost all game. But the agent advanced only three levels. The manager repeatedly asked for clearer results, and Output (26) lagged far behind Skill (73). The HR "risks have piled up" warning appeared 13 times, but Defuse legacy risk was used only 3 times. Health recovery was sometimes bought while Health was already high. The agent also loaded the empty template Skill. |
| v2 (final) | Removed the template Skill. Assessment now reads narrative feedback (HR and manager remarks), treats repeated risk warnings as `reduce-risk`, uses `prepare-review` after middling feedback, and treats maximum Health and Dignity as spendable buffer. Event decisions accept small survival costs when the buffer is full. Quarterly planning stops recovering capped statistics, defuses risk after warnings, and shifts Energy toward Output when it lags. | Not re-run locally before the deadline. | Expected effects: higher review grades and level, better risk control, and a similar end-of-run Health and Dignity. |
