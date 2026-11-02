# econ5200-lab10-causal-logic
# Causal Logic and DAGs: Diagnosing Bad Controls

## Objective

I wanted to see how the choice of control variables changes a regression estimate, so I built cases where I knew the true effect in advance and checked what happens when I condition on the wrong things.

## Methodology

- Simulated a case where the true effect was zero and a variable was caused by both the treatment and the outcome, then compared the estimate with and without that variable in the regression.
- Simulated a second case with a true effect of zero where the control was measured before treatment, to test whether timing alone makes a control safe.
- Built a mediator case with a known total effect of 4.0 and a known direct effect of 1.0, and ran it both with and without the mediator in the model to recover each quantity separately.
- Compared the LaLonde job-training experiment against an observational version of the same question, substituting CPS comparison units for the randomized control group, and estimated the effect before and after adjusting for covariates.
- Wrote and used `dag_utils.py`, which takes a DAG and returns the adjustment sets that identify the effect of interest.
- Asked an AI to propose a control set for a job-training study, ran the regression with its set, checked the set against the study's DAG, revised my prompt once, and ran it again.

## Key Findings

**Conditioning on a common effect of treatment and outcome creates an association from nothing.** The estimate moved from -0.012 to -0.593 once that variable was added, against a true effect of zero. R-squared went up to 0.585 at the same time, so the fit statistic rewarded the model that gave the wrong answer. I can't use R-squared to tell me whether a control belongs in the regression.

**Measuring a control before treatment does not make it safe.** My pre-treatment control still pulled the estimate to -0.201 when the true effect was zero. What matters is the variable's position in the causal structure, not when it was recorded.

**Total and direct effects are different quantities and need different models.** Leaving the mediator out recovered the total effect at 3.978 against a truth of 4.0. Putting it in recovered the direct effect at 0.988 against a truth of 1.0. Neither model is wrong; they answer different questions.

**The LaLonde comparison shows how much the control set is doing.** The experimental benchmark is $1,794. Using CPS controls and no covariate adjustment, I got $8,498, which overstates the benchmark by roughly a factor of five. After covariate adjustment the same data gave $699, which undershoots it. Adjustment moved the estimate a long way but did not land it on the experimental number.

**The AI's first control set was wrong and the DAG caught it.** Its initial set produced 3.622 against a true effect of 4.0. I checked the set against the study's DAG, revised my prompt once, and the second set produced 4.047. The estimate from the first set looked reasonable on its own, so I only knew it was off because I had the DAG and the true effect to check it against.
