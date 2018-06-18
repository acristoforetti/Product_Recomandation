# Technical Assessment &#39;Santander Product Recommendation&#39;



**Introduction**

The objective is to provide better product recommendations to the customer base. The base provided by Santander includes the history of around 1 million customers in a training set and a similar number in the test. The variables included the customer information (age, salary, gender etc. etc.) and a Boolean flag for each of the 24 products. The period of the data is between January 2015 and May 2016. The challenge is to predict which new product the test users are most likely to buy in June 2016. The label for the June 2016 is not available, but the score of the prediction can be tested by submitting on the kaggle website.

**Approach followed**

###### Insights from the EDA

I have started with the data exploration on the two files provided (test\_ver2 and training\_ver2).  The first 24 variables are sociodemographic variables. These variables do not change over the months (excluded for &#39;antiguedad&#39; the tenure and the age). For the products, we have a binary variable for each month (1 if the product is owned). In the script, a new dataframe was created to analyse the product acquisition (product owned in t1 and not in t0) and product abandoned (owned in t0 but not in t1). This dataframe is used in the main code to calculate features and target.

_The major insight from the EDA were:_

- A very small part of the customer base owns a product
- There are a few products which are very popular while the rest are not so popular (some of them are equal to zero in some periods)
- Acquisition during the year seams to follow a seasonal pattern. This is both a general seasonal effect, and a specific trend for group of products. This needs to be accounted for when choosing the training period. Moreover, one of the peaks is in June 2015. This is very relevant as we have to predict June 2016
- Customers tend to abandon products with similar frequency as observed for acquisition of products. These variables are used in the model. The hypothesis is that customers tend to substitute products with other products. So, this can be a factor to predict acquisitions.
- Training and test data have the same distribution

###### Pre-Processing

For the numerical variables, (age, income and tenure) I have treated the missing values (with average approach) and outliers. For the categorical variables, I have encoded these to numerical values. For these variables, I have not treated the missing values, but managed them as an additional category.

###### Feature engineering and modelling

_Feature built_

I have created several lagged features for the products. Using the product owned, acquired and dropped.

_Target selection_

Due to the variability of the product, I have tested two different training sets. The first one, had the target on the product acquired on may 2016 (this is the closest to June 2016). For the second one, to have the most signals from the seasonality effect, I have used June 2015 as target (as we have to predict June 2016). With an initial XGboost, without any optimization, I submitted both versions on the Kaggle website. The first one performed around 0.019 while the second one performed around 0.026 (with the target in 2015). This is a big difference, so I focussed on the second version to try to optimise the prediction.

_Model strategy_

From the result of the basic model, I performed a feature selection using the feature importance from the XGBoost output. I have removed the features with value zero and close to zero.

With the new list of variables, I have performed the grid search to optimise the model. The result for the last model was 0.0298528 (in this I did not use the feature selection and I used all the features). 
###### Future improvements

These are the list of possible areas that I have identified as potential improvements:

- I have used training data with target in June 2015 which worked better that 2016. However, this is not optimal, as I have not used the most recent data. A good approach can be to try to combine the result from the two models.
- I have used a single model with multiple categories. However, products seem to change in different way during the year with some general trends and single product trends. Maybe, by building a single binary model for each product, prediction can be improved.
- More time could be dedicated to understanding the relationship between abandoning and acquiring new products.
- I have not tested a solution based collaborative filter. I have tried to use this in the past on tariff recommendation models in telco with very negative results. However, this could be something to test.
- Finally, I have only tested a XGBoost, while logistic regression and random forest were other potential candidates.