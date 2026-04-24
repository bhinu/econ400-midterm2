# Lecture 15: Randomized Controlled Trials (RCTs)
 
**Exam relevance:** This is the foundation for the RCT questions on your midterm 2 MC section (ITT, ATE, attrition, non-compliance). It also sets up the Ch 12 IV/2SLS short-answer question, since IV is the tool you use to recover the ATE when an RCT has non-compliance.
 
**Connection to midterm 1:** The whole lecture is really about why randomization satisfies the first OLS assumption (zero conditional mean, `E[u|X] = 0`) by design. If you got OVB questions right on midterm 1, you already have the intuition. RCTs are just OVB prevention by experimental construction.
 
---
 
## 1. Why We Need RCTs in the First Place
 
### The Setup Problem
 
Recall the California student-teacher ratio example. You run:
 
```
test_score = β₀ + β₁(STR) + u
```
 
You get `β̂₁ = −2.28` and a statistically significant p-value. Done, right?
 
No. Statistical significance only rules out **spurious relationships** (chance correlations). It does NOT rule out:
 
1. **Reverse causality** — maybe low test scores cause districts to hire more teachers, not the other way around.
2. **Simultaneity** — STR and test scores are jointly determined by something else.
3. **Confounders (OVB)** — richer districts have both smaller classes and higher scores because of parental income, not class size.
Multiple regression can handle confounders you can observe and measure (add them as controls). But you can never be sure you've controlled for every unobserved confounder. This is the problem the RCT solves.
 
### Why Randomization Is the Fix
 
If you **randomly assign** treatment, then by construction treatment status `T` is uncorrelated with every other variable in the universe, observed or unobserved. That means:
 
```
E[u | T] = 0
```
 
holds automatically. The zero conditional mean assumption, which you spent all of midterm 1 worrying about, is satisfied by the experimental design itself. You don't need to argue about it. You engineered it.
 
**This is why RCTs are called the "gold standard" for causal inference.**
 
---
 
## 2. The Rubin Causal Model (The Conceptual Core)
 
### The Fundamental Problem of Causal Inference
 
For a single person, the true causal effect of treatment would be:
 
```
(Outcome if treated) − (Outcome if NOT treated)
```
 
But you can only observe ONE of these for any given person. The other is the **counterfactual** — what would have happened under the other treatment state. Counterfactuals are unobservable. This is called the **fundamental problem of causal inference**.
 
### The Workaround: Group Averages
 
Since you can't observe individual counterfactuals, you switch to averages across groups:
 
- **Treatment group:** average outcome = `E[Y | T = 1]`
- **Control group:** average outcome = `E[Y | T = 0]`
The **Average Treatment Effect (ATE)** is:
 
```
ATE = E[Y | T = 1] − E[Y | T = 0]
```
 
### The Key Question
 
The control group is what you observe. The counterfactual (what the treatment group would have looked like without treatment) is what you want. The central question of causal inference is:
 
> **How well does the control group proxy for the counterfactual?**
 
- If people self-select into treatment → control group is NOT a good proxy (selection bias)
- If you randomize → control group IS a good proxy (in expectation)
That's the whole argument for RCTs in one line.
 
---
 
## 3. Estimating the ATE (Two Equivalent Methods)
 
Let `T` be a treatment dummy (1 if treated, 0 if not) and `Y` be the outcome.
 
### Method 1: Two-sample t-test (Econ 310 throwback)
 
```
ttest Y, by(T) unequal
```
 
This spits out group means and the difference. The slide example gave:
- Control mean: 561.14
- Treatment mean: 1080.51
- Difference: 519.37 (t = 4.36, p < 0.001)
### Method 2: Regress Y on T
 
```
regress Y T, robust
```
 
Fit the model `Y = β₀ + β₁T + u`. Plug in the dummy values:
 
- When `T = 0`: `E[Y|T=0] = β₀` → this is the control group mean
- When `T = 1`: `E[Y|T=1] = β₀ + β₁` → this is the treatment group mean
- Difference: `β₁` → **this is the ATE**
The regression output gave `β̂₁ = 519.37`, the same number as the t-test. No coincidence — they're mathematically identical when T is a single binary regressor.
 
**Punchline for FRQs:** *If you regress the outcome on a treatment dummy, the coefficient on the dummy IS your estimate of the average treatment effect.*
 
### Why This Matters for Your Sample Paper
 
The midterm 2 sample paper's IV/2SLS short-answer question is basically asking you to recover an ATE when randomization alone isn't enough (because of non-compliance). The logic of "regress Y on T to get ATE" is the starting point. Then IV extends it.
 
---
 
## 4. ITT vs ATE (High-Yield Exam Topic)
 
This distinction is explicitly on your midterm 2 MC section. Lock it in.
 
### The Definitions
 
- **Intent to Treat (ITT):** The effect of being **assigned** to treatment, whether or not the person actually took it.
- **Average Treatment Effect (ATE):** The effect of **actually receiving** treatment.
### When They Differ
 
They differ whenever there's **non-compliance**:
 
- **No-shows:** assigned to treatment but didn't take it
- **Crossovers:** assigned to control but found a way to get treated
When compliance is imperfect, ITT **understates** the true ATE, because the "treated" group contains people who didn't actually receive treatment, which dilutes the estimated effect.
 
### The Key Exam Fact
 
**ITT = ATE only under perfect compliance.** That means 100% of the treatment group took treatment AND 0% of the control group got it. Anything less, and ITT ≠ ATE.
 
Sample paper Q4 (from our earlier notes): answer is A (perfect compliance). Attrition, Ashenfelter Dip, and "always" are wrong.
 
### FRQ-Ready Phrasing
 
> "The intent-to-treat effect measures the impact of treatment assignment, not treatment receipt. When compliance is imperfect, the ITT is a biased estimator of the ATE because some members of the treatment group did not actually receive treatment. Under perfect compliance, ITT equals ATE."
 
---
 
## 5. Attrition (Another High-Yield MC Topic)
 
**Attrition** = participants leaving the study before it ends. This can happen because of:
 
- Moving away
- Death
- Loss of interest
- Being unreachable
Sample paper Q5 answer: **D (all of the above)**. Any way a subject drops out of observation counts as attrition.
 
### Why Attrition Breaks an RCT
 
Randomization creates balanced groups **at baseline**. If dropout is non-random — say, people whose treatment didn't work get discouraged and quit — the remaining sample is no longer randomized. You've reintroduced selection bias through the back door.
 
**FRQ phrasing:** "Attrition threatens internal validity when it is correlated with treatment status or outcomes, because the observed sample is no longer comparable to the original randomized sample."
 
---
 
## 6. When NOT to Randomize
 
RCTs are powerful but not always appropriate. The slides give two examples.
 
### Example 1: Negative health shock on work productivity
 
You'd have to deliberately make people sick. Obviously unethical. **Moral: RCTs may be inappropriate for ethical reasons.**
 
### Example 2: Additional year of schooling on lifetime earnings
 
You'd have to randomly prevent some kids from going to school, then wait 40 years to measure lifetime earnings. Infeasible and ruinously expensive. **Moral: RCTs may be inappropriate for cost or time reasons.**
 
### The General Point
 
When an RCT isn't viable, that's when you reach for the other tools in your midterm 2 toolkit:
- **Instrumental variables (Ch 12)** — when you can't randomize, find something that acts like random assignment
- **Difference-in-differences (Ch 10)** — when you have panel data with a policy change
- **Regression discontinuity (Ch 13)** — when treatment is assigned by a threshold
Each of these is a "second-best" attempt to approximate what an RCT would have given you.
 
---
 
## 7. Ethics of Randomization
 
### The Historical Frame
 
- **Nuremberg Code (1949)** — first international guidelines, post-WWII
- **Tuskegee Syphilis Study (1932–1972)** — despite Nuremberg, this unethical US study ran for 40 years. Black men with syphilis were observed without treatment, even after penicillin became the standard cure.
- **National Research Act (1974)** — US response. Created **Institutional Review Boards (IRBs)** to oversee human subjects research at federally funded institutions.
### The Three Ethical Requirements
 
1. **Informed consent** — participants must be fully informed of risks and voluntarily agree.
2. **Risk-benefit balance** — risks to participants must be offset by societal benefit.
3. **Equipoise** — genuine uncertainty about whether the treatment works. If you already know the treatment is beneficial, it's unethical to withhold it from the control group.
### The Trolley Problem Analogy
 
The slides use the trolley problem to frame the ethical debate:
- **Naive view:** Randomization is a "switch" that helps the treatment group and harms the control group.
- **Reframe:** Randomization is actually a switch that helps HALF the people. Doing nothing helps zero people.
- **Counter-counter:** Some argue there's a third option — treat everyone. But in reality, no intervention (randomized or not) can treat everyone due to resource constraints.
You probably won't get a philosophy question on this. But if ethics shows up as an MC distractor, the right framing is: *randomization is ethical when there's equipoise and informed consent, and when the alternative (no study) means no one benefits from the knowledge generated.*
 
---
 
## 8. Is the RCT a Silver Bullet?
 
No. Even a well-randomized RCT can fail because of:
 
- **Non-compliance** (covered above)
- **Attrition** (covered above)
- **Hawthorne effects** (subjects behave differently because they know they're in a study)
- **Spillovers** (control group is affected by the treatment group's treatment)
- **External validity** (results from one RCT don't generalize to other settings)
Lecture 16 goes into specific failure modes. For midterm 2 MC, you mainly need to recognize that running an RCT does not automatically guarantee a clean causal estimate. Implementation matters.
 
---
 
## 9. What You Need to Be Able to Do on the Exam
 
| Skill | How It Shows Up |
|---|---|
| State what E[u\|T]=0 means in an RCT context | MC distractor, possibly short answer |
| Compute ATE from group means | "Given these two means, what's the ATE?" |
| Recognize that `regress Y T` gives ATE as coefficient on T | MC, reading Stata output |
| Distinguish ITT from ATE | MC, definitely tested (sample Q4) |
| Know ITT = ATE requires perfect compliance | MC, tested (sample Q4) |
| Know attrition = any dropout from observation | MC, tested (sample Q5) |
| Explain why randomization solves OVB | Short answer or MC distractor |
| Identify when an RCT is NOT appropriate | MC (ethics, cost examples) |
 
---
 
## 10. FRQ-Ready Language Bank
 
Copy these phrasings if an open-ended question comes up:
 
**On why RCTs work:**
> "Random assignment of treatment makes treatment status uncorrelated with all other variables, including unobserved confounders. This satisfies the zero conditional mean assumption E[u|T] = 0 by design, so the estimated coefficient on treatment is an unbiased estimate of the average treatment effect."
 
**On ITT vs ATE:**
> "The intent-to-treat (ITT) estimator measures the effect of treatment assignment. The average treatment effect (ATE) measures the effect of treatment receipt. These differ when compliance is imperfect, and ITT is a biased estimate of ATE in that case."
 
**On adding controls to an RCT:**
> "Adding pre-treatment controls is not necessary for unbiasedness because randomization ensures balance in expectation. However, controls can improve precision by reducing residual variance, and they correct for small chance imbalances in the realized sample."
 
**On attrition:**
> "Attrition is a threat to internal validity when it is systematically related to treatment status or outcomes. Non-random attrition breaks the comparability established by randomization and reintroduces selection bias."
 
**On when NOT to randomize:**
> "Randomization is inappropriate when it would require exposing subjects to serious harm (ethical violation) or when the intervention is infeasible at scale (cost or time constraint). In such cases, quasi-experimental methods like IV, DiD, or RD are used as second-best alternatives."
 
---
 
## Bottom Line
 
Lecture 15 is the conceptual anchor for the entire causal inference half of your course. Every later topic (IV, panel data, DiD, RD) is really answering the question: *what do you do when you can't randomize?* Nail the RCT logic here and everything downstream is easier.
