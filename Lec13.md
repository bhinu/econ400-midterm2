
# LECTURE 13: DATA ISSUES
 
## Big Picture: Internal vs External Validity
 
Before we talk about what can go wrong, your professor sets up a framework: two kinds of "validity" that any study can be judged on.
 
**Internal validity:** Did we do a good job estimating the parameter for the population we're actually studying? This is about bias. If OLS is biased for the population in our study, we are not internally valid.
 
**External validity:** Can the findings from our studied population be generalized to a different population? If we studied public high schools in California in 2001, can we draw conclusions about public high schools in Wisconsin in 2026? This is a judgment call about how similar the contexts are.
 
Midterm 1 was all about internal validity (did OLS estimate the parameter correctly). Midterm 2 adds external validity to the picture, but most of the exam still lives in internal validity land. The question is: what new things can break OLS?
 
We already know omitted variables break Assumption #1 (E[u|X] = 0). Lecture 13 adds three more things that can break OLS.
 
## The Three Data Issues to Know
 
1. Measurement error
2. Missing data / non-random samples (selection bias)
3. Outliers
We'll cover each one, tying back to which assumption it violates and what bias it creates.
 
---
 
## 1. Measurement Error
 
Your professor uses this running example: we want to estimate how calories eaten affects body weight.
 
E(weight | Calories Eaten) = α₀ + α₁ Calories Eaten
 
The key question is, what happens if either weight OR calories is not measured perfectly? The answer depends entirely on WHICH variable is mismeasured, because Y and X behave very differently.
 
### 1a. Measurement Error in Y (the Dependent Variable)
 
**The setup on the slides:**
 
The regression we WANT to run is:
Y = β₀ + β₁X + u
 
But we don't observe the true Y. We observe Ỹ (read "Y tilde"), which is the error-ridden version. So the regression we ACTUALLY run is:
 
Ỹ = β₀ + β₁X + [(Ỹ - Y) + u]
 
Notice what's in the brackets. The measurement error (Ỹ - Y) got absorbed into the error term along with the original u. Our new combined error term is [(Ỹ - Y) + u].
 
**Does Assumption #1 still hold?**
 
Assumption 1 requires E[error | X] = 0. Our new error is [(Ỹ - Y) + u], so we need:
 
E[(Ỹ - Y) + u | X] = E[(Ỹ - Y) | X] + E[u | X] = 0
 
The second piece, E[u | X], is zero by our original assumption. So we just need the FIRST piece, the measurement error piece, to also be zero given X.
 
**Two cases:**
 
**Good case:** E[(Ỹ - Y) | X] = 0
 
In plain English: knowing X doesn't tell us anything about how wrong the Y measurement is. If we know someone ate 3000 calories, we can't predict whether they over-reported or under-reported their weight. The measurement error is just noise, uncorrelated with X. OLS stays unbiased.
 
**Bad case:** E[(Ỹ - Y) | X] ≠ 0
 
In plain English: the measurement error in Y depends on X. Example from the slides: people with high caloric intake systematically over-report OR under-report their weight. Now knowing X tells us something about the ME, which means it sneaks into the error term in a way that correlates with X. Assumption #1 is violated. OLS is biased, and the direction depends on the specifics.
 
**The bottom line on ME in Y:** it's usually not fatal. If the error is random with respect to X (the "good case"), your estimates are still unbiased, just noisier.
 
### 1b. Measurement Error in X (the Independent Variable)
 
This one is much nastier.
 
**The setup on the slides:**
 
The regression we WANT to run is:
Y = β₀ + β₁X + u
 
But we don't observe the true X. We observe X̃, the error-ridden version. The regression we can ACTUALLY run is:
 
Y = β₀ + β₁X̃ + [β₁(X - X̃) + u]
 
Notice the structure of the new error term: [β₁(X - X̃) + u]. There's an extra piece β₁(X - X̃) that scales with β₁ itself. This is where things get ugly.
 
**Does Assumption #1 still hold?**
 
We need E[β₁(X - X̃) + u | X̃] = 0, which becomes:
 
β₁ E[(X - X̃) | X̃] + E[u | X̃] = 0
 
Again the second piece is zero by assumption. The question is whether E[(X - X̃) | X̃] = 0, meaning does knowing the OBSERVED X̃ tell us anything about the measurement error?
 
**The slides walk through three cases:**
 
**Perfect case (crossed out on slide 17 with a big X):** E[(ME in X) | X̃] = 0
This would mean the observed X̃ tells us nothing about the measurement error. But this is basically never true. If you observe a very high X̃, that's probably partly because the observation has a large positive measurement error. So the perfect case is essentially impossible.
 
**Good case (aka "classical measurement error"):** E[(ME in X) | X] = 0
Note this is conditioning on the TRUE X, not the observed one. It says: knowing the true value of X doesn't tell us anything about how wrong our measurement is. This is the standard assumption people make, and it leads to attenuation bias.
 
**Bad case:** E[(ME in X) | X] ≠ 0
The measurement error systematically depends on the true X. Bias is in an unknown direction.
 
**What is attenuation bias?**
 
This is the big concept. Under classical measurement error, your OLS slope estimate is biased TOWARD ZERO.
 
The slides give three pictures on slides 18-20:
- **Negative/downward bias:** if true β = 5, your estimate is expected to be less than 5 (could be 3, 0, or even -2). The whole number line to the left of 5.
- **Positive/upward bias:** if true β = 5, your estimate is expected to be greater than 5. The whole number line to the right of 5.
- **Attenuation bias (bias toward zero):** if true β = 5, your estimate is expected to be BETWEEN 0 AND 5. Shrunk toward zero but same sign. If true β = -5, your estimate is between -5 and 0.
Attenuation bias is a SPECIFIC kind of bias: the estimate is closer to zero than the truth, but on the same side of zero.
 
**Intuition for why attenuation bias happens:** imagine true X is "true calories eaten" and you measure it with random noise. The noise adds variation to your regressor that has nothing to do with Y. So your regression line looks flatter than it should. It gets pulled toward zero.
 
**The calories example from slides:** the video clip shows that people UNDER-report calories, AND the under-reporting is probably correlated with true intake (people who eat a lot under-report more). That's a BAD case, not classical. So the direction of bias depends on the specifics.
 
### 1c. Summary Table
 
Your professor's summary table from slide 24:
 
|                    | ME in Y         | ME in X                       |
|--------------------|-----------------|-------------------------------|
| Well-behaved ME    | Unbiased        | Attenuation Bias              |
| Badly-behaved ME   | Biased (unknown direction) | Biased (unknown direction) |
 
"Well-behaved" means E(ME | X) = 0, aka classical measurement error. "Badly-behaved" means E(ME | X) ≠ 0.
 
**This exact table is what the exam tests.** Sample paper Q2 says "there is measurement error in Y and knowing X doesn't tell us anything about the expected value of this measurement error." That's the well-behaved ME in Y row: **UNBIASED**. Answer B.
 
### 1d. How to Answer Measurement Error FRQ
 
If the exam asks "what happens if X is measured with error":
1. Write out the regression with the error-ridden X̃
2. Identify that the ME becomes part of the error term: β₁(X - X̃)
3. State whether it's classical (E[ME|X]=0) or not
4. If classical: attenuation bias (estimate toward zero)
5. If not classical: bias in unknown direction
If ME is in Y:
1. Write out the regression with error-ridden Ỹ
2. Identify ME becomes part of the error term
3. If E[ME|X] = 0: OLS unbiased
4. If E[ME|X] ≠ 0: biased in unknown direction
---
 
## 2. Missing Data and Non-Random Samples
 
This is the sample selection bias section. Your professor frames it as: can missing data cause a violation of Assumption #2 (i.i.d. random sampling)?
 
### 2a. The Phone Survey Example
 
From slide 26: a political telephone poll has a response rate of 29% to 32%. Are we working with a simple random sample?
 
No, because the 70% who didn't respond might be systematically different from the 30% who did (maybe busier people, younger people, people without landlines). If they're different in ways that relate to Y (voting intentions), our sample is biased.
 
### 2b. The Key Distinction (slide 27)
 
**Missing completely at random:** those with missing data are identical in every way to those with observed data. In this case, ignore it, OLS is fine.
 
**Missing in a non-random way (correlated with Y or X):** this creates selection bias. OLS can be biased.
 
### 2c. The Mroz Example (slides 28-29)
 
Your professor uses the classic Mroz dataset. Setup:
- Total women in dataset: 753
- In labor force (inlf = 1): 428
- Not in labor force (inlf = 0): 325
You want to regress wage on education. But wage is only observed for women in the labor force (because women not working have no wage to observe). So when Stata runs the regression, it uses only 428 observations.
 
**The bias issue:** the 428 women in the labor force are NOT a random sample of all women. They self-selected into working. Maybe the ones who chose to work have higher underlying motivation, which also affects wages. Now the sample is non-random with respect to the variable we care about.
 
**Two perspectives (slide 29):**
 
**Perspective 1:** "I want to learn about all U.S. women." Then the selected sample of 428 workers causes selection bias. Not internally valid for that population.
 
**Perspective 2:** "I only want to learn about women in the labor force." Then 428 workers is a perfectly fine sample of that smaller population. Internally valid (but externally limited).
 
**Is reframing always fine?** Sometimes yes, sometimes no. Depends on what question you actually want to answer.
 
### 2d. The Cartoon Example (slide 30)
 
"Raise your hand if you're familiar with selection bias" at a statistics conference. Obviously not a random sample of the U.S. population. But if the speaker's actual goal is just "do MY conference attendees know this?", it's fine.
 
### 2e. The Marathon Example (slide 31)
 
The NBC News article about marathon runners and arthritis. They surveyed runners in the 2019/2021 Chicago Marathon. Findings: knee arthritis wasn't associated with running miles.
 
The problem: their CONCLUSION generalizes beyond the studied population ("long-distance running doesn't cause arthritis"). But their sample was specifically people who completed a marathon. People who got arthritis from running probably dropped out of running and didn't run the marathon. The sample is selected on the outcome. This is textbook survivorship bias, a form of sample selection bias.
 
### 2f. How to Answer Selection Bias FRQ
 
If the exam asks about selection bias:
1. Identify the selection rule (how did observations enter or leave the sample?)
2. Ask whether that selection rule is related to Y
3. If yes, explain that the sample is not a simple random sample of the population of interest
4. Consequence: OLS estimates may be biased for the population of interest
5. Possible fix: redefine the population of interest to match the sample (but this limits external validity)
---
 
## 3. Outliers and Least Absolute Deviation
 
### 3a. The Infant Mortality Example
 
From slides 32-37: regression of infant mortality on per capita income for 50 states + DC.
 
There's a clear outlier: DC has very high infant mortality relative to its income. The question is what to do with it.
 
**With the outlier (slide 34):**
- Coefficient: 0.42 (positive)
- p-value: 0.808 (not statistically significant)
- Interpretation: "As per capita income increases by $10,000, infant mortality increases by 0.4 per thousand." (but statistically insignificant)
**Without the outlier (slide 35):**
- Coefficient: -1.29 (negative)
- p-value: 0.047 (statistically significant)
- Interpretation: "As per capita income increases by $10,000, infant mortality decreases by 1.29 per thousand." (statistically significant)
**Completely different story!** The outlier flips both the sign and the significance.
 
### 3b. Should You Drop the Outlier?
 
Your professor does NOT give a black-and-white answer. The guidance:
 
- If the outlier is a clear data entry error, fix it or drop it
- If it's legitimately measured data, dropping it means throwing away a real observation because you don't like how it looks
- The DC example is a real place with real data. Dropping it means your conclusion is "per capita income reduces infant mortality among U.S. states, excluding DC." That's a narrower claim.
The slide 37 question "does it affect your decision if I tell you the outlier is DC?" hints that you should be uncomfortable just dropping valid data.
 
### 3c. Why is OLS So Sensitive to Outliers? (slide 40)
 
OLS minimizes Σû²ᵢ, the sum of SQUARED residuals. If an outlier has residual 10, that contributes 100 to the objective function. One outlier can dominate everything else. OLS will tilt the line hard to reduce that squared residual, at the cost of fitting the rest of the data worse.
 
### 3d. LAD as an Alternative
 
**Least Absolute Deviation (LAD)** minimizes Σ|ûᵢ|, the sum of ABSOLUTE residuals. An outlier with residual 10 contributes 10 to the objective function, not 100. Outliers have proportional weight, not disproportionate weight.
 
**The infant mortality LAD result (slide 42):**
- Coefficient: -0.48
- p-value: 0.586 (not significant)
This is a "middle ground" answer between the with-outlier OLS (+0.42) and without-outlier OLS (-1.29). LAD pays less attention to the outlier than OLS does, but doesn't throw it away entirely.
 
### 3e. Why Isn't LAD the Default? (slide 41)
 
Two reasons:
1. **No closed-form solution.** Because the absolute value function has a kink at zero, you can't just take a derivative and solve. You have to iterate computationally.
2. **Historical:** before cheap computing, this was prohibitive. OLS won because it had a formula.
Over the last 30 years, LAD has grown in popularity as compute got cheap.
 
### 3f. The Recommended Approach
 
Your professor's advice from slide 39:
1. Run OLS both with and without the outlier
2. If results are similar, great, move on
3. If results differ substantially, disclose both and let the reader know
4. Consider LAD as a middle-ground alternative
**This is exactly sample paper Q3 answer C.** "It can be informative to run both OLS and LAD to determine the extent to which the outlier influences the regression results." Not dropping it, not keeping it blindly, but diagnosing the outlier's influence.
 
---