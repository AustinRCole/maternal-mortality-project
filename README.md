
# Maternal Mortality: US and Global Perspectives

## Table of Contents

1. [Purpose of Project](#purpose-of-project)
2. [Project Overview](#project-overview)
3. [Objective](#objective)
4. [Data Sources](#data-sources)
5. [Data Processing and ETL](#data-processing-and-etl)
    - [Extract](#extract)
    - [Transform](#transform)
    - [Load](#load)
6. [Data Exploration](#data-exploration)
7. [Statistical Analysis](#statistical-analysis)
    - [Global](#global)
    - [United States](#united-states)
8. [Machine Learning](#machine-learning)
    - [Purpose](#purpose)
    - [Model Creation & Selection](#model-creation-and-selection)
    - [User Self-Input](#user-self-input)
    - [10-Year Forecast](#10-year-forecast)
9. [Flask Web Application](#flask-web-application)
    - [Home Page](#home-page)
    - [United States: Affordable Care Act Page](#united-states-affordable-care-act-page)
    - [United States: Ranked Measured Comparisons Page](#united-states-ranked-measured-comparisons-page)
    - [Machine Learning Models Page](#machine-learning-models-page)
    - [Machine Learning Playground Page](#machine-learning-playground-page)
    - [Machine Learning 10-Year Forecast Page](#machine-learning-10-Year-Forecast-Page)
    - [News and Articles Page](#news-and-articles-page)
    - [Methodology Page](#methodology-page)
    - [About Us Page](#about-us-page)
10. [Library and Tools](#libraries-and-tools)
11. [Contributors](#contributors)


## Purpose of Project

The United States has the highest maternal mortality rate among 11 developed countries, and has seen rising deaths from 1987-2017 [source](https://www.ajmc.com/view/us-ranks-worst-in-maternal-care-mortality-compared-with-10-other-developed-nations).  Compared with any other wealthy nation, the United States spends the highest percentage of its gross domestic product on health care.  We discovered that as of 2017, Medicaid coverage was responsible for financing 43% of U.S. births but covered medical services and income eligibility for Medicaid varied by state.  We wanted to explore by state if these variations affect maternal mortality rates.


## Project Overview

For this project, we developed an interactive dashboard for users to explore maternal mortality data globally and within the United States. Users will be able to visualize maternal mortality data alongside data for potentially related factors, such as access to health insurance and Medicaid.
We are focusing on factors at the state level because maternal mortality rates and healthcare policies and access vary widely between states.

## Objective

Our objective is for this dashboard to function in a way that allows for users to see patterns between maternal mortality rates and potential influencing factors.
For example, Does health insurance coverage affect maternal mortality rates? Does a state’s election of the medicaid expansion affect maternal mortality rates?  Are there any other factors that might affect maternal mortality?

## Data Sources

The data for this project was sourced from the following sources:

- UNICEF [Source](https://data.unicef.org/topic/maternal-health/maternal-mortality/)

- Insurance Coverage Data showing changes in Insurance Policy over time: [Source](https://www.kff.org/womens-health-policy/fact-sheet/womens-health-insurance-coverage-fact-sheet)

- Centers for Disease Control Wonder [Source](https://wonder.cdc.gov/)

- American Health Rankings: United Health Foundation [Source](https://www.americashealthrankings.org/explore/annual/measure/Outcomes/state/ALL)

- Medicaid Expansion [Source](https://data.medicaid.gov/Enrollment/State-Medicaid-and-CHIP-Applications-Eligibility-D/n5ce-jxme/data) 

## Data Processing and ETL

### **Extract**

- UNICEF:    Downloaded the latest data for Maternal Mortality Worldwide (2017).

- Centers for Disease Control Wonder:    Data on maternal deaths from 2009-19 in the US: death counts were queried on specific ICD codes for maternal deaths up to 42 days after delivery and late maternal deaths (defined by the WHO as death of a woman from direct or indirect obstetric causes).

- Kaiser Family Foundation:    Pulled health insurance coverage in the US for females aged 19-64 in the years 2009-2019.

- America's Health Rankings United Health Foundation:    Pulled report for overall health of women and children for 2019 as well as overall health outcomes by the US state for years 2009-19.

### **Transform**

**Cleaning CDC data on maternal deaths and births in the U.S.**
- Pulled CDC data on maternal deaths according to causes of death listed above for years 2009-2019 as well as births (not all states reported each year).  We then merged separate deaths and births DataFrames with an inner join on the shared keys “State,” “State Code,” and “Year.” 
- Calculated the Maternal mortality ratio = (Number of maternal deaths / Number of live births) x 100,000, and added the ratio as a new column in the final DataFrame. 
- Exported cleaned data to a csv. 


**Cleaning Kaiser Data on Health Insurance Coverage of Females 19-64:**
- Collected data from the Kaiser Family Foundation site for years 2009-2019.
- Used fillna() function to remove NaN after confirming that totals for insurance coverage equaled 100%. and converted values for insurance coverage to percentages.
- Built new Dataframes with an added column for the year.
- Used pd.concat to combine DataFrames from each year from 2009-2019 and Sorted final dataframe by year and location. 
- Exported cleaned data to a csv. 


**Cleaning Health of Women and Children Data**
- Downloaded CSV of report data for 2019 
- Used .str.contains to select each relevant measure, storing as its own variable (For measures where demographic breakdowns were available) separated out that data and exported as their own CSVs
- Merged into one big CSV and Exported combined csv

**Cleaning overall Health Outcomes**
- Downloaded individual year CSVs in from years 2009-19 and read CSVs into Jupyter Notebook with Pandas, create individual dataframes
- Locate “Measure Names” pertinent to our information from .unique() list, investigate common entries throughout dataframes over time
- Replaced Measure Name for select values where name changed over time, select needed columns, reset indexes and used concat in order to combine dataframes
- Output dataframes to CSV

**Cleaning Maternal Mortality Global**
- Downloaded the latest data (2017) and read CSV into Jupyter Notebook with Pandas and create a dataframe
- Added columns (latitude, longitude) and based on location column, split item into 2 part and updated latitude and longitude columns
- Selected only columns that will be used and exported the final data to csv file in order to store in database


### **Load**

Within Jupyter Notebook, we exported cleaned CSVs into PostGres as tables in a unified database.  We then set an object and declared base in SQLAlchemy.  Next  table schemes were created corresponding to the individual CSV files.  We also created an engine and connection to the Postgres database and created the tables. A similar process was followed to create a local database: connection was made to SQLite file, tables were specified to be loaded, created, and binded to the local database.

- To help visualize connections and see the composition of dataset, we created a database diagram via QuickDatabase

![Database Visual](Images/QuickDBD_ERD.png)

 
## Data Exploration

- The US has a unique place within peer countries for outcomes of women's overall and maternal health due to a variety of factors and there are specific challenges related to the US's healthcare system that could lead to difficulties caring for its population, particularly women.

- It was hypothesized that insurance coverage could affect health, and specifically women's health.  The period of 2009-2019 was selected due to two specific changes in policy during this time period: in 2010 coverage was allowed for dependents up to age 26 and in 2014 the Affordable Care Act was implemented with expansion of Medicaid coverage made available to the states.

- Other health factors were also considered and investigated in order to evaluate insurance coverage's relative importance within the US health system.  It is important to remember there are some differences in reporting over time including between 2003 and 2017, where states were incrementally implementing pregnancy checkbox on death certificates with universal implementation by 2017.

## Statistical Analysis

### Global

- As the bar charts presented below, **South Sudan** has the highest number of MMR and **Belarus** has the lowest number of MMR.

**Top 10 MMR** | **Bottom 10 MMR**
--------------------- | ---------------------
![Highest MMR](Images/globaltopten.png)|![Lowest_MMR](Images/globalbottomten.png)


- Comparing among **developed countries** as the bubble chart presented below, **United States** has the highest number of MMR and **Norway** has the lowest number of MMR. According to Maternal mortality and maternity care in the United States compared to 10 other developed countries studied by Tikkanen and others, the U.S. is the only country that will not guarantee the accessibility to home visit or paid parental leave after giving birth. Also, the shortage of maternity care providers affects the rate significantly.

![Compared Developed Countries](Images/compared_deloveled_countries.png)

- Based on **worldwide** causes-of-deaths data presented as pie charts below, *excluding indirect causes*, Haemorrhage has a higher percentage than the other causes. 

<p align="center">
  <img src="Images/worldwide_causes.png" >
</p>

- Comparing between developed, developing and under-developing (Africa) regions, *excluding indirect causes*, Haemorrhage (green area in chart) is 1.5 to 2 times more in the developing region and under-developing region than the developed region. As a result of healthcare accessibility and medical advancement, there is a lower rate of Haemorrhage in the developed region as the charts presented below.

<p align="center">
  <img src="Images/compared_causes.png">
</p>

### United States

- Overall statistical analysis was performed for selected data sets to visualize the dataframes created and to explore further the information that was cleaned.  We questioned which states might have the highest MMR per specific years identified. We then isolated various years to view what the mortality rate looked like across states.

![2019 Data Isolation](Images/explore_us_2019_mmr.png)

- We also explored which states have the highest and lowest mortality ratio overall within the United States.  

**Top 10 MMR** | **Bottom 10 MMR**
--------------------- | ---------------------
![Highest MMR](Images/explore_us_highest_mmr.png)|![Lowest_MMR](Images/explore_us_lowest_mmr.png)

## Machine Learning

### Purpose

Maternal Mortality Rates have continued to increase in the United State despite improvements in health care and quality of life. This project examines the impact of various demographic factors, existing health conditions, and differences in access to care on maternal mortality rates during the period of 2009 to 2019 and asks by identifying which factors contribute to an increased MMR, can we create a functional model to predict risk?

In order to maximize our chances at creating a smart machine learning model, we decided that we needed more data to train it with, so we expanded the data we originally collected by diving deeper into race for MMR, and gathered more healthcare measure data similar to what we used for the Ranked Comparison page featured on our app. 

We collected health measure data from America’s Health Rankings for 28 measures across each state from 2009-2019. We used pandas to select the values we wanted, and created one comprehensive dataframe with all of the measure data across our chosen interval, grouped by state and year. 


### Model Creation and Selection

To better visualize our data and select the optimal model, we seperated our large comprehensive dataframe into two distinct datasets: Maternal Mortality Stratified by Race & Maternal Mortality without Race:

**First Dataset- Maternal Mortality Rate Stratified by Race**

This dataset contains MMR data stratified by race.  The races included were:
    - African American
    - White, non-hispanic
    - White, hispanic
    - Asian or Pacific Islander

Other columns found in this dataset are births and deaths by race, population by race, as well as state ID and location

*Models Tested*

[Linear Regression](#linear-regression) | [Lasso Regression](#lasso-regression) | [Logistic Regression](#logistic-regression)


**Linear Regression**

    - For the linear regression model we collected publicly available mortality data from the CDC Wonder site, selecting for ICD codes A34 (Obstetrical tetanus) and O00 to O99 (Chapter XV Pregnancy, childbirth, and the puerperium), which captures maternal deaths owing to obstetrical tetanus, maternal deaths up to 42 days after delivery, and late maternal deaths (up to a year following the termination of a pregnancy). 

    - In the heatmap below, we can see strong positive correlations (likely to indicate higher MMR) for Black or African American women, and negative correlations (likely to indicate lower MMR) for White women. 

![Heat Map](/static/img/heatmap_mmr_strat_by_race.png)

    -We fit a linear regression model and experimented with feature selection after running RFE to identify insignificant variables. However, removing the insignificant variables did not improve the R2 value for any of the linear regression models. 
    
    -We experimented with scaling our data using Standard Scaler, best for outliers, and fit our model again, but the resulting R2 score was slightly lower: 0.586.

    -Our highest scoring Linear Regression model with the data stratified by race was with non-scaled data, using each of our race and hispanic origin categories, and population data, stratified by race. These are the resulting scores:
        -MSE: 364.27539582893286
        -R2 Testing: 0.5550222997732394
        -R2 Training: 0.587634628814633


**Lasso Regression**

    -Using the Lasso Regression Model, all of the features were selected for the x value, and identified MMR by race as the y value
    
    -Because the dataset included categorical data, `get.dummies` was applied to the dataframe to transform the columns containing race features which allowed those values to be read when scaling was applied.  `StandardScaler` was selectd as the method to scale the data because of outliers previously identified in the dataset

    -After fitting and training the model, the data was ran through the Lasso Regression model with the following results:
        -MSE: 0.37425190453114504
        -R2: 0.6956700138016816

    -The results of the Lasso Regression were promising with a R squared value higher than 0.5.  However, it was identified that running the model with the death by race and births by race columns skewed the data because those values were already used in calculating the MMR.  After those features were dropped, the model was re-ran and the R squared value dropped significantly
        -MSE: 0.6478563653918986
        -R2: 0.47318339238591234


**Logistic Regression**

After doing Linear Regression models, we tried Logistic Regression, converting our y-value to categorical.
We binned our mmr data stratified by race into three categories:
Low (MMR <= 20)
Medium (MMR > 20 and <= 50)
High (MMR > 50)
We also experimented with creating distinction between the bins, adjusting the values for the bins. This created a segment of the data that did not fall into any of three bins, so we reverted to using bins that would contain all the data.
Our scores for this model improved after we removed the birth and death data points: 
R2 Testing: 0.5979381443298969
R2 Training: 0.7594501718213058
It’s clear from the initial data that there are wide disparities in MMR by race and ethnicity. We were interested in looking at possible factors that could be contributing to that disparity, so we moved forward with our dataset and models that included features such as access to care. 
Our confusion matrix shows that classifying MMR as "medium" risk was most successful, followed by classifying appropriately for "high" risk.



*Limitations and Considerations*

If a certain group has fewer than 10 deaths for a given state and year, that data is suppressed. 
One limitation of publicly available mortality data is the CDC Wonder site suppresses counts of nine or fewer. As a result, only four racial and ethnic groups are represented in our dataset, and some groups are missing data for some years in our range of 2009-2019.


We knew from earlier exploratory analysis that our data had outliers.


Because our outcome, MMR, is a continuous variable, we needed to run Regression models rather than Classification models.


*Processes and Visulzations*


**Second Dataset- Maternal Mortality Rate without Race**

Other columns found in this dataset are births and deaths by race, population by race


*Models Tested*

[Linear Regression](#linear-regression) | [Lasso Regression](#lasso-regression) | [Logistic Regression](#logistic-regression) | [Ridge Regression](#ridge-regression) | [Neural Network](#neural-network) 


**Linear Regression**


**Lasso Regression**


**Logistic Regression**


**Ridge Regression**


**Neural Network**


*Limitations and Considerations*


*Processes and Visulzations*



### User Self-Input


### Predictive Analysis


### 10-Year Forecast


## Flask Web Application

Web application is deployed on Heroku: [Maternal Mortality Heroku App](https://maternal-mortality-project.herokuapp.com/)

### Home Page

- Created the initial landing page to showcase global mortality ratio per 100,000 births. The map shows each country's MMR, ranking and category defined by WHO when users hover over any country on the map. This map was created using the Javascript Library (AnyChart).

<p align="center">
  <img alt="Global Mortality Ratio Map" src="Images/global_mmr_map.png">
</p>

- Visualized the Mortality Ratio amongst the developed countries in the world.  Graph shows that the United States has the highest Rate of Maternal Mortality among the developed countries.

<p align="center">
  <img alt="Developed Countries" src="Images/developed_countries_hovered.png">
</p>

- Visualized the causes of Maternal Mortality as a pie graph that includes a drop down function with the ability to search by regions around the globe.  The pie graph highlights
the many complications that could lead to death during pregnancy and/or childbirth.

<p align="center">
  <img alt="Maternal Deaths" src="Images/causes_region_select.png" height="400">
</p>

### United States Affordable Care Act Page

- Created a map of the United States that shows the Maternal Mortality Ratio of each state across the selected time period, 2009-2019. This map was created using the Javascript Library (AnyChart). Also, the slide bar was created in order to allow users to select a year of interest.

<p align="center">
  <img alt="slide bar" src="Images/slide_bar.png">
</p>

**US Map 2009** | **US Map 2019**
|:-: | :-:|
| ![US Map 2009](Images/us_map_2009.png) | ![US Map 2019](Images/us_map_2019.png) |

- Visualized the Maternal Mortality Ratio by state. Drop down selection was included to allow for exploration of data for all states.  *There is no MMR for the District of Columbia and Puerto Rico*

![State Mortality Rates](Images/State_Comparison.PNG)

- Visualized the comparison of insured and uninsured females by state, specifically focusing on medicaid insurance coverage.  Drop down selection was included to allow for exploration of data for all states.

![State Mortality Rates](Images/Comparison_of_Medicaid_Coverage.PNG)

- Visualized the Mortality Ratio of States that decided to not expand their medicaid coverage.  Drop down selection was included to allow for exploration of coverage (or lack thereof) by year.

![State Mortality Rates](Images/MMR_No_Expansion.JPG)

### United States Ranked Measured Comparisons Page

- Visualized how the states with the highest and lowest mortality rates compared against related health care measures.

![Ranked Healthcare Measures](Images/Infant_Mortality_RHM.JPG)

### Machine Learning Models Page

### Machine Learning Playground Page

### Machine Learning 10-Year Forecast Page

### News and Articles Page

- This page contains news and articles retrived from News API.  

### Methodology Page

- This page contains the overview that users might be interested to know about the project.

### About Us Page

- This page contains contact information including Github, LinkedIn and E-mail of all the contributors for this project in case users have questions.

<p align="center">
  <img alt="team" src="Images/team.png" width="500px">
</p>

## Libraries and Tools

<p>
<a href="http://www.python.org" target="_blank">Python</a>
<span> | </span>
<a href="http://www.javascript.com" target="_blank">Javascript</a>
<span> | </span>
<a href="https://www.postgresql.org/" target="_blank">PostgreSQL</a>
<span> | </span>
<a href="https://pandas.pydata.org/" target="_blank">Pandas</a>
<span> | </span>
<a href="https://jupyter.org/" target="_blank">Jupyter Notebook</a>
<span> | </span>
<a href="https://flask.palletsprojects.com/en/1.1.x/" target="_blank">Flask</a>
<span> | </span>
<a href="https://www.sqlalchemy.org/" target="_blank">SQLAlchemy</a>
<span> | </span>
<a href="https://plotly.com/" target="_blank">Plotly</a>
<span> | </span>
<a href="https://getbootstrap.com/" target="_blank">Bootstrap</a>
<span> | </span>
<a href="https://www.anychart.com/products/anychart/overview/" target="_blank">AnyChart</a>
<span> | </span>
<a href="https://chrome.google.com/webstore/detail/table-capture/iebpjdmgckacbodjpijphcplhebcmeop?hl=en" target="_blank">Chrome Table Capture</a>
<span> | </span>
<a href="https://scikit-learn.org/stable/tutorial/machine_learning_map/index.html" target="_blank">Scikit-Learn</a>
</p>

## Contributors

| Team Member | GitHub | LinkedIn | E-mail Address |
| :-: | :-: | :-: | :-: |
| <b>Akilah Hunte</b> | <a href="https://github.com/Kiki-99" target="_blank"><img alt="Github" height="30" src="https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png" /></a> | <a href="https://www.linkedin.com/in/akilah-hunte-68070140" target="_blank"><img alt="LinkedIn" height="20" src="https://content.linkedin.com/content/dam/me/brand/en-us/brand-home/logos/In-Blue-Logo.png.original.png" /></a> | <a href="mailto:ahunt173@gmail.com"><img href="mailto:ahunt173@gmail.com" alt="ahunt173@gmail.com" height="30" src="https://lh3.googleusercontent.com/0rpHlrX8IG77awQMuUZpQ0zGWT7HRYtpncsuRnFo6V3c8Lh2hPjXnEuhDDd-OsLz1vua4ld2rlUYFAaBYk-rZCODmi2eJlwUEVsZgg"/></a> |
| <a><b>Atcharaporn B Puccini </b></a> | <a href="https://github.com/abpuccini" target="_blank"><img alt="Github" height="30" src="https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png" /></a> | <a href="https://www.linkedin.com/in/abpuccini/" target="_blank"><img alt="LinkedIn" height="20" src="https://content.linkedin.com/content/dam/me/brand/en-us/brand-home/logos/In-Blue-Logo.png.original.png" /></a> | <a href="mailto:b.atcharaporn@gmail.com"><img href="mailto:b.atcharaporn@gmail.com" alt="b.atcharaporn@gmail.com" height="30" src="https://lh3.googleusercontent.com/0rpHlrX8IG77awQMuUZpQ0zGWT7HRYtpncsuRnFo6V3c8Lh2hPjXnEuhDDd-OsLz1vua4ld2rlUYFAaBYk-rZCODmi2eJlwUEVsZgg"/></a> |
| <a><b>Austin Cole </b></a> | <a href="https://github.com/AustinRCole" target="_blank"><img alt="Github" height="30" src="https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png" /></a> | <a href="https://www.linkedin.com/in/austin-cole-167923b7/" target="_blank"><img alt="LinkedIn" height="20" src="https://content.linkedin.com/content/dam/me/brand/en-us/brand-home/logos/In-Blue-Logo.png.original.png" /></a> | <a href="mailto:AustinRCole2@gmail.com"><img href="mailto:AustinRCole2@gmail.com" alt="AustinRCole2@gmail.com" height="30" src="https://lh3.googleusercontent.com/0rpHlrX8IG77awQMuUZpQ0zGWT7HRYtpncsuRnFo6V3c8Lh2hPjXnEuhDDd-OsLz1vua4ld2rlUYFAaBYk-rZCODmi2eJlwUEVsZgg"/></a> |
| <a><b>Chahnaz Kbaisi </b></a> | <a href="https://github.com/Chahnaz-Kbaisi" target="_blank"><img alt="Github" height="30" src="https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png" /></a> | <a href="https://www.linkedin.com/in/chahnaz-kbaisi/" target="_blank"><img alt="LinkedIn" height="20" src="https://content.linkedin.com/content/dam/me/brand/en-us/brand-home/logos/In-Blue-Logo.png.original.png" /></a> | <a href="mailto:chahnaz.kbaisi@gmail.com"><img href="mailto:chahnaz.kbaisi@gmail.com" alt="chahnaz.kbaisi@gmail.com" height="30" src="https://lh3.googleusercontent.com/0rpHlrX8IG77awQMuUZpQ0zGWT7HRYtpncsuRnFo6V3c8Lh2hPjXnEuhDDd-OsLz1vua4ld2rlUYFAaBYk-rZCODmi2eJlwUEVsZgg"/></a> |
| <a><b>Lee Prout </b></a> | <a href="https://github.com/LeeProut" target="_blank"><img alt="Github" height="30" src="https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png" /></a> | <a href="https://www.linkedin.com/in/lee-prout-21977948/" target="_blank"><img alt="LinkedIn" height="20" src="https://content.linkedin.com/content/dam/me/brand/en-us/brand-home/logos/In-Blue-Logo.png.original.png" /></a> | <a href="mailto:wleeprout@gmail.com"><img href="mailto:wleeprout@gmail.com" alt="wleeprout@gmail.com" height="30" src="https://lh3.googleusercontent.com/0rpHlrX8IG77awQMuUZpQ0zGWT7HRYtpncsuRnFo6V3c8Lh2hPjXnEuhDDd-OsLz1vua4ld2rlUYFAaBYk-rZCODmi2eJlwUEVsZgg"/></a> |
| <a><b>Shay O'Connell </b></a> | <a href="https://github.com/Shayoconnell7" target="_blank"><img alt="Github" height="30" src="https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png" /></a> | <a href="https://www.linkedin.com/in/shayoconnell7/" target="_blank"><img alt="LinkedIn" height="20" src="https://content.linkedin.com/content/dam/me/brand/en-us/brand-home/logos/In-Blue-Logo.png.original.png" /></a> | <a href="mailto:shay.oconnell7@gmail.com"><img href="mailto:shay.oconnell7@gmail.com" alt="shay.oconnell7@gmail.com" height="30" src="https://lh3.googleusercontent.com/0rpHlrX8IG77awQMuUZpQ0zGWT7HRYtpncsuRnFo6V3c8Lh2hPjXnEuhDDd-OsLz1vua4ld2rlUYFAaBYk-rZCODmi2eJlwUEVsZgg"/></a> |
| <a><b>Wesley Lo </b></a> | <a href="https://github.com/wesleylo" target="_blank"><img alt="Github" height="30" src="https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png" /></a> | <a href="#"><img href="#" alt="LinkedIn" height="20" src="https://content.linkedin.com/content/dam/me/brand/en-us/brand-home/logos/In-Blue-Logo.png.original.png" /></a> | <a href="mailto:weslo404@gmail.com"><img href="mailto:weslo404@gmail.com" alt="weslo404@gmail.com" height="30" src="https://lh3.googleusercontent.com/0rpHlrX8IG77awQMuUZpQ0zGWT7HRYtpncsuRnFo6V3c8Lh2hPjXnEuhDDd-OsLz1vua4ld2rlUYFAaBYk-rZCODmi2eJlwUEVsZgg"/></a> |

© UNC Boot Camp 2021 - All Right Reserved ©
