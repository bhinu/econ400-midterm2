# RD Exam Essentials (Lec 23 + 24)

## The one assumption you MUST know cold

**Key RD assumption (sample MC Q10, will likely repeat):**
> The only thing changing suddenly as we move from just below to just above the threshold is one's probability of being treated.

Everything else (preferences, demographics, unobservables) is continuous through the cutoff. Just above and just below = essentially identical except for treatment.

**NOT parallel trends (that's DnD).**

---

## Three terms you must define on sight

| Term | Definition |
|------|-----------|
| **Running variable** | The continuous variable that determines treatment by crossing a threshold (income, test score, enrollment, vote share, birth date) |
| **Cutoff / threshold** | The value of the running variable where treatment status changes |
| **Treatment effect** | The vertical JUMP in the outcome at the threshold |

---

## Sharp vs Fuzzy (must know the distinction)

- **Sharp:** Treatment probability jumps from 0 to 1 at the cutoff. Deterministic.
- **Fuzzy:** Treatment probability jumps but NOT from 0 to 1. Some eligible people don't take it, some ineligible find loopholes.

**Fuzzy RD formula (memorize this):**

$$\text{LATE} = \frac{\text{Local ITT (jump in outcome)}}{\text{Jump in participation at cutoff}}$$

Same structure as ATE = ITT / compliance rate from RCTs. Same structure as IV.

**Numerical example:** Outcome jumps $50, participation jumps 50pp → LATE = 50/0.5 = $100.

---

## The main threat: SORTING (manipulation)

People game the running variable to land on the favorable side.

**When sorting is plausible:** cutoff is public + manipulable + people have incentive.
**When sorting is impossible:** birth date, exact integer enrollment, lottery numbers.

**Two checks:**
1. **McCrary density test:** plot histogram of running variable. Spike on favorable side = red flag.
2. **Balance test:** check if pre-determined covariates jump at cutoff. If yes, units aren't comparable.

---

## LATE / external validity (one-liner answer)

RD only uses observations near the cutoff, so the estimate is **local** to that point on the running variable. Doesn't generalize to people far from the threshold or different doses of treatment.

If a question asks "what's the limitation of RD even when assumptions hold?" → **LATE / external validity**.

---

## Bandwidth tradeoff

- **Wide:** more data, smaller SEs, but distant units less comparable → bias UP, variance DOWN
- **Narrow:** cleaner comparisons, fewer obs, bigger SEs → bias DOWN, variance UP

Classic bias-variance tradeoff. `rdrobust` picks optimal bandwidth automatically.

---

## Go-to example: Maimonides Rule (Angrist & Lavy)

- **Rule:** Israeli classes split when enrollment exceeds 40
- **Running variable:** enrollment (NOT class size, that's the treatment)
- **Cutoff:** 40
- **Treatment:** smaller class size (going from 1 class of ~40 to 2 classes of ~20)
- **Why fuzzy:** rule isn't perfectly applied
- **What it identifies:** effect of going from 40-student class to 20-student class. NOT effect of going from 25 to 12.

If you forget every other example, remember this one.

---

## Comparison table for "RD vs other methods" question

| Method | Key assumption |
|--------|---------------|
| RCT | Random assignment, compliance |
| DnD | **Parallel trends** |
| FE/FD | Time-invariant OVB |
| IV | **Exclusion restriction** (untestable) |
| **RD** | **Continuity at threshold** (no manipulation) |

---

## Likely exam questions (ranked by probability)

1. **MC: key RD assumption** → answer is "only treatment probability changes discontinuously at threshold"
2. **MC: sorting/manipulation threat** → answer involves people gaming the running variable
3. **MC: LATE / local effect** → only identifies effect near the cutoff
4. **MC: sharp vs fuzzy distinction** → sharp = 0 to 1, fuzzy = partial jump
5. **Possible short answer: identify running variable, cutoff, treatment from a description** → don't confuse running variable with treatment (Maimonides trap: enrollment is running var, class size is treatment)
6. **Possible numerical: compute fuzzy RD LATE** → LATE = ITT / first stage jump

---

## What to skip

- Specific `rdrobust` syntax and output reading (low probability)
- Functional form sensitivity details (low probability blue book material)
- Shingles vaccine study deep dive (unlikely on exam, just know the structure of fuzzy RD)
- Compulsory schooling / QOB stuff (that's IV not RD)

---

## 30-second self-check before exam

Can you answer these without thinking?
- [ ] What's the key RD assumption? (only treatment changes discontinuously)
- [ ] Sharp vs fuzzy in one sentence each?
- [ ] Fuzzy RD LATE formula?
- [ ] Two checks for sorting? (density + balance)
- [ ] What does "local" in LATE mean? (effect at cutoff only)
- [ ] Running variable vs treatment in Maimonides? (enrollment vs class size)

If yes to all six, you're done with RD. Move on.
