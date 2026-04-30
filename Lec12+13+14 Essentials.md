# Lec 12-14 Exam Essentials

## Exam weight ranking (based on sample midterm)

| Topic | Sample hits | Expected exam load |
|-------|-------------|-------------------|
| **LPM vs Probit/Logit (Lec 14)** | Full short answer Q1 | **Almost certainly a full short answer (15-20 pts)** |
| **Data issues (Lec 13)** | 3 MCs (Q1, Q2, Q3) | **2-3 MCs (8-12 pts)** |
| **Interaction terms (Lec 12)** | 1 MC (Q11) | **1 MC (4 pts)** |

Spend your time accordingly. Lec 14 is the highest priority by far.

---

# LECTURE 14: LPM vs Probit/Logit (HIGHEST PRIORITY)

## The model setups

**LPM:**
$$Pr(Y=1|X) = \beta_0 + \beta_1 X$$

Just OLS on a binary Y. Linear function of X.

**Probit:** $Pr(Y=1|X) = \Phi(\beta_0 + \beta_1 X)$ where $\Phi$ is standard normal CDF
**Logit:** $Pr(Y=1|X) = \frac{e^{\beta_0 + \beta_1 X}}{1 + e^{\beta_0 + \beta_1 X}}$

Both pass the linear index through a bounded S-curve so probabilities stay in [0,1].

## The three exam questions you WILL be asked

### Q: Main disadvantage of LPM?
**Answer:** LPM can produce predicted probabilities outside [0,1], which are nonsensical. This happens because a linear function is unbounded. In the Mroz example, 16 observations had predicted prob < 0 and 17 had predicted prob > 1.

### Q: How does the non-linear model resolve this?
**Answer:** Probit/logit pass the linear index through a CDF (link function G) that is bounded between 0 and 1. So $Pr(Y=1|X) = G(\beta_0 + \beta_1 X)$ is always a valid probability. Probit uses standard normal CDF, logit uses logistic function. Both are S-shaped, bounded, monotonic.

### Q: Are LPM and probit/logit coefficients directly comparable?
**Answer: NO.**
- In **LPM**, $\beta_1$ IS the marginal effect on probability (derivative of linear function = coefficient)
- In **probit/logit**, $\beta_1$ is the effect on the latent index, NOT the marginal effect on probability
- True marginal effect in probit/logit = $G'(\beta_0 + \beta_1 X) \times \beta_1$, which varies across observations
- To compare: compute average marginal effect using `margins, dydx(*)` in Stata. The avg marginal effect typically matches the LPM coefficient closely.

**Mroz example numbers (good to drop in an answer):**
- LPM educ coef: 0.038
- Logit educ coef: 0.221 (NOT comparable to 0.038)
- Probit educ coef: 0.131 (NOT comparable to 0.038)
- Logit avg marginal effect: 0.0395 (comparable! matches LPM)
- Probit avg marginal effect: 0.0394 (comparable! matches LPM)

---

# LECTURE 13: Data Issues (Three Things That Break OLS)

## 1. Measurement Error (memorize this 2x2 table)

|                        | ME in Y                    | ME in X                    |
|------------------------|----------------------------|----------------------------|
| **Well-behaved** (E[ME\|X]=0) | **Unbiased**          | **Attenuation bias** (toward zero) |
| **Badly-behaved**      | Biased, unknown direction | Biased, unknown direction |

**Sample MC Q2** asked: "ME in Y, knowing X tells us nothing about expected value of ME" → top-left cell → **Unbiased (B)**.

**Key concept: Attenuation bias** = estimate is biased TOWARD ZERO but stays on same side. If true β = 5, attenuated estimate is between 0 and 5. If true β = -5, estimate is between -5 and 0.

**One-sentence intuition for attenuation:** Random noise in X adds variation that has nothing to do with Y, so the regression line gets pulled flatter (toward zero slope).

## 2. Sample Selection Bias

**The principle:** Bias arises when the sample is selected in a way correlated with Y.

**Sample MC Q1 answer:** Selection bias arises when **data is missing for some observations** in a non-random way (D).

**Two types:**
- **Missing completely at random:** OK, OLS is fine
- **Missing in non-random way (correlated with Y or X):** selection bias, OLS biased

**Classic examples:**
- Mroz: regressing wage on educ, but wage only observed for women in labor force (self-selected workers)
- Marathon study: surveying marathon runners about running and arthritis (people with running-induced arthritis dropped out)
- Phone polls with 30% response rate

**Reframing escape hatch:** if you redefine your population of interest to match the sample (e.g., "women in labor force" not "all women"), it's no longer selection bias. But this limits external validity.

## 3. Outliers and OLS vs LAD

**Why OLS is sensitive:** OLS minimizes squared residuals, so outlier with residual 10 contributes 100 to the loss. One outlier dominates.

**LAD (Least Absolute Deviation):** minimizes |residuals|, so outlier residual 10 contributes 10. Proportional weight, not disproportionate.

**Sample MC Q3 answer:** **C** - "It can be informative to run both OLS and LAD to determine the extent to which the outlier influences the regression results."

NOT A (OLS isn't always better), NOT B (LAD isn't always better), NOT D (don't blindly drop valid data).

**Why LAD isn't default:** No closed-form solution (absolute value has a kink), requires iterative computation. Historical compute limits made OLS standard.

---

# LECTURE 12: Interaction Terms

## The model
$$Y = \beta_0 + \beta_1 X_1 + \beta_2 X_2 + \beta_3 (X_1 \times X_2) + u$$

Effect of $X_1$ on Y now depends on $X_2$:
$$\frac{\partial Y}{\partial X_1} = \beta_1 + \beta_3 X_2$$

## THE exam question (Sample MC Q11) - memorize this

> In $Y = \beta_0 + \beta_1 X + \beta_2 D + \beta_3(X \times D) + u$ where X is continuous and D is binary, the parameter $\beta_3$:
>
> **C) indicates the difference in the slopes across the two groups** ✓

**Why the others are wrong:**
- A wrong: slope when D=1 is $\beta_1 + \beta_3$, not $\beta_3$ alone
- B wrong: D not being normal doesn't matter (CLT applies to residuals)
- D wrong: $\beta_3$ has meaning even when interaction term equals zero in some observations

## Coefficient interpretation cheat sheet (continuous × dummy case)

For $Y = \beta_0 + \beta_1 X + \beta_2 D + \beta_3 (X \times D)$:

| Coefficient | Meaning |
|-------------|---------|
| $\beta_0$ | Intercept for D=0 group |
| $\beta_0 + \beta_2$ | Intercept for D=1 group |
| $\beta_1$ | Slope for D=0 group (omitted/baseline) |
| $\beta_1 + \beta_3$ | Slope for D=1 group |
| $\beta_2$ | Difference in intercepts |
| $\beta_3$ | **Difference in slopes** |

## Hypothesis tests

| Test | H0 | Type |
|------|-----|------|
| Slopes equal across groups | $\beta_3 = 0$ | Single t-test |
| Identical wage profile (no diff at all) | $\beta_2 = 0$ AND $\beta_3 = 0$ | F-test (joint, 2 restrictions) |
| Slope is zero for D=1 group | $\beta_1 + \beta_3 = 0$ | `lincom` linear combination |

## Glass ceiling example (lock this in)
If wage = $\alpha_0 + \alpha_1 \cdot female + \alpha_2 \cdot exper + \alpha_3 (female \times exper)$:
- Male return to experience = $\alpha_2$
- Female return to experience = $\alpha_2 + \alpha_3$
- $\alpha_3 < 0$ and significant = glass ceiling supported

---

## 90-second self-check

Can you answer these cold?

**Lec 14 (highest priority):**
- [ ] Main disadvantage of LPM? (predictions outside [0,1])
- [ ] How does probit/logit fix it? (CDF G bounds index in [0,1])
- [ ] Are coefficients comparable? (NO. LPM coef = marginal effect; probit/logit coef = effect on latent index)
- [ ] How to compare? (compute avg marginal effect via `margins`)

**Lec 13:**
- [ ] ME in Y, well-behaved → ? (Unbiased)
- [ ] ME in X, classical → ? (Attenuation bias toward zero)
- [ ] What does attenuation bias mean? (toward zero, same sign as truth)
- [ ] When does selection bias arise? (sample selected correlated with Y)
- [ ] OLS vs LAD with outliers? (run both, compare)

**Lec 12:**
- [ ] What does $\beta_3$ on the interaction term mean? (difference in slopes)
- [ ] Slope for D=1 group? ($\beta_1 + \beta_3$, not $\beta_3$)
- [ ] How to test if profiles are identical? (joint F-test, 2 restrictions)

If yes to all 12, you're set on Lec 12-14.
