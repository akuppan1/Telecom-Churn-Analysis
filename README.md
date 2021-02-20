# Telco Customer Churn Analysis and Recommendations

## What this project is about:
  We were tasked to analyze a telecom company's customer data in order help them mitigate their customer churn rate.

## Summary
  We saw that there indeed was a pattern for churn. Customers who churned were usually people with month-to-month contracts and churned within the first 12 months of their contract. We based our recommendations on this observation as well as others found in the data. We utilized random forest decision trees to find feature importance with GridSearchCV tool to fine-tune the parameters of the model in order to optimize the features selected. 

## The Data
https://www.kaggle.com/blastchar/telco-customer-churn

This data is from the IBM business solution called "IBM Cognos Analytics"

Additional information about the dataset can be found [**here**](https://community.ibm.com/community/user/businessanalytics/blogs/steven-macko/2018/09/12/base-samples-for-ibm-cognos-analytics)


![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/IBM-Cognos-analytics-logo-540X280.jpg?raw=true)

From: https://senturus.com/wp-content/uploads/2016/05/IBM-Cognos-analytics-logo-540X280.jpg


## Agenda
1. Clean the data to prepare it for analysis
2. Analyze the most important features within the data 
3. Give business recommendations based on the analysis of the features


### 1. Cleaning the data

Our goal here is to make the data able to play nice with the classifiers/modules that we want to use.
I saved the final cleaned dataframe to a new modified csv file called: TelcoCustomerChurn[MODIFIED].csv
Decisions that we made with the data to prep it for the classifiers:

| Column | Change | Reason |
| ----- | ----- | ----- |
| Contract | 1) Month to month --> 0, 2) One Year --> 1, 3) Two year --> 3 | This is to turn string into numbers for the random forest.
| PaymentMethod | 1) Electronic Check/Bank Transfer(auto)/Credit Card(auto) --> 0, 2) Mailed Check --> HighFrictionPayment --> 1 | Redundant information. Reduced complexity : 0 is for LowFrictionPayment and 1 is for HighFrictionPayment|
| TotalCharges | Removed rows with blank values | The blank value rows were interfering with changing column type to float |  
| TotalCharges | Scaled it with MinMax Scaler formula | To make it work well with classifier | 
| MonthlyCharges | Scaled it with MinMax Scaler formula | To make it work well with classifier | 
| tenure | Scaled it with MinMax Scalar formula | To make it work well with classifier | 
| MultipleLines, InternetService, OnlineSecurity, OnlineBackup, DeviceProtection, TechSupport, StreamingTV, StreamingMovies | Switched to 1/0 | In order to simplify the data for the classifier, I changed any yes/no to 1/0. Also male/female to 1/0. I replaced some redundant "no" information as well. |
| 'Unnamed' Index | Dropped column | I had 2 index columns. Dropped the one I don't need |


### 2. Analyzing the most important features within the data

To find the features that stand out, I went with RandomForestDecisionTree classifier and used GridSearchCV in order to find the best parameters
Customized code for ROC curve from [this link](https://medium.com/all-things-ai/in-depth-parameter-tuning-for-random-forest-d67bb7e920d)
I used this code to lower the number of parameters to pass through GridSearchCV in order to not have my computer run forever on tens of millions of runs
Managed to get GridSearchCV runs down to 288000 fits by guessing param sizes with the ROC curve checks
* 'bootstrap': [True, False],
* 'max_depth': [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, None],
* 'max_features': ['auto', None],
* 'min_samples_leaf': [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
* 'min_samples_split': [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
* 'n_estimators': [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15]

### Results from GridSearchCV:

{'bootstrap': True, <br/>
 'max_depth': 9, <br/>
 'max_features': 'auto', <br/>
 'min_samples_leaf': 7, <br/>
 'min_samples_split': 6, <br/>
 'n_estimators': 14} 

### 3. Analysis of features and recommendations
#### Feature Importances
After I ran the RandomForestDecisionTree classifier, I plotted the feature importances in descending order:
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/features_results.PNG)
<br/>
##### ------------- CONTRACT TYPES --------------------------------------------
Analyzing the month-to-month contracts we see the following information below. We see that a good portion of the people who churn happen within the first year of maintaining a month-to-month contract.  <br/>
<br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/features_results_2.PNG)
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/features_results_3.PNG)
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/features_results_4.PNG)
<br/>
<br/>
We can compare churn in month-to-month with other contract types. Here are the 1-Year and 2-Year contracts. We see that the churn goes lower as the contract number goes higher. <br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/features_results_5.PNG)
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/features_results_6.PNG)
<br/>
<br/>
<br/>
##### --------------- TENURE ----------------------------------------------------
Analyzing tenure for customers that churned, we see that it confirms the Contract type analysis from above. We see a huge spike in tenure in the first 12 months.
<br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/tenure_churn_yes_pie.PNG)
<br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/feature_results_bar_number_churned_vs_tenure_range.PNG)
<br/>
<br/>
We also see that tenure has many contracts with high month amounts where the customer does not churn.
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/feature_results_bar_No-Churn_analysis.PNG)
<br/>
##### ------------------ MONTHLY CHARGES -----------------------------------------------------
Below we see the month-to-month pricing for churned customers. The spikes may indicate that there is a higher number of base-plan tier users, but we don't have enough information to be sure of that. <br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/feature_results_bar_1monthtenure_vs_monthlycharges.PNG)
<br/>
We can compare that amongst non-churned customers with 72 month tenure below. Again, we don't have too much information but we can see there are certain price tiers and a good amount are on the lower price point. <br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/feature_results_bar_72nochurn_vs_monthlycharges.PNG)
<br/>
<br/>
We see below number churned vs. Monthly Charges:
<br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/feature_results_bar_YES-CHURN_vs_monthlycharges.PNG)
<br/>
We see below number NOT-churned vs. Monthly Charges:
<br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/feature_results_bar_NO-CHURN_vs_monthlycharges.PNG)

##### -------------------TOTAL CHARGES ---------------------------------------------------------






