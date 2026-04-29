# Lecture 20: Applications of Panel Data

This lecture is mostly Lecture 19 applied to two examples (rental prices, marriage premium). The new content: Stata mechanics, robust vs clustered SEs, and the time trend wrinkle.

---

## Setup: rental prices example
Model:
```
lrent_it = β0 + β1*lpop_it + β2*lavginc_it + β3*pctstu_it + a_i + u_it
```

Data: 64 cities, 2 time points (1980, 1990). N = 128.

What's where:
- `a_i` (time-invariant): geographic amenities, climate, distance to coast, "vibe" of the city
- `u_it` (time-varying): construction of new housing, business cycles, local shocks

---

## Method 1: Pooled OLS

Just stack the data and run OLS. In Stata:
```stata
regress lrent lpop lavginc pctstu, vce(cluster city)
```

### Robust SE vs clustered SE
Same point estimates either way. Pooled OLS coefficients don't change based on which SE you choose.

| | Robust SE | Clustered SE |
|---|---|---|
| lpop | 0.031 (0.027) | 0.031 (0.032) |
| lavginc | 0.877 (0.044) | 0.877 (0.046) |
| pctstu | 0.007 (0.001) | 0.007 (0.002) |

Pattern: clustered SEs are usually larger than robust (rule of thumb: robust ≤ clustered, but exceptions exist, see the constant term). Why? Because clustered accounts for the fact that you have less independent information than your N suggests.

Also notice the F-stat degrees of freedom drop: F(3, 124) → F(3, 63). The 63 = number of clusters minus 1.

### Bias in pooled OLS
Both time-invariant (geography) AND time-varying (construction) factors can cause bias if they correlate with regressors. Pooled OLS doesn't address either.

The MC question for this point on the sample exam (had it appeared): **Answer = C, all of the above.**

---

## Method 2: First Differences (FD)

Take the difference for each city:
```
Δlrent_i = β1*Δlpop_i + β2*Δlavginc_i + β3*Δpctstu_i + Δu_i
```

In Stata:
```stata
reg clrent clpop clavginc cpctstu, noconstant vce(cluster city)
```

### Why `noconstant`?
The original β0 was a constant: subtract β0 from itself, get zero. So the FD model has no intercept by default.

Stata's default behavior is to add an intercept automatically. You override that with `noconstant`.

**Big exception:** If your original model has a year dummy, the FD intercept comes back. (See "Adding time trends" below.)

### What FD addresses for the rental example
- Geographic amenities (in `a_i`): SWEPT OUT. No bias from this anymore.
- New construction (in `u_it`): STILL THERE. Could still bias your coefficients.

The MC question version: **Answer = B, only construction (which is in `u_it`).**

---

## Method 3: Fixed Effects (FE)

Group-demean everything, then run OLS:
```
ỹ_it = β1*x̃_it + ũ_it
```

In Stata:
```stata
xtreg lrent lpop lavginc pctstu, i(city) fe vce(cluster city)
```

### Reading Stata's FE output: things to ignore
- **`_cons`**: Stata reports a constant. IGNORE IT. The model has no intercept. Stata uses that line to report an estimate of the average size of `a_i`, which isn't a statistic you care about.
- `sigma_u`, `sigma_e`, `rho`: not on the exam, not relevant for interpretation.

### What you DO use
- Coefficient table (with clustered SEs)
- Within R-squared (the relevant goodness-of-fit measure for FE)
- F-stat for joint significance

### Reading xtreg's three R-squareds
Stata reports three R-squareds in FE output: within, between, overall.

```
R-sq:
    within  = 0.9337    ← use this for FE
    between = 0.4318
    overall = 0.6871    ← what pooled OLS would give
```

| Number | What it measures |
|---|---|
| within | How much WITHIN-unit variation is explained. THIS is the relevant R² for FE. |
| between | How much variation in unit means is explained. Not what FE is fitting. |
| overall | The standard R² you'd get from pooled OLS on the same data. |

If asked to "interpret the R²" in FE output, use the WITHIN one. The within R² of 0.9337 in the rental example means about 93% of within-city variation in log rent is explained by the model.

---

## The "within" interpretation of FE coefficients (HIGH PROBABILITY EXAM TOPIC)

FE coefficients have a specific meaning. They represent the relationship between **within-unit changes** in x and **within-unit changes** in y. NOT the relationship across different units.

Pooled OLS uses BOTH cross-sectional variation (between cities) AND time variation (within cities) to estimate β.
FE uses ONLY within-unit time variation.

### Example interpretation (rental data)
FE coefficient on lpop = 0.297.

Pooled OLS interpretation: "A 1% increase in population is associated with a 0.297% increase in rent."

FE interpretation: "A 1% increase in population **within a city over time** is associated with a 0.297% increase in **that city's rent**."

The "within" framing matters. If asked for a "substantive interpretation" of an FE coefficient, lead with the within framing. Don't copy the OLS phrasing.

### Why this matters for inference
A variable can be statistically significant in pooled OLS but insignificant in FE. That's not a contradiction. It means the variable matters for explaining differences ACROSS units but not for explaining changes WITHIN a unit over time.

Example from the rental data: lpop is barely significant in pooled OLS (t=1.15, p=0.336 with clustered SEs) and insignificant in FE (t=1.52, p=0.134). The substantive read: there is no within-city effect of population on rent. Whatever weak relationship pooled OLS picked up was driven by between-city variation.

If asked "why does this variable lose significance under FE?", the answer is usually that the variable mostly varies between units, not within them, so FE has little variation left to estimate the effect.

---

## When FD = FE (memorize this)

**T = 2 → FD and FE are IDENTICAL.** Same coefficients, same SEs.

Verified in the rental data:
| | OLS | FD | FE |
|---|---|---|---|
| lpop | 0.031 (0.032) | 0.297 (0.196) | 0.297 (0.196) |
| lavginc | 0.877 (0.046) | 0.940 (0.048) | 0.940 (0.048) |
| pctstu | 0.007 (0.002) | 0.019 (0.006) | 0.019 (0.006) |

T > 2 → FD and FE differ slightly. FE is more common in applied work.

---

## Adding a time trend (the wrinkle)

What if you want to allow the dependent variable to drift over time, separate from your regressors? Add a year dummy:
```
lrent_it = β0 + β1*y90_it + β2*lpop_it + β3*lavginc_it + β4*pctstu_it + a_i + u_it
```

### In pooled OLS
Just add y90 as another regressor. β1 = 0.262 means rents went up by ~26 log points from 80 to 90, controlling for everything else.

### In FE
Add y90 to xtreg. It gets demeaned just like any other variable. Estimate stays interpretable.

### In FD (THE TRICKY PART)
Take FD of y90:
- y90 in 1980 = 0
- y90 in 1990 = 1
- Δy90 = 1 for every observation

So:
```
Δlrent_i = β1*1 + β2*Δlpop + β3*Δlavginc + β4*Δpctstu + Δu_i
         = β1 + β2*Δlpop + ...
```

**β1 became the intercept of the FD model.**

### The moral
Whether to include an intercept in the FD model depends on whether the original model had a time trend.
- Original model had y90 → keep the intercept in FD (it's the time trend)
- Original model had no time trend → use `noconstant` in FD

This could easily show up as a Stata interpretation question.

### The y90 = FD intercept identity
Comparing rental results with time trend added:

| | Pooled OLS | FD | FE |
|---|---|---|---|
| y90 | 0.262 (0.064) | (intercept) 0.386 (0.048) | 0.386 (0.048) |
| constant | -0.569 | 0.386 (0.048) | (n/a) |

Notice: **the y90 coefficient in FE equals the intercept in the FD model with year dummy added, both at 0.386.** Same number, same SE. This isn't a coincidence. They're estimating the same thing. If asked "why are these identical?", that's the answer: both are extracting the within-unit time trend.

---

## OVB from time itself (the lavginc story)

Compare lavginc across all three specifications:

| Spec | lavginc coefficient |
|---|---|
| Pooled OLS, no time trend | 0.877 |
| FE, no time trend | 0.940 |
| FE, with y90 added | 0.310 |

What happened? Without the time trend, FE was attributing the 1980-1990 income growth to "income causes rent." Once you control for the time trend (y90), a chunk of that effect goes away. The "true" within-city, time-trend-adjusted effect of income on rent is much smaller (~31% vs ~94%).

Substantive read: between 1980 and 1990, both incomes AND rents rose due to general macroeconomic trends (inflation, growth). If you don't control for time, you mistake "both went up together due to time" for "income causes rent."

This is a classic OVB story where time itself is the omitted variable. Once you control for it, the estimated effect changes substantially. The exam could test this with any "before vs after adding time trend" comparison.

---

## Marriage wage premium example (Korenman & Neumark 1991)

Setup:
```
log(wage_it) = β0 + β1*married_it + a_i + u_it
```

What's in `a_i`? Innate ability, intelligence, family background, "marriageable type." Plausibly correlated with both wages AND marriage (higher-ability men more likely to marry AND earn more).

### Results
| Method | Married coefficient |
|---|---|
| Pooled OLS | 0.11 (0.02) |
| Fixed Effects | 0.06 (0.03) |

### Interpretation (this is exam-relevant logic)
Pooled OLS is biased UPWARD relative to FE.

Why? Positive selection into marriage. The kind of guys who get married are also the kind of guys who earn more, even before they get married. Pooled OLS captures both the causal effect of marriage AND the selection effect. FE strips out the selection effect (since "type of guy" is time-invariant and goes into `a_i`).

The FE estimate (0.06) is the "true" causal effect IF we assume all OVB is time-invariant. If something time-varying drives both wage and marriage simultaneously (like a promotion making you more attractive on the dating market), FE doesn't fix it.

### General rule for comparing OLS vs FE (HIGH PROBABILITY EXAM TOPIC)
This is the panel-data version of the OLS vs 2SLS comparison from Q3 of the sample midterm. Same structural pattern.

| Pattern | Interpretation |
|---|---|
| Pooled OLS > FE | Time-invariant unobservable is **positively correlated** with the regressor (positive selection) |
| Pooled OLS < FE | Time-invariant unobservable is **negatively correlated** with the regressor (negative selection) |
| Pooled OLS ≈ FE | No time-invariant OVB, or `a_i` uncorrelated with regressor |

### Worked example: marriage premium
OLS = 0.11, FE = 0.06. OLS > FE. Conclusion: time-invariant unobservable (ability, marriageable type) is positively correlated with the marriage dummy. Higher-type men both earn more AND marry more. OLS captures both, FE isolates the causal effect.

### Worked example: returns to education (Q3 logic)
On Q3 of the sample midterm, OLS slope = 0.09, 2SLS slope = 0.06. OLS > 2SLS. Possible explanations:
1. Ability bias: OLS picks up positive correlation between ability and education, biasing OLS upward. 2SLS strips this out.
2. Attenuation bias is NOT the right answer here, because attenuation would bias OLS toward zero (making OLS smaller than 2SLS, the opposite of what we see).

Same structural diagnostic, different econometric tool. The exam will reward students who recognize the pattern across panel data and IV settings.

---

## Sample midterm tie-ins

### Short Answer Q2 (the big one)
Model: `wage_it = α0 + α1*iq_i + α2*married_it + a_i + u_it`

This is essentially Korenman & Neumark, but with IQ added explicitly as a control (which makes the question subtler).

**Subscript check:**
- `iq_i`: only i, so **time-invariant**
- `married_it`: it, so **time-varying**

**(a) Time-varying OVB for α2:**
Need something that varies over time AND correlates with both wage and married.
Examples: job experience, tenure at firm, age, hours worked, kids, location, having a stable career.

**(b) Time-invariant OVB for α2:**
Need something stable about a person that correlates with both wage and married, beyond IQ (since IQ is already controlled).
Examples: family background / parental wealth, attractiveness, underlying ambition, personality traits, "marriageable type."

**(c) Does FD avoid the OVB you discussed in (a) and (b)?**
- (a) time-varying: **NO**. FD only sweeps out `a_i`. Your time-varying OVB is in `u_it`, untouched.
- (b) time-invariant: **YES**. FD eliminates `a_i`, which is exactly where time-invariant OVB lives.

**(d) If primary goal is α1 (the IQ coefficient), is FD appealing?**
**NO**. IQ is time-invariant (subscript i only). FD differences out anything time-invariant, including iq itself. You'd lose your coefficient of interest. Same is true for FE. To estimate α1, you have to use pooled OLS and rely on controls for OVB.

This is THE point about the FD/FE tradeoff. They handle a specific kind of OVB but cost you the ability to study time-invariant variables.

### MC Q7
> "A difference-in-difference estimator can be used:"

**Answer: D) with either panel or repeated cross-sectional data.**

DnD just compares group means before/after. You don't need to follow the same individuals over time. A repeated cross-section works fine. (The "Card & Krueger minimum wage NJ vs PA" study used a repeated cross-section.)

### MC Q6 (DnD estimator formula)
With:
```
outcome_it = β0 + β1*treat_i + β2*after_t + β3*(treat × after) + u_it
```

The DnD estimator is the coefficient on the **interaction term**: **β3**.

Quick logic:
- β0 = control group, before
- β0 + β1 = treatment group, before
- β0 + β2 = control group, after
- β0 + β1 + β2 + β3 = treatment group, after
- (Treat after − Treat before) − (Control after − Control before) = β3

---

## Quick exam checklist

When you see a panel data question, run through these:
1. What's in `a_i` (time-invariant)? What's in `u_it` (time-varying)?
2. Is the OVB time-invariant or time-varying? Determines if FD/FE will help.
3. Is the **regressor of interest** time-invariant or time-varying? If time-invariant, FD/FE won't even let you estimate it.
4. Is the question about **bias** or **standard errors**? Clustered SEs fix the SE problem only.
5. T = 2? FD = FE.
6. Is there a year dummy in the original model? If yes, FD model needs an intercept.
7. **Compare OLS vs FE coefficients.** If they differ, what does the gap tell you about time-invariant OVB?
8. **Interpret FE coefficients with "within" framing.** A 1-unit change WITHIN a unit produces a β change in y FOR THAT UNIT.
9. **Variable significant in OLS but not FE?** Likely the variable mostly varies between units, not within them.
10. **Adding a time trend changes coefficients?** Time itself was an omitted variable. Both rose together.

---

## Stata commands reference

```stata
* Pooled OLS with clustered SEs
regress y x1 x2, vce(cluster unit_id)

* First differences (manually constructed Δ variables)
reg dy dx1 dx2, noconstant vce(cluster unit_id)
* (with time dummy in original, drop noconstant)

* Fixed effects
xtreg y x1 x2, i(unit_id) fe vce(cluster unit_id)
```
