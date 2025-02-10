# Real-World COVID-19 Vaccination and Mortality Analysis  

## Overview  
- **Objective**: Assess the impact of COVID-19 vaccination on mortality rates using real-world data.  
- **Tools Used**: R (Data Cleaning, Visualization, Negative Binomial Regression)  
- **Key Findings**:  
  - Vaccination consistently provided a protective effect across all age groups.  
  - Despite occasional fluctuations, rate ratios confirmed overdispersion effects.  

## Files  
- [Analysis Code](https://raw.githubusercontent.com/JackyChong611/PortfolioProjects/33640ee4743fa6458854f7157aeb6ffce44e914c/Real-World_COVID19_Analysis/Data%20Analytics%20Project%20Supplementary%20Materials-20250208T165233Z-001.zip)
- [Full Report](https://raw.githubusercontent.com/JackyChong611/PortfolioProjects/ad76d60f48002fa9f48b328cd188dbbbf7442ace/Real-World_COVID19_Analysis/All-cause%20Mortality%20in%20Relation%20to%20COVID-19%20Vaccination-%20Analysis%20of%20the%20UK%20ONS%20Public%20Data.docx)
 
# 🏥 Real-World COVID-19 Vaccination and Mortality Analysis  

## 📌 Overview  
This study analyzes **all-cause mortality** in relation to **COVID-19 vaccination** using publicly available data from the **UK Office for National Statistics (ONS)**. While clinical trials confirm vaccine efficacy in preventing COVID-19 deaths, this research examines **broader mortality trends** to understand the overall impact of vaccination. The study employs **rate ratio (RR) analysis** and **Negative Binomial regression** to assess trends across age groups from **April 2021 – May 2023**.

## 🔍 Key Findings  
- 📉 **Vaccination is consistently associated with lower all-cause mortality** across age groups, despite occasional fluctuations.  
- 🧪 **First-dose recipients (≥21 days) showed elevated mortality risks in some age groups**, likely due to pre-existing health conditions among early vaccine adopters.  
- 📊 **The third and fourth doses exhibited the strongest protective effect**, with rate ratios (RRs) below 1.0 in most cases.  
- 🔎 **Negative Binomial regression confirmed a statistically significant reduction in all-cause mortality** among vaccinated individuals in all age groups.  
- ⚠️ **Biases such as the healthy vaccinee effect and underestimation of the unvaccinated population** may influence mortality trends.  

## 📁 Dataset & Sources  
- Data was obtained from the **UK Office for National Statistics (ONS)**:  
  📂 [Deaths by vaccination status, England (ONS)](https://www.ons.gov.uk/peoplepopulationandcommunity/birthsdeathsandmarriages/deaths/datasets/deathsbyvaccinationstatusengland)  

- The dataset includes:  
  - Total deaths by **vaccination status** and **age group**  
  - Stratification by **first, second, third, and fourth dose**  
  - Exclusion of COVID-19-specific deaths to analyze all-cause mortality  

## 🛠 Methodology  
### **1️⃣ Data Collection & Preprocessing**  
- Data extracted from **ONS public datasets (April 2021 – May 2023)**  
- **Relative Risk (RR) calculations** performed for each dose level, comparing vaccinated vs. unvaccinated individuals  
- **Data Cleaning:**  
  - **Excluded COVID-19 deaths**  
  - Handled **<3 death counts** as missing values  
  - Age groups: **18-39, 40-49, 50-59, 60-69, 70-79, 80-89, 90+**  

### **2️⃣ Statistical Analysis**  
#### **A. Rate Ratio (RR) Calculation**  
📈 **Formula for RR:**  
\[
RR = \frac{\text{Mortality rate in vaccinated group}}{\text{Mortality rate in unvaccinated group}}
\]
- RR **< 1.0**: Vaccination is protective  
- RR **> 1.0**: Higher mortality in vaccinated group  

#### **B. Poisson Regression (Baseline Mortality Model)**  
\[
\log (\lambda_i) = \beta_0 + \beta_1 X_i + \log(\text{PersonYears})
\]
- Poisson regression assumes **equal mean-variance**, but real-world mortality data often shows **overdispersion**.  

#### **C. Negative Binomial Regression (Final Model to Address Overdispersion)**  
\[
\lambda_i = e^{\beta_0 + \beta_1 X_i + \log(\text{PersonYears})}
\]
- Accounts for **extra variance** in mortality data.  
- Coefficient **β₁ < 0** indicates **a protective effect of vaccination**.  

### **3️⃣ Bias & Sensitivity Analysis**  
- **Healthy Vaccine Bias**: Vaccinated individuals generally have better health behaviors (e.g., diet, exercise).  
- **Indication Bias**: Early vaccination prioritized **high-risk individuals**, inflating early mortality rates.  
- **Harvesting Effect**: Frail individuals may die shortly after vaccination, skewing short-term risk assessments.  

## 📊 Data Visualization  
We used **RStudio** for statistical analysis & visualization:  
- 📌 **Box plots** of **RR values** across age groups  
- 📌 **Logarithmic mortality trends** by dose category  
- 📌 **Negative Binomial regression coefficients** by age group  

## Files  
- [Analysis Code](https://raw.githubusercontent.com/JackyChong611/PortfolioProjects/33640ee4743fa6458854f7157aeb6ffce44e914c/Real-World_COVID19_Analysis/Data%20Analytics%20Project%20Supplementary%20Materials-20250208T165233Z-001.zip)
- [Full Report](https://raw.githubusercontent.com/JackyChong611/PortfolioProjects/ad76d60f48002fa9f48b328cd188dbbbf7442ace/Real-World_COVID19_Analysis/All-cause%20Mortality%20in%20Relation%20to%20COVID-19%20Vaccination-%20Analysis%20of%20the%20UK%20ONS%20Public%20Data.docx)
  
📜 **Includes:**  
- `RR_Box_Plots.R` (Whisker plots of Rate Ratios)  
- `Negative_Binomial_Regression.R` (Final mortality model)  
- `ONS_Mortality_Data.xlsx` (Raw dataset for replication)  

## 🔬 Conclusion  
- COVID-19 vaccination **significantly reduces all-cause mortality** in all age groups.  
- **Rate Ratios (RRs) confirm this effect**, although **short-term fluctuations exist** due to cohort biases.  
- **Negative Binomial regression solidifies vaccination’s protective impact**, with the **strongest effect in middle-aged adults (40-79 years).**  
- Further research should incorporate **comorbidity data** and **longer timeframes** beyond May 2023.  

---

## 🤝 Acknowledgments  
- **Data Source**: UK Office for National Statistics (ONS)  
- **Tools Used**: RStudio, ggplot2, Poisson/Negative Binomial models  
- **References**: See full report for all citations  

