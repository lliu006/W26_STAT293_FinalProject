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

This is the actual foldr and file layout used in this project:

```text
├── README.md                                 # Project description and workflow
│
├── heart.csv                                 # Dataset used in the project
├── 01_DataSimulation.R                       # All helper functions, singl-run simullation, and GIMME wrapper
├── 11_Method.Rmd                             # Runs all simulations and writes combine results
├── 21_Example_and_Analysis.Rmd               # Loads results and creates final summary tables and plots
├── 31_Full_Code.Rmd                          # GIMME code in its entirety
│
├── sim_data/                                 # Auto-generated simulated time series (100 .txt files per run)
├── sim_results/                              # Auto-generated GIMME output (path counts, plots, model files)
├── results/                                  # Combined metrics and saved figures
│   ├── all_results.csv
│   ├── TPR_vs_T_A_Phi.png             
│   ├── TPR_A_errorbars.png            
│   ├── TPR_vs_T_A_Phi_analysis.png     
│   ├── TPR_A_errorbars_analysis.png   
│   └── network plot figures
|
├── STAT293_FinalProject.Rproj                 # RStudio project file
├── STAT_293_Final_Project_Presentation.pdf    # Compiled presentation PDF
├── STAT_293_Final_Project_Presentation.zip    # Prensetation source files
├── STAT_293_Final_Project_Report.pdf          # Compiled report PDF 
└── STAT_293_Final_Project_Report.tex          # Report source file        
```

## Software

R packages include:

- `dplyr`
- `ggplot2`
- `JM`
- `MASS`
- `naniar`
- `nlme`
- `survival`

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
  title={Acomparisonof twoapproaches to dynamicprediction: joint modeling and landmark modeling},
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

- Dr. Esra Kürüm for course instruction
- Lim et al. for foundational work
- The developers of `JM`, `nlme`, and `survival`

Last Updated: March 2, 2026
