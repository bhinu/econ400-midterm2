# LECTURE 14: LIMITED DEPENDENT VARIABLES
 
## The Setup: Binary Dependent Variables
 
Up to now, Y has been continuous (wages, scores, heights). Lecture 14 tackles the case where Y is a dummy variable: either 0 or 1.
 
Your professor's main example is breast cancer risk:
br_cancer = 1 if the person has or had breast cancer, 0 otherwise
 
## The Linear Probability Model (LPM)
 
### 1a. Why LPM is Called That
 
Start with a normal regression:
br_cancer = β₀ + β₁ age + β₂ family_history + u
 
Take expected value:
E[br_cancer | age, family_history] = β₀ + β₁ age + β₂ family_history
 
Here's the trick (slide 5). For a DISCRETE 0/1 variable, the expected value equals the probability of being 1:
 
E[br_cancer | X] = Pr(br_cancer = 1 | X) × 1 + Pr(br_cancer = 0 | X) × 0
                 = Pr(br_cancer = 1 | X)
 
So the regression equation can be rewritten as:
 
**Pr(br_cancer = 1 | age, family_history) = β₀ + β₁ age + β₂ family_history**
 
That's it. The linear probability model is just an OLS regression with a binary Y, where we interpret the predicted value as a probability.
 
### 1b. Interpreting the Coefficients (slide 6)
 
**Intercept (β₀):** the probability of having breast cancer for a newborn woman with no family history. (age = 0, family_history = 0)
 
**Slope on a continuous variable (β₁):** the change in the PROBABILITY of breast cancer for a one-year increase in age, holding family history constant. This is a true marginal effect on probability because the relationship is linear.
 
**Slope on a dummy variable (β₂):** the DIFFERENCE in probability of breast cancer between someone with family history and someone without, holding age constant.
 
This is the same "gap vs marginal effect" language from midterm 1. In LPM, the dummy coefficient is a gap in probability.
 
### 1c. Using the LPM to Predict
 
From slide 7: suppose the estimated model is:
 
P̂r(br_cancer = 1) = 0.050 + 0.001·age + 0.030·family_history
 
For a 50-year-old woman with family history:
P̂r = 0.050 + 0.001(50) + 0.030(1) = 0.130 = 13% chance of breast cancer
 
## The LPM's Main Drawback (this is the sample paper Q1a answer)
 
### 2a. The Problem: Nonsensical Predicted Probabilities
 
This is the KEY problem with LPM. Because the model is linear, predicted probabilities can fall outside [0, 1]. A probability of -0.15 or 1.3 makes no sense.
 
### 2b. The Mroz Example (slides 9-13)
 
Your professor runs a labor force participation LPM on the Mroz data:
 
inlf = β₀ + β₁nwifeinc + β₂educ + β₃exper + β₄expersq + β₅age + β₆kidslt6 + β₇kidsge6 + u
 
Focusing on a specific type of household (nwifeinc=50, exper=5, age=30, kidslt6=1, kidsge6=0), the slide simplifies this down to:
 
Pr̂(inlf = 1 | educ) = -0.146 + 0.038·educ
 
Look at this. When educ = 0, the predicted probability of labor force participation is **-0.146**. That's a negative probability. Nonsensical.
 
When educ is very high (around 30 or so), the predicted probability would exceed 1. Also nonsensical.
 
**Slide 12 confirms in the actual data:**
- 16 observations have predicted probability < 0
- 17 observations have predicted probability > 1
Out of 753, that's only about 4% of the sample, but it's 4% of your output that's mathematically impossible.
 
### 2c. Why Linearity Causes This (slides 14-17)
 
Your professor uses a great SMBC comic intuition: "If she loves you more every day, then by linear regression she hated you before you met."
 
If something grows linearly forever, at some point it must have been negative (or will exceed any bound). That's unavoidable with linearity.
 
Applied to LPM: if education has any nonzero linear effect on labor force participation probability, then at low enough education, predicted probability goes below zero. At high enough education, it exceeds one. Unless the slope is exactly zero, this is unavoidable.
 
### 2d. How to Phrase This on the Exam
 
From sample paper Q1a: "What is the main disadvantage of a linear probability model?"
 
**Answer template:** "The main disadvantage of a linear probability model is that it can produce predicted probabilities that are less than zero or greater than one, which are nonsensical. This arises because the model imposes a linear relationship between X and the probability, and a linear function cannot be bounded within [0, 1] except in the degenerate case of a zero slope. In our Mroz example, 16 women had predicted probabilities below zero and 17 had predicted probabilities above one."
 
## The Non-Linear Probability Model Solution
 
### 3a. The Idea (slide 18)
 
Replace the linear predictor with something that's automatically bounded between 0 and 1:
 
Pr̂(Yi = 1 | Xi) = G(β̂₀ + β̂₁Xi)
 
where G is a "link function" that takes any real number and outputs a value in [0, 1].
 
We keep the linear index β̂₀ + β̂₁Xi (still has infinite range), but pass it through G to get a number in [0, 1].
 
**Key insight:** the probability is now a NON-LINEAR function of X, because G is non-linear.
 
### 3b. What Makes a Good Link Function
 
From the slides, a good G should be:
- Defined for any real input (infinite domain)
- Output between 0 and 1 (range [0, 1])
- Non-decreasing (higher input → higher probability)
- Continuously differentiable (so marginal effects exist)
### 3c. Probit: The Standard Normal CDF
 
From slide 19: use G = Φ, the cumulative distribution function of the standard normal distribution. This is the classic S-shaped curve. Takes any input from -∞ to +∞, outputs values in [0, 1], never exceeds the bounds.
 
Model:
Pr(Yi = 1 | Xi) = Φ(β̂₀ + β̂₁Xi)
 
Stata command:
```
probit y x, robust
```
 
### 3d. Logit: The Logistic Function
 
From slide 21: use G = exp(z) / (1 + exp(z)). Different formula, but also S-shaped, bounded in [0, 1], and non-decreasing.
 
Model:
Pr(Yi = 1 | Xi) = exp(β̂₀ + β̂₁Xi) / (1 + exp(β̂₀ + β̂₁Xi))
 
Stata command:
```
logit y x, robust
```
 
### 3e. Probit vs Logit (slide 23)
 
They look very similar. Both are S-curves. In practice, results are nearly identical. The choice is mostly disciplinary:
- **Economists tend to use probit**
- **Sociologists tend to use logit**
You can always run both and compare.
 
## Interpreting Probit and Logit (the trickiest part)
 
### 4a. The Marginal Effect Problem (slide 24)
 
In LPM:
∂Pr(Yi = 1 | Xi) / ∂X = β̂₁
 
The slope coefficient IS the marginal effect on probability. Easy.
 
In probit/logit:
∂Pr(Yi = 1 | Xi) / ∂X = G'(β̂₀ + β̂₁Xi) × β̂₁
 
The slope coefficient is NOT the marginal effect. The marginal effect is β̂₁ multiplied by G'(·), which is the derivative of the CDF (the PDF, evaluated at the linear index). This means the marginal effect depends on WHERE you are on the curve (i.e., it varies across observations).
 
**This is why parameter estimates from probit/logit are NOT directly comparable to LPM estimates.** They mean different things.
 
### 4b. Getting the Marginal Effect in Stata (slide 25)
 
Use the `margins` post-estimation command:
```
probit y x, robust
margins, dydx(*)
```
 
This computes the marginal effect averaged over all observations. Then you CAN compare to LPM.
 
### 4c. The Actual Comparison in the Mroz Example
 
**Comparing parameter estimates (slide 33):** totally different numbers.
- LPM educ coefficient: 0.038
- Logit educ coefficient: 0.221
- Probit educ coefficient: 0.131
These look wildly different, but it's because they're measuring different things (probability slope vs change in latent index).
 
**Comparing marginal effects (slide 34):** nearly identical!
- LPM: 0.038
- Logit: 0.0395
- Probit: 0.0394
When we compute the marginal effect from probit/logit (using margins), they line up almost perfectly with the LPM coefficient. This is reassuring: the three models tell basically the same story about how education affects probability.
 
### 4d. How to Write This on the Exam
 
From sample paper Q1c: "Are estimates of β₁ from the linear and non-linear probability model directly comparable?"
 
**Answer template:** "No, the parameter estimates are not directly comparable. In the linear probability model, β₁ is the marginal effect of X on the probability that Y = 1, because the derivative of a linear function is the coefficient itself. In a non-linear probability model (probit or logit), β₁ is the effect on the latent index β₀ + β₁X, which is then passed through the link function G. The actual marginal effect on probability is G'(β₀ + β₁X) × β₁, which depends on where on the curve we evaluate it and is generally different from β₁ alone. To compare across models, one should compute the average marginal effect from the non-linear model (using Stata's margins command), which is typically similar to the LPM coefficient."
 
## When to Use Each Model (slide 35, the bottom line)
 
Your professor's rule of thumb:
 
**Use LPM when:**
- Your main goal is to estimate marginal effects
- Predicted probabilities are not a focus
- You want simpler interpretation
- The results are often very close to probit/logit marginal effects anyway
**Use probit or logit when:**
- Your main goal is to produce predicted probabilities
- You can't live with some predictions outside [0, 1]
- You're modeling a rare outcome where LPM might predict poorly at the tails
**Summary bullet from slide 35:** "If the primary goal is to estimate marginal effects, then probably it's no big deal [to use LPM]. In this case, it's often sufficient (and more straightforward) to use a LPM. But if generating predicted probabilities is an important goal of your analysis, then nonsensical predicted values are probably a deal breaker. In this case, logit/probit is clearly more appropriate."
 
---
 
# Tying It All Back to the Sample Paper
 
## Lecture 13 → Sample Paper Q1, Q2, Q3
 
**MC Q1 (Sample selection bias):** answer D. Selection bias arises when data is missing in a non-random way. Lectures 13 slides 26-31.
 
**MC Q2 (ME in Y, well-behaved):** answer B. Unbiased. This is the top-left cell of the summary table on slide 24.
 
**MC Q3 (Outliers, OLS vs LAD):** answer C. Run both and compare. Lecture 13 slide 39.
 
## Lecture 14 → Sample Paper SA Q1
 
**Q1(a) main disadvantage of LPM:** nonsensical predicted probabilities (outside [0, 1]) because of linearity.
 
**Q1(b) how non-linear model resolves this:** uses a link function G with range [0, 1], so all predicted probabilities are bounded.
 
**Q1(c) are estimates comparable:** NO. LPM β is a marginal effect on probability; probit/logit β is an effect on the latent index. To compare, compute marginal effects via margins.
 
---
 
# Answer Scaffolds for Exam Day
 
## Measurement Error Question Template
1. Identify whether ME is in Y or X.
2. Write out the regression incorporating the ME (show it entering the error term).
3. Identify whether the ME is classical / well-behaved (E[ME|X] = 0).
4. Apply the 2×2 table:
   - Well-behaved + ME in Y → unbiased
   - Well-behaved + ME in X → attenuation bias (toward zero)
   - Badly-behaved in either → biased in unknown direction
5. Give substantive interpretation in context.
## Selection Bias Question Template
1. Describe how observations enter / leave the sample (the selection rule).
2. Argue whether the selection rule is correlated with Y.
3. If correlated: sample is non-random → selection bias → OLS biased for the broader population.
4. Note the option of reframing the population of interest to match the sample.
## LPM Disadvantage Template
1. Linear functional form unbounded.
2. Predicted probabilities can fall outside [0, 1].
3. These are nonsensical as probabilities.
4. Non-linear models (probit/logit) solve this by passing the linear index through a bounded CDF.
## Probit/Logit vs LPM Comparability Template
1. In LPM, β = marginal effect on probability. Straightforward.
2. In probit/logit, β = effect on latent index, NOT the marginal effect.
3. Marginal effect in probit/logit = G'(linear index) × β, varies across observations.
4. To compare: compute avg marginal effect from probit/logit using `margins, dydx(*)`.
5. Average marginal effects from probit/logit and LPM coefficients are usually similar.