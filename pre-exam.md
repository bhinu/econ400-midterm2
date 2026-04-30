# ECON 400 Midterm 2 - Pre-Exam Checklist (1 Hour Before)

Read top to bottom. Don't skip sections. If something doesn't click, mark it and come back.

---

## Part 1: The 5 Causal Inference Tools (the big picture)

| Method | Key Assumption | Fixes | Doesn't Fix |
|--------|---------------|-------|-------------|
| **RCT** | Random assignment, compliance | Everything (gold standard) | Attrition, spillovers if poorly designed |
| **DnD** | **Parallel trends** | Time-invariant differences in levels | Trend differences, anticipation |
| **FE/FD** | Time-invariant OVB only | Time-invariant unobservables | Time-varying OVB |
| **IV/2SLS** | **Exclusion restriction** (untestable) | Endogeneity if instrument is valid | Weak instrument problems |
| **RD** | Only treatment changes discontinuously at cutoff | OVB if no manipulation | External validity (LATE only) |

**If asked "which method should I use?"** → match the data structure and assumption you're willing to defend.

---

## Part 2: Stata Output Interpretation Templates

### Two-sided t-test (default)
- t-stat = coef / SE
- |t| > 1.96 → reject at 5%
- p-value < 0.05 → reject at 5%

### One-sided t-test (e.g. "is effect less than zero?")
- p-value = (two-sided p) / 2 IF the coefficient sign matches H1
- Sample MC: price coef = -0.0004618, two-sided p = 0.004 → one-sided p = **0.002**

### F-test (joint hypothesis)
- Tests multiple restrictions simultaneously
- Reject if F > critical value or p < 0.05

### Reading "[95% Conf. Interval]"
- If 0 is OUTSIDE → reject H0: β=0 at 5%
- If 0 is INSIDE → fail to reject

---

## Part 3: Bias Direction Reasoning (will appear)

### OVB direction formula
$$\text{Bias direction} = \text{sign}[\text{cov}(Z, X)] \times \text{sign}[\text{cov}(Z, Y)]$$

Where Z is the omitted variable, X is your regressor, Y is the outcome.

| cov(Z,X) | cov(Z,Y) | OVB direction |
|----------|----------|---------------|
| + | + | Upward (positive) |
| − | − | Upward (positive) |
| + | − | Downward (negative) |
| − | + | Downward (negative) |

### Comparing OLS vs IV (or OLS vs FE)
- **OLS > IV/FE** → upward OVB (positive selection, ability bias, etc.)
- **OLS < IV/FE** → downward OVB
- **OLS ≈ IV/FE** → no OVB or omitted variable not correlated with regressor

### Attenuation bias (classical ME in X)
- Always biases TOWARD ZERO
- If true β = 5, estimate is between 0 and 5
- If true β = -5, estimate is between -5 and 0
- Same sign, smaller magnitude

---

## Part 4: Lecture-by-Lecture Quick Hits

### Lec 12: Interaction Terms (1 MC expected)

Model: $Y = \beta_0 + \beta_1 X + \beta_2 D + \beta_3 (X \times D)$

| Coefficient | Meaning |
|-------------|---------|
| $\beta_0$ | Intercept for D=0 group |
| $\beta_0 + \beta_2$ | Intercept for D=1 group |
| $\beta_1$ | Slope for D=0 group |
| $\beta_1 + \beta_3$ | Slope for D=1 group |
| $\beta_2$ | Difference in intercepts |
| $\beta_3$ | **Difference in slopes** ← THE answer to MC Q11 |

**Tests:**
- Slopes equal? H0: $\beta_3 = 0$ → t-test
- Profiles identical? H0: $\beta_2 = 0$ AND $\beta_3 = 0$ → joint F-test (2 restrictions)
- Slope for D=1 group equals zero? H0: $\beta_1 + \beta_3 = 0$ → `lincom`

### Lec 13: Data Issues (2-3 MCs expected)

**Measurement Error 2x2 table:**

|                  | ME in Y                  | ME in X                       |
|------------------|--------------------------|-------------------------------|
| Well-behaved     | **Unbiased**             | **Attenuation bias** (toward 0) |
| Badly-behaved    | Biased, unknown direction | Biased, unknown direction     |

**Selection bias:** arises when data is missing in a non-random way correlated with Y.
- Mroz (wage observed only for working women)
- Marathon survey (arthritis dropouts)
- Phone polls

**Outliers + LAD:** Run BOTH OLS and LAD, compare. Don't blindly drop. LAD = minimizes |residuals|, less sensitive.

### Lec 14: LPM vs Probit/Logit (FULL SHORT ANSWER LIKELY)

**LPM:** $Pr(Y=1|X) = \beta_0 + \beta_1 X$ (linear, predictions can go outside [0,1])

**Probit:** $\Phi(\beta_0 + \beta_1 X)$ (standard normal CDF, bounded)
**Logit:** $e^{XB}/(1+e^{XB})$ (logistic CDF, bounded)

**The 3 questions you WILL be asked:**

1. **Main LPM disadvantage?** Predictions outside [0,1] are nonsensical. Linear functions are unbounded.

2. **How does non-linear model fix it?** Pass linear index through CDF G that's bounded in [0,1]. Always valid probabilities.

3. **Are coefficients comparable?** NO.
   - LPM β = marginal effect on probability (direct)
   - Probit/logit β = effect on latent index, NOT on probability
   - True marginal effect = G'(·) × β, varies across observations
   - To compare: use `margins, dydx(*)` for average marginal effect

### Lec 15-16: RCTs (2-3 MCs expected)

**ATE = ITT only under PERFECT compliance** (sample Q4 answer: A)

**Attrition** = ANY way someone leaves the study (moving, dying, quitting). Sample Q5 answer: D (all of the above).

**Direction of bias:**
- Non-compliance → typically biases ATE toward zero (understates)
- Attrition → can go EITHER direction (depends on who leaves)
- Spillovers → can go either direction

**ITT vs ATE:**
- ITT = effect of being ASSIGNED to treatment (always unbiased, researcher controls Z)
- ATE = effect of actually RECEIVING treatment
- Recovery formula (random non-compliance): ATE ≈ ITT / compliance rate

### Lec 17-18: DnD (1-2 MCs expected)

**The model:** $Y_{it} = \beta_0 + \beta_1 \cdot treat_i + \beta_2 \cdot after_t + \beta_3 (treat \times after) + u_{it}$

| Coefficient | Meaning |
|-------------|---------|
| $\beta_0$ | Control group, before |
| $\beta_1$ | Pre-existing group difference |
| $\beta_2$ | Time trend in control group |
| $\beta_3$ | **DnD ESTIMATOR (causal effect)** ← MC Q6 answer |

**Sample MC Q7:** DnD works with **panel OR repeated cross-section** (D).

**Parallel trends** = the central assumption. CANNOT be directly tested (it's a counterfactual). Defend via:
1. Pre-treatment trends
2. Post-treatment trends
3. Pseudo-treatment test
4. Pseudo-outcome test

**Threats to DnD:**
1. Spillovers → biases toward zero
2. Parallel trends violation → direction depends
3. Ashenfelter dip → typically overestimates
4. Reverse causality → context-dependent

### Lec 19-20: Panel Data (FULL SHORT ANSWER LIKELY)

**Error decomposition:** $error_{it} = a_i + u_{it}$
- $a_i$ = time-invariant unobservable
- $u_{it}$ = time-varying error

**Two problems:**
1. **Within-group serial correlation** → only breaks SEs, NOT bias. Fix: clustered SEs.
2. **Zero conditional mean violation** → if OVB is in $a_i$ or $u_{it}$, OLS biased.

**FD and FE:** Both sweep out $a_i$. Both fix time-invariant OVB. Neither fixes time-varying OVB.

**Big tradeoff:** FD/FE drop time-invariant variables (IQ, gender, race) entirely.

**When T=2:** FD = FE exactly.

**Sample SA Q2 (THE pattern to know):**
Model: $wage_{it} = \alpha_0 + \alpha_1 \cdot iq_i + \alpha_2 \cdot married_{it} + a_i + u_{it}$

| Part | Question | Answer logic |
|------|----------|--------------|
| (a) | Time-varying OVB for $\alpha_2$? | Experience, tenure, age, hours worked |
| (b) | Time-invariant OVB for $\alpha_2$? | Family background, ambition, attractiveness (NOT IQ, already controlled) |
| (c) | Does FD avoid OVB in (a) and (b)? | (a) NO (time-varying stays). (b) YES (time-invariant swept out) |
| (d) | If goal is $\alpha_1$ (IQ coef), is FD appealing? | NO. IQ is time-invariant, FD drops it entirely |

**FE coefficient interpretation:** Always use **WITHIN** framing. "A 1-unit change in X **within a unit over time** causes a β change in Y for that unit."

### Lec 21-22: IV/2SLS (FULL SHORT ANSWER LIKELY)

**Two conditions for valid instrument:**
1. **Relevance:** $cov(z, x) \neq 0$ (TESTABLE via first-stage F-stat)
2. **Validity** (aka exclusion restriction, aka exogeneity): $cov(z, u) = 0$ (NOT directly testable)

**Weak instrument** (sample MC Q9): $cov(z, x)$ is small but nonzero. Answer: C.

**F > 10 rule:** First-stage F-stat below 10 = weak instrument.
- Single instrument: |t| > ~3.16 in first stage = strong

**Bias formula:** $\text{bias} \approx \frac{cov(z,u)}{cov(z,x)}$
- Weak instrument (small denominator) AMPLIFIES any validity violation

**2SLS mechanics:**
- Stage 1: Regress endogenous regressor on instrument(s) + exogenous controls. Save $\hat{x}$.
- Stage 2: Replace x with $\hat{x}$ in structural equation. Run OLS.

**FRQ template for "is z a good instrument":**
> "For z to be a good instrument, must satisfy:
> 1. **Relevance:** cov(z,x) ≠ 0. [Argue mechanism, e.g. distance raises cost of attending → less education]
> 2. **Validity:** cov(z,u) = 0. [Argue why z is or isn't related to omitted variables]
> Both must hold for IV to deliver an unbiased estimate."

**Sample Q3 walkthrough (likely format):**
- Q3a: OVB direction in `lwage = β0 + β1*educ + u`. Standard answer: ability. Upward bias.
- Q3b: Distance to college as instrument. Discuss relevance (yes, mechanism clear) and validity (debatable).
- Q3c: OLS=0.09, 2SLS=0.06. OLS > 2SLS → consistent with upward bias prediction. ✓
- Q3d: Test relevance from first stage. t = -0.07/0.01 = -7, |t| > 1.96, reject H0. Relevant.

**Counting practice (likely MC):**
- Endogenous regressors = items in parentheses LEFT of "="
- Instruments = items in parentheses RIGHT of "="
- Exogenous controls = items OUTSIDE parentheses
- Number of first stages = number of endogenous regressors
- Order condition: # instruments ≥ # endogenous regressors

### Lec 23-24: RD (1-2 MCs expected)

**Key RD assumption (sample MC Q10):** Only the probability of treatment changes discontinuously at the threshold. Everything else continuous.

**Sharp vs Fuzzy:**
- Sharp: 0 → 1 deterministic at cutoff
- Fuzzy: probability jumps but not 0 → 1

**Fuzzy RD LATE formula:**
$$\text{LATE} = \frac{\text{Local ITT (jump in outcome)}}{\text{Jump in participation at cutoff}}$$

**Sorting threat:** people manipulate running variable. Checks:
1. Density (McCrary): histogram of running variable, look for spike on favorable side
2. Balance: pre-determined covariates shouldn't jump at cutoff

**Maimonides Rule (memorize this example):**
- Running variable: ENROLLMENT (not class size)
- Cutoff: 40
- Treatment: smaller class size
- Common trap: confusing running variable with treatment

**LATE limitation:** Effect identified ONLY near cutoff. Doesn't generalize.

---

## Part 5: Substantive Interpretation Templates

When asked to "interpret a coefficient," ALWAYS include:
1. Direction (positive/negative effect)
2. Magnitude with units
3. The "holding constant" phrase if multiple regressors
4. Significance statement

### Continuous-on-continuous coefficient
> "A one-unit increase in X is associated with a β-unit increase in Y, holding [other variables] constant. This effect is [statistically significant at 5%/not significant]."

### Dummy coefficient
> "Group with D=1 has Y that is β units higher (lower) than the omitted group, holding [other variables] constant."

### Log-Y models (semi-elasticity)
> "A one-unit increase in X is associated with approximately (β × 100)% change in Y, holding [others] constant."

### Log-X models
> "A 1% increase in X is associated with a (β/100)-unit change in Y."

### Log-log models (elasticity)
> "A 1% increase in X is associated with a β% change in Y."

### Quadratic models
For $Y = \beta_0 + \beta_1 X + \beta_2 X^2$:
- Marginal effect at X = $\beta_1 + 2\beta_2 X$
- Maximum/minimum at $X^* = -\beta_1 / (2\beta_2)$
- NEVER interpret $\beta_1$ alone as "the effect of X"

### IV / 2SLS coefficient
> "For compliers (those whose X was actually moved by the instrument), a one-unit increase in X causes a β-unit change in Y."

### FE coefficient
> "A one-unit change in X **within a unit over time** is associated with a β change in Y for that unit."

### DnD coefficient (β3 on interaction)
> "The treatment caused a β-unit change in Y for the treated group, beyond what would have occurred in the absence of treatment (as proxied by the control group's time trend)."

### Fuzzy RD LATE
> "Among those who actually received treatment because of being on the eligible side of the cutoff (compliers), treatment caused a β-unit change in Y."

---

## Part 6: Hypothesis Test Writeup Template

For ANY hypothesis test FRQ, write all 4 parts:

> **1. State hypotheses:** H0: [parameter restriction]. H1: [alternative].
>
> **2. Test statistic:** t = (estimate − null)/SE = ___, OR F = ___, OR p-value = ___
>
> **3. Decision rule + conclusion:** |t| [>/<] critical value, OR p [</>]  α. Therefore [reject/fail to reject] H0 at the 5% significance level.
>
> **4. Substantive interpretation:** "There is [statistically significant evidence/insufficient evidence] that [substantive claim about the world]."

**Common critical values:**
- 5% two-sided t: 1.96
- 5% one-sided t: 1.645
- F-stat with 1 restriction: 3.84 (= 1.96²)
- F-stat with 2 restrictions: 3.00
- F-stat with 3 restrictions: 2.60

---

## Part 7: Common Traps (don't fall for these)

1. **Interaction term β3 ≠ slope for D=1 group.** Slope for D=1 = $\beta_1 + \beta_3$.

2. **ME in Y vs ME in X are DIFFERENT.** Memorize the 2x2 table.

3. **Attenuation bias is TOWARD ZERO, not "smaller."** A negative β stays negative but moves toward 0.

4. **FD/FE eliminate time-invariant variables.** Can't estimate effects of IQ, gender, race using FD/FE.

5. **Within-group correlation breaks SEs, not coefficients.** Use clustered SEs, not FE, for this issue alone.

6. **Validity is UNTESTABLE.** Don't say "I'd test validity by..." — only relevance is testable.

7. **Weak instrument: COV(z,x) IS SMALL, not zero.** Cov(z,x)=0 = irrelevant, not weak.

8. **DnD works with both panel AND repeated cross-section.** Doesn't require tracking same individuals.

9. **Parallel trends is COUNTERFACTUAL.** Cannot directly test. Only build circumstantial case.

10. **In Maimonides, ENROLLMENT is the running variable, not class size.** Class size is the treatment.

11. **OLS > IV → upward bias** (consistent with positive selection / ability bias). OLS < IV → downward bias.

12. **ITT = ATE only under PERFECT compliance.** Not under "no attrition" or "no Ashenfelter dip."

---

## Part 8: 5-Minute Final Self-Check

Answer these without thinking. If any feels shaky, jump back.

- [ ] What does $\beta_3$ on an interaction term mean? **Difference in slopes**
- [ ] ME in Y, well-behaved → ? **Unbiased**
- [ ] ME in X, classical → ? **Attenuation bias toward zero**
- [ ] LPM main disadvantage? **Predictions outside [0,1]**
- [ ] Are LPM and probit coefficients comparable? **NO. Compute marginal effects via `margins`**
- [ ] ITT = ATE when? **Perfect compliance only**
- [ ] What counts as attrition? **Any way of leaving the study (move, die, quit)**
- [ ] DnD estimator is which coefficient? **Coefficient on interaction (treat × after) = $\beta_3$**
- [ ] DnD works with what data? **Panel OR repeated cross-section**
- [ ] Two RCT/DnD assumptions that fail = ? **Spillovers + parallel trends violation**
- [ ] Two pieces of panel error decomposition? **$a_i$ (time-invariant) + $u_{it}$ (time-varying)**
- [ ] What does FD/FE fix? **Time-invariant OVB**
- [ ] What does FD/FE NOT fix? **Time-varying OVB and time-invariant regressor coefficients**
- [ ] When T=2, FD vs FE? **Identical**
- [ ] Two IV conditions? **Relevance: cov(z,x)≠0. Validity: cov(z,u)=0**
- [ ] Which is testable? **Relevance only**
- [ ] Weak instrument means what? **cov(z,x) is small but nonzero**
- [ ] Weak instrument F-stat threshold? **F < 10 = weak**
- [ ] Key RD assumption? **Only treatment probability changes discontinuously at cutoff**
- [ ] Sharp vs fuzzy RD? **Sharp: 0→1. Fuzzy: partial jump**
- [ ] Fuzzy RD LATE formula? **Local ITT / jump in participation**
- [ ] OLS > IV means what? **Upward OVB (positive selection)**
- [ ] Running variable in Maimonides Rule? **Enrollment, not class size**

If 22+ of these feel automatic, you're ready.

---

## Part 9: Exam Strategy (last 5 minutes before walking in)

1. **Do MCs first.** They're 4 pts each, fastest points. Don't burn 10 min on a hard MC.
2. **For blue book, write 4-part hypothesis test format every time.** Even partial credit is valuable.
3. **If unsure on MC, eliminate clearly wrong answers.** Sample exam shows distractors are usually 2 wrong + 2 plausible.
4. **For "is z a good instrument?" questions ALWAYS discuss BOTH relevance AND validity.** Even if one is obvious.
5. **Show your work on tests.** Don't just write "reject." Write t = X/Y = Z, |Z| > 1.96, reject.
6. **Manage time:** 75 min, 11 MCs + 3 blue books. Roughly 2 min per MC, 17 min per blue book, 2 min buffer.
7. **If stuck, move on and come back.** Don't get lost on Q3 and miss Q9.
8. **Read every "explain your reasoning" prompt twice.** Half the question is in the prompt.

---

You've got this. Eat something. Walk in. Crush it.
