# Joint Longitudinal-Survival Analysis of Postoperative Left Ventricular Mass Index (LVMI) and Mortality After Aortic Valve Replacement 

## Winter 2026
## Instructor: Dr. Esra Kürüm

**Authors:** Ryan Kitagawa, Linlin Liu, and Nancy Lopez <br>
**Date:** March 12, 2026

## Overview

This project investigates the association between postoperative trajectories of left ventricular mass index (LVMI) and time to death following aortic valve replacement. Using data from an observational study with repeated measurements, we apply joint longitudinal-survival modeling to assess whether the current level of LVMI is associated with mortality risk. We further extend the analysis by evaluating alternative association structures, such as slope parameterization, and conducting sensitivity analyses to assess the robustness of the findings.

This work extends classical linear mixed-effects (LMM) and Cox proportional hazards (PH) models into a unified framework that accounts for within-subject correlation in repeated LVMI measurements, informative dropout due to death, and dynamic association between biomarker progression and survival.

## Key Result

The joint model suggests that higher postoperative LVMI levels are associated with increased mortality risk, while the rate of change in LVMI does not provide additional prognostic information beyond the current level.

## Data Description

The dataset consists of 988 observations from 256 unique patients undergoing aortic valve replacement. Each row represents a follow-up visit.

Key variables include:

- `num` - patient ID
- `time` - observed time point, with surgery date as the time origin (years)
- `lvmi` - left ventricular mass index (LVMI) at follow-up visit
- `log.lvmi` - natural log transformation of LVMI 
- `fuyrs` -  maximum follow-up time, with surgery date as the time origin (years)
- `status` - censoring indicator (1 = died and 0 = lost at follow-up)
- Baseline covariates include age, sex, body surface area (`bsa`), preoperative LV function (`lv`), valve type (`hs`: homograft vs. stentless), etc.
  
## Objectives

- Fit a shared-parameter joint model to quantify the association between the latent LVMI trajectory and the hazard of death.
- Evaluate whether the slope/rate of change of LVMI adds prognostic value beyond the current LVMI level.
- Conduct model diagnostics and sensitivity analyses.
  
## Statistical Methodology

### Longitudinal submodel using LMM

The longitudinal evolution of LVMI was modeled using a LMM, which accounts for repeated measurements within patients and subject-specific heterogeneity in trajectories.

$$
Y_{ij} = m_i(t_{ij}) + \varepsilon_{ij}, \ \ \ \varepsilon_{ij} \overset{iid} \sim \mathcal{N}(0, \sigma^2)
$$

$$
m_i(t) = \beta_0 + \beta_1 t + \boldsymbol{\beta}^\top \mathbf{Z}_i + b_{0i} + b_{1i} t,
$$

where

- $Y_{ij} represents the observed LVMI measurement for subject $i$ at time $t_{ij}$
- $\textbf{Z}_i$ contains baseline covariates, and
- $b_{0i}, \ b_{1i}$ represent subject-specific random intercepts and slopes.

Random intercepts and slopes allow subject-specific LVMI trajectories over time.

### Survival submodel using Cox PH

The time-to-event process was modeled using a PH survival model linked to the longitudinal LVMI trajectory.

$$
h_i(t) = h_0(t) \text{exp}(\gamma^\top \mathbf{X}_i + \alpha m_i(t)),
$$

where

- $h_i(t)$ is the hazard of death at time $t$ for subject $i$,
- $h_0(t)$ is the baseline hazard,
- $\mathbf{X}_i$ represents baseline covariates, and
- $m_i(t)$ is the latent true LVMI trajectory from the longitudinal model.

The parameter $\alpha$ quantifies the association between the underlying LVMI level and the risk of mortality. A positive value of $\alpha$ indicates that higher LVMI levels are associated with increased mortality risk.

### Slope parameterization

To assess whether the rate of change of LVMI carries prognostic information beyond its current level, we extended the survival model to include the instantaneous slope of the longitudinal trajectory:

$$
h_i(t) = h_0(t) \text{exp}(\gamma^\top \textbf{X}_i + \alpha_1 m_i(t) + \alpha_2 m_i'(t)).
$$

Here, $m_i'(t)$ represents the derivative or slope of the LVMI trajectory with respect to time. Clinically, two patients with the same LVMI value may have different prognoses if one is improving while the other is worsening. Including the slope parameter allows the model to capture this dynamic information. Current-value and slope parameterizations are commonly used in the dynamic prediction literature to represent different biological mechanisms linking longitudinal biomarkers to survival outcomes.

### Model assumptions and sensitivity analysis

Model diagnostics were conducted for both the longitudinal and survival submodels prior to fitting the joint model. For the LMM, we assessed residual versus fitted plots, Q-Q plots of residuals, and the distribution of random effects to evaluate normality and homoscedasticity assumptions. We also compared linear and quadratic time effects to verify that a linear time specification adequately captured LVMI trajectories.

For the survival submodel, the PH assumption was evaluated using Schoenfeld residual tests and graphical diagnostics, and the functional form of continuous predictors was examined using Martingale residual plots. Sensitivity analyses were conducted by evaluating alternative survival model specifications and association structures (current-value, slope, and combined parameterizations) to assess the robustness of the LVMI–mortality relationship.

## Repository Structure

This is the file layout used in this project:

```text
├── README.md                                  # Project description and workflow
│
├── heart.Rdata                                # Dataset used in the project
│                               
├── EDA.Rmd                                    # Exploratory data analysis of the dataset
│
├── Modeling_and_Sens.Rmd                      # Runs all primary and extension modeling, model assumption checks, and sensitivity analysis, and outputs results
|
├── STAT_293_Final_Project_Presentation.pdf    # Compiled presentation PDF
├── STAT_293_Final_Project_Presentation.zip    # Prensetation source files
├── STAT_293_Final_Project_Report.pdf          # Compiled report PDF 
└── STAT_293_Final_Project_Report.tex          # Report source file        
```

## Running the Project

Required R packages include `dplyr`, `ggplot2`, `JM`, `MASS`, `nlme`, and `survival`.

### Exploratory data analysis (EDA)

Open and knit `EDA.Rmd` in RStudio to reproduce the EDA.

### Modeling and sensitivity analyses

Open and knit `Modeling_and_Sens.Rmd` to reproduce the modeling workflow, including assumption checks and sensitivity analyses.

## Citations

```bibtex
@article{hickey2016group,
  title={Joint modelling of time-to-event and multivariate longitudinal outcomes: recent developments and issues},
  author={Hickey, Graeme L and Philipson, Pete and Jorgensen, Andrea and Kolamunnage-Dona, Ruwanthi},
  journal={BMC Medical Research Methodology},
  volume={16},
  number={1},
  pages={117},
  year={2016}
}

@article{li2023group,
  title={A comparison of two approaches to dynamic prediction: joint modeling and landmark modeling},
  author={Li, Wenhao and Li, Liang and Astor, Brad C},
  journal={Statistics in Medicine},
  volume={42},
  number={13},
  pages={2101--2115},
  year={2023}
}

@article{lim2008group,
  title={Longitudinal study of the profile and predictors of left ventricular mass regression after stentless aortic valve replacement},
  author={Lim, Eric and Ali, Ayyaz and Theodorou, Panagiotis and Sousa, Ines and Ashrafian, Hutan and Chamageorgakis, Themis and Duncan, Alison and Henein, Michael and Diggle, Peter and Pepper, John},
  journal={The Annals of Thoracic Surgery},
  volume={85},
  number={6},
  pages={2026--2029},
  year={2008}
}

@article{parr2022group,
  title={Joint models for dynamic prediction in localised prostate cancer: a literature review},
  author={Parr, Harry and Hall, Emma and Porta, Nuria},
  journal={BMC Medical Research Methodology},
  volume={22},
  number={1},
  pages={245},
  year={2022}
}
```

## Acknowledgements

- Dr. Kürüm for course instruction
- Lim et al. for foundational work
- The developers of all R packages used in this project

Last Updated: March 3, 2026
