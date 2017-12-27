# Lending Club Predictive Modeling

This project explores a dataset provided by [Lending Club](www.lendingclub.com), which connects borrowers with investors through their online marketplace.  

Lending club publicly provides loan statistics for all loans within their database.  Data can be downloaded via their [statistics page](https://www.lendingclub.com/info/download-data.action).

For the purpose of this analysis, we will examine loan data from 2007 to 2017 Q3.

The questions we are looking to answer in this project are as follows:
1. Based on a loanee's application data, is possible to predict whether a loanee will default.  
2. If a loanee will default, can we predict how much the loanee is likely to repay before default?

# Exploratory Data Analysis
An exploratory analysis of the data occurred prior to building models.  The full [EDA notebook can be found here](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project2-predictive-modeling/notebooks/01-lending-club-eda.ipynb)

## Exploring Loan Amounts
Lending club allows borrowers to request up to $40,000.  Examining the loan data, we find that the average loan hovers around $14,445.  We find the majority of loans lie within the $7,500 to $20,000 range.  

![Loan Amnts](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project2-predictive-modeling/reports/figures/loan_amnt_hist.png)

The dataset contains loans in a variety of statuses.  The breakdown is as follows:

| Status | Share (%) |
| --- |:---:|
| Current | 60.4 |
| Fully Paid | 29.1 |
| Charged Off | 6.9 |
| Late (31-120 days) | 1.6 |
| In Grace Period | 1.0 |
| Late (16-30 days) | 0.4 |
| Does not meet the credit policy. Status: Fully Paid | 0.3 |
| Does not meet the credit policy.  Status: Charged Off | 0.1 |
| Issued | 0.1 |
| Default | 0.0 |

Loans provided by Lending Club appear to be very sound, with ~90% of loans residing in either a Current or Fully Paid status. The average loan amount tends to be higher in statuses of Late, Default, and Charged Off, which may identify that a higher loan amount is a factor of loan defaults.

![Loan Amnts By Status](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project2-predictive-modeling/reports/figures/loan_amnt_hist_by_status.png)

Although a status of 'Late' does not mean that a loan has defaulted, we are assuming that it will lead to a default status in this project.  It also allows us to have more data for classifying and predicting bad loans.

![Loan Amnts By Default vs NonDefault](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project2-predictive-modeling/reports/figures/loan_amnt_hist_defaultvsnondefault.png)

In the above graph, we've grouped the following statuses into the 'Default' category:
* Charged Off
* Default
* Does not meet the credit policy.  Status: Charged Off
* Late (16-30 days)
* Late (31-120 days)

## Exploring Issue Date
The lending club provides the month & year that each loan was issued.  Plotting this data allows us to see how lending club has grown over the years.

![Loan Amnts By Issue Date](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project2-predictive-modeling/reports/figures/loan_amnt_by_date.png)

Loan club went public in 2014 - which could explain the sharp jump in loans issues during this time.  If we look closer at the number of loans issued per month, we find a trend that loans increase at the end of each quarter as well as toward the end of the year.  This could be related to financial commitments occurring at the beginning of each quarter (e.g. financial repayments).

![Loan Amnts By Issue Month](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project2-predictive-modeling/reports/figures/loan_count_by_month.png)

## Exploring Terms
The Lending Club offers repayment terms of 36 & 60 months.  

![Loans By Term](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project2-predictive-modeling/reports/figures/terms_defaultvsnondefault.png)

Examining terms based on default vs non-default status, we do not see any particular trends.  However, looking at loan distribution, we find that a longer term often correlates to a higher loan amount.

![Loan Amnts by Term](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project2-predictive-modeling/reports/figures/loan_amnt_hist_by_term.png)

## Exploring Loan Grades
Lending Club assigns a loan grade to each loan representing the risk associated to the loan.  The higher the grade, the less risk associated to the loan.

![Loan Amnts by Date and Grade](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project2-predictive-modeling/reports/figures/loan_amnt_by_date_grade.png)

As we can see from the plot, Lending Club has expanded the number of riskier loans (grades B, C, D & E) over the past 5 years.  The most popular loan is grade B, followed closely by grade C.

If we again use our classification of 'bad' loans as mentioned above, we see the following default rates per grade:

| Grade | Default (%) |
| --- |:---:|
| A | 3.0 |
| B | 6.0 |
| C | 9.0 |
| D | 15.0 |
| E | 21.0 |
| F | 26.0 |
| G | 25.0 |

## Exploring Interest Rates
As with loan grades, Lending Club also assigns an interest rate for each loan.  The higher the interest rate, the riskier the loan.  The average interest rate is 13.1%

![Interest Rates](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project2-predictive-modeling/reports/figures/int_rate_hist.png)

As one would imagine, the lower the loan grade, the higher the interest rate.

![Interest Rates by Grade](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project2-predictive-modeling/reports/figures/int_rate_hist_by_grade.png)

## Exploring Purpose
Lending club categorizes loan purposes for easy aggregation.  We see that the majority of loans are for debt consolidations, followed by credit card repayment.

![Purpose](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project2-predictive-modeling/reports/figures/loan_purpose_hist.png)

Digging deeper, we can can see that loans for educational purposes are most likely to default - with loans related to cars, credit card and home improvements being the least likely to default.

| Loan Purpose | Default (%) |
|---|:---:|
| educational | 21.0 |
| small_business | 16.8 |
| renewable_energy | 13.8 |
| moving | 11.1 |
| wedding | 10.7 |
| house | 10.4 |
| medical | 10.1 |
| debt_consolidation | 9.6 |
| other | 9.4 |
| vacation | 8.4 |
| major_purchase | 8.3 |
| home_improvement | 7.6 |
| credit_card | 7.6 |
| car | 6.6 |

## Exploring Loan Location
Lending Club provides loans to everyone within the United States.  Looking at the loan distribution, California, New York, Texas & Florida are the leading states in lending.

![Location](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project2-predictive-modeling/reports/figures/loan-club-EDA-map.png)

## Exploring Null Values
Before moving on, we need to address missing values within our dataset.  It was found that all loans prior to 2012 having null data for the following columns:

* tot_coll_amt
* tot_cur_bal
* total_rev_hi_lim
* acc_open_past_24mths
* avg_cur_bal
* mo_sin_old_rev_tl_op
* mo_sin_rcnt_rev_tl_op
* mo_sin_rcnt_tl
* mort_acc
* num_accts_ever_120_pd
* num_actv_bc_tl
* num_actv_rev_tl
* num_bc_sats
* num_bc_tl
* num_il_tl
* num_op_rev_tl
* num_rev_accts
* num_rev_tl_bal_gt_0
* num_sats
* num_tl_30dpd
* num_tl_90g_dpd_24m
* num_tl_op_past_12m
* tot_hi_cred_lim
* total_bal_ex_mort
* total_bc_limit
* total_il_high_credit_limit

Rather than dropping 5 years worth of data, we will impute the mean for the above columns on all loans prior to 2012.

Of the remaining data - any column missing more than 10% of data was removed.

# Classification

[see full classification notebook](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project2-predictive-modeling/notebooks/02-lending-club-classification.ipynb)

We are looking to create a classification model to predict whether a loanee will default - based only on the information we have about the loanee's current financial history.  

## Default Status
A loan enters default status once it is overdue by 120 days.  For the purpose of our classification exercise we will assume loans in a late status will convert into a default status.  This assumption also increases the population size of our data that will be used for model training. As such, we will assume the following statuses are 'default'
* Late (16 - 30 days)
* Late (31 - 120 days)
* Default
* Charged Off  

## Measuring Success
We will define model success by looking at a model's ROC AUC score.  This will allow us to measure how well our model predicts default loans, taking into account true positives & false positives.

## Models
We tested 5 models during this process
* Logistic Regression - Using only Engineered features
* Logistic Regression - Using all features
* Logistic Regression - Using feature selection
* Random Forest Regression - Using Feature Selection
* Random Forest Regression - Using Feature Selection & Balanced Classes

### Logistic Regression (Engineered Features)
As referenced above, we created 8 features during our feature engineering stage.  We want to test if our engineered features alone can successfully predict a default status using Logistic Regression.  


| Feature Name | Description |
| --- | --- |
| low_deliqent_job |
| credit_line_length_mnths |
| inq_last_6mths_cat |
| pub_rec_cat |
| fully_funded |
| issue_d_year |
| issue_d_month |

Creation of this model produced an roc auc score of only 57%; slight better than randomly guessing the outcome.  Having a closer look at the classification report it was found that the model was not able to predict the positive class (default status).  

<< insert classification matrix >>>

This is likely due to small number of features used to create the model and also unbalanced classes (which we address at a later stage).  It was determined that this model was not a suitable solution, and additional features were required to achieve a better model.

### Logistic Regression (All Features)
Stating that we used all features for the next model is slightly mis-leading.  The current dataset contains features that relate to Lending Club's classification (e.g. loan grade, sub grade, loan status) as well as the current health status of a loan (e.g. current payments, interest collected, late fees collected).  

Since we want to create a model that determines default status before a loanee receives the loan, we will remove features that are highly correlated to the loan's current status.  These features included:

* total_pymnt
* total_pymnt_inv
* total_rec_int
* total_rec_late_fee
* total_rec_prncp
* funded_amnt
* funded_amnt_inv
* out_prncp
* out_prncp_inv
* last_pymnt_amnt
* loan_status
* grade
* sub_grade

With the above features removed, we create a simple Logistic Regression model using the remaining features to achieve an roc of 86%

<< insert code snap shot>>>

<< insert cross validation score>>

### Logistic Regression (Feature Selection)
We have increased our roc auc score considerably using all features.  Can we achieve improve the performance of our model by decreaseing teh number of features required to predict a similar roc auc score?

We can answer this by performing feature selection on our dataset.  We want to be experimental here by testing several feature selection techniques.   Specifically we will test:

* SelectFromModel using LinearSVC & ExtraTreeClassifier
* SelectKBest using f_classif
* PCA

For SelectFromModel, we test various thresholds levels. At each level we record the number of features selected and the resulting roc auc score.  

For SelectKBest we test various numbers of features and record teh resulting roc auc score.  

For PCA, we test selecting various number of components and record the resulting roc auc score.  

We can then plot the results to identify a correlation between the number of features selected and roc auc score:

<< inser feature selection graph >>

SelectFromModel using LinearSVC appears to have performed best.  An roc auc score of ~87% can be achieved using only 40 features (a third of the total number of features).  SelectFromModel using ExtraTreeClassifier can achieve a similar score, however appears to have a sharp drop off using features less than 40.  This could represent a less robust model.  

Although PCA doesn't select features (rather it selects components), we find that PCA would require over 120 components in order to match a score of 87%. We are not reducing the complexity of our model in this case, and therefore we will not use PCA in this analysis.

Finally, SelectKBest appears to have performed almost as good as LinearSVC.  The difference is that SelectKBest achieves results in a fraction of time over LinearSVC. As a result, we are going to use SelectKBest as our feature selection, using only 35 features.

In order to optimize Logistic Regression as much as possible we have also performed hyperparamter tuning.  We determined that a value of C =500 performs optimally for our model.

Putting this all together, our model has now improved to a cross validation score of 88%.

<< insert code>>

<< insert cross validation score >>

We are aware of issues with the recall score in this model, oly producing 56% - still not great.  We have trouble correctly identifying default loans.  For the current model, we report 44% of default loans will not default.  We have room for improvement.  Can we improve using a different model?

### Random Forest Regression (Feature Selection)
We created a model using Random Forest Regression, implementing our SelectKBest feature selection algrothm as previously identified.

We want to tune the Random Forest Regression much like the logistic regression model.  Random forest regression does have additional parameters than Logistic Regression.  As such, we first perform a tuning of each parameter on its own to identify optimal ranges to be used in hyperparamter tuning.  We tune the following parameters

<< insert parameter listing>>

The result is the following graph.

<< insert parameter graph>>

With this, we identify a parameter grid to perform RandomizedSearchCV - identifying optimal parameters

<< insert paramter grid tuning results>

We achieve an 88% result, but again horrible recall score.  It appears that this is a class unbalanced issue.  In the next model we address class imbalance.

### Random Forest regression (Feature Selection - Balanced Classes)
To address the issue of class imbalance, we look at using SMOTE prior to performing classification.  

With classed balanced, we recreate our random forest classifier using the tuned parameters as above.  

Our results are considerably better, achieving an roc auc score of 97%.  

<< insert classification report >>>

We also see that our recall score has increased significantly, up to 85%.  We are much happier with these results.  

### Classification Conclusion
Brining it all together, we plot the ROC Curve for all models to compare results:

We find that our Random Forest Classififer using SMOTE for class balancing is performing the best.  

This is likley due to the fact that Random Forests produces non-linear decision boundaries, whereas Logistic Regression produces a single linear decision boundary. We also must note that balancing our class greatly improved our classification success, improving our Random Forest model to 97% during our last cross validation test.

When investigating feature importances, we find the following information is most important when predicting defaults:

delinq_2yrs: The number of of 30+ days past-due incidences of delinquency in the borrower's credit in the past 2 years.
open_acc: The number of open credit lines in the borrower's credit file
int_rate: Interest rate on the loan
term: The number of payments on the loan (in months)
mort_acc: Number of mortgage accounts


# Regression




Conclusions
* Given time, I would recreate models with & without the columns missing from prior to 2012.  I feel that I had added bias to the data by imputing the mean for each column in data prior to 2012.  Creating models with & without the columns would allow comparisons to see how imputing data affects the outcome.  
* Balance classes sooner - use throughout
