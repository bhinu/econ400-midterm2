# Lecture 24: Applications of RD (What Can Go Wrong)

**Sample midterm hits:** MC Q10 (key RD assumption, same as Lec 23). Lec 24 is the deeper dive into when RD breaks and how to spot it.

---

## The setup (one-line refresher)

RD identifies a treatment effect by fitting a regression on each side of a threshold and reading off the **vertical jump at the cutoff**. Key assumption: things change *gradually* across the running variable, but treatment status changes *suddenly* at the cutoff.

Lec 24 is about four ways this assumption gets violated or messy in practice:

1. **Sorting** (people game the running variable)
2. **Functional form** (your line shape changes the answer)
3. **LATE** (estimate is local to the cutoff, doesn't generalize)
4. **Fuzzy compliance** (treatment doesn't go cleanly from 0 to 1)

---

## Issue #1: Sorting (are units similar aside from treatment?)

**Sorting** = behavioral response where people manipulate their value of the running variable to land on the favorable side. If sorting happens, units just above and just below the cutoff are no longer comparable, and unobservables (motivation, info, ability to game paperwork) jump at the cutoff right alongside treatment. Design dies.

**Two checks:**

**Check A: Density of the running variable around the cutoff** (this is the McCrary density test from Lec 23).
- Smooth distribution → fine.
- Spike just on the favorable side → red flag.

Lecture's example: Conover & Camacho (2011), Colombia poverty index.
- 1996 (rule not yet public): smooth histogram. You couldn't guess where the cutoff was.
- 1997+ (rule public): visible pile-up just below the eligibility cutoff. Households learned the rule and underreported income to qualify.

**Check B: Balance of pre-determined covariates at the cutoff.**
Run a t-test or RD on baseline characteristics that *shouldn't* be affected by treatment. If they jump at the cutoff, units aren't comparable.

Lecture's class-size example:
- t-test of `perc_disadvantaged` for enrollment 40 vs 41: t = 0.30, p = 0.77. Fail to reject.
- RD on `perc_disadvantaged` as outcome: lines line up at cutoff.
- Conclusion: no evidence of sorting.

**When sorting is plausible vs not:**
- Plausible: cutoff is public, individual can manipulate the running variable, has incentive to cross it.
- Implausible: cutoff is something the individual can't control (birth date, exact integer enrollment).

---

## Issue #2: Functional Form

The estimated jump depends on the line you fit on each side. Linear vs quadratic vs cubic can give wildly different answers on the same data. The lecture showed exactly this: linear fit suggested a huge program effect, quadratic fit on the same data showed almost nothing.

**How to deal:**

1. **Plot the data.** Always. "A picture is worth a thousand words." Show a binned scatter with the regression overlaid. If the bins clearly trace a curve and your line is straight, you have a problem.
2. **Try multiple specs.** Linear, quadratic, sometimes cubic. Report whether the answer is robust.
3. **Use local polynomial regression with a bandwidth.** This is what `rdrobust` does. Fits a flexible polynomial only near the cutoff so your global curvature stops contaminating the local jump.
4. **Bandwidth tradeoff** (same as Lec 23): wide → more data, more bias. Narrow → cleaner, more variance. CCT picks one for you.

**Reading `rdrobust` output (the class-size table from the slides):**
- `c(41)`: cutoff
- `p(2)`: quadratic local polynomial
- `BW type CCT`: data-driven bandwidth
- `Kernel = Triangular`: down-weights observations far from cutoff
- `Conventional` row: standard estimate + SE + p-value
- `Robust` row: bias-corrected p-value, this is the one you report

Class-size example with quadratic: coef = 3.47, SE = 2.92, robust p = 0.368. Fail to reject zero effect.

---

## Issue #3: Local Average Treatment Effect (LATE)

RD only uses observations near the cutoff. So the estimate is local: it tells you the effect at *that* point on the running variable, not anywhere else.

**Class-size example:**
- The Maimonides Rule splits a class once enrollment exceeds 40.
- So the "treatment" the RD identifies is going from one class of ~40 to two classes of ~20.
- That tells you nothing about going from 20 to 10, or from 30 to 15.

**Internal vs external validity:**
- Internal validity: does the estimate correctly identify the causal effect for the population near the cutoff? RD is strong here.
- External validity: does it generalize to other populations or other doses? RD is weak here.

**Exam shortcut:** if a question asks "what's the limitation of RD even when assumptions hold," the answer is LATE / external validity.

---

## Issue #4: Compliance (Sharp vs Fuzzy)

(You saw this in Lec 23 too. Lec 24 puts a formula on it.)

- **Sharp RD:** treatment probability goes 0 → 100% at the cutoff. The outcome jump = LATE.
- **Fuzzy RD:** probability jumps but not all the way. The outcome jump = **Local ITT**, which understates the LATE because not everyone complied.

**Same logic as RCT with non-compliance:**

$$\text{ATE} = \frac{\text{ITT}}{\text{Compliance Rate}}$$

In fuzzy RD:

$$\text{LATE} = \frac{\text{Local ITT}}{\text{Change in participation at the discontinuity}}$$

**Worked example from the slides:**
- Eligibility raises participation by 50 pp (not 100).
- Food spending jumps $50 at the cutoff.
- LATE = $50 / 0.50 = **$100**.

The $50 ITT understated the true effect because half the eligible people didn't actually participate.

**For fuzzy RD you need two graphs:**
1. Participation vs running variable (does treatment jump at the cutoff?)
2. Outcome vs running variable (does the outcome jump at the cutoff?)

---

## The shingles vaccine study (Eyting et al., 2025)

Capstone example. This is exactly the kind of study you should be able to walk through on an FRQ.

**Setup:**
- Wales offered the shingles (zoster) vaccine to people **born on or after Sept 2, 1933**. Those born before were ineligible.
- Running variable: week of birth relative to Sept 2, 1933.
- Cutoff: week 0.
- Treatment: vaccine eligibility.

**Why this is clean:**
- Birth date in 1933 cannot be manipulated retroactively, so sorting is impossible.
- No other major policy uses this exact birthday cutoff.

**The plots they showed:**

1. **First stage** (vaccine uptake): jumps from ~0% to ~47% at the cutoff. Coef = 47.2 pp. **Fuzzy RD** because uptake is 47%, not 100%.
2. **Balance / placebo checks**: no jump at the cutoff for past shingles, past dementia, past flu vaccine, past PPV uptake, past statin use. All confirm comparability.
3. **Reduced form on shingles**: eligibility cuts shingles diagnosis by 1.0 pp (p = 0.01). This is the Local ITT.
4. **Reduced form on dementia** (the headline): eligibility cuts dementia by 1.3 pp (p = 0.022). Local ITT.
5. **By sex**: women −2.9 pp (p = 0.001), men 0.1 pp (p = 0.93). Concentrated in women.

**Implied LATE on dementia:**

LATE = Local ITT / first stage = −1.3 / 0.472 ≈ **−2.75 pp**.

So among people who actually got vaccinated because of being eligible, dementia probability fell by about 2.75 pp over 7 years.

---

## Quick exam-prep checklist for Lec 24

- [ ] Can I list the four threats to RD validity?
- [ ] Can I describe the two checks for sorting (density + covariate balance)?
- [ ] Can I explain why functional form sensitivity matters and how to address it?
- [ ] Can I explain what "local" in LATE means and why it limits external validity?
- [ ] Can I distinguish sharp from fuzzy RD and write the formula relating LATE, Local ITT, and the first-stage jump?
- [ ] Can I compute a fuzzy RD LATE given numerical inputs?
- [ ] Can I walk through the shingles study end-to-end (running variable, cutoff, sorting, first stage, reduced form, LATE)?

---

# Practice Questions: Lecture 24

## Multiple Choice

**1.** Sorting in an RD design refers to:
A) Ranking observations by the running variable before estimation
B) Dropping outliers from the sample
C) Behavioral manipulation of the running variable to land on the favorable side of the cutoff
D) Using a triangular kernel near the cutoff

<details><summary>Answer</summary>

**C.** Sorting is the threat to the RD identifying assumption: people gaming the running variable.
</details>

**2.** Which is the best evidence that sorting is NOT a problem?
A) Large sample size
B) Continuous running variable
C) Smooth density of the running variable across the cutoff AND no jumps in baseline covariates at the cutoff
D) Statistically significant treatment effect

<details><summary>Answer</summary>

**C.** The two standard checks: smooth density (McCrary-style) plus balanced baseline covariates at the cutoff.
</details>

**3.** A linear RD spec gives a large, significant effect. A quadratic spec on the same data gives a small, insignificant effect. The right interpretation is:
A) Linear is always preferred for simplicity
B) Quadratic is always preferred for flexibility
C) The result is sensitive to functional form, which is a real concern
D) The discontinuity doesn't exist

<details><summary>Answer</summary>

**C.** This is Issue #2 in textbook form. Neither spec is automatically right; the takeaway is that the result is fragile and you should plot the data plus try local polynomials.
</details>

**4.** "Local" in Local Average Treatment Effect refers to:
A) The geographic location of the study
B) That the effect is identified only near the cutoff value of the running variable
C) The use of local polynomial regression
D) Small sample size

<details><summary>Answer</summary>

**B.** Local = near the cutoff. This is the external validity caveat.
</details>

**5.** In a fuzzy RD, the LATE is recovered by:
A) The jump in the outcome at the cutoff
B) The jump in the outcome divided by the jump in participation, both at the cutoff
C) The jump in participation at the cutoff
D) Treatment minus control means

<details><summary>Answer</summary>

**B.** Same logic as ATE = ITT / compliance rate from RCTs. Local ITT divided by first-stage jump.
</details>

**6.** Sorting is most likely a serious threat when:
A) The cutoff is exact birth date
B) The cutoff is randomly assigned
C) The cutoff is publicly known and people can manipulate the running variable to qualify
D) The cutoff is determined by a lottery number

<details><summary>Answer</summary>

**C.** Public + manipulable + incentive = textbook sorting. This is the Conover & Camacho Colombia setup.
</details>

**7.** In the shingles vaccine study, the running variable was:
A) Vaccination status
B) Probability of dementia
C) Week of birth relative to Sept 2, 1933
D) Age at study time

<details><summary>Answer</summary>

**C.** The eligibility rule used birth date as the cutoff, so birth date is the running variable.
</details>

**8.** The first-stage jump in vaccine uptake in the shingles study was about 47 pp (not 100). This means:
A) The design fails because compliance is imperfect
B) It's a fuzzy RD; the LATE is recovered by scaling the reduced form by the first-stage jump
C) The dementia estimate is biased and uninterpretable
D) The running variable was poorly chosen

<details><summary>Answer</summary>

**B.** Fuzzy RD is fine. Scale the reduced form by the first stage to get the LATE.
</details>

**9.** What is the standard visual check for sorting?
A) Scatter of outcome vs running variable
B) Density / histogram of the running variable around the cutoff
C) Residual plot
D) Q-Q plot of the dependent variable

<details><summary>Answer</summary>

**B.** Density of the running variable. A spike on the favorable side is the smoking gun.
</details>

**10.** RD estimates a "local" treatment effect because:
A) The data is restricted to a single region
B) Only observations near the cutoff identify the jump
C) Only locally-funded programs qualify
D) RD requires a small sample

<details><summary>Answer</summary>

**B.** Identification comes from observations near the cutoff. The estimate doesn't speak to people far from it.
</details>

---

## Short Answer

**FRQ 1. Sorting diagnosis.** A program offers cash transfers to households with a poverty index below 50. The cutoff became public in 1997. You have data from 1996 to 1999.

a) What pattern would make you suspect sorting?
b) Why does sorting break the RD assumption?
c) Suggest one check that doesn't use the histogram of the running variable.

<details><summary>Model answer</summary>

a) Plot a histogram of the poverty index for each year and look at the shape near 50. Under no sorting, the density should be smooth, you couldn't guess where the cutoff is from the picture alone. Sorting shows up as a pile-up just below 50 (eligible side) and/or a dip just above. Lecture's example: 1996 looks smooth, 1997+ shows a clear spike below the cutoff once households learned the rule.

b) The RD assumption requires that everything other than treatment changes only gradually across the running variable. If households manipulate their reported score to qualify, then unobserved characteristics (information access, motivation, willingness to game the system) also change discontinuously at the cutoff. Units just above and just below are no longer comparable, and the estimated jump in the outcome conflates the program effect with selection on these unobservables.

c) Balance check on baseline covariates. Run a t-test or RD on pre-determined characteristics (household head age, education, baseline assets) just above vs just below the cutoff. If anything jumps, the two groups aren't comparable.
</details>

**FRQ 2. Functional form.** A researcher reports a large RD effect using a linear spec. With a quadratic spec on the same data, the effect halves and loses significance.

a) Explain in plain English what's happening.
b) What single piece of evidence would help decide which spec is more credible?

<details><summary>Model answer</summary>

a) The estimated jump depends on the line you fit. A linear fit forces straight lines on each side, which can mechanically create a "jump" at the cutoff if the true relationship is curved. A quadratic absorbs the curvature into the polynomial instead of attributing it to a discontinuity. The fact that the answer changes substantially across specs means the result is being driven by functional-form assumptions, not by a clean discontinuity.

b) A binned scatter plot of the outcome vs running variable with both fits overlaid. If the bins clearly trace a curve, the linear fit is misleading. If the bins look approximately linear on each side, the quadratic is over-fitting noise. A second useful step is re-estimating with a narrow-bandwidth local polynomial (e.g. `rdrobust`), which relies less on global functional-form assumptions.
</details>

**FRQ 3. LATE / external validity.** The Maimonides Rule splits Israeli classes once enrollment exceeds 40, so a school with 41 students has two classes of about 20 and a school with 40 has one class of 40.

a) What is the "treatment" the RD identifies? Be precise.
b) Why is this a Local ATE rather than just an ATE?
c) A policymaker wants to use this estimate to predict the effect of cutting all classes from 25 to 12. Reasonable?

<details><summary>Model answer</summary>

a) The treatment is going from one class of about 40 to two classes of about 20. Equivalently, halving class size from 40 to 20. The RD does not identify the effect at any other margin.

b) RD only uses observations near enrollment = 41 to identify the jump. The estimate applies to schools that happen to be near that cutoff, at the 40-to-20 margin. Schools far from that enrollment, or different class-size changes (e.g. 20 to 10), might respond very differently. The estimate is "local" to the cutoff value.

c) No, this is the external validity problem with LATE. The RD estimate is identified at the 40-to-20 margin in schools near enrollment 41. Going from 25 to 12 is a different baseline class size in different schools, and the marginal benefit of reducing class size is plausibly non-linear. The RD doesn't speak to the second margin at all.
</details>

**FRQ 4. Fuzzy RD numerical.** A program is offered to households below an income threshold of $30,000. At the cutoff:

- Just below: 80% participate, average food spending is $480.
- Just above: 20% participate (some find loopholes), average food spending is $420.

a) Sharp or fuzzy? Why?
b) Compute the Local ITT for food spending.
c) Compute the LATE.
d) Why is the LATE larger than the Local ITT?

<details><summary>Model answer</summary>

a) Fuzzy. Sharp RD requires participation to jump from 0 to 100% at the cutoff. Here it jumps from 20% to 80%, a discontinuity but not a complete one. Some eligible households opt out and some ineligible ones get in.

b) Local ITT = jump in outcome at cutoff = $480 − $420 = **$60**. This is the effect of being eligible regardless of whether the household actually participated.

c) LATE = Local ITT / change in participation = $60 / (0.80 − 0.20) = $60 / 0.60 = **$100**. Among households whose participation was actually moved by being on the eligible side (compliers), the program raises monthly food spending by about $100.

d) The Local ITT averages over everyone near the cutoff, including always-takers and never-takers whose participation didn't change. They contribute zero to the participation jump but still get averaged into the outcome, diluting it. The LATE strips out that dilution by scaling up by the share of compliers, giving the effect on the people who actually got treatment because of the rule.
</details>

**FRQ 5. Shingles study walkthrough.** In Wales, the shingles vaccine became available in 2013 to people born on or after Sept 2, 1933. Researchers used this rule to study effects on dementia over 7 years.

a) Identify the running variable, cutoff, and treatment.
b) Why is sorting essentially impossible here?
c) The researchers showed that past shingles, past dementia, past flu vaccine uptake, past PPV uptake, and past statin use did NOT jump at the cutoff. What's the purpose of these plots?
d) The first-stage jump in vaccine uptake was 47 pp and the reduced-form effect on dementia was −1.3 pp. Compute the implied LATE and interpret.

<details><summary>Model answer</summary>

a) Running variable: date of birth (often expressed as week of birth relative to Sept 2, 1933). Cutoff: Sept 2, 1933 (week 0). Treatment: eligibility for the shingles vaccine.

b) Birth date in 1933 was determined decades before the policy existed. No one in 2013 could manipulate when they were born to qualify, and the policy was unknowable in 1933 so anticipatory manipulation is impossible too. The mechanical impossibility of sorting is what makes birthday-based RDs especially clean.

c) Balance / placebo checks. They test whether the populations just above and just below the cutoff are comparable on observable characteristics measured before the program rolled out. If any of these jumped at the cutoff, that would suggest either sorting or another policy using the same birthday cutoff, threatening identification. The fact that all are smooth provides strong evidence that the two groups are comparable on baseline health and healthcare-seeking behavior.

d) LATE = Local ITT / first stage = −1.3 / 0.472 ≈ **−2.75 pp**. Among those who actually got vaccinated because of being eligible (compliers), the probability of a new dementia diagnosis over the 7-year follow-up fell by about 2.75 percentage points. The reduced-form estimate of −1.3 pp understates the true effect of the vaccine itself because only 47% of eligible people actually got vaccinated.
</details>

**FRQ 6. RD vs DnD comparison.** You want to estimate the effect of attending a high-quality preschool on test scores at age 10. Admission is determined by an index score with a publicly-known cutoff: 70% of applicants above the cutoff enroll, 10% below find ways in. Compare RD with a DnD design that compares admitted vs non-admitted students before and after preschool.

<details><summary>Model answer</summary>

**RD (fuzzy):** Cutoff in the index score creates a jump in enrollment of 70% − 10% = 60 pp. Estimate the reduced-form jump in test scores at the cutoff and divide by 0.60 to get the LATE.

**RD strengths over DnD:** RD only requires that observations near the cutoff are similar in the absence of treatment. DnD requires parallel trends, that admitted and non-admitted students would have followed the same time path absent the program. If the index ranks by ability or family resources, those two groups have systematically different trajectories and parallel trends almost certainly fails.

**DnD strengths over RD:** DnD identifies an effect for the whole admitted population, not just the marginal applicants near the cutoff. If you care about the program's overall effect rather than the effect on borderline kids, DnD answers a more policy-relevant question, but only if parallel trends are credible, which they likely aren't here.

**Sorting concern under RD:** the cutoff is public and applicants might know their score, creating risk of manipulation through reapplication, portfolio gaming, etc. Check density of the index and balance of baseline covariates (parental education, income, prior cognitive measures) at the cutoff before trusting the design.

**Bottom line:** RD is much more credible for causal identification here. DnD is plausible only with strong reasons to believe parallel trends.
</details>
