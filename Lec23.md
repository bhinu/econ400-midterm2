# Lecture 23: Regression Discontinuity (RD)

**Sample midterm hits:** MC Q10 (the key RD assumption — this is a near-direct copy from lecture)

---

## What a discontinuity is

A discontinuity is an **arbitrary jump** in treatment status caused by a quirk of law or nature, where similar individuals end up treated differently because they fall on opposite sides of some threshold.

Examples:
- Income just below poverty line → eligible for food stamps. Just above → not eligible.
- Class enrollment of 41 → split into two classes (small). Enrollment of 40 → one class (big).
- Test score above cutoff → admitted to selective school. Below → not.
- Vote share > 50% → win election. < 50% → lose.

The variable that determines treatment by crossing the threshold is the **running variable** (aka forcing variable). Income, enrollment, test score, vote share.

---

## The big idea

Just below the threshold and just above the threshold, units are essentially identical EXCEPT for treatment. So compare them.

But just comparing means above vs below can be misleading because the running variable itself usually correlates with the outcome (richer people spend more on food regardless of food stamps). So:

1. Estimate a regression line on each side of the threshold.
2. Use the **gap (jump) between the two lines AT the threshold** as the estimate of the average treatment effect.

That's RD.

---

## The food stamp example (intuition pump)

Outcome: household food expenditures. Running variable: household income. Threshold: poverty line.

Naive ideas that fail:
- **Idea 1: Compare avg food expenditures of eligible vs ineligible HH.** Bad: eligible HH have lower income, which lowers food spending regardless of food stamps. Confounded.
- **Idea 2: DnD with before/after food stamps.** Bad: food stamps have existed for decades; no "before" data.
- **Idea 3: Compare narrow bins on each side of the poverty line.** Better but throws out most data → noisy estimates. Wide bins → comparing dissimilar HH again.
- **Idea 4 (RD, the winner):** Fit a regression on each side, USE ALL THE DATA, but only the JUMP at the threshold counts as the treatment effect. The slopes capture how income relates to spending; the jump captures food stamps.

---

## The key RD assumption

> **MC Q10: In a regression discontinuity design, the key assumption is:**
> **B) the only thing changing suddenly as we move from just below to just above a threshold is one's probability of being treated** ✓

Word-for-word from the lecture: "we're assuming that the only thing that changes suddenly as we move from just below to just above the cutoff is one's treatment status."

Not A (parallel trends is for DnD).
Not C (that's just a definitional sentence, not the key assumption).
Not D (only B is right).

This assumption means: people on either side of the threshold are similar in everything else (preferences, demographics, unobservables) — the only thing that changes discretely is treatment. Continuity of all confounders across the threshold.

---

## When the RD assumption can FAIL

The assumption fails if people can **manipulate** their position relative to the threshold. Examples:
- Students retake the SAT to clear an admissions cutoff → people just above are systematically different from people just below.
- Households underreport income to qualify for food stamps → income just below the line is selected.
- Politicians stuff ballots in close elections → vote share near 50% is manipulated.

When manipulation is possible, units just above and just below stop being comparable. The RD assumption is violated.

How to check (informally): plot the **density of the running variable**. If there's a spike just on one side of the threshold, that's evidence of manipulation (McCrary density test).

---

## Class size example — Angrist & Lavy 1999

### Question
Does smaller class size improve student achievement?
```
math score = β0 + β1*classize + u
```
Naive OLS gives β1 = +0.32 (POSITIVE — bigger classes have higher scores). Why? OVB. Wealthier districts have bigger classes AND better outcomes; small classes might be remedial, etc.

### The discontinuity (Maimonides Rule, Israel)
Israeli law: when enrollment exceeds 40, the grade is split into two classes. So:
- Enrollment = 40 → one class of 40
- Enrollment = 41 → two classes (~20 and ~21)

A jump from 40 to 41 in enrollment causes a JUMP DOWN in average class size — a discontinuity.

### Running variable: enrollment. Treatment: smaller class.

### Mean comparisons at threshold
- Enrollment = 40: avg class size = 29.9, avg math = 60.5
- Enrollment = 41: avg class size = 22.7, avg math = 64.0

Class size drops by ~7 students. Math score rises by ~3.5 points. Direction is as expected (smaller classes → higher scores).

### `rdrobust` estimate
RD treatment effect on avg math = +5.04 (SE 2.25, p = 0.025). Statistically significant.

This is causal under the RD assumption: the jump at enrollment=41 is due to the rule, not to anything else changing discretely at that exact enrollment level.

---

## Mechanics of RD in a regression

The basic RD regression:
```
y = β0 + β1*(running var - cutoff) + β2*Treated + β3*(running var - cutoff)*Treated + u
```
- β2 is the **jump at the threshold** = treatment effect.
- β1 captures the slope on the left.
- β1 + β3 captures the slope on the right (allows different slopes).
- Centering at the cutoff makes β2 directly interpretable as the gap at the threshold.

Stata: `rdrobust y running_var, c(cutoff) p(1)` — `p(1)` means linear local polynomial.

---

## Sharp vs. fuzzy RD (terminology check)

- **Sharp RD:** crossing the threshold causes treatment with probability 1 (or 0 → 1). E.g., legal eligibility based on age. Treatment = function of running variable, deterministic.
- **Fuzzy RD:** crossing the threshold changes the PROBABILITY of treatment but not from 0 to 1 (some on the eligible side don't take treatment, some on the ineligible side find a way). Estimate this with IV: use threshold-crossing as an instrument for treatment.

The class size example is technically fuzzy because the rule isn't perfectly applied (see lecture: at enrollment=40, average class size was 30, not 40 — the rule has slippage).

---

## Trade-offs and what can go wrong

### Bandwidth choice
- **Wide bandwidth** → more data, smaller SEs, but units far from the threshold may not be comparable. Bias up, variance down.
- **Narrow bandwidth** → cleaner comparisons, but few observations, huge SEs. Bias down, variance up.

`rdrobust` picks an optimal bandwidth automatically (CCT method).

### Manipulation of the running variable (already covered above)
Plot the density. Look for bunching just on one side.

### Other discontinuities at the same threshold
What if multiple programs use the same poverty line? Then the "jump" combines all of them. RD identifies the JOINT effect, not just food stamps.

### Functional form
You assume the relationship between running variable and outcome is smooth except at the threshold. If the true relationship has weird curvature, you can mistake curvature for a jump. Try different polynomial orders, different bandwidths, and see if results are stable.

### Local effect only
RD identifies the treatment effect AT the threshold. People far from the threshold (very poor, very rich) might respond differently. RD gives a **local** average treatment effect (LATE), not a global one.

---

## Why RD is so popular

From the lecture chart: RD usage has exploded since ~2000.

- Cleaner identification than IV (no judgment call about validity, you can SEE the discontinuity in graphs)
- Works on cross-sectional data (no panel or before/after needed)
- Visual evidence is compelling — readers can literally see the jump
- Many policies have administrative thresholds → discontinuities everywhere

---

## RD vs. other causal strategies

| Method | Key requirement | Key assumption |
|--------|-----------------|----------------|
| RCT | Random assignment | Compliance, no attrition |
| DnD | Before/after data, comparison group | Parallel trends |
| Fixed effects | Panel data | Time-invariant OVB |
| IV | Valid + relevant instrument | Exclusion restriction (untestable) |
| **RD** | Sharp threshold in treatment | Continuity at threshold (no manipulation, no other jumps) |

---

## Quick exam-prep checklist for Lec 23

- [ ] Can I state the key RD assumption in plain English? (MC Q10)
- [ ] Can I identify a discontinuity from a written description (running variable, threshold, treatment)?
- [ ] Can I explain why naive comparisons (means above vs below) fail?
- [ ] Can I explain why narrow vs wide bins each have drawbacks, and how RD solves both?
- [ ] Can I name 2 things that can go wrong with RD (manipulation, other coincident discontinuities, functional form, local-effect limitation)?
- [ ] Can I read an `rdrobust` output and identify the estimated treatment effect and its significance?
- [ ] Can I distinguish sharp RD from fuzzy RD?

---

# Practice Questions — Lecture 23

## Multiple Choice

**1.** In a regression discontinuity design, the key assumption is that:
A) In the absence of treatment, treatment and control groups would have parallel time trends
B) The only thing changing suddenly as we move from just below to just above a threshold is one's probability of being treated
C) The discontinuity is uncorrelated with the regressor
D) The running variable is randomized

<details><summary>Answer</summary>

**B.** Sample midterm Q10. Word-for-word from lecture. A is parallel trends (DnD). C is meaningless. D is wrong — the running variable is observed and not random; it's the JUMP at the threshold that's quasi-random.
</details>

**2.** In a sharp RD design:
A) Treatment probability jumps from one positive value to a higher value at the threshold
B) Treatment is deterministic — probability jumps from 0 to 1 (or 1 to 0) at the threshold
C) Treatment is randomly assigned within a small bandwidth
D) The running variable is randomly assigned

<details><summary>Answer</summary>

**B.** Sharp = deterministic crossing → instant assignment. If the rule is imperfectly applied (some "untreated" cross over, etc.), it's fuzzy.
</details>

**3.** Manipulation of the running variable around the threshold:
A) Strengthens the RD design
B) Has no effect on validity
C) Threatens the assumption that units near the threshold are comparable
D) Is required for identification

<details><summary>Answer</summary>

**C.** Manipulation means people just above and just below stop being similar. The whole logic of RD (similar units, different treatment) breaks. Check with a McCrary density test (look for bunching).
</details>

**4.** Wider bandwidth in RD:
A) Reduces both bias and variance
B) Reduces bias but increases variance
C) Reduces variance but increases bias
D) Has no effect

<details><summary>Answer</summary>

**C.** More observations far from the threshold → smaller SEs (lower variance), but those distant observations may not be comparable across the threshold (more bias). Classic bias-variance tradeoff.
</details>

**5.** Fuzzy RD is typically estimated using:
A) OLS only
B) Fixed effects
C) IV, where threshold-crossing instruments for treatment
D) Difference-in-differences

<details><summary>Answer</summary>

**C.** When crossing the threshold only changes treatment probability (not 0→1), use threshold-crossing dummy as an instrument for actual treatment. Combines RD logic with IV machinery.
</details>

**6.** RD identifies:
A) The average treatment effect for the entire population (ATE)
B) The local average treatment effect at the threshold (LATE)
C) The treatment effect on the treated (ATT)
D) The intent-to-treat effect (ITT)

<details><summary>Answer</summary>

**B.** RD only uses variation right at the threshold. The estimate applies to people NEAR the cutoff. Far-from-threshold treatment effects could be totally different. Important caveat for external validity.
</details>

**7.** The McCrary density test checks for:
A) Whether the running variable is normally distributed
B) Whether there is bunching/manipulation around the threshold
C) Whether the outcome jumps at the threshold
D) Whether bandwidth was chosen correctly

<details><summary>Answer</summary>

**B.** Plot the density (histogram) of the running variable. A spike on one side of the threshold suggests people are gaming the system to be on a particular side. That violates the RD assumption.
</details>

**8.** In the Angrist & Lavy class size study, the running variable and discontinuity are:
A) Class size; the threshold is at 40 students per class
B) Enrollment; the threshold is at enrollment = 40 (Maimonides Rule)
C) Test score; the threshold is the average
D) Year; the threshold is 1999

<details><summary>Answer</summary>

**B.** Enrollment is the running variable. Once enrollment exceeds 40, the rule kicks in and the grade gets split. Class size is the TREATMENT (what jumps at the threshold), not the running variable. Don't confuse them.
</details>

## Short Answer

**FRQ 1.** A school district admits students to a magnet school based on test score ≥ 75. Researchers want to estimate the effect of attending on college enrollment.

a) Identify the running variable, threshold, and treatment.
b) State the key RD assumption in this context.
c) Sketch (in words) the graphs you'd want to see.
d) Identify ONE specific threat to the design and how to check for it.

<details><summary>Model answer</summary>

a) Running variable: test score. Threshold: 75. Treatment: attending the magnet school.

b) Students just below 75 (say 74) and just above 75 (say 76) are essentially identical except that those above are admitted. Their potential outcomes are continuous through the threshold. The only thing changing discretely at score=75 is treatment status.

c) Two graphs:
- College enrollment vs. test score, with a fitted line on each side. We want to see a clear vertical jump at score = 75.
- Density of test scores. We want a smooth distribution through 75 — no spike just above (which would suggest students retook the test or scores were inflated to push them over).

d) **Threat: score manipulation.** Teachers might bump scores from 74 to 75 for borderline students. Or students might retake the test specifically to clear 75. Check: McCrary density test — plot the histogram of scores and look for bunching at or just above 75. Also, sharp RD is fuzzy in practice if some kids with score < 75 still get in via appeal — switch to fuzzy RD with score ≥ 75 as instrument for actual attendance.
</details>

**FRQ 2.** You replicate Angrist & Lavy with US data. Around the enrollment threshold of 80 (where classes split):
- Enrollment = 80: avg class size = 28, avg math = 72
- Enrollment = 81: avg class size = 22, avg math = 76

a) Compute a back-of-envelope IV-style estimate of the effect of class size on math.
b) Why is this called fuzzy RD rather than sharp RD?
c) What single graph would most strengthen your argument that this is causal?

<details><summary>Model answer</summary>

a) Class size dropped by 28 − 22 = 6 students. Math rose by 76 − 72 = 4 points.
Effect ≈ ΔMath / ΔClassSize = 4 / (−6) ≈ −0.67 points per student. Each additional student lowers math by ~0.67 points (or each one fewer student raises math by ~0.67 points).

b) Sharp RD would mean class size jumps from exactly some value to exactly half at enrollment=81. Real world is messier — at enrollment=80, classes don't always have 80 students (some have 18, some have 40, average 28). At 81, they don't all split into perfect halves. The rule changes the PROBABILITY/EXPECTED class size, not deterministic class size. Hence fuzzy.

c) A scatter plot of math scores against enrollment, with regression lines fitted on each side of 81 (or 80). I'd want to see: (i) a smooth relationship across enrollment values away from threshold, (ii) a clear vertical jump at the threshold equal to ~4 points, (iii) no obvious jumps at other enrollment values. This visualizes the discontinuity and rules out general curvature artifacts.
</details>

**FRQ 3.** Compare RD to IV. When would you prefer RD? When would you prefer IV?

<details><summary>Model answer</summary>

**Prefer RD when:**
- A clear administrative threshold determines treatment (eligibility cutoffs, score thresholds, age cutoffs).
- You can plot the discontinuity and visually demonstrate the jump.
- Manipulation is implausible (random or hard-to-game running variable).
- You only care about effects near the threshold.

RD's advantage: the identifying assumption (continuity at the threshold) is more transparent than IV's exclusion restriction. You can show a graph and people can see the jump. McCrary density test gives indirect evidence of no manipulation.

**Prefer IV when:**
- No relevant threshold exists in your setting.
- You have an instrument that creates plausibly random variation in the endogenous regressor (not necessarily at a sharp cutoff).
- You want effects that generalize beyond a narrow population near a threshold.

IV's advantage: more flexibility in research design. Doesn't require an administrative cutoff. But the exclusion restriction is fundamentally untestable, requires more leaps of faith, and weak instruments amplify bias.

**Note:** they can be combined — fuzzy RD literally uses threshold-crossing as an instrument for treatment, blending both approaches.
</details>

**FRQ 4.** A researcher claims to estimate the effect of receiving food stamps on food expenditures using RD with the federal poverty line as the threshold. State two distinct concerns about this design.

<details><summary>Model answer</summary>

**Concern 1: Other programs change at the same threshold.** Many means-tested programs use the federal poverty line — Medicaid, WIC, housing assistance, EITC schedules, etc. The "jump" at the poverty line therefore captures the COMBINED effect of many programs, not just food stamps. RD identifies a bundled treatment effect, which may not answer the food-stamps-specific question.

**Concern 2: Income manipulation / measurement.** Households near the threshold may strategically report or arrange income to fall just below to qualify (working fewer hours, off-the-books pay). This violates the no-manipulation assumption. Households just below the threshold are systematically different from those just above (they're the ones who optimized to qualify). Check with McCrary density test on income; look for bunching just below the threshold.

**(Other valid concerns to mention):** Measurement of food expenditures includes the value of food stamps directly, which mechanically inflates the outcome for the eligible side. The local effect at the threshold may not generalize to deeply poor households. Functional form assumptions on each side could be wrong, especially if income has a complex relationship with food spending.
</details>
