# Lecture 18: Applications of DnD (What Can Go Wrong)

> **Why this lecture matters for the exam:** Lec 17 gave you the machinery. Lec 18 gives you the *threats to validity* — and threats to validity are how short-answer questions get asked. The sample paper short-answer Q1 and Q2 don't directly test DnD assumptions, but the conceptual logic ("under what conditions does this estimator capture the true causal effect?") is the exact same logic as Ch 9 internal validity threats. Expect a short answer or several MC items pulling from this material.

---

## Sample Exam Mapping

| Sample Q | Connection |
|---|---|
| MC Q1 (sample selection bias) | Same family of "what threatens this estimate" thinking |
| MC Q4 (ITT vs ATE under perfect compliance) | Compliance is the RCT analog of the spillover/parallel-trends issue |
| MC Q6, Q7 | Foundation, see Lec 17 notes |
| Short Answer Q2c, Q2d (first-difference vs pooled OLS) | Builds directly on the DnD assumption logic |

**Sleeper exam points from this lecture:**
- The parallel trends assumption is a **counterfactual** — it cannot be directly tested. This phrasing is exam gold for short answers. If a prompt asks "can you test the parallel trends assumption?" the answer is *no, not directly, because it's a counterfactual claim about what would have happened in the absence of treatment*.
- There are **four** things that can go wrong with DnD, not just two. Most students stop at parallel trends and spillovers. Adding Ashenfelter dips and reverse causality to your answer signals you actually understand the lecture.

---

## 1. The Two Core DnD Assumptions

Both must hold for δ1 (the interaction coefficient) to identify the causal effect.

### Assumption 1: No Spillover Effects

Treatment affects only the treatment group. The control group is *completely unaffected* by whether treatment occurs.

**The graphical version:** the control group line in the post-period is exactly where it would have been with no treatment anywhere in the world.

**Incinerator violation example:** suppose the incinerator's pollution drifts beyond the 5-mile radius and slightly depresses prices for "far" houses too. Then the control group's δ0 *understates* the true counterfactual. Subtracting an understated δ0 from the treatment group's δ0 + δ1 will *underestimate* δ1 (the absolute size of the effect). The DnD estimate is biased toward zero.

**General logic to memorize:** if treatment leaks into the control group, the control group is no longer a clean counterfactual, and you understate the true effect.

**Solutions:** define the control group farther away from treatment so spillovers can't reach. Use a buffer zone — drop observations near the boundary.

### Assumption 2: Parallel Trends

In the absence of treatment, the time trend in the treatment and control groups would have been the same. They could be at different *levels* (the β1 in the 2x2 table) — that's fine, that's why we need DnD in the first place. But the *change* over time would have to be identical without treatment.

**This is a counterfactual.** It claims something about a world that didn't happen (the world where treatment never occurred). You cannot directly test it with data.

**Why it's "the central assumption":** if it fails, your DnD estimator is biased. The control group's δ0 is no longer a valid stand-in for what the treatment group's time trend would have been without treatment.

**Two failure modes:**
- Control group's trend is *higher* than what treatment group's would have been → DnD estimator is biased *more negative* than the true effect
- Control group's trend is *lower* → DnD estimator is biased *more positive* than the true effect

> **The direction of bias is signable here, unlike attrition or spillovers in some cases.** If you can argue which way the violation goes, you can sign the bias. This is a money move on short answers.

---

## 2. Four Strategies to Make Parallel Trends More Plausible

You can't *prove* parallel trends. But you can build a case. The four strategies from the lecture:

### Strategy 1: Examine Pre-Treatment Trends

Get more pre-treatment periods (1975, 1976, 1977 for the incinerator). If the two groups moved in parallel before treatment, it's plausible they would have continued to move in parallel without treatment. **The pre-trend is the best evidence you can offer.**

### Strategy 2: Examine Post-Treatment Trends

Look at the trend after treatment. If the treatment-control gap stabilizes after treatment, that's reassuring (it suggests the gap reflects a one-time treatment effect, not a divergent trend that would have happened anyway).

> **Sleeper point — two interpretations of post-treatment divergence:**
> 1. Parallel trends was violated (the gap was diverging anyway, not because of treatment)
> 2. The treatment effect takes time to fully realize
>
> You can't always tell these apart. State both possibilities on a short answer.

### Strategy 3: Pseudo-Treatment

Pretend the treatment occurred at a time when it didn't. Run DnD around that fake date. You should get a null result. If you don't, your DnD machinery is picking up something other than the actual treatment effect.

### Strategy 4: Pseudo-Outcome

Find an outcome that *shouldn't* be affected by the treatment. Run DnD on that outcome. You should get a null result. Example: for the incinerator, you might check whether the price of identical houses in completely different cities followed the same time trend. If your DnD machinery says yes, something is off.

> **Connection to Ch 9:** these four falsification strategies are conceptually identical to the placebo tests, robustness checks, and pre-trend analyses you'd discuss for any internal validity threat. This is why the Ch 9 / DnD material blurs together on the exam.

---

## 3. Threat #3: Ashenfelter Dip / Anticipatory Effects

Named after Orley Ashenfelter (1978) who first noticed it for job training programs.

**The phenomenon:** participants in a training program experienced a *decline* in earnings just before the program started. This isn't because the program hurt them — they hadn't entered yet. It's because people who *self-select* into training tend to do so right after a bad shock (job loss, hours cut). Their earnings dip first, then they enroll.

**Why this destroys DnD:**
- If you use the dip year as your "before" period, the treatment group's pre-treatment level is artificially low.
- Then the natural recovery from the dip looks like a treatment effect.
- **You overstate the program's impact.**

**Generalized version — anticipatory effects:** people change behavior in anticipation of treatment. Example: if firms know a regulation is coming next year, they adjust this year, contaminating the pre-period.

**Solution:** drop the dip period. Use an earlier "before" period as the baseline (1962 or 1961 instead of 1964 in the Manpower example), so the pre-treatment level isn't depressed by the dip.

**Direction of bias from Ashenfelter dip:** typically *overstates* the treatment effect, because the dip artificially depresses the pre-period and the post-period recovery looks like treatment success.

---

## 4. Threat #4: Reverse Causality

The treatment didn't cause the outcome change — the outcome change caused the treatment.

**The smoking ban example from the lecture:** suppose smoking rates in some states started declining, and *because* of that decline, those states felt confident enough to pass smoking bans. Then post-ban you observe lower smoking in treatment states, but the ban didn't cause the decline — the decline caused the ban.

**Diagnostic clue:** if you see a divergent pre-trend where the treatment group is *already* moving in the direction the treatment is supposed to push them, suspect reverse causality.

**Connection to incinerator:** the lecture revisits the story with hypothetical pre-treatment data showing prices were already crashing in the soon-to-be-incinerator area. If the incinerator was sited there *because* values were crashing (cheap land), reverse causality on the underlying mechanism. The 1978 baseline is unreliable.

---

## 5. The Big Picture (Lecture's Closing Frame)

The lecture wraps with this synthesis: all four threats boil down to *whether you've picked an appropriate control group*.

A good control group:
- Is not affected by the treatment (no spillovers)
- Would have followed the same trend as the treatment group absent treatment (parallel trends)
- Is not contaminated by anticipation or pre-treatment shocks (no Ashenfelter dip)
- Is not selected based on the outcome trajectory (no reverse causality)

Different *levels* between treatment and control pre-treatment? Fine. That's the β1 in the model. DnD is *built* to handle level differences.

Different *changes* between treatment and control around the treatment date? Bad. That's what the four threats produce, and the DnD estimator can't tell that apart from a real treatment effect.

---

## 6. Summary Cheat Sheet (the night-before version)

**Two core assumptions:**
1. No spillover effects (control group untouched by treatment)
2. Parallel trends (in the absence of treatment, both groups would have trended identically)

**Two additional threats:**
3. Ashenfelter dip / anticipatory effects (selection into treatment based on a recent shock)
4. Reverse causality (outcome trends caused treatment, not the other way around)

**Four ways to defend parallel trends (cannot be directly tested):**
- Examine pre-treatment trends (most powerful)
- Examine post-treatment trends (with caveats)
- Pseudo-treatment test (run DnD at a fake date, hope for null)
- Pseudo-outcome test (run DnD on an unaffected outcome, hope for null)

**Direction of bias quick-reference:**
| Violation | Typical direction |
|---|---|
| Spillovers into control | Underestimate effect (toward zero) |
| Parallel trends fail (control trends up faster) | Underestimate / wrong sign possible |
| Parallel trends fail (control trends down faster) | Overestimate effect |
| Ashenfelter dip | Overestimate effect |
| Reverse causality | Mechanism-dependent, often overstates |

---

## 7. FRQ Practice — How to Write These on the Exam

### Prompt Type 1: "Discuss a threat to the validity of this DnD estimate"

**Strong answer pattern:**
> *Name the threat. State the assumption it violates. Tell a plausible story for this specific context. Sign the direction of bias. Suggest a diagnostic or solution.*

**Example:**
> A possible threat is **violation of parallel trends**. Specifically, if [treatment group] would have grown faster than [control group] even in the absence of treatment, then the control group's time trend δ0 understates the counterfactual, and our DnD estimate δ1 *overstates* the true effect. To assess this, I would examine pre-treatment trends across multiple periods. If both groups followed parallel paths from year X to year Y (pre-treatment), the parallel trends assumption is more credible. I could also run a pseudo-treatment test by re-estimating the model pretending treatment occurred in an earlier year — a null result there would be reassuring.

### Prompt Type 2: "Why can't you test parallel trends directly?"

**Strong answer:**
> The parallel trends assumption is a claim about a *counterfactual*: what would have happened to the treatment group's outcome in the absence of treatment. Since treatment did occur, we never observe this counterfactual trajectory. We can only build *circumstantial* evidence by examining pre-treatment trends, post-treatment trends, pseudo-treatments, and pseudo-outcomes. None of these is a direct test, but consistent results across them strengthen the case.

### Prompt Type 3: "DnD vs other strategies"

If the prompt mentions DnD alongside RCTs, IV, or RD, the contrast matters:
- **RCT:** randomization handles the counterfactual directly. DnD relies on parallel trends as a substitute.
- **DnD:** strong when you have pre-treatment data and a credible control group. Weak when trends differ or treatment is anticipated.
- **IV:** different fix, addresses endogeneity through an instrument. Doesn't require pre-treatment data.
- **RD:** uses a sharp threshold, exploits local randomness. Doesn't require parallel trends, but only identifies a local effect.

---

## 8. Connecting Back to the Sample Exam

**MC Q1 (sample selection bias):** Conceptually the same family as DnD threats — both ask whether your sample/control group represents the right counterfactual. Sample selection occurs when data is missing for some observations in a non-random way. Answer: **D**.

**MC Q4 (ITT vs ATE under perfect compliance):** ITT measures the effect of being *assigned* to treatment. ATE measures the effect of *actually receiving* treatment. They diverge when there's non-compliance (people assigned to treatment don't take it, or vice versa). Under perfect compliance, everyone assigned takes treatment, so ITT = ATE. Answer: **A**.

The conceptual link: in DnD, treatment is the *event* (incinerator built, tax raised). There's no compliance question because everyone in the treatment group is mechanically exposed once treatment kicks in. But the spillover and parallel trends issues create the same kind of identification problem that compliance does in RCTs — your "treatment effect" estimate gets contaminated by something other than the treatment itself.

---

## 9. Things Most Students Miss

These are the subtle points that distinguish a B answer from an A answer:

- **Parallel trends is about *changes*, not *levels*.** Different baseline levels between groups are fine and expected. DnD is *designed* to handle level differences. What you need is identical *trend slopes* in the absence of treatment.
- **Pre-trend evidence is necessary but not sufficient.** Even if pre-trends are parallel for 5 years, they could have started diverging at the treatment date for reasons unrelated to treatment. You can't fully rule this out.
- **Two different stories can produce identical post-treatment divergence:** (a) treatment effect taking time to manifest, (b) parallel trends violation. The data alone can't distinguish them.
- **Ashenfelter dip ≠ parallel trends violation.** They're related but distinct. Ashenfelter dip is specifically about the pre-period being contaminated by selection-into-treatment behavior. Parallel trends violation is about the underlying counterfactual trend differing across groups for any reason.
- **The parallel trends assumption operates on the underlying potential outcomes, not the observed outcomes.** This is why we say "in the absence of treatment" — we're not making a claim about what we observe, we're making a claim about a counterfactual world.
