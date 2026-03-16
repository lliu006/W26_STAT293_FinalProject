# Joint Longitudinal-Survival Analysis of Postoperative Left Ventricular Mass Index and Mortality After Aortic Valve Replacement 

## University of California, Riverside
## Winter 2026
## Instructor: Dr. Esra Kürüm

**Authors:** Ryan Kitagawa, Linlin Liu, and Nancy Lopez <br>
**Date:** March 18, 2026

## Overview

This project investigates the association between postoperative trajectories of left ventricular mass index (LVMI) and mortality following aortic valve replacement. Using data from an observational study with repeated measurements, we apply a joint longitudinal-survival modeling framework to assess whether the current level of LVMI is associated with the risk of mortality. We further extend the analysis by evaluating alternative association structures, such as slope parameterization, and conducting sensitivity analyses to assess the robustness of the findings. This work extends classical linear mixed-effects models (LMMs) and Cox proportional hazards (PH) models into a unified framework that accounts for within-subject correlation in repeated LVMI measurements, informative dropout due to death, and dynamic association between biomarker progression and survival.

## Key Result

The joint model suggests that higher postoperative LVMI levels are associated with increased mortality risk, while the rate of change in LVMI does not provide additional prognostic information beyond the current level.

## Data Description

The dataset consists of 988 observations from 256 unique patients who underwent aortic valve replacement surgery. Each row represents a follow-up visit.

Key variables include:

- `num` - patient ID,
- `time` - observed time point, with surgery date as the time origin (years),
- `log.lvmi` - natural log transformation of left ventricular mass index (LVMI) at follow-up visit,
- `fuyrs` -  maximum follow-up time, with surgery date as the time origin (years), and
- `status` - censoring indicator (1 = died and 0 = lost at follow-up).

Baseline covariates are selected based on previous studies and include:

- `sex` - 0 = male, 1 = female,
- `age` - age at surgery (years),
- `bsa` - preoperative body surface area (BSA),
- `lvh` - preoperative left ventricular hypertrophy (LVH),
- `prenyha` - preoperative New York Heart Association (NYHA) classification (1 = I/II, 3 = III/IV),
- `redo` - previous cardiac surgery,
- `dm` – preoperative diabetes,
- `lv` – left ventricular ejection fraction (1 = good, 2 = moderate, 3 = poor), and
- `hs` – valve type (1 = homograft, 0 = stentless porcine).
  
## Objectives

- Fit a shared-parameter joint model to quantify the association between the latent LVMI trajectory and the hazard of death.
- Evaluate whether the rate of change of LVMI adds prognostic value beyond the current LVMI level.
- Conduct model diagnostics and sensitivity analyses.
  
## Methodology

### Longitudinal submodel using LMM

The longitudinal evolution of LVMI was modeled using a LMM, which accounts for repeated measurements within patients and subject-specific heterogeneity in trajectories.

$$
Y_{ij} = m_i(t_{ij}) + \varepsilon_{ij}, \ \ \ \varepsilon_{ij} \overset{iid} \sim \mathcal{N}(0, \sigma^2)
$$

$$
m_i(t) = \beta_0 + \beta_1 t + \boldsymbol{\beta}^\top \mathbf{Z}_i + b_{0i} + b_{1i} t,
$$

where

- $Y_{ij}$ represents the observed LVMI measurement for subject $i$ at time $t_{ij}$,
- $\textbf{Z}_i$ contains baseline covariates, and
- $b_{0i}, \ b_{1i}$ represent subject-specific random intercepts and slopes, allowing subject-specific LVMI trajectories over time.

### Survival submodel using Cox PH

The time-to-event process was modeled using a Cox PH model and is represented as

$$
h_i(t) = h_0(t) \text{exp}(\boldsymbol{\gamma}^\top \mathbf{X}_i),
$$

where

- $h_i(t)$ is the hazard of death at time $t$ for subject $i$,
- $h_0(t)$ is the baseline hazard,
- $\mathbf{X}_i$ represents baseline covariates, and
- $m_i(t)$ is the latent true LVMI trajectory from the longitudinal model.

### Joint longitudinal-survival model

The joint longitudinal-survival model is written as

$$
h_i(t) = h_0(t) \text{exp}(\boldsymbol{\gamma}^\top \mathbf{X}_i + \alpha m_i(t)).
$$

The parameter $\alpha$ quantifies the association between the underlying LVMI level and the risk of mortality. A positive value of $\alpha$ indicates that higher LVMI levels are associated with increased mortality risk.

### Slope parameterization

To assess whether the rate of change of LVMI carries prognostic information beyond its current level, we extended the survival model to include the instantaneous slope of the longitudinal trajectory:

$$
h_i(t) = h_0(t) \text{exp}(\boldsymbol{\gamma}^\top \textbf{X}_i + \alpha_1 m_i(t) + \alpha_2 m_i'(t)).
$$

Here, $m_i'(t)$ represents the derivative or slope of the LVMI trajectory with respect to time, and $\alpha_2$ quantifies the association between the rate of change of LVMI and mortality risk. Clinically, two patients with the same LVMI value may have different prognoses if one is improving while the other is worsening. Including the slope parameter allows the model to capture this dynamic information. Current-value and slope parameterizations are commonly used in the dynamic prediction literature to represent different biological mechanisms linking longitudinal biomarkers to survival outcomes.

### Model assumptions and sensitivity analysis

Model diagnostics were conducted for both the longitudinal and survival submodels prior to fitting the joint model. For the LMM, we assessed residual versus fitted plots, Q-Q plots of residuals, and the distribution of random effects to evaluate normality and homoscedasticity assumptions. We also compared linear and quadratic time effects to verify that a linear time specification adequately captured LVMI trajectories.

For the survival submodel, the PH assumption was evaluated using Schoenfeld residual tests, and the functional form of continuous predictors was examined using Martingale residual plots. 

Sensitivity analyses were conducted by evaluating alternative survival model specifications and value-and-slope association structures to assess the robustness of the relationship between LVMI trajectory and mortality. 

## Repository Structure

This is the file layout used in this project:

```text
├── README.md                                  # Project description and workflow
│
├── heart.rdata                                # Dataset used in the project
│                               
├── STAT_293_Final_Project_EDA.Rmd             # All exploratory data analysis 
│
├── STAT_293_Final_Project_Modeling.Rmd        # All primary and extension modeling, model assumption checks, and sensitivity analyses
|
├── STAT_293_Final_Project_Presentation.pdf    # Compiled presentation PDF
├── STAT_293_Final_Project_Presentation.zip    # Prensetation source files
├── STAT_293_Final_Project_Report.pdf          # Compiled report PDF 
└── STAT_293_Final_Project_Report.tex          # Report source file        
```

## Running the Project

Required R packages include `dplyr`, `ggplot2`, `JM`, `MASS`, `nlme`, and `survival`.

### Exploratory data analysis (EDA)

Open and knit `EDA.Rmd` in RStudio to reproduce the EDA results.

### Modeling and sensitivity analyses

Open and knit `Modeling_and_Sens.Rmd` to reproduce the modeling workflow, including assumption checks and sensitivity analyses.

## Acknowledgements

- Dr. Kürüm for course instruction and project guidance
- Lim et al. (2008) for foundational work
- The developers of all R packages used in this project

Last Updated: March 16, 2026
