# Lecture 21: Instrumental Variables (IV)

**Sample midterm hits:** MC Q9 (weak instrument), Short Answer Q3 (IV/2SLS, distance to college as instrument, relevance test)

---

## The problem IV solves

You want the causal effect of x on y:
```
y = β0 + β1*x + u
```

But you suspect `cov(x, u) ≠ 0` because the error contains an omitted variable that's correlated with x. Classic example: returns to education.
```
wage = β0 + β1*educ + u
```
The error u contains ability. Ability is correlated with educ (smarter people get more schooling). So OLS β1 is biased.

We've already seen 3 fixes: RCT, DnD, fixed effects. Each has limits. IV is the 4th tool.

---

## What an instrument is

An instrument z is a variable that satisfies two conditions:

| Condition | Math | Plain English |
|-----------|------|---------------|
| **Relevant** | `cov(z, x) ≠ 0` | z is correlated with the endogenous regressor |
| **Valid** (aka exclusion restriction, aka exogenous) | `cov(z, u) = 0` | z is uncorrelated with the error |

Both must hold. Either one fails, IV breaks.

**Classic exam question:** "Is z a good instrument?" The answer ALWAYS discusses both relevance AND validity. Never just one.

---

## Walking through the returns-to-education example

`wage = β0 + β1*educ + u`, where u contains ability.

| Proposed instrument z | Relevant? | Valid? | Verdict |
|----------------------|-----------|--------|---------|
| Last digit of SSN | No (random) | Yes (random) | Fails relevance |
| IQ test score | Yes | No (IQ is ability, which is in u) | Fails validity |
| Mother's education | Yes (educated parents → educated kids) | Maybe (could correlate with ability genetically or with parenting investments) | Debatable |
| Number of siblings | Yes (more sibs → less per-kid investment → less educ) | Maybe (could correlate with parenting time, which affects wages) | Debatable |

**Trick to rescue a "maybe valid" instrument:** if you worry z correlates with omitted variable W, just **add W as a control**. Now W is no longer in the error term, so the new error doesn't contain W, so z might be uncorrelated with the new error.

```
wage = β0 + β1*educ + β2*(parental time) + u
```
Now "number of siblings" might be a valid instrument because parental time isn't in the error anymore.

---

## Vocabulary that shows up on exams

| Variable | In model? | Correlated with error? | Name |
|----------|-----------|------------------------|------|
| educ | Yes | Yes | **Endogenous regressor** |
| parental time | Yes | No | **Exogenous regressor** (control) |
| number of siblings | No | No | **Instrument** |

Note: the instrument is NOT in the structural model. It only enters through the first stage.

---

## Testing relevance (you CAN test this)

Run the first-stage regression:
```
x = α0 + α1*z + v
```
Test:
- H0: α1 = 0 (z is irrelevant)
- H1: α1 ≠ 0 (z is relevant)

**Reject H0** → cov(z,x) ≠ 0 → z is relevant. ✓
**Fail to reject** → can't conclude relevance → look for a different instrument.

Rule of thumb: F-stat on the excluded instrument(s) > 10 in the first stage. Below that, you have a **weak instrument** (more on this in Lec 22).

**Sample midterm Q3d connection:** "Can you test whether distance to nearest college is a relevant instrument?" YES. Look at the first stage output. The reported coefficient on distcol is -0.07 with SE 0.01, so t ≈ -7. Reject H0 that α1=0. Distance is a relevant instrument.

---

## Testing validity (you CAN'T directly test this)

Why not? Because the error u is unobserved. You can't run `u = β0 + β1*z + v` because you don't have u.

You can sometimes get **indirect** evidence (over-identification test, covered in Lec 22 if you have multiple instruments), but in general, validity is a judgment call. This is why IV always involves a **leap of faith**.

---

## 2SLS mechanics (the actual estimation)

The intuition: x has good variation (uncorrelated with u) and bad variation (correlated with u). Use z to extract just the good part.

### Stage 1: regress endogenous regressor on instrument
```
x = α0 + α1*z + v
```
Pull out the predicted values:
```
x̂ = α̂0 + α̂1*z
```
By assumption (validity), x̂ is NOT correlated with u. The residual v̂ is the bad variation; toss it.

### Stage 2: replace x with x̂ in the structural equation
```
y = β0 + β1*x̂ + u
```
Run OLS on this. The β1 you get is the IV estimate.

Why this works: cov(x̂, u) = 0, so the Stage 2 regression is unbiased.

### Stata syntax
```
ivregress 2sls wage (educ=sibs), first robust
```
- `wage` is y
- `(educ=sibs)` says "instrument educ with sibs"
- `first` shows the first stage output
- `robust` for heteroskedasticity-robust SEs

---

## Reduced form

Skip 2SLS entirely. Just regress y directly on z:
```
y = π0 + π1*z + error
```
This tells you the total effect of z on y, which under IV assumptions only operates THROUGH x. The variable being instrumented (x) is not in the reduced form.

Reduced form is sometimes more useful when you don't care about the magnitude of β1, just whether the policy lever (z) moves the outcome (y).

---

## How to write up an FRQ on IV (exam format)

Sample midterm Q3 essentially asks this. Template answer for "is z a good instrument":

> "For z to be a good instrument, it must be (1) relevant — cov(z,x) ≠ 0 — and (2) valid — cov(z,u) = 0.
>
> **Relevance:** [Argue why z is or isn't related to x, with a mechanism. e.g., 'Distance to college raises the cost of attending, so people farther away should get less education.']
>
> **Validity:** [Argue why z is or isn't related to omitted variables. e.g., 'Distance to college may be correlated with rural residence, which could affect wages directly through local labor markets, not just through education. So validity is questionable.']
>
> Both must hold for IV to deliver an unbiased estimate of β1."

---

## Bias direction reasoning (Sample Q3a, Q3c)

Sample midterm Q3a asks for OVB direction in `lwage = β0 + β1*educ + u`.

Standard story: omitted variable is **ability**.
- cov(ability, educ) > 0 (smarter people get more school)
- cov(ability, lwage) > 0 (smarter people earn more)
- Product is positive → OVB is **upward** → OLS β1 is biased UP.

Sample Q3c then asks: comparing OLS (0.09) and 2SLS (0.06), was OLS biased upward as predicted? YES — OLS is larger than 2SLS, consistent with upward bias from ability.

(Caveat: this assumes distance to college is a valid instrument. If it isn't, 2SLS is also biased.)

---

## Quick exam-prep checklist for Lec 21

- [ ] Can I write down the two IV assumptions in math AND in words?
- [ ] Can I explain why validity isn't directly testable?
- [ ] Can I run a relevance test from first-stage output (just a t-test on α1)?
- [ ] Can I describe Stage 1 and Stage 2 of 2SLS in one sentence each?
- [ ] Can I distinguish endogenous regressor, exogenous regressor, and instrument?
- [ ] Can I sign OVB given a story about an omitted variable?
- [ ] Can I compare OLS vs IV estimates and say whether the OVB direction was as expected?

---

# Practice Questions — Lecture 21

## Multiple Choice

**1.** The exclusion restriction states that:
A) cov(z, x) ≠ 0
B) cov(z, u) = 0
C) cov(x, u) = 0
D) cov(z, y) = 0

<details><summary>Answer</summary>

**B.** Exclusion restriction = validity = "instrument uncorrelated with error." A is the relevance condition. C is what would make OLS unbiased (not what IV requires of z). D is unrelated.
</details>

**2.** Which is NOT a synonym for the validity assumption?
A) Exogeneity
B) Exclusion restriction
C) Relevance
D) cov(z, u) = 0

<details><summary>Answer</summary>

**C.** Relevance is the OTHER assumption (z correlated with x). Validity has three names: exogeneity, exclusion restriction, and the math version cov(z,u)=0.
</details>

**3.** In 2SLS, the first stage regresses:
A) y on z
B) y on x
C) x on z
D) y on x̂

<details><summary>Answer</summary>

**C.** First stage extracts the "good variation" in x using z. Second stage is then y on x̂.
</details>

**4.** The reduced form regression is:
A) y on x
B) y on z
C) x on z
D) y on x̂

<details><summary>Answer</summary>

**B.** Reduced form skips x entirely and looks at the direct relationship between z and y. Useful when you care about whether the policy lever moves the outcome, not the magnitude of β1.
</details>

**5.** We cannot directly test the validity assumption because:
A) The error term u is unobserved
B) The instrument z is unobserved
C) The relevance assumption must be tested first
D) Stata doesn't have a command for it

<details><summary>Answer</summary>

**A.** You'd need to regress u on z, but u is never observed (only the residual from a potentially biased regression). Validity is fundamentally untestable with a single instrument.
</details>

**6.** A regressor that's in the model and uncorrelated with the error is called:
A) An endogenous regressor
B) An exogenous regressor
C) An instrument
D) A reduced form variable

<details><summary>Answer</summary>

**B.** Endogenous = in model, correlated with error. Exogenous = in model, uncorrelated with error (a control). Instrument = NOT in the structural model, uncorrelated with error, correlated with x.
</details>

**7.** After Stage 1 of 2SLS, the residual v̂ represents:
A) Variation in x correlated with u (the "bad" variation, discarded)
B) Variation in x uncorrelated with u (the "good" variation, kept)
C) Variation in y unexplained by x
D) Variation in z unexplained by x

<details><summary>Answer</summary>

**A.** Fitted values x̂ are the good variation (uncorrelated with u by validity). Residuals v̂ are the leftover, including the part correlated with u. Toss them.
</details>

**8.** If z is a valid AND relevant instrument, then:
A) cov(x̂, u) = 0
B) cov(x̂, u) ≠ 0
C) cov(x, u) = 0
D) cov(z, x) = 0

<details><summary>Answer</summary>

**A.** Validity says cov(z,u)=0. Since x̂ = α̂0 + α̂1*z is just a linear function of z, cov(x̂, u) = α̂1 * cov(z, u) = 0. This is what makes Stage 2 unbiased.
</details>

## Short Answer

**FRQ 1.** You want to estimate the effect of children on women's labor force participation:
`LFP = β0 + β1*kids + u`. Someone proposes "first two children are same sex" (a dummy) as an instrument.

a) What does relevance require? Is it plausible?
b) What does validity require? Is it plausible?
c) Describe how you'd estimate β1 using 2SLS.

<details><summary>Model answer</summary>

a) Relevance: cov(samesex, kids) ≠ 0. Plausible. Parents with two same-sex kids may try for a third (preference for variety). Testable: regress kids on samesex; t-test the coefficient.

b) Validity: cov(samesex, u) = 0. Plausible because the sex of children is essentially a biological coin flip — uncorrelated with the mother's career preferences, education, or anything else in u. This is Angrist & Evans (1998).

c) Stage 1: regress kids on samesex, save predicted values k̂. Stage 2: regress LFP on k̂. Coefficient on k̂ is the IV estimate. Stata: `ivregress 2sls LFP (kids = samesex), robust`.
</details>

**FRQ 2.** Estimating `wage = β0 + β1*experience + u`, you worry the error contains "job-market savvy."

a) Sign the OVB.
b) Someone proposes "distance from home to nearest professional sports stadium" as an instrument. Discuss relevance and validity.

<details><summary>Model answer</summary>

a) cov(savvy, wage) > 0 (savvy → higher wages). cov(savvy, experience) plausibly ≥ 0 (savvy people stay employed). Product positive → upward bias, OLS β1 too high.

b) Relevance: implausible. No clear mechanism linking stadium distance to work experience. Probably cov(z, x) ≈ 0 — irrelevant. Validity: also questionable. Stadiums cluster in big cities, so distance correlates with urban/rural status, which affects wages through local labor markets. Both assumptions fail. Bad instrument.
</details>

**FRQ 3.** First-stage regression: `educ = 14.0 − 0.05*distcol`, robust SE = 0.02 on distcol, n = 2000. Test relevance.

<details><summary>Model answer</summary>

H0: α1 = 0 (irrelevant), H1: α1 ≠ 0 (relevant)

t = −0.05 / 0.02 = −2.5. With n=2000, critical value at 5% ≈ 1.96. |t| = 2.5 > 1.96 → reject H0.

Conclusion: distance is significantly correlated with education. The instrument is relevant.

(Note: t=2.5 → F ≈ 6.25, which is below the rule-of-thumb F>10 for strong instruments. So z is statistically relevant but borderline weak. Worth flagging on an exam.)
</details>

**FRQ 4.** Reduced form. You estimate `wage = π0 + π1*sibs + error` and get π̂1 = −7. Interpret this in the context of using number of siblings as an instrument for education.

<details><summary>Model answer</summary>

The reduced form coefficient says: each additional sibling is associated with a $7 lower wage on average. Under the IV assumptions (sibs only affects wage through education), this entire effect operates through reduced education. We can't directly read off the return to education, but we know:

π1 = β1 (return to educ) × α1 (effect of sibs on educ)

So if α1 = −0.23 (from first stage), then β1 = π1 / α1 = −7 / −0.23 ≈ 30.4. This matches the 2SLS estimate. Reduced form is also useful on its own if you care about the policy question "do larger families produce lower-earning children?" without needing to attribute it specifically to education.
</details>
