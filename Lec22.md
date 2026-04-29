# Lecture 22: Applications of IV

**Sample midterm hits:** MC Q9 (weak instrument definition), Short Answer Q3 (whole question is IV applied to returns to education with distance as instrument)

This lecture is mostly examples + the "what can go wrong" part of IV. Lec 21 set up the machinery; this one stress-tests it.

---

## Quarter of birth (QOB) — Angrist & Krueger 1991

### The setup
Want returns to education: `wage = β0 + β1*educ + u`. Worried about ability bias. Propose QOB as instrument.

### Why QOB might be relevant
Compulsory schooling laws + age-of-entry cutoffs create a quirk:
- Born Q1 (Jan-Mar) → start school **older** → reach legal dropout age (16 or 18) earlier in their senior year → can legally drop out with **less** schooling
- Born Q3-Q4 → start school **younger** → reach legal dropout age later → forced to stay in school longer → **more** schooling

So QOB shifts educ even though no one is choosing their birthday. Relevance check: graphs show Q1 babies have ~12.65 years of education vs. Q3 babies ~12.85. Small but statistically detectable.

### Why QOB validity is debated
- **Angrist & Krueger:** birth quarter is essentially random with respect to ability. So it should be uncorrelated with u.
- **Bound & Jaeger (1996) critique:** being old/young in your grade might affect labor outcomes through channels OTHER than education — confidence, athletic success, social development. If so, QOB violates the exclusion restriction.
- A&K's defense: evidence for those channels was "weak and inconclusive."
- B&J's reply: "this is comfort only if we take the position that instruments are valid unless proven otherwise."

### The takeaway
Validity assumptions can't be directly tested. Reasonable economists disagree. **Causal inference requires a leap of faith.**

---

## What can go wrong with IV

### Problem 1: invalid instrument
If `cov(z, u) ≠ 0`, then x̂ from Stage 1 is correlated with u. So Stage 2 has the same OVB problem we started with. **Right back to square one.**

### Problem 2: completely irrelevant instrument
If `cov(z, x) = 0`, Stage 1 has α1 = 0, x̂ has no variation, Stage 2 is undefined. 2SLS falls apart entirely. Rare in practice.

### Problem 3: weak instrument (the realistic version of Problem 2)
**This is MC Q9 on the sample midterm.**

A weak instrument is one where `cov(z, x)` is **small** but not zero.

> **MC Q9: a weak instrument is one for which... C) Cov(x,z) is small** ✓
>
> Not A (Cov(u,z)=0 means valid, not weak). Not B (Cov(x,z)=0 means irrelevant, not weak). Not D (small Cov(u,z) describes a nearly-valid instrument, not weakness).

**Two consequences of a weak instrument:**

1. **Huge standard errors.** With little variation in x̂, Stage 2 is imprecise. (See the smoking-and-birthweight example: IV coefficient on packs is 345.5 with SE 1037.9 — meaningless.)

2. **Bias amplification, even with small validity violations.** This is the subtle one. The IV bias is approximately:
   ```
   bias ≈ Cov(z, u) / Cov(z, x)
   ```
   - If z is highly relevant (big denominator), even a non-zero Cov(z,u) causes only modest bias.
   - If z is weak (tiny denominator), even a tiny Cov(z,u) blows up the ratio. Bias can be ENORMOUS.

   This is the deepest critique of QOB: even if Cov(z,u) is small, QOB is so weak that the bias can be larger than the OLS bias it was supposed to fix.

### How to detect weakness
First-stage F-stat. Rule of thumb: F < 10 = weak.

In the smoking/birthweight example: first stage F was 0.12 (essentially zero). Cigarette price barely moves cigarette consumption. Total weak instrument disaster.

---

## Multiple endogenous regressors / multiple instruments

You can have:
- More than one endogenous regressor (e.g., both educ and experience)
- More than one exogenous control (e.g., age, race, region)
- More than one instrument (e.g., both sibs and birth order)

### Rules:
1. **You need at least as many instruments as endogenous regressors.** (Order condition.)
2. Run a **separate first stage for each endogenous regressor**, with ALL instruments AND ALL exogenous controls on the right-hand side.
3. In the second stage, replace each endogenous regressor with its fitted value.

### Example from lecture (counting practice)
```
ivregress 2sls lwage (exper educ = sibs brthord) age black south, first robust
```
- Endogenous regressors: exper, educ → **2**
- Instruments: sibs, brthord → **2**
- Exogenous regressors: age, black, south → **3**
- First-stage regressions: one for exper, one for educ → **2**

Each first stage has all 5 right-hand side variables (sibs, brthord, age, black, south).

---

## Over-identification test (validity-adjacent)

When you have MORE instruments than endogenous regressors (over-identified case), you can do an indirect validity check.

### Idea
Run 2SLS using only z1, then 2SLS using only z2. If both instruments are valid, both should estimate the same β1. If estimates differ a lot, at least one is invalid.

### Stata
After `ivregress 2sls`, run `estat overid`. Reports a chi-square test:
- H0: all instruments are valid
- H1: at least one instrument is invalid (or they disagree)

**Fail to reject** (high p-value) → can't conclude any are invalid. Comforting but not a clean bill of health.
**Reject** → at least one is invalid.

Important caveat: this test ASSUMES at least one instrument is valid. It can only tell you they disagree, not which one is wrong. So it's indirect evidence, not a proof.

---

## Vietnam draft lottery — Angrist 1990 / Angrist et al. 2010

Question: causal effect of military service on wages.
```
ln(wage) = β0 + β1*veteran + u
```
OVB worry: veterans differ from non-veterans in unobserved ways (motivation, family background, etc.).

**Instrument:** draft lottery number. Random by construction → valid by design. Relevant because being drafted raised the probability of serving (though imperfectly — some drafted didn't serve, some non-drafted enlisted voluntarily).

### First stage results (effect of draft on serving)
- White men: ~14-18 percentage points more likely to serve if drafted
- Non-white men: ~6-9 pp (smaller because non-white men were more likely to enlist voluntarily anyway)
- Instrument is more relevant for whites.

### Second stage results
2SLS for white men 1970-1979: β1 = -0.153 (i.e., 15% wage penalty for veterans).
OLS for the same period: β1 = +0.015 (basically zero).

**Big difference between OLS and IV.** OLS missed the negative effect because positive selection (motivated guys enlist voluntarily) cancels out the wage penalty from lost civilian experience.

### Why the wage gap shrinks over time (1990s, 2000s)
"Loss of experience" theory: veterans missed years of work experience. With diminishing returns to experience, this gap narrows as everyone accumulates lots of experience eventually.

---

## When to prefer IV over other strategies

| Strategy | Requires | When to use |
|----------|----------|-------------|
| RCT | Ability to randomize treatment | Best when feasible — no leap of faith |
| DnD | Before/after data + comparison group | Policy change with untreated control |
| Fixed effects | Panel data | OVB is time-invariant |
| **IV** | A clever instrument | OVB that's time-varying AND unobservable, no policy break to exploit |

IV's advantage: works even when the omitted variable is **unobserved AND time-varying**. Less data required than panel methods.

IV's drawback: hard to find a believable instrument. Validity is a judgment call.

---

## Mapping to Sample Midterm Q3 (the IV short answer)

Q3 walks through exactly this lecture's logic:

**Q3a:** OVB in `lwage = β0 + β1*educ + u`. Standard answer: ability. Sign: upward bias (cov(ability,educ)>0, cov(ability,wage)>0).

**Q3b:** Is "distance to nearest college" a good instrument?
- Relevance: Plausibly yes — distance raises cost of attending → less education. Mechanism is clear.
- Validity: Less clear. Distance might correlate with rural residence, which could affect wages through local labor markets independent of education. Or with family wealth (rich families live near nice colleges). Reasonable people might disagree.

**Q3c:** OLS = 0.09, 2SLS = 0.06. OLS bigger → OLS was biased upward → consistent with the ability story in Q3a. ✓

**Q3d:** Can we test relevance from this output?
- **YES.** First stage: `educ = 13.96 - 0.07*distcol`. SE on -0.07 is 0.01, so t ≈ -7. Reject H0 that the coefficient is zero. Distance is a relevant instrument.
- (We can't test validity directly. We could test it indirectly with an over-id test if we had a second instrument, but we only have one here.)

---

## Quick exam-prep checklist for Lec 22

- [ ] Can I define a weak instrument? (Cov(z,x) is small, not zero — this is MC Q9)
- [ ] Can I explain WHY weak instruments are bad? (huge SEs + bias amplification when validity is imperfect)
- [ ] Can I count endogenous regressors, instruments, exogenous controls, and first stages from a Stata command?
- [ ] Can I describe the over-identification test and what it can / can't tell us?
- [ ] Can I write up the QOB or draft-lottery example as an instrument argument (relevance + validity)?
- [ ] For Sample Q3: can I assess distance-to-college as an instrument and run a relevance test from the first-stage output?

---

# Practice Questions — Lecture 22

## Multiple Choice

**1.** A weak instrument is one for which:
A) cov(u, z) = 0
B) cov(x, z) = 0
C) cov(x, z) is small but nonzero
D) cov(u, z) is small

<details><summary>Answer</summary>

**C.** This is sample midterm Q9. A weak instrument has small but nonzero covariance with x. (B is irrelevant, not weak. A is valid. D is "nearly valid" — different concept.)
</details>

**2.** With a weak instrument that is also slightly invalid (small but nonzero cov(z,u)), the IV bias is approximately:
A) Smaller than OLS bias
B) Equal to OLS bias
C) Potentially much LARGER than OLS bias
D) Always exactly zero

<details><summary>Answer</summary>

**C.** Bias ≈ cov(z,u) / cov(z,x). If the denominator is tiny (weak), even a small numerator (slight invalidity) creates huge bias. This is the central critique of weak instruments — they amplify validity violations.
</details>

**3.** The over-identification test requires:
A) Exactly one instrument and one endogenous regressor
B) More instruments than endogenous regressors
C) More endogenous regressors than instruments
D) No exogenous controls

<details><summary>Answer</summary>

**B.** "Over-identified" means you have extra instruments to play with. With one instrument and one endogenous regressor, the model is exactly identified and there's nothing to test.
</details>

**4.** Consider: `ivregress 2sls lwage (educ exper = sibs brthord motheduc) age black, robust`. The number of first-stage regressions is:
A) 1
B) 2
C) 3
D) 5

<details><summary>Answer</summary>

**B.** One first stage per endogenous regressor. Endogenous = educ, exper → 2 first stages. Each first stage has all 3 instruments and both exogenous controls (age, black) on the right.
</details>

**5.** Quarter of birth might violate the exclusion restriction because:
A) Quarter of birth is not random
B) Birth quarter affects education (which it should — that's relevance)
C) Birth quarter might affect labor outcomes through channels other than education
D) Schools don't actually use age cutoffs

<details><summary>Answer</summary>

**C.** This is Bound & Jaeger's critique. Being old/young in your grade might affect confidence, athletic success, or social development, which could affect wages independent of education. If so, QOB → wages through paths other than education → exclusion restriction violated.
</details>

**6.** An over-identification test fails to reject the null. We can conclude:
A) All instruments are valid
B) We cannot conclude any are invalid (but they could all still be invalid)
C) At least one instrument is invalid
D) The instruments are weak

<details><summary>Answer</summary>

**B.** The over-id test only rejects when instruments DISAGREE. If they all agree (and all are wrong in the same direction), the test won't catch it. Failure to reject is comforting but not a clean bill of health.
</details>

**7.** A first-stage F-statistic of 3 indicates:
A) The instrument is irrelevant (cov = 0)
B) The instrument is weak
C) The instrument is strong
D) The instrument is invalid

<details><summary>Answer</summary>

**B.** Rule of thumb: F > 10 = strong, F < 10 = weak. F = 3 is well below 10. The instrument has some correlation with x (so not totally irrelevant — A is wrong) but it's too weak to trust 2SLS results. Validity is a separate question.
</details>

**8.** In the Vietnam draft lottery study, the OLS coefficient on veteran status was near zero while the 2SLS coefficient was −0.15. This pattern is consistent with:
A) Negative selection into military service (drafted men were less able)
B) Positive selection into military service (volunteers were more motivated/able)
C) The instrument being invalid
D) Random sampling error only

<details><summary>Answer</summary>

**B.** OLS lumps together drafted veterans (random) with volunteers (positively selected — motivated, often facing limited civilian options). Volunteer wages exceed those of comparable non-veterans, masking the true negative effect from lost civilian experience. IV uses only the random variation (draft lottery) and isolates the negative causal effect.
</details>

## Short Answer

**FRQ 1.** (Sample-midterm-style.) You estimate `health = β0 + β1*income + u` and worry about OVB.

a) Suggest a plausible omitted variable and sign the bias.
b) Lottery winnings (won big / didn't) is proposed as an instrument for income. Discuss relevance and validity.
c) OLS gives β̂1 = 0.40, IV gives β̂1 = 0.15. Is this consistent with your bias direction in (a)?
d) The first stage shows lottery → +$20,000 on income, SE = $4,000. Test relevance.

<details><summary>Model answer</summary>

a) Omitted variable: education (or general human capital). cov(educ, income) > 0 (educated → richer), cov(educ, health) > 0 (educated → healthier through better health behaviors and access). Product positive → upward bias, OLS β̂1 too high.

b) Relevance: cov(lottery, income) ≠ 0. Yes — winning the lottery directly raises income. Strong, mechanical relevance. Validity: cov(lottery, u) = 0. Plausible because lottery wins are random (assuming we condition on having played). The error u (containing education, genetics, family background) shouldn't correlate with whether you won. One concern: lottery players may differ systematically from non-players, so restrict the sample to players only. With that sample restriction, validity is highly plausible.

c) Yes — OLS (0.40) > IV (0.15) is consistent with the upward bias predicted in (a). The OLS overestimate matches the prediction that omitted education (or similar) makes income look more important than it really is for health.

d) H0: α1 = 0, H1: α1 ≠ 0. t = 20000/4000 = 5. |t|=5 > 1.96 → reject. F = 25 > 10 → strong instrument. Lottery is a relevant (and strong) instrument for income.
</details>

**FRQ 2.** Counting practice. Given:
`ivregress 2sls earnings (educ exper military = qob qoy_int motheduc) age south urban, robust`

a) How many endogenous regressors?
b) How many instruments?
c) How many exogenous controls?
d) How many first-stage regressions?
e) Is the model under-, exactly-, or over-identified?

<details><summary>Model answer</summary>

a) **3** endogenous: educ, exper, military.
b) **3** instruments: qob, qoy_int, motheduc.
c) **3** exogenous controls: age, south, urban.
d) **3** first-stage regressions (one per endogenous regressor). Each has all 3 instruments + all 3 exogenous controls on the right.
e) **Exactly identified.** #instruments (3) = #endogenous regressors (3). Need at least equal — under-identified would be #instruments < #endogenous. Over-identified would be more instruments than needed. Note: with exactly identified, the over-id test cannot be performed (no extra instruments to compare against).
</details>

**FRQ 3.** Why does OLS underestimate (in absolute value) the wage penalty of military service in the Vietnam-era draft lottery study? Frame your answer in terms of selection.

<details><summary>Model answer</summary>

OLS compares all veterans to all non-veterans. The veteran group is a mix of two types:
1. **Drafted veterans** — randomly selected, no selection bias. They suffer the true negative effect from lost civilian experience.
2. **Voluntary enlistees** — positively selected. Often motivated young men, sometimes from backgrounds with limited civilian options. Their post-service wages may be higher than comparable non-veterans because of their underlying motivation/ability.

When OLS averages these two groups, the positive selection of volunteers partially or fully offsets the negative draft-induced effect. Result: OLS β̂1 ≈ 0 (essentially no relationship) even though there's a real negative causal effect.

IV using draft lottery isolates only the random draft-induced variation. This strips out volunteer selection. The IV estimate (−0.15) reflects just the causal penalty of being forced into service: about 15% lower wages, primarily through lost civilian work experience.
</details>

**FRQ 4.** Critically assess Quarter of Birth as an instrument for education in `wage = β0 + β1*educ + u`.

<details><summary>Model answer</summary>

**Relevance:** Yes, plausibly. Compulsory schooling laws set a fixed dropout age (e.g., 16 or 18). Combined with school-entry age cutoffs, quarter of birth determines how much schooling a student must complete before legally being able to drop out. Born Q1 → older for grade → can drop out earlier with less schooling. Born Q3-Q4 → younger for grade → forced to stay longer. Empirically, A&K (1991) document a small but statistically detectable difference in years of education by QOB. Testable via first-stage F-stat.

**Validity:** Disputed. A&K argued QOB is essentially random with respect to ability, so cov(QOB, u) = 0. Bound & Jaeger (1996) countered that being old/young relative to classmates can affect labor outcomes through channels OTHER than education — confidence, athletic success, social development, peer effects. If true, the exclusion restriction is violated.

**Weak instrument concern:** QOB is also weak (small first-stage coefficient). Bias ≈ cov(z,u)/cov(z,x). With small denominator, even minor validity violations cause amplified bias.

**Overall:** Useful as a thought experiment but considered a weak and questionably valid instrument in modern practice. Illustrates the central dilemma of IV: causal inference requires assumptions that cannot be directly tested, and reasonable researchers can disagree.
</details>
