# Lecture 17: Difference in Differences (DnD)

> **Why this lecture matters for the exam:** DnD shows up directly on the sample paper as MC Q6 (identifying the DnD estimator coefficient) and MC Q7 (which data structures DnD works with). It also bleeds into the panel data short answer (Q2) because first-differences and DnD share the same conceptual root. If you only had 30 minutes to prep, this lecture is one of the highest-leverage ones.

---

## Sample Exam Mapping

| Sample Q | What it tests | Where in this lecture |
|---|---|---|
| MC Q6 | Identifying the DnD coefficient in `outcome = β0 + β1·treat + β2·after + β3·treat·after + u` | Section 4 (the interacted regression) |
| MC Q7 | DnD works with panel OR repeated cross-section | Sections 1 and 5 |
| MC Q8 (panel error structure) | Repeated obs on same unit | Section 5 |
| Short Answer Q2 (panel/first-diff) | Builds on the DnD intuition introduced here | Section 5 |

**Locked answers from these questions:**
- **MC Q6 → C (β3)**. The interaction coefficient IS the DnD estimator. The other betas pick up baseline group differences and time trends.
- **MC Q7 → D (either panel or repeated cross-sectional data)**. DnD is about comparing two differences over time and group, NOT about whether you track the same individuals.

---

## 1. Data Structures (the setup)

Three types of data you should be able to identify on sight:

**Cross-sectional:** Sample drawn at one point in time. Indexed by `i` only. No time variation. This is everything we did in Midterm 1.

**Repeated (or pooled) cross-sectional:** Two or more samples drawn from the same population at different times. Different individuals each wave. Indexed by `i` AND `t`. Example: the wage/exper data with 2000 obs and 2010 obs as separate people.

**Panel (or longitudinal):** Same individuals tracked across time. Indexed by `i` AND `t`, but `i=1` in 2000 is the same person as `i=1` in 2010. **Visually identical to repeated cross-section in a table.** The difference is conceptual, not visual.

**Why this matters:** The exam loves to test that DnD works with EITHER repeated cross-section OR panel (MC Q7 → D). The tracking-the-same-individual feature of panel data isn't required for DnD. We'll see this used differently when you get to Lec 18 panel methods.

---

## 2. Pooled Regression with a Year Dummy (warm-up)

If you have repeated cross-sections and run:

```
wage_it = β0 + β1·exper_it + u_it
```

This is called a **pooled regression**. You're treating both years as one big sample.

**Problem:** if wages are systematically higher in 2010 than 2000 (because of inflation, productivity growth, whatever), the pooled regression will confuse this time trend with the experience effect. The intercept is wrong because it's averaging two different intercepts.

**Fix:** add a year dummy.

```
wage_it = β0 + β1·exper_it + δ0·yr2010_t + u_it
```

Now `δ0` captures the shift in the intercept between 2000 and 2010. The regression line is allowed to be higher in 2010.

**Going further:** if you also think the *return* to experience changed (slope, not just level), interact:

```
wage_it = β0 + β1·exper_it + δ0·yr2010_t + δ1·exper_it·yr2010_t + u_it
```

Now `δ1` = the change in the experience-wage slope between 2000 and 2010. This is a **test of structural change** — testing whether the wage equation itself stayed the same over time.

> **Connection to Midterm 1:** This is just the interacted dummy variable model from your Lec 14ish material. New name, same machinery. The exam will use this without warning, so make sure the interaction interpretation is automatic.

---

## 3. The Incinerator Setup (the difference estimator)

Now the causal inference framing. Pulling straight from the lecture:

A garbage incinerator was built in North Andover, MA. We want the effect of being near it on housing prices.

**First attempt — difference estimator** (only 1981 data, after construction):

```
rprice_i = β0 + β1·nearinc_i + u_i
```

Stata gives β1 = -$30,688. Houses near the incinerator are $30K cheaper.

**The problem:** Where do incinerators get built? In neighborhoods that were already cheap. So β1 is picking up two things: the actual incinerator effect AND pre-existing neighborhood quality differences. **OVB.**

This is the natural experiment framing:
- **Experiment** = RCT, randomly assigned by researchers
- **Natural / quasi-experiment** = variation that *looks* random but wasn't designed by anyone (proximity to an unexpectedly built incinerator)

The exam may ask whether something qualifies as a natural experiment. The test: is the variation *as good as random* with respect to outcomes? Here, no — the incinerator location was probably correlated with low-value land.

---

## 4. The DnD Estimator (the core idea)

The fix: get pre-treatment data (1978, before the incinerator was built) and compare the pre-post change in the treatment group to the pre-post change in the control group.

### The 2x2 Table (memorize this)

|  | 1978 (pre) | 1981 (post) | Difference (post - pre) |
|---|---|---|---|
| Far from incinerator | β0 | β0 + δ0 | δ0 |
| Near incinerator | β0 + β1 | β0 + δ0 + β1 + δ1 | δ0 + δ1 |
| Difference (near - far) | β1 | β1 + δ1 | **δ1** |

**The DnD estimator is the bottom-right cell: δ1.** It's literally the difference of two differences.

### The Two Equivalent Interpretations

**Path 1 (across rows then columns):** Take the near-far difference in each year, then subtract those differences across years.
- 1978: near - far = β1 (pre-existing neighborhood gap)
- 1981: near - far = β1 + δ1 (gap, plus incinerator effect)
- Subtracting: δ1 ← isolates the incinerator effect

**Path 2 (across columns then rows):** Take the time trend in each group, then subtract.
- Far group time trend = δ0 (what would have happened anyway)
- Near group time trend = δ0 + δ1 (anyway + incinerator)
- Subtracting: δ1 ← isolates the incinerator effect

Both paths give δ1. This is why the method is called *difference in differences* and why it works either way.

### The Numbers from the Lecture

|  | 1978 | 1981 | Difference |
|---|---|---|---|
| Far | $82,517 | $101,308 | $18,791 |
| Near | $63,693 | $70,619 | $6,926 |
| Difference | -$18,824 | -$30,688 | **-$11,864** |

The single-difference estimate (-$30,688) overstated the damage because it included the pre-existing $18,824 gap. The DnD estimate (-$11,864) is the *causal* impact of the incinerator alone.

---

## 5. The Single-Regression DnD Model (THIS is what the exam tests)

Running two separate regressions and computing differences manually is fine for intuition, but for hypothesis testing you want one regression. The DnD model:

```
rprice_it = β0 + δ0·yr1981_t + β1·nearinc_t + δ1·nearinc_it·yr1981_t + u_it
```

**Interpreting each coefficient — this is exam gold:**

| Coefficient | What it captures |
|---|---|
| β0 | Baseline: avg price for far-from-incinerator houses in 1978 |
| δ0 | Time trend in the control group (price change in far houses 1978→1981) |
| β1 | Pre-treatment level difference (near vs far in 1978, neighborhood quality) |
| **δ1** | **The DnD estimator — causal effect of the incinerator** |

Why this works: the interaction coefficient picks up the *extra* change in the treatment group beyond what happened to the control group. That's exactly what we want.

### Stata Output from the Lecture

```
nearinc       -18824.37    (β1: pre-existing neighborhood gap)
y81            18790.29    (δ0: time trend everywhere)
nearincXy81   -11863.90    (δ1: DnD estimate, p = 0.170)
_cons          82517.23    (β0: baseline)
```

The DnD estimate is -$11,864 with p = 0.170. Not statistically significant at conventional levels. So while the *point estimate* says the incinerator hurt prices, we can't reject the null of zero effect. **Notice: this contradicts the simple difference estimate, which was wildly significant. That's the OVB getting cleaned up.**

### Direct Mapping to MC Q6

The sample exam writes this exact model with generic labels:
```
outcome_it = β0 + β1·treat_i + β2·after_t + β3·treat_i·after_t + u_it
```

The DnD estimator is **β3**. Map: `β1 ↔ nearinc coeff`, `β2 ↔ y81 coeff`, `β3 ↔ interaction coeff`. The interaction is always the DnD coefficient because it's the only term that activates only for the treated group AFTER treatment — exactly the cell you need to isolate.

---

## 6. Vocabulary You Need to Have Cold

These appear in MC questions and short-answer prompts:

- **Treatment variable:** the dummy for `after` (when treatment started). In the incinerator model, this is `yr1981`.
- **Treated group:** the dummy for `near`/`treat`. The group that *would* be affected when treatment kicks in.
- **Control group:** the untreated group. Provides the counterfactual time trend.
- **Program evaluation / causal inference:** general name for what DnD does.
- **DnD estimator:** the coefficient on the interaction, period.

If a question asks "which coefficient gives the average treatment effect," it's asking for the interaction coefficient. Always.

---

## 7. Panel Data DnD (the Michigan cigarette tax example)

Same machinery, panel data. In 1993 Michigan raised cigarette tax from 25¢ to 75¢. Question: did it reduce smoking during pregnancy?

```
gen mi = (state==26)              // treatment GROUP dummy
gen hike = (month >= 33)          // treatment TIME dummy
gen miXhike = mi * hike           // interaction = DnD estimator

regress smoked mi hike miXhike, robust
```

Mapping the sample-exam framing:
- `mi` = the `treat` dummy (defines the group)
- `hike` = the `after` dummy (defines the time)
- `miXhike` = the DnD coefficient — this is the average treatment effect

**Without controls:** miXhike = -0.0052, p = 0.373. Not significant.

**With controls (married, black, other):** miXhike = -0.0145, p = 0.010. Significant.

### Why controls matter here

If demographic mix differs between MI and the control states AND demographics correlate with smoking, you have the same OVB issue we keep hitting. Controls absorb that variation, leaving the cleaner treatment effect. This is a useful pattern to remember: *DnD doesn't always rule out OVB by itself — sometimes you still need controls for time-varying confounders within groups.*

This is the bridge to Lec 18, which is all about what can still go wrong with DnD.

---

## 8. Quick-Recall Summary (the night-before version)

- **DnD = difference of two differences = isolates causal effect by netting out the control-group time trend from the treatment-group time trend.**
- **Single-regression form:** `Y = β0 + δ0·after + β1·treat + δ1·(treat·after) + u`. The interaction coefficient is the DnD estimator. Always.
- **Works with:** repeated cross-section OR panel data (MC Q7 → D).
- **What each coefficient does:** intercept = baseline control pre-treatment. `after` coeff = time trend in control. `treat` coeff = pre-existing group gap. Interaction = causal effect.
- **The 2x2 table is your friend.** If you can fill it in with parameters, you can derive everything.
- **What it fixes vs simple difference:** the simple `regress Y on treat` confounds treatment effect with pre-existing group differences (OVB). DnD subtracts off the pre-existing gap using the pre-period.
- **What it doesn't fix:** parallel trends violations, spillovers, anticipation, reverse causality. That's Lec 18.

---

## 9. FRQ Practice — How to Write This on the Exam

**Hypothetical short-answer prompt:**
> A researcher estimates `Y_it = β0 + δ0·post_t + β1·treat_i + δ1·(treat·post)_it + u_it` and gets δ1_hat = 4.2 with SE = 1.8. Interpret δ1, then test whether the treatment had any effect.

**Strong answer structure:**

> **Interpretation.** δ1 is the difference-in-differences estimator. It captures the average change in Y for the treated group from pre to post period, *net of* the change experienced by the control group over the same period. Estimated effect: treatment is associated with a 4.2-unit increase in Y, beyond what would have happened in the absence of treatment.
>
> **Hypothesis test.** Null: H0: δ1 = 0 (no treatment effect). Alternative: H1: δ1 ≠ 0.
> Test statistic: t = 4.2 / 1.8 ≈ 2.33. Compare to critical value ~1.96 at 5% significance. |t| > 1.96, so reject H0.
> **Conclusion:** there is statistically significant evidence at the 5% level that the treatment affected Y. The point estimate suggests treatment increases Y by 4.2 units on average.

The pattern: state what the coefficient *is* (DnD estimator, with interpretation). Then mechanical hypothesis test. Then substantive conclusion. Don't skip the substantive part.
