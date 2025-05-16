# NHS Dissatisfaction & Political Leanings

This project investigates whether public dissatisfaction with the UK's National Health Service (NHS) is shaped by **political leanings**, using British Social Attitudes (BSA) survey data from 2015–2019 and 2021–2023.

## 🧭 Overview

The study asks:  
*Are the reasons people give for feeling dissatisfied with the NHS rooted in ideological differences?*

Using party identification as a proxy for political leaning, it compares **Labour (left)**, **Conservative (right)**, and **Unaffiliated** individuals. Demographic controls include age, sex, ethnicity, education, employment status, and pandemic-related temporal effects.

---

## 🔍 Key Insights

- **Labour supporters** blame systemic issues like under-funding, staff shortages, and policy reform.
- **Conservative supporters** focus on perceived waste and operational inefficiency.
- **Unaffiliated individuals** cite long waiting times and service unavailability.
- **Older adults** and **employed respondents** are consistently more dissatisfied.
- **COVID-19 years** (2021–2022) produced a cross-cutting spike in dissatisfaction.

---

## ⚙️ Techniques Used

- **Data Wrangling & Visualisation**: R (`tidyverse`, `ggplot2`, `janitor`)
- **Exploratory Analysis**:
  - NHS satisfaction trends (1983–2023)
  - Phi and Cramér’s V association tests
- **Modeling**:
  - Stepwise logistic regression:
    - Model 0: Political leaning
    - Model 1: + Year fixed effects
    - Model 2: + Age & interactions
    - Model 3: + Full demographic controls
- **Model Evaluation**: Log odds coefficients, McFadden’s pseudo-R², AIC

---

## 📊 Key Results

<table>
  <tr>
    <td align="center"><strong>NHS Satisfaction by Party (1983–2023)</strong></td>
    <td align="center"><strong>Logistic Regression Coefficients</strong></td>
  </tr>
  <tr>
    <td>
      <img src="Logistic Regression with NHS Dissatisfaction.png"
           alt="NHS Satisfaction by Party"
           width="600" height="600">
      <p align="left" style="font-size: 12px;">
        This figure shows 40 years of NHS satisfaction data from the British Social Attitudes survey,
        grouped by party identification (Labour vs Conservative). Satisfaction fluctuated with the party
        in power, with notably lower satisfaction under Conservative-led governments after 2010.
      </p>
    </td>
    <td>
      <img src="Multivariate Logistic Regression Results for NHS Dissatisfaction Reasons by Political Affiliation and Demographics .png"
           alt="Logistic Regression Coefficients"
           width="600" height="600">
      <p align="left" style="font-size: 12px;">
        This table presents multivariate logistic regression results for nine specific reasons for NHS dissatisfaction.
        Labour supporters emphasized systemic issues (e.g., funding, staffing), while Conservative supporters were more
        likely to cite operational inefficiency. Age and employment also emerged as consistent predictors.
      </p>
    </td>
  </tr>
</table>



## 📌 Citation

Data Source:  
> British Social Attitudes Survey 2015–2023, NatCen Social Research.


*Created by Jacky Chong (2025)*  
[Back to Portfolio ➜](https://github.com/JackyChong611/PortfolioProjects)

