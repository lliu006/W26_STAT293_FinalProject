# Joint Longitudinal-Survival Analysis of Postoperative Left Ventricular Mass Index (LVMI) and Mortality After Aortic Valve Replacement 

## Winter 2026
## Instructor: Dr. Esra Kürüm

**Authors:** Ryan Kitagawa, Linlin Liu, and Nancy Lopez <br>
**Date:** March 12, 2026

## Overview

This project investigates the association between postoperative trajectories of left ventricular mass index (LVMI) and time to death following aortic valuve replacement.

Using data from an observational study with repeated measurements, we apply joint longitudinal-survival modeling to assess wether:

- the current level of LVMI is associated with the risk of mortality, and
- the slope/rate of change of LVMI provides additional prognostic information.

This work extends classical linear mixed-effects model (LMM) and Cox proportional hazards (PH) model into a unified framework that accounts for:

- within-subject correlation in repeated LVMI measurements,
- informative dropout due to death, and
- dynamic association between biomarker progression and survival.

## Data Description

The dataset consists of 988 observations from 256 unique patients undergoing aortic valve replacement. Each row represents a follow-up visits.

Key variables include:

### Longitudinal variables

- `num` - patient ID
- `time` - observed time point, with surgery date as the time origin (years)
- `lvmi` - left ventricular mass index (LVMI) at follow-up visit
- `log.lvmi` - natural log transformation of LVMI 
  
### Survival variables

- `fuyrs` -  maximum follow-up time, with surgery date as the time origin (years)
- `status` - censoring indicator (1 = died and 0 = lost at follow-up)

Baseline covariates include age, sex, body surface area (`bsa`), preoperative LV function (`lv`), valve type (`hs`: homograft vs. stentless), etc.
  
## Objectives

- Fit a shared-parameter joint model (JM) to quantify the association between latent LVMI level and hazard of death.
- Evaluate whether the slope/rate of change of LVMI adds prognostic value beyond the current level.
- Perform model diagnostics and sensitivity analyses.
  
## Statistical Methodology

### Longitudinal submodel using LMM

$Y_{ij} = m_i(t_{ij}) + \varepsilon_{ij}, \ \ \ \varepsilon_{ij} \overset{iid} \sim \mathcal{N}(0, \sigma^2)$

$m_i(t) = \beta_0 + \beta_1t + \beta^\top \textbf{Z}_i + b_{0i} + b_{1i}t$

Random intercepts and slopes allow subject-specific trajectories. 

### Survival submodel using Cox PH

$Y_{ij} = m_i(t_{ij}) + \varepsilon_{ij}, \ \ \ \varepsilon_{ij} \overset{iid} \sim \mathcal{N}(0, \sigma^2)$

$h_i(t) = h_0(t) \text{exp}(\gamma^\top \textbf{X}_i + \alpha m_i(t))$,

where

- $m_i(t)$ is the latent true LVMI trajectory, and
- $\alpha$ quantifies the association between LVMI and hazard of death.

### Slope Parameterization

We extended the model to:

$h_i(t) = h_0(t) \text{exp}(\gamma^\top \textbf{X}_i + \alpha_1 m_i(t) + \alpha_2 m_i'(t))$.

Clinically, two patients with the same LVMI value may have different prognoses if one is improving while the other is worsening. Incorporating the slope parameter allowed us to assess whether dynamic LVMI progression carries additional prognostic information beyond the current value. This extension is supported in the dynamic prediction literature, where current-value and slope association structures are commonly used to capture different biological mechanisms linking longitudinal biomarkers to survival outcomes.

### Sensitivity Analysis

We evaluated additional baseline covariates in the survival model, alternative association structures, and PH assumption using Schoenfeld residuals. PH assumption was satifised, and LVMI-mortality association was robust to moderate model expansion.

## Repository Structure

├── data/
│   └── heart.csv
├── eda/
│   └── exploratory_analysis.R
├── models/
│   ├── lmm_model.R
│   ├── joint_model_value.R
│   ├── joint_model_slope.R
│   └── sensitivity_analysis.R
├── figures/
├── report/
└── README.md

## Software

R packages include:

- `dplyr`
- `ggplot2`
- `JM`
- `MASS`
- `naniar`
- `nlme`
- `survival`

## References

## Acknowledgements

