# Lecture 19: Panel Data

## What is panel data
Panel data (aka longitudinal): same units (people, households, firms, cities) observed at multiple points in time.

Subscripts:
- `i` indexes the unit (household, city, person)
- `t` indexes time

Why we care, in two sentences:
1. **Bad news:** panel data breaks the iid assumption.
2. **Good news:** panel data lets you control for time-invariant unobservables you couldn't measure even if you tried.

---

## The error decomposition (the foundation for everything)
Panel data error term has two pieces:

```
error_it = a_i + u_it
```

- `a_i` = time-invariant component. Same value across all t for unit i. Examples: innate ability, IQ, family background, geography of a city, distance to coast.
- `u_it` = time-varying component. Different at each t. Examples: random shocks, household size in a given year, weather, business cycle.

Every concept in this lecture (FD, FE, clustered SEs) is built on this decomposition. Get it locked in.

---

## Problem 1: within-group serial correlation (a small problem)

### What's broken
Least Squares Assumption #2 requires iid observations:
```
cov(error_i, error_j) = 0 for all pairs i ≠ j
```

With panel data, you observe the same unit at multiple t. So consider the errors for the same household at two time periods:
```
cov(error_i1, error_i2) = cov(a_i + u_i1, a_i + u_i2)
                        = cov(a_i, a_i) + cov(a_i, u_i1) + cov(a_i, u_i2) + cov(u_i1, u_i2)
                        = var(a_i) + 0 + 0 + 0
                        = var(a_i) ≠ 0
```

Even if every other covariance is zero (heroic assumption), the same `a_i` shows up in both errors, so cov is non-zero. iid is dead.

### What you lose vs what survives
| Property | Survives? |
|---|---|
| OLS unbiasedness | YES (still unbiased) |
| OLS consistency | YES |
| OLS variance formula (default SEs) | NO (broken) |
| Asymptotic normality / valid t-stats / valid CIs | NO (broken) |

### The fix
Use **clustered standard errors**. In Stata:
```stata
regress y x1 x2, vce(cluster hhid)
```

Clustered SEs estimate the within-group covariance and adjust the SE formula. They don't fix bias (because there isn't any from this issue), only fix inference.

### Exam trap (REALLY IMPORTANT)
Within-group correlated errors does **NOT** cause bias in coefficients. It only screws up SEs.

If a question asks about **bias from panel data structure**, this is the wrong answer. If it asks about **what's wrong with OLS standard errors**, this is the right answer.

---

## Problem 2: zero conditional mean (the BIG problem)

### What's broken
Least Squares Assumption #1:
```
E(error_it | regressors) = 0
```

With the decomposition `error_it = a_i + u_it`, this requires both:
```
E(a_i | regressors) = 0
E(u_it | regressors) = 0
```

If your OVB is **time-invariant**, it lives in `a_i`.
If your OVB is **time-varying**, it lives in `u_it`.

### Why this matters more than Problem 1
If OLS is biased, your point estimates are wrong. Worrying about SEs at that point is "rearranging deck chairs on the Titanic" (the prof literally said this).

Clustered SEs do nothing for this. You need a different tool.

---

## The two solutions: FD and FE

Both work by **sweeping out `a_i`**. Both eliminate bias from time-invariant OVB. Neither fixes time-varying OVB.

### First Differences (FD)
Take this period's equation minus last period's:
```
y_it       = β0 + β1*x_it       + a_i + u_it
y_{i,t-1}  = β0 + β1*x_{i,t-1}  + a_i + u_{i,t-1}
─────────────────────────────────────────────────
Δy_i       =       β1*Δx_i              + Δu_i
```

Notice what got swept out:
- β0 (the constant): gone, since β0 - β0 = 0
- `a_i`: gone, since a_i - a_i = 0
- Any time-invariant regressors (like IQ, gender, race): gone

### Fixed Effects (FE)
Subtract group means from original equation. Group mean = average across all t for unit i:
```
y_it       = β0 + β1*x_it    + a_i + u_it
ȳ_i        = β0 + β1*x̄_i     + a_i + ū_i
─────────────────────────────────────────
ỹ_it       =       β1*x̃_it        + ũ_it
```

(Tildes = deviations from group means.)

Same things swept out: β0, a_i, time-invariant regressors.

In Stata: `xtreg y x, i(unit_id) fe vce(cluster unit_id)`

### When FD = FE
**When T = 2 (two time periods), FD and FE give identical coefficients and SEs.** Always.

When T > 2, they differ slightly. FE is more common in practice.

---

## The big tradeoff
FD/FE buy you protection against time-invariant OVB.

The cost: you lose the ability to estimate coefficients on time-invariant variables.

If you care about the effect of something that doesn't change (IQ, race, gender, distance to coast, family background), FD and FE drop it from the model entirely. You can't get a coefficient on it.

This tradeoff is the source of part (d) on the sample midterm.

### Why time-invariant variables get dropped (the technical reason)
After group-demeaning, a time-invariant variable becomes zero for every observation:
```
iq_i - mean(iq_i) = iq_i - iq_i = 0
```
A column of zeros has perfect multicollinearity with anything (including the implicit constant), so OLS literally can't compute a coefficient. Stata will drop the variable automatically.

If asked "why can't you include gender in an FE model on individuals?", this is why.

---

## What "controlling for" means in panel data (the key conceptual point)
FE controls for ALL time-invariant variables, observed AND unobserved, without you needing to measure them or even name them.

This is the magic of FE and the answer to "why is panel data better than cross-section?"

In a cross-section, to control for ability you'd need to measure ability. Most surveys don't.
In FE with panel data, ability gets swept out automatically as long as it doesn't change over time.

This is also why FE is so popular in applied work: it removes a huge category of OVB without requiring extra data.

The price: it can ONLY remove time-invariant unobservables. If your unobservable changes over time, FE does nothing.

---

## The CCT example (Lecture 19's running example)
The lecture also walks through a Conditional Cash Transfer example. Same logic as the marriage premium, different setup. If the exam frames a question around a treatment program rather than a labor market outcome, same logic applies.

Setup:
```
foodexp_it = β0 + β1*cct_it + β2*income_it + β3*iq_i + a_i + u_it
```

Where:
- cct_it = amount of cash transfer (varies across HH and time)
- income_it = HH income (time-varying control)
- iq_i = HH head's intelligence (time-invariant control)
- a_i = HH-level time-invariant unobservables (preferences regarding food, family background)
- u_it = HH-level time-varying error (HH size in a year, local food prices)

The OVB worry: if treatment isn't randomized, HHs that select into CCT are different from those that don't. Question is whether those differences are time-invariant (then FD/FE fix it) or time-varying (then they don't).

Same template applies to any treatment effect question with panel data.

---

## Sample midterm tie-ins

### MC Q8
> "In panel data, the regression error:"

**Answer: A) is likely to be correlated across time periods within each sampling unit.**

This is the within-group serial correlation point. The same `a_i` is in every error for unit i, so errors at different t are correlated.

Wrong answers:
- B (across units within a period): not the issue panel data creates
- C (iid due to random sampling): panel data violates iid, that's the whole point
- D: would only be true if A weren't, which it is

### Short Answer Q2 (CRITICAL question)
Model: `wage_it = α0 + α1*iq_i + α2*married_it + a_i + u_it`

Subscripts matter:
- `iq_i` has only i: **time-invariant**
- `married_it` has it: **time-varying**

**a) Time-varying OVB for α2:**
Examples: experience, tenure, age, hours worked, location, kids. These vary over time AND plausibly correlate with both marriage status and wage.

**b) Time-invariant OVB for α2:**
Examples: family background, attractiveness, "marriageable type," underlying ambition. Anything stable about a person beyond IQ (since IQ is already controlled).

**c) Does FD avoid the OVB?**
- For (b) time-invariant OVB: **YES**. FD sweeps `a_i` out, so any time-invariant unobservable is gone.
- For (a) time-varying OVB: **NO**. FD does not touch `u_it`. The time-varying OVB stays and still biases α2.

**d) If primary goal is α1 (the IQ coefficient), is FD appealing?**
**NO.** IQ is time-invariant. FD sweeps it out entirely. You literally cannot estimate α1 via FD or FE. You're stuck with pooled OLS for the iq coefficient, which means you have to control for OVB the old-fashioned way (add controls).

This is the "big tradeoff" showing up on an exam.

---

## Quick reference cheat sheet

| Issue | Fix |
|---|---|
| Within-group correlated errors (SE problem only) | Clustered SEs |
| Time-invariant OVB | FD or FE |
| Time-varying OVB | Neither FD nor FE. Need IV or controls. |
| Want coefficient on time-invariant variable | Pooled OLS (must control for OVB). FD/FE drop it. |
| T = 2 | FD = FE always |
| T > 2 | FD ≠ FE. FE more common. |
