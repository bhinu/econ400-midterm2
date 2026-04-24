# Lecture 16: Applications of RCTs (Balance, Compliance, Attrition, Spillovers)
 
**Exam relevance:** This is the highest-yield lecture for the RCT portion of your midterm 2 MC. It directly feeds into sample Q4 (ITT vs ATE), Q5 (attrition definition), and provides the conceptual foundation that appears as MC distractors elsewhere. The compliance and attrition examples here are classic professor-fodder for "will this bias up or down?" style questions.
 
**Professor grading note (literally on slide 32):** *"When I ask you to give an example of non-compliance or attrition, it's important to take a stand on whether or not we have data!"* This is the clearest hint you'll get. The distinction between compliance and attrition is entirely about whether you still have outcome data on the person.
 
**Connection to Lecture 15:** L15 built the theory (why RCTs work, what ATE means). L16 is about all the ways RCTs fail in practice, and what to do about each failure mode.
 
---
 
## 1. Historical Context (Low Priority for Exam, Skim It)
 
The textbook claim that experiments are "rare" in economics is outdated. Since the late 1990s, RCTs have exploded in labor and development economics. Three landmark early studies worth recognizing if they appear as MC options:
 
- **Rand Health Insurance Experiment (1974 to 1982):** Randomly varied co-pay amounts (0%, 25%, 50%, 95%). Finding: higher co-pays reduced health care use across the board, including for necessary care, especially among the poor.
- **National Supported Work Demonstration (starting 1975):** Offered a year of paid work to underemployed individuals. Finding: only effective for single mothers on welfare (AFDC), not former addicts, former offenders, or youth. Influenced welfare reform.
- **Miguel & Kremer Deworming Study (Kenya, 1998):** The study used on the slides to illustrate balance problems.
You don't need to memorize these. You just need to recognize that RCTs are a standard tool now.
 
---
 
## 2. Issue 1: Balance (The Hidden OVB Problem in RCTs)
 
### What "Balance" Means
 
Randomization is supposed to produce treatment and control groups that are statistically equivalent on all pre-treatment characteristics. "In expectation" is the crucial phrase. On average across many hypothetical randomizations, the groups match. But in any single realized experiment, you might get unlucky and end up with groups that differ.
 
**Checking for balance** means running a pre-treatment comparison of covariates across treatment and control. You literally subtract the control mean from the treatment mean for each pre-treatment variable, compute standard errors, and test whether the difference is zero.
 
### Why It Matters
 
The Miguel & Kremer deworming study found that treated pupils were slightly worse off than control pupils on some dimensions (year of birth differed, blood in stool was higher in treatment, etc.) despite randomization. The paper even acknowledges this:
 
> *"Despite randomized assignment, which produces groups with similar characteristics in expectation, treatment pupils appear to be worse off than control pupils along some dimensions, potentially creating a bias."*
 
If the groups aren't balanced at baseline, your ATE estimate conflates the true treatment effect with whatever pre-existing differences were there. You've essentially reintroduced OVB through bad luck.
 
### Solutions
 
**Before and during data collection:**
- Stratified randomization: split the sample into strata (by age, income, gender, etc.) and randomize within each stratum. This guarantees balance on the stratifying variables.
- Large sample sizes: imbalance shrinks as N grows.
- Re-randomize if an initial draw produces severe imbalance (controversial, but done).
**After data collection:**
- Control for the imbalanced variables in your regression: `Y = β₀ + β₁T + β₂X + u` where X is the imbalanced covariate. This absorbs the pre-existing difference.
- If you have pre-treatment outcome data, use **difference-in-differences** (covered in Lec 17 onward). This nets out any pre-existing level differences.
### FRQ-Ready Phrasing
 
> "Randomization produces balanced treatment and control groups in expectation, but any single realized experiment may exhibit chance imbalance on pre-treatment covariates. Checking for balance involves comparing pre-treatment means across groups. If imbalance is found, adding controls for the imbalanced covariates in the regression model can correct for the resulting bias."
 
### Connection Back to the Sample Paper You Already Worked On
 
This is exactly what came up in the NSW Q4 you got 0.5/1 on earlier. The answer that would have earned full credit starts with "yes, include controls" precisely because even randomized studies can have baseline differences (AFDC had a high school degree gap between treatment and control). The reasoning on this lecture's slide 14 is the exact argument you needed.
 
---
 
## 3. Issue 2: Compliance (High-Yield for Sample Q4)
 
### Definition
 
**Non-compliance** occurs when individuals assigned to treatment don't receive it, or individuals assigned to control find a way to get treated. Also called **diffusion** or **contamination**.
 
**Critical distinction from attrition:** with non-compliance, you still have data on the person. They just didn't take the treatment you assigned. With attrition, you've lost the person entirely (no outcome data).
 
This is the grading note distinction from slide 32. Memorize it.
 
### Why Non-Compliance Happens
 
People agree to participate but then fail to comply for many reasons: the treatment has side effects, they forget, they feel they don't need it, the control group finds a workaround, and so on.
 
### Direction of the Bias: Work Through the Cases
 
The lecture walks through five scenarios using a simple setup with 15,000 treated, 30,000 controls, three health categories (1 = unhealthy, 2 = moderate, 3 = healthy), and a treatment that moves everyone to health level 3. True ATE = 1.00. Here's the consolidated table:
 
| Scenario | Estimated ATE | Bias direction |
|---|---|---|
| Perfect compliance | 1.00 | Correct |
| Healthiest fail to comply | 1.00 | Correct (lucky) |
| Sickest fail to comply | 0.33 | Under-estimated |
| Random 50% non-compliance | 0.50 | Under-estimated |
| No one complies | 0.00 | Under-estimated |
| Defiers in control (extreme) | 2.00 | Over-estimated |
 
### The Rule of Thumb
 
**Non-compliance typically biases the ATE estimate toward zero (downward in magnitude).** It makes the treatment look less effective than it really is, because some members of the treatment group weren't actually treated, which dilutes the observed effect.
 
**The exception:** defiers. If people in the control group deliberately seek out treatment, or worse, if control-group defiers end up in outcome states that mimic the treatment effect, you can over-estimate. But this is rare and the exam will usually ask about the typical case.
 
### Why the "Healthiest Fail to Comply" Case Still Gave the Correct ATE
 
In that specific setup, the treatment had no room to improve the healthiest group anyway (they were already at health = 3). So their non-compliance didn't dilute anything. This is a useful edge case: *non-compliance only biases the estimate when the non-compliers would have responded to treatment.*
 
### Solutions
 
**Before and during data collection:**
- Choose a receptive population (people likely to comply).
- Make treatment as effortless as possible (remove barriers).
- Educate research subjects.
- Find ways to verify compliance.
**After data collection:**
1. Learn as much as possible about who isn't complying. If you can characterize the non-compliers, you can at least speculate about the direction and extent of the bias.
2. Settle for estimating the ITT instead of the ATE. See next section.
3. Use IV/2SLS with treatment assignment as the instrument for treatment receipt (this is the Ch 12 short-answer material, and it's the most sophisticated solution).
---
 
## 4. Intent-to-Treat (ITT): The Elegant Workaround
 
### The Setup
 
Instead of trying to measure the effect of *actually receiving* treatment (which is hard when there's non-compliance), you measure the effect of *being assigned* to treatment. You can't control who takes the medicine, but you have full control over who you offer it to.
 
**Notation shift:**
- `T = 1` means the person actually received treatment. `T = 0` means they didn't.
- `Z = 1` means the person was assigned to treatment. `Z = 0` means they weren't.
Under perfect compliance, T = Z for everyone. Under imperfect compliance, they differ.
 
### The ITT Estimator
 
Instead of comparing average outcomes by actual treatment receipt, you compare by assignment:
 
```
ITT = E[Y | Z = 1] − E[Y | Z = 0]
```
 
Since Z is randomized by the researcher, there's no way for subjects to mess with it. The ITT is always unbiased for the effect of assignment to treatment.
 
### Same Number, Different Interpretation
 
Here's the slick part. In the random non-compliance example from the slides, the observed difference between groups was 0.50. Previously you'd have called this a biased estimate of the ATE (since true ATE was 1.00). But if you instead call it an ITT estimate, it's exactly right. The effect of being *offered* the treatment, in a world where half of offered people take it, really is 0.50.
 
From the consolidated table on slide 29:
 
| Scenario | T minus C | As estimate of ATE | As estimate of ITT |
|---|---|---|---|
| Perfect compliance | 1.00 | Correct ATE | Correct ITT |
| Healthiest fail to comply | 1.00 | Correct ATE | Correct ITT |
| Sickest fail to comply | 0.33 | Under-estimated | Correct ITT |
| Random non-compliance | 0.50 | Under-estimated | Correct ITT |
| No one complies | 0.00 | Under-estimated | Correct ITT |
 
**The punchline:** The same number is a biased ATE and an unbiased ITT. What changes is the question you're asking.
 
### When Do You Want ATE vs ITT?
 
- **Pharmaceutical company developing a drug:** wants the ATE. They want to know what the drug does when taken, not what happens when it's offered.
- **Government rolling out a policy:** often wants the ITT. Real-world policy rollouts always have non-compliance, so the policy-relevant number is the effect of *offering* the program to the population.
### Recovering ATE from ITT (Rough Formula)
 
If you have random non-compliance, you can back out the ATE from the ITT using:
 
```
Recovered ATE ≈ ITT / (Compliance Rate)
```
 
Testing this on the table:
 
| Scenario | ITT | Compliance | Recovered? |
|---|---|---|---|
| Perfect | 1.00 | 1.00 | 1.00 ✓ |
| Healthiest don't comply | 1.00 | 0.67 | 1.49 ✗ |
| Sickest don't comply | 0.33 | 0.67 | 0.49 ✗ |
| Random | 0.50 | 0.50 | 1.00 ✓ |
| No one | 0.00 | 0.00 | undefined |
 
**Verdict:** The formula works when non-compliance is random (uncorrelated with potential outcomes). When non-compliance is systematic (sicker or healthier people more likely to skip), it fails. This is a conceptual limitation, not a math error.
 
The formal fix for this is IV/2SLS, which uses assignment Z as an instrument for actual treatment T. That's your Ch 12 short-answer material and is the full-credit answer to "how do we properly recover the ATE from an RCT with non-compliance."
 
### FRQ-Ready Phrasing
 
> "Intent-to-treat is defined as the difference in average outcomes between those assigned to treatment and those assigned to control, regardless of actual treatment receipt. The ITT is unbiased even in the presence of non-compliance because assignment is fully controlled by the researcher. When non-compliance is random, the ATE can be recovered approximately by dividing the ITT by the compliance rate. When non-compliance is systematic, this simple adjustment fails, and instrumental variables methods are needed to recover the ATE."
 
### Tie-in to Sample Q4
 
The sample paper asks: **"The ITT and ATE are equal when... (A) perfect compliance, (B) no attrition, (C) no Ashenfelter Dip, (D) always equal."** Answer is A. From slide 29's table, notice that only the perfect compliance row has both "Correct ATE" and "Correct ITT." That's exactly the condition for equality.
 
---
 
## 5. Issue 3: Attrition (High-Yield for Sample Q5)
 
### Definition
 
**Attrition** is when participants drop out of the sample entirely. Unlike non-compliance, the person is gone. You have no outcome data on them.
 
The sample paper Q5 asks what counts as attrition. Answer is D (all of the above): opting to leave, moving and becoming unreachable, and dying during the study all count.
 
### The Key Distinction (Grading Note from Slide 32)
 
Scenario: someone in the treatment group decides they no longer want treatment and stops receiving it.
 
- If you still have outcome data on them (they're willing to be surveyed, just not take the medicine), it's **non-compliance**.
- If they also stop participating in the study altogether and you can't measure their outcome, it's **attrition**.
Take a stand on data availability when the professor asks for an example. This is the grading hint.
 
### Direction of the Bias: Work Through the Cases
 
Same setup as before (true ATE = 1.00, treatment moves everyone to health = 3):
 
| Scenario | T minus C | Bias direction |
|---|---|---|
| No attrition | 1.00 | Correct ATE |
| Random 10% attrition | 1.00 | Correct ATE |
| Unhealthy control attrit | 0.50 | Under-estimated |
| Healthy children attrit (both arms) | 2.00 | Over-estimated |
 
### The Lesson: No Consistent Direction
 
Unlike non-compliance, where the bias is typically downward, **attrition can bias your estimate in either direction**. It depends entirely on who's leaving.
 
- If the people leaving are on average worse off in the *control* group, the remaining control group looks healthier than it should, and the estimated treatment effect shrinks.
- If the people leaving are on average healthier in both groups, but healthier controls would have ended up matching treatment outcomes anyway, you can over-estimate the effect.
**FRQ phrasing:** *"The direction of bias from attrition depends on the characteristics of those who attrit. Unlike non-compliance, which typically biases the ATE toward zero, attrition can bias the estimate in either direction, making it difficult to sign the bias without additional information about the attritors."*
 
### Why Random Attrition Is OK
 
If attrition is truly random (every subject has the same probability of leaving, regardless of treatment status or outcomes), then the groups shrink proportionally but remain balanced. Your ATE is unbiased, just less precise because of the smaller sample.
 
**Non-random attrition is the killer.** If attrition correlates with treatment status or potential outcomes, it reintroduces selection bias and undermines the whole point of randomization.
 
### Solutions
 
**Before and during data collection:**
- Monitor attrition in real time.
- Spend heavily on tracking participants (home visits, repeated follow-up, bonuses for responding).
- Design the study so the control group also gets something of value, reducing their incentive to drop out.
**After data collection:**
- Check whether attrition appears random (run a regression of an attrition indicator on pre-treatment characteristics and treatment status).
- If attrition is non-random, the study is in serious trouble. You may need bounds analysis (Lee bounds) or to acknowledge the limitation.
- If you can identify the attritors, you can at least speculate about the direction of the bias.
---
 
## 6. Issue 4: Spillovers (Medium Priority)
 
### Definition
 
**Spillovers** occur when treatment of the treated group actually affects outcomes for the control group. This violates the **Stable Unit Treatment Value Assumption (SUTVA)**, which says each subject's outcome depends only on their own treatment status.
 
### Examples from the Slides
 
**Example 1: Bed nets for malaria.** Treating households with bed nets reduces mosquitoes in the whole neighborhood, which lowers malaria rates even in control households. The control group isn't really untreated anymore. You'll under-estimate the ATE because the control outcomes improved too.
 
**Example 2: Income shock and child health.** If treatment raises incomes for a large enough share of the population, prices of food might rise, hurting the control group. You'll over-estimate the ATE because controls got worse.
 
### Direction of Bias
 
Like attrition, spillovers can go either way. Positive spillovers (bed nets) cause under-estimation. Negative spillovers (price effects) cause over-estimation.
 
### Solutions
 
The main fix: **randomize at a higher level**. Instead of randomizing individuals within a village, randomize entire villages. Now the "untreated" villages are physically separated from treated villages and can't be affected by spillovers. This is called **cluster randomization**.
 
Trade-off: cluster randomization reduces effective sample size. You need more clusters to get statistical power, which makes studies more expensive.
 
---
 
## 7. Criticism of RCTs: Deaton and External Validity
 
### The Critique
 
Angus Deaton (Princeton, Nobel laureate) is the most prominent RCT skeptic. He coined the term **"randomistas"** for economists who want to RCT every question.
 
Deaton's main argument:
 
> *"Under ideal circumstances, randomized evaluations of projects are useful for obtaining a convincing estimate of the average effect of a program or project. The price for this success is a focus that is too narrow and too local to tell us 'what works' in development."*
 
### The Internal vs External Validity Trade-off
 
- **Internal validity:** does the study accurately estimate the causal effect for the population studied? RCTs are the gold standard here.
- **External validity:** do the results generalize to other populations, contexts, or scales? RCTs often struggle here.
The deworming study, for instance, was done in 75 schools in southern Busia, Kenya. Does that finding generalize to northern Kenya? To Bangladesh? To Peru? To a nationwide rollout vs a small pilot? Not obviously.
 
### Why RCTs Have Poor External Validity
 
1. **Context matters.** The effect of a program can depend on local institutions, culture, infrastructure.
2. **Scale-up effects.** Small pilot programs often succeed because of intense implementation; larger rollouts may not replicate that quality.
3. **General equilibrium effects.** A cash transfer to a few families doesn't change market prices. A cash transfer to everyone might.
4. **Selection of study sites.** Researchers often run RCTs in places where cooperation is easiest, which may not be representative.
### What This Means for Exam Framing
 
If you get a question about the limits of RCTs, the right framing is: RCTs have strong internal validity (they cleanly identify the causal effect in the study sample) but may have weak external validity (the effect may not transfer to other settings or scales). This is a classic way to frame the limitation without dismissing the method.
 
---
 
## 8. What You Need to Be Able to Do on the Exam
 
| Skill | How It Shows Up on Midterm 2 |
|---|---|
| Distinguish non-compliance from attrition based on data availability | Short answer or MC distractor (grading note hints at this) |
| Know ITT = ATE requires perfect compliance | Sample Q4, directly tested |
| Know attrition includes leaving, becoming unreachable, dying | Sample Q5, directly tested |
| Sign the bias from various compliance failures | Likely MC, possibly short answer |
| Explain why attrition direction of bias is unsignable | Likely short answer if it appears |
| Recover ATE from ITT when non-compliance is random | Possible MC ("what does ITT / compliance rate give you") |
| Identify spillovers and explain randomization at higher level | Possible MC |
| Frame internal vs external validity trade-off | Possible short answer on RCT limitations |
 
---
 
## 9. FRQ-Ready Language Bank
 
**On the non-compliance vs attrition distinction:**
> "The key distinction is data availability. Non-compliance occurs when a subject assigned to treatment does not receive treatment but remains in the study, so outcome data is still observed. Attrition occurs when a subject exits the study entirely, so no outcome data is available."
 
**On the direction of bias from non-compliance:**
> "Non-compliance typically biases the estimated ATE toward zero. Subjects assigned to treatment who do not comply are still counted as treated in the analysis, which dilutes the measured effect. The exception is the case of defiers in the control group, where the bias can go in either direction."
 
**On why ITT is easier than ATE:**
> "The ITT is defined over treatment assignment, which is fully controlled by the researcher. Because subjects cannot self-select into assignment, the ITT is unbiased by design. The ATE, in contrast, depends on actual treatment receipt, which subjects can influence through non-compliance, introducing selection bias."
 
**On attrition:**
> "Attrition is a threat to internal validity because it can reintroduce selection bias. When attrition is correlated with treatment status or potential outcomes, the remaining sample is no longer comparable across arms. The direction of the resulting bias depends on the characteristics of the attritors and cannot be signed without additional information."
 
**On spillovers:**
> "Spillovers occur when treatment of the treated group affects outcomes in the control group. Positive spillovers bias the estimate toward zero; negative spillovers bias it away from zero. The standard solution is cluster randomization, where entire units such as villages are randomized together, isolating treatment effects from control units."
 
**On the internal vs external validity trade-off:**
> "Well-designed RCTs have strong internal validity because randomization identifies the causal effect for the study sample. External validity, however, may be limited by context-specificity, scale-up effects, and general equilibrium considerations. Results from a local pilot may not generalize to other settings or to nationwide implementation."
 
