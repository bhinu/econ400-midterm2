# Lecture 12: Interaction Terms

Note: This is technically pre-midterm-1 material, but **MC Q11 on the sample midterm tests interaction terms directly**, so it's fair game for Thursday. Worth 30 minutes.

## The big idea
An interaction term lets the effect of one variable depend on the value of another. Without an interaction, you're forcing the slope (or intercept gap) to be constant across groups or across values. With an interaction, you let it vary.

The model:
```
Y = β0 + β1*X1 + β2*X2 + β3*(X1 × X2) + u
```

Take the partial derivative with respect to X1:
```
∂Y/∂X1 = β1 + β3*X2
```

The effect of X1 on Y now depends on X2. That's the whole point. β3 is the "how much does the slope on X1 change as X2 changes" coefficient.

## The three types of interactions

| Type | Example | Use case |
|---|---|---|
| Dummy × Dummy | female × black | Letting wage gap differ by race |
| Dummy × Continuous | female × experience | Different slopes for different groups (glass ceiling) |
| Continuous × Continuous | ecoprc × faminc | Effect of price depends on income |

Same math, different interpretation.

---

## Type 1: Dummy × Dummy

### Setup
Without interaction:
```
E[wage|female, black] = β0 + β1*female + β2*black
```

This forces the gender wage gap to be the **same for whites and blacks** (both equal β1). That's an assumption, not a finding.

### Adding the interaction
```
E[wage|female, black] = γ0 + γ1*female + γ2*black + γ3*(female × black)
```

### The four cells
| | White | Black |
|---|---|---|
| Male | γ0 | γ0 + γ2 |
| Female | γ0 + γ1 | γ0 + γ1 + γ2 + γ3 |

### What each coefficient tells you
- γ1 = gender wage gap **for whites** (white female - white male)
- γ1 + γ3 = gender wage gap **for blacks** (black female - black male)
- γ3 = **difference in the gender wage gap** between blacks and whites

If γ3 ≠ 0, the gender wage gap differs by race.

### Stata example from lecture
```
female:        -2.305 (0.473)
black:         -1.771 (0.699)
femaleXblack:   1.337 (1.126)
```

Read: white women earn ~$2.30/hr less than white men. Black women earn (-2.305 + 1.337) = ~$0.97/hr less than black men. The gap is smaller (less negative) for blacks. But γ3's t-stat is 1.19 (p=0.236), so we can't reject γ3 = 0. Don't have enough evidence to say the gaps actually differ by race.

---

## Type 2: Dummy × Continuous (THE MOST IMPORTANT TYPE FOR THE EXAM)

### Motivation: the glass ceiling hypothesis
The model without interaction:
```
E[wage|female, exper] = γ0 + γ1*female + γ2*exper
```

Return to experience for both groups: γ2. Same slope. The female dummy just shifts the intercept down. There's no "glass ceiling" possible in this spec.

### Two ways to relax this

**Approach A: stratify (run two separate regressions)**
```
E[wage|Male, exper]   = β0_m + β1_m * exper
E[wage|Female, exper] = β0_f + β1_f * exper
```

Different intercepts, different slopes. But then testing whether the entire wage profile is the same becomes annoying because the coefficients are from different regressions.

**Approach B: one regression with interaction (preferred)**
```
E[wage|female, exper] = α0 + α1*female + α2*exper + α3*(female × exper)
```

Plug in female=0:
```
E[wage|male, exper] = α0 + α2*exper
```

Plug in female=1:
```
E[wage|female, exper] = (α0 + α1) + (α2 + α3)*exper
```

So:
- Male intercept = α0
- Female intercept = α0 + α1
- Male slope = α2
- Female slope = α2 + α3

### What each coefficient tells you (memorize this)
- α1 = **difference in intercepts** (female - male)
- α3 = **difference in slopes** (female - male)
- α2 = return to experience **for males** (the omitted/baseline group)
- α2 + α3 = return to experience **for females**

This is exactly Q11 on the sample midterm.

### Hypothesis tests you should know

**Test 1: Is the entire wage profile the same for men and women?**
H0: α1 = 0 AND α3 = 0 (joint test, F-test, two restrictions)

In Stata: `test female femaleXexper`

If you reject H0, the wage profile differs in some way (intercept, slope, or both).

**Test 2: Are the returns to experience the same for men and women?**
H0: α3 = 0 (single t-test on the interaction coefficient alone)

This is what most papers actually care about. The slope, not the intercept, captures things like the glass ceiling.

**Test 3: Is there no gender wage gap at zero experience?**
H0: α1 = 0 (single t-test on the female dummy)

### Worked example
From the lecture's wage data:
```
female:           -0.765  (0.829)   [α1: intercept gap, not significant on its own]
exper:             0.081  (0.026)   [α2: male slope, significant]
femaleXexper:     -0.080  (0.038)   [α3: difference in slopes, significant at 5%]
```

Male return to experience = 0.081 ($0.08/hr per year of exp).
Female return to experience = 0.081 + (-0.080) = ~0.001 ($0/hr per year).

Translation: men's wages rise with experience, women's don't. **Glass ceiling supported by the data.**

The joint F-test of "wage profiles are identical" gave F=15.53, p<0.001. Reject the null.

---

## Type 3: Continuous × Continuous (the eco-apples example)

### The setup
Two hypotheses about eco-apple demand:
1. High-income HH buy more eco-apples regardless of price (intercept shifter)
2. High-income HH are less price-responsive (slope shifter for price)

Without interaction:
```
ecolbs = β0 + β1*ecoprc + β2*faminc + u
```

This handles hypothesis 1 (β2 captures whether income shifts demand up). It does **NOT** handle hypothesis 2. The price coefficient β1 is forced to be the same regardless of income.

### With interaction
```
ecolbs = β0 + β1*ecoprc + β2*faminc + β3*(ecoprc × faminc) + u
```

Take partial with respect to price:
```
∂ecolbs/∂ecoprc = β1 + β3*faminc
```

The price elasticity now depends on income. β3 tells you how much the price response changes per unit of income.

### Stata results from the lecture
```
ecoprc:    -1.611  (0.394)   [price coefficient at faminc=0]
faminc:    -0.013  (0.006)   [income coefficient at ecoprc=0]
epXfi:      0.015  (0.006)   [interaction, significant at p=0.017]
```

Price effect at family income = 50 ($50K):
```
∂ecolbs/∂ecoprc = -1.611 + 0.015*50 = -1.611 + 0.75 = -0.86
```

Price effect at family income = 100 ($100K):
```
∂ecolbs/∂ecoprc = -1.611 + 0.015*100 = -1.611 + 1.50 = -0.11
```

Translation: low-income HHs are very price-responsive (a $1 price increase drops purchases by ~0.86 lb). High-income HHs are barely price-responsive (a $1 price increase drops purchases by ~0.11 lb). Hypothesis 2 supported.

### Watch out: interpreting main effects in continuous × continuous
β1 = -1.611 is the price effect when **faminc = 0**. Not the average price effect. Not the price effect at the mean.

If you want "the price effect for a typical buyer," plug in the mean of faminc.

### Centering trick (good to know)
If you center faminc around its mean before creating the interaction:
```
faminc_c = faminc - mean(faminc)
ecolbs = β0 + β1*ecoprc + β2*faminc_c + β3*(ecoprc × faminc_c) + u
```

Now β1 is the price effect at the **mean** family income, which is a more interpretable baseline. Doesn't change β3 or its t-stat.

---

## The dummy variable trap for interactions

Suppose someone tries:
```
E[wage] = α0 + α1*female + α2*exper + α3*(female × exper) + α4*(male × exper)
```

They excluded the male dummy thinking they'd avoided the trap. But:
```
female × exper + male × exper = (female + male) × exper = 1 × exper = exper
```

So the interactions are perfectly collinear with `exper`. Stata will drop one of them.

### Two valid alternatives

**Option A: standard form**
```
α0 + α1*female + α2*exper + α3*(female × exper)
```
Male is the omitted group. Male slope = α2. Female slope = α2 + α3.

**Option B: no constant, both groups explicit**
```
α1*female + α2*male + α3*(female × exper) + α4*(male × exper)
```
No intercept term. α1 = female intercept, α2 = male intercept. α3 = female slope, α4 = male slope.

Both estimate identical regression lines. Different parameterization. The "right" one depends on which coefficients you want directly.

---

## MC Q11 from the sample midterm (THE EXAM HIT)

> In the regression model `Y_i = β0 + β1*X_i + β2*D_i + β3*(X_i × D_i) + u_i` where X is continuous and D is binary, the parameter β3:
>
> A) indicates the slope of the regression when D = 1
> B) has a standard error that is not normally distributed even in large samples since D is not normally distributed
> C) **indicates the difference in the slopes across the two groups**
> D) has no meaning since (X_i × D_i) = 0 when D_i = 0

**Answer: C**

### Why each wrong answer is wrong
- **A is wrong**: When D=1, the slope is β1 + β3, not β3 alone. β3 is the *difference* in slopes, not the slope itself.
- **B is wrong**: D not being normally distributed doesn't matter. OLS coefficient SE asymptotic normality comes from CLT applied to the residuals, not the regressors. Many regressors (dummies, log-transformed variables, etc.) aren't normal.
- **D is wrong**: When D=0, the term (X × D) = 0, but β3 still has meaning. It tells you what happens to the slope as soon as you flip D to 1. The fact that the term is zero in one half of the sample is exactly what creates the variation needed to identify β3 (as you compare X's effect across the D=0 and D=1 groups).

### How to spot this question type
Anything of the form `Y = β0 + β1*continuous + β2*binary + β3*(continuous × binary)` is the wage-gender setup with relabeled variables. The interpretation is fixed:
- β1 = slope for the omitted group (D=0)
- β2 = intercept gap
- β3 = slope gap

If they give a Stata output and ask "what is the return to education for women?", the answer is β1 + β3 (assuming D = female).

---

## Quick reference cheat sheet

### General rule for interpreting interactions
For model `Y = β0 + β1*X1 + β2*X2 + β3*(X1 × X2)`:
- ∂Y/∂X1 = β1 + β3*X2 (effect of X1 depends on X2)
- ∂Y/∂X2 = β2 + β3*X1 (effect of X2 depends on X1)
- β1 = effect of X1 when X2 = 0
- β2 = effect of X2 when X1 = 0
- β3 = how the effect of X1 changes per unit of X2 (also: how the effect of X2 changes per unit of X1)

### When to use which test
| Want to test | H0 | Test type |
|---|---|---|
| No interaction (slopes equal) | β3 = 0 | t-test |
| No effect at all (no level diff, no slope diff) | β2 = 0 AND β3 = 0 | F-test (joint) |
| Slope is zero for the D=1 group | β1 + β3 = 0 | t-test on linear combination (`lincom` in Stata) |

### Stata reminder
```stata
generate femaleXexper = female*exper
regress wage female exper femaleXexper, robust

* Joint test
test female femaleXexper

* Linear combination test (return to exp for women)
lincom exper + femaleXexper
```

### Common interpretation mistakes to avoid
1. Treating β1 (main effect of X1) as "the effect of X1 in general." It's only the effect when X2 = 0. If X2 = 0 is meaningless (e.g., experience = 0, or income = 0 in a sample of $30K+ HHs), the main effect coefficient is hard to interpret on its own.
2. Forgetting to multiply through. Female slope is α2 + α3, not just α3.
3. Confusing β3's significance with whether the interaction is "needed." Always look at the joint test as well.
4. Reading the sign of the interaction without thinking about scale. With faminc in $1000s, β3 = 0.015 means $0.015 increase per $1000 of income, not 1.5%.
