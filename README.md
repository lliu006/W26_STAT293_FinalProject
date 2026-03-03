# Joint Longitudinal-Survival Analysis of Postoperative Left Ventricular Mass Index (LVMI) and Mortality After Aortic Valve Replacement 

## Winter 2026
## Instructor: Dr. Esra Kürüm

**Authors:** Ryan Kitagawa, Linlin Liu, and Nancy Lopez <br>
**Date:** March 12, 2026

**Primary Reference:** Lim, E., et al. (2008). A longitudinal study of the profile and predictors of left ventricular mass regression after stentless aortic valve replacement. *Ann Thorac Surg.* 2008; 85(6):2026 - 2029.

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

The dataset consists of 988 observations from 256 unique patients undergoing aortic valve replacement. 

Each row represents a follow-up visits and includes:

### Longitudinal variables

- `num` - patient ID
- `time` - observed time point, with surgery date as the time origin (years)
- `lvmi` - left ventricular mass index (LVMI) at follow-up visit
- `log.lvmi` - natural log transformation of LVMI 
  
### Survival variables

- `fuyrs` -  maximum follow-up time, with surgery date as the time origin (years)
- `status` - censoring indicator (1 = died and 0 = lost at follow-up)
  
### Baseline covaraites 

- age
- sex
- body surface area (`bsa`)
- preoperative LV function (`lv`)
- diabetes (`dm`)
- valve type (`hs`: homograft vs. stentless)
  
## Objectives

The goals of this project are to:

- Fit a shared-parameter joint model (JM) to quantify the association between latent LVMI level and hazard of death.
- Evaluate whether the slope/rate of change of LVMI adds prognostic value beyond the current level.
- Perform model diagnostics and sensitivity analyses.
  
## Statistical Methodology

### Longitudinal submodel

We modeled repeated LVMI measurements using a LMM:

$Y_{ij} = m_i(t_{ij}) + \varepsilon_{ij}, \ \ \ \varepsilon_{ij} \overset{iid} \sim \mathcal{N}(0, \sigma^2)$

$m_i(t) = \beta_0 + \beta_1t + \beta^\top Z_i + b_{0i} + b_{i1}t$

Random intercepts and slopes allow subject-specific trajectories. 

### Survival submodel


