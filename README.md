# Omitted Variable Bias in Neuromarketing Models: A Monte Carlo Simulation

## Question
Neuromarketing studies often use several biometric signals at once to predict how much people like a product. Some of these signals, such as pupil dilation and gaze duration, tend to move together. What happens when a model leaves one of them out?

This project tests how omitting a correlated predictor biases regression results, and how that bias grows as the correlation gets stronger.

## Setup
I simulated data from a known "true" model, so the correct answers are known in advance:

| Predictor | True effect on product liking |
|---|---|
| Frontal lobe activation | 4.0 (strongest) |
| Pupil dilation | 2.0 |
| Gaze duration | 1.5 |
| Heart rate | 0.5 (weakest) |

Pupil dilation and gaze duration are correlated with each other. Heart rate and frontal lobe activation are independent.

## Method
- **Monte Carlo simulation:** for each correlation level between pupil dilation and gaze duration (0.1 to 0.9), I generated 500 brand-new samples of 200 participants. That is 9 × 500 = 4,500 simulated datasets.
- On each dataset I fit two OLS regressions:
  - **Full model:** includes all four predictors.
  - **Restricted model:** leaves out gaze duration.
- For each run I recorded:
  - the estimated pupil dilation coefficient in both models,
  - whether the restricted model wrongly ranked pupil dilation above frontal lobe activation,
  - whether the restricted model's 95% confidence interval for pupil dilation contained the true value.

## Results

| Correlation | Avg. pupil coef. (full) | Avg. pupil coef. (restricted) | Wrong "strongest predictor" | 95% CI contains true value |
|---|---|---|---|---|
| 0.1 | 0.66 | 0.77 | 0% | 85% |
| 0.2 | 0.67 | 0.86 | 0% | 53% |
| 0.3 | 0.66 | 0.96 | 0% | 20% |
| 0.4 | 0.67 | 1.07 | 1% | 2% |
| 0.5 | 0.67 | 1.17 | 5% | 0% |
| 0.6 | 0.67 | 1.27 | 25% | 0% |
| 0.7 | 0.67 | 1.37 | 68% | 0% |
| 0.8 | 0.66 | 1.47 | 94% | 0% |
| 0.9 | 0.67 | 1.57 | 99% | 0% |

True pupil dilation coefficient: 0.667 (2.0 / 3, because the outcome was rescaled).

## Key findings
1. **The full model stays accurate** at every correlation level. Its average estimate sits right on the true value.
2. **The restricted model's bias grows steadily with correlation.** At 0.9, it more than doubles the true effect of pupil dilation.
3. **Confidence intervals fail early.** A correct 95% interval should contain the true value about 95% of the time. By a correlation of just 0.4, the restricted model's intervals almost never do. The model looks precise while being wrong.
4. **Conclusions about "what matters most" flip.** Past a correlation of about 0.6, the restricted model increasingly names pupil dilation as the strongest predictor, and at 0.9 it does so in 99% of runs.

**Takeaway:** leaving out a correlated variable doesn't just add noise. It can produce confident, wrong answers about which factors drive behavior.

## Limitations
- The data is simulated, not collected from real participants. This is intentional, since the true effects must be known to measure bias.
- The "strongest predictor" comparison uses raw coefficients on different measurement scales. Standardizing the predictors would make that comparison more rigorous.

## Author
Chiara Saladini, M.S. Applied Statistics & Psychometrics, Boston College
[LinkedIn](https://www.linkedin.com/in/chiara-saladini)
