# Forecasting-HIV-Infections
This project was originally used as my capstone project for the data science intensive program at [Galvanize Inc.](https://www.galvanize.com/denver-platte/data-science) Prior to participating in this program I spent about a decade in the HIV research field.  My work had largely focused on the molecular host-pathogen  interactions involved in HIV infection and on the impacts HIV has on the immune system.  Although I have always been interested in the epidemiology of HIV, this had never been a focus of my research projects. I ultimately decided to use my capstone project as an opportunity try my hand at HIV epidemiology and learn something new about the HIV epidemic in the US.  Although my time in this data science program has ended, I intend to continue work on this project whenever I can find the time.  So if this is something that you find interesting, I would suggest checking this repo from time to time to see how my analysis has been extended. For those with critiques and suggestions for improving this analysis I encourage you to get in contact with me (elogue01@gmail.com).

If you are only looking for a brief summary of this project, I would suggest looking at the [poster](https://github.com/elogue01/Forecasting-HIV-Infections/blob/master/Eric_Logue_poster.pdf) that I presented at the Galvanize Data Science Capstone Showcase.  However, for those of you that you are interested in a more detailed dive into this project, then I encourage you to read on.
## Background
Due to the development of anti-retroviral therapies the HIV/AIDS epidemic is generally considered to be under control in the US.  However, as of 2015 there were 971,524 people living with diagnosed HIV in the US with an estimation of 37,600 new HIV diagnoses in 2014.  HIV infection rates continue to be particularly problematic in communities of color, among men who have sex with men (MSM), the transgender community, and other vulnerable populations in the US. Socioeconomic factors are a significant risk factor for HIV infection and likely contribute to HIV infection risk in these communities.  The current US opioid crisis has further complicated the efforts to combat HIV with HIV infection outbreaks now hitting regions that weren’t previously thought to be vulnerable to such outbreaks.  

A model that can accurately forecast regional HIV infection rates would be beneficial to local public health officials.  Provided with this information, these officials will be able to better marshal the resources necessary to combat HIV and prevent outbreaks from occurring.  Accurate modeling will also identify risk factors for communities with high HIV infection rates and provide clues as to how officials may better combat HIV in their respective communities.

## Project Goal
1)	To accurately model HIV incidences (new infections per 100,000) in US counties by building a linear regression model that utilizes HIV infection data, census data, data on the opioid crisis, and data on sexual orientation.

2)	Identify features that are the most significant drivers of HIV infection rates and learn how these drivers differ between different regions.

## About the Data

### Sources
Data were obtained from four sources for this project:  
1. The largest collection of HIV and opioid data was obtained from the [opioid database](http://opioid.amfar.org/) maintained by the American Foundation for AIDS Research (amfAR).  
2. Additional HIV data was gathered from the datasets maintained by the [AIDSVu](https://aidsvu.org/resources/downloadable-maps-and-resources/).  
3. Demographic and economic data were obtained from the 5yr - American Community Survey which are available at the [US census bureau website](https://factfinder.census.gov/faces/nav/jsf/pages/searchresults.xhtml?refresh=t).
4. Estimates for the [MSM population](http://emorycamp.org/item.php?i=48) in each county were obtained from the Emory Coalition for Applied Modeling for Prevention (CAMP).

### Methods

Data were manipulated prior to analysis in the following ways:
1. State and county codes were merged in datasets were they were separated so they could be used later to merge dataframes together.
2. To simplify the intitial analysis, data was restricted to the year 2015.
3. Counties in which HIV incidence data were suppressed (counties with 0-4 new HIV diagnoses) in 2015 were removed from the dataset.  This resulted in a dataset containing 747 counties.
4. Data from Scott County, Indiana was removed from the dataset.  This small rural county experienced an HIV outbreak in 2014-2015 due to IV drug use.  This county was an obvious outlier in the data and made it difficult to image all data in a plot.
![alt text](https://github.com/elogue01/Forecasting-HIV-Infections/blob/master/images/outlier_removal_plot.png)
#### Figure 1: HIV incidence vs HIV prevalence data with and without the removal of suppressed counties and the Scott County, IN outlier data

5. The total number of MSM-12months and MSM-5years in counties were transformed into percentages of the adult male population for each county.
6. The percentages for the 20-24 population, the 25-34 population, and the 35-44 population were combined into a percentage for the 20-44 population.
7. Household income data was log transformed.

## Exploratory Data Analysis
Exploratory data analysis was performed on the dataset to identify the features were most promising for use in a multiple linear regression model. This analysis also identified features that would benefit from a transformation of their data.  The following text summarizes the data analysis that was perform. The reader can find a more complete picture of the exploratory data analysis in the [EDA-presentation.ipynb](https://github.com/elogue01/Forecasting-HIV-Infections/blob/master/EDA-presentation.ipynb) jupyter notebook.

### Identifying features with linear relationship to HIV incidence.
After cleaning and merging our different data sources there were 42 features to be explored in our dataframe.  Initial analysis focused on finding features that appeared to have a linear relationship with HIV incidence.  Pair plots and joint plots were used to identified five features (HIV prevalence, % African American, % White, % of Population Uninsured, and Poverty Rate) where a linear relationship to HIV incidence looked likely. Furthermore, spearman correlations for each of these features indicated moderate positive or negative correlation with HIV incidence.  It is not surprising that  HIV prevalence, which measures the number of HIV+ individuals per 100,000 in the population, shows the highest correlation with HIV incidence levels.  The fact that the other features in this group relate to demographics and economics is also to be expected since there is ample evidence indicating that race and socioeconomic status impact an individuals risk for HIV infection.
![alt text](https://github.com/elogue01/Forecasting-HIV-Infections/blob/master/images/high_corr_features)
#### Figure 2: Five features associated with HIV demographicsshowed moderate correlation with HIV incidence.

In addition to these features with a stronger correlation to HIV incidence, we also identified four features (Household Income (log transformed), % Unmet Drug Treatment Need, % Drug Dependent, % of Nonmedical Pain Reliever Use) that show significant though low level correlation with HIV incidence. It was somewhat surprising that the household income levels were not more highly correlated with HIV incidence like some of our other socioeconomic indicators.  It is clear that even with a log transformation, this feature may not have a straight forward linear relationship to HIV incidence. In addition, this group of features also include three features associated with opioid use. A closer look at this figure indicates that while these features may not be linearly related to HIV incidence for all US counties, it is possible that there will be a linear relationship in a subset of counties.  The opioid epidemic has had a particularly strong impact in white rural communities and the impact of the opioid crisis on HIV incidence may only be apparent in state with a number of such counties.  

![alt text](https://github.com/elogue01/Forecasting-HIV-Infections/blob/master/images/lower_corr_features)
#### Figure 3: Features associated with opioid use and household income showed significant though low level correlation with HIV incidence.

One major assumption when building a linear model is that the features used in that model are not co-linear with one another.  After having identified several features with low to moderate correlation with HIV incidence levels, I wanted to check how these different features correlation with one another.  A high degree of correlation between features would suggest that the one feature should be excluded from the model.  Looking at the nine features identified above we only see the % African American and % White features have a correlation coefficient with an absolute value greater than 0.8.  We see a lower level of correlation between the two features dealing with race and HIV prevalence.  This is to be expected since HIV has had an outsized impact on the African American community in the US.  Having correlation coefficients with an absolute value just above 0.7, a feature dealing with race may still be useful in a model with HIV prevalence.  Ultimately we will see how these features play with each other once we begin the feature selection process.
![alt text](https://github.com/elogue01/Forecasting-HIV-Infections/blob/master/images/feature_corr_heatmap.png)
#### Figure 3: A heatmap displays the correlation matrix for nine features with low to moderate correlation to HIV incidence.

## Building Models

### Feature selection using a Lasso regression model
After identifying features that appeared to have a linear relationship to HIV incidence, I wanted to start testing how features actually worked in linear regression models.  A linear regression model with lasso regularization was chosen to test different feature combinations.  The lasso regularization is beneficial in that it can function as an automatic feature selection because it will take the coefficients for unimportant or colinear features to zero.  All models were scored using the BIC (Bayes information criterion) and AIC (Akaike information criterion) which score models based in their fit but with a penalty for model complexity.  Models that have the lowest BIC and AIC scores are those that best balance goodness of fit with model complexity.  Overall, this approach is a good way to narrow our feature space to those that will contribute the most in a model for HIV incidence.

In total there were 22 feature combinations that were tested.  The BIC scoring suggested that a model using HIV prevalence, % of the population that is African American, % of the population that is uninsured, and the log of household income is the preferred model.  The AIC score differs in its assessment of the models as the addition of more features further lowered the model scores in this assessment.  This discrepancy was likely due to the fact that the BIC score more heavily penalizes the model complexity than the AIC scoring.  The simpler four feature model was ultimately selected for further evaluation using a Bayesian approach to building a linear regression model.

This four feature model resulted in a linear regression with the intercept at 16.0, a coefficient for the HIV prevelance variable of 6.6, a coefficient for the % African American variable of 4.1, a coefficient for the % uninsured variable of 2.4, and a coefficient for the log household income variable of -1.8.  This tells us that HIV prevelance is the strongest positive driver of HIV incidence in US counties, followed by the percentage of the population that is African American and the percentage of the population that lacks health insurance.  The negative coefficient for the household income variable suggests that HIV incidence are decreased in response to the increased wealth of a county.  These coefficients are informative as to the factors that drive HIV incidence in US counties and are consistent with the knowledge that the HIV epidemic is impacting communities of color and lower socioeconomic status to a larger extent than other communities in the US. However, one draw back to this model is that the we can not assign any level of confidence to the coefficient values delivered by this model and I therefore cannot say that HIV prevelance variable has a greater impact on HIV incidence than the % African American variable beyond some level of certainty.  We are also unable to take into account regional differences in the way that these variables are impacting HIV incidence levels.  In the following sections I will take a Bayesian approach to building the linear regression model in order to alleviate some of these issues.

![alt text](https://github.com/elogue01/Forecasting-HIV-Infections/blob/master/images/model_selection_table.png)
#### Figure 5: Feature combinations in Lasso regression models were scored using AIC and BIC.

### Building Bayesian Models
After building a Lasso linear regression model above, I sought to take the linear regression model into the Bayesian world.  In contrast to the standard frequentist approach that I used above, a Bayesian approach results in a linear regression model where the coefficients are probability distributions rather than point values.  One key value to this approach is that confidence intervals can be assigned to the coefficients having particular values.  This allows for hypothesis testing with these coefficients.  For instance, we can determine if the value of a coefficient is significantly greater than or less than zero.

I used PyMC3 to build three different Bayesian linear regression models.  I built these models with the aid of a fantastic PyMC3 [tutorial](http://docs.pymc.io/notebooks/multilevel_modeling.html) for multi-level modeling.  The first model was a pooled model, which builds a single linear regression model for a all the counties in the US.  The second model was an unpooled model in which a separate linear regression model was built for counties in each state.  The final model that was built was a multi-layered model, which serves as a compromise between the extremes of the other two models.  In this multi-layered model there were still separate models for each state, however, the coefficients in these models were constrained by a hyper prior distribution. The effect of this nesting within a hyper prior was to shrink these different coefficients back toward the global coefficient value. The multi-layered approach generally is the best approach for models that are using data with a regional quality to them.  After all counties in different states may be quit different from one another, however, there is also an inherent similarity for all counties in the US that should not be ignored.

#### Pooled model
A pooled linear model was built using the Generalized Linear Model (GLM) module in PyMC3.  The means for the resulting probability distributions for the intercept and variable coefficients were very similar to the point values for the coefficients I obtained using the Lasso model above.  However, with this Bayesian approach we can assign a probability to these coefficients having any particular value and we obtain a range in which the true coefficient value is likely to be contained. For example, we can now say with over 95% confidence that the coefficient for the HIV prevalence variable is between 5.5 and 7.5 while the coefficient for the % African American variable is between 3 and 5. Since these ranges do not overlap I can now say with high confidence that HIV prevalence has a greater impact on HIV incidence in US counties than the percentage of African Americans. However, I am still building one linear model for the entire US and cannot determine how these variables might impact regions of the US differently.

![alt text](https://github.com/elogue01/Forecasting-HIV-Infections/blob/master/images/pooledmodel_traceplot.png)
#### Figure 6: Probability distributions for coefficients in the pooled linear model.

#### Unpooled model
In addition to the pooled model, I also built an unpooled model in which the intercept was allowed to vary for each state.  This model essentially builds a separate linear regression model for each state.  This is not a completely unpooled model, however, as the coefficients for the different variables remain the same for each state.  A completely unpooled model was attempted where the intercept and all coefficients were allowed to vary but a number of the  resulting posterior distributions looked as if they had failed to converge (data not shown).  Overall, the unpooled model approach is problematic since it allows regression models to be fit for states with 1-3 counties.  The counties in these states will have an outsized role in determining the linear regression model and may result in unreasonable inferences for these sates. Could there be a compromise where we use the information from the global average (pooled model) while still building separate models for each state (unpooled)?

![alt text](https://github.com/elogue01/Forecasting-HIV-Infections/blob/master/images/unpooledmodel_traceplot.png)
#### Figure 7: Probability distributions for coefficients in the unpooled linear model.

#### Multi-level model
Why yes question asker from the previous section, there is a way to find a compromise between the extremes of the pooled and unpooled models .  A multi-level model is the preferred model where the observations have a natural hierarchical structure which is often the case for data with a regional quality .  In multi-level model the coefficients are viewed as a sample from a population distribution of coeffiecients.  In effect, the model allows information to be shared between the states while still building the different regression model for each state. This information sharing can be particularly important for the states where there are fewer counties.  

I built a multi-level model in which the intercepts and variable coefficients were nested under hyperprior distributions.  The result of adding a hyperprior level was to shrink the resulting coefficients back toward the global mean for each coefficient.  This shrinkage was greater for the states with fewer counties providing data, preventing the coefficients from becoming too dependent on a small number of data points, and ultimately resulting in a more robust model.    In this model we see that the intercept and coefficient for percent African American (beta2) does not vary much between states.  However, we do see variability between states for the coefficients for HIV prevalence (beta1), the percent uninsured (beta3), and the log household income (beta4) variables.  I will explore the interpretation of the differing coefficients in a later section.     

![alt text](https://github.com/elogue01/Forecasting-HIV-Infections/blob/master/images/multimodel_traceplot.png)
#### Figure 8: Probability distributions for coefficients in the multi-level linear model.

### Model Evaluation
To evaluate my models I used three different scoring methods.  The first method was the WAIC(Watanabe-Akaike information criterion) which like other information criterion scores calculates the goodness of fit with a penalty for model complexity.  However, it is a more appropriate score for a Bayesian linear regression model than the AIC or BIC score discussed previously.  The second method I used was the LOO (leave one out) score which does a cross-validation for the model in which the data gets partitioned and the model is repeatedly fit and scored using this partitioned data. The final scoring was done using a RMSE (root mean squared error) which measures the error in the model predictions.  With all three scoring metrics the multi-level model had the lowest (best) score, indicating that it is in fact the best model for HIV incidence.
![alt text](https://github.com/elogue01/Forecasting-HIV-Infections/blob/master/images/Model_score_table.png)
#### Figure 9: Comparison of WAIC, LOO, and RMSE scores for the pooled, unpooled, and multi-level models.

### Model Interpretation
One of the values of building a linear regression model is 
#### HIV Prevalence

![alt text](https://github.com/elogue01/Forecasting-HIV-Infections/blob/master/images/hiv_prev_forestplot.png)
#### Figure 10: Comparison of coefficients for HIV prevalence across states.

![alt text](https://github.com/elogue01/Forecasting-HIV-Infections/blob/master/images/prevalence_coeff_map.png)
#### Figure 11: Mapping the mean coefficients for HIV prevalence across states.

#### Percentage of population that is uninsured

![alt text](https://github.com/elogue01/Forecasting-HIV-Infections/blob/master/images/perc_unins_coeff_trace.png)
#### Figure 12: Highlighting coefficient probability distributions for states where the percent uninsured has a significant positive effect on HIV incidence.

#### Household income

![alt text](https://github.com/elogue01/Forecasting-HIV-Infections/blob/master/images/house_income_coeff_trace.png)
#### Figure 13: Highlighting coefficient probability distributions for states where the household income has a significant positive or negative effect on HIV incidence.

### Conclusions
