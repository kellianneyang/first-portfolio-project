# **Food Sales Predictions**
The goal of this analysis is to provide insights into the retailer's sales data and to create a machine learning model that predicts some of the variation in the data.

# **Data Dictionary**
|**Variable Name**|	**Description**|
| ----- | ----- |
|Item_Identifier|	unique product id; 1559 unique instances in dataset|
|Item_Weight|	weight of product|
|Item_Fat_Content|	fat content of the product (low fat or regular)|
|Item_Visibility|	proportion of total display area in a store given to the product|
|Item_Type|	food or drink category (e.g., seafood, breakfast, dairy, snacks, etc.)|
|Item_MRP|	Maximum Retail Price (in rupees)|
|Outlet_Identifier|	unique store identifier; 10 unique instances in dataset|
|Outlet_Establishment_Year|	year in which store was established|
|Outlet_Size|	size of store (small, medium, or high/large)|
|Outlet_Location_Type|type of area the store is located in (Tier 1, Tier 2, or Tier 3)|
|Outlet_Type|	type of store (Grocery Store or Supermarket 1, 2, or 3)|
|Item_Outlet_Sales|	the sales of the item in the store, given in rupees; this is the target variable for the machine learning model|

# **Cleaning**
Before machine learning preprocessing, the data was cleaned with the following steps:

- Duplicate rows were dropped
- Spelling inconsistencies were fixed
- Categorical ordinal data was numerically encoded
- High-cardinality feature was dropped ('Item_Identifier')

# **Exploratory Visualization**
To begin to understand the data, I created several exploratory visualizations. Three of them are displayed here.

## Exploratory Scatterplots and Histograms
The first is an initial exploration of all the numeric variables plotted against each other as scatterplots, to see which numeric variables have a relationship with which others. Along the diagonal are univariate histograms showing the distribution of each variable in the plot.

![image](https://github.com/kellianneyang/food-sales-predictions/blob/main/images/food%20sales%20pairplots.png)

The second exploratory visualization, below, is a group of barplots. Each barplot shows the distribution of one of the categorical variables in the data; each count is the number of rows in the data set assigned to that particular value.

![image](https://github.com/kellianneyang/food-sales-predictions/blob/main/images/item%20fat%20content%20barplot.png)

![image](https://github.com/kellianneyang/food-sales-predictions/blob/main/images/item%20type%20barplot.png)

![image](https://github.com/kellianneyang/food-sales-predictions/blob/main/images/outlet%20size%20barplot.png)

![image](https://github.com/kellianneyang/food-sales-predictions/blob/main/images/outlet%20location%20type%20barplot.png)

![image](https://github.com/kellianneyang/food-sales-predictions/blob/main/images/outlet%20type%20barplot.png)

The third exploratory visualization is a heatmap, which shows the correlation coefficient between each of the numeric variables (this is another way of visualizing their relationships, and gives the same sort of information as the pairplots above). 

![image](https://github.com/kellianneyang/food-sales-predictions/blob/main/images/food%20sales%20heatmap.png)

## 1. Do high-priced items comprise a larger proportion of sales than low-priced items?

We can see from the scatterplot below that price and sales are positively correlated, that is, as an item's price rises, so does the absolute rupee amount of its sales. This makes intuitive sense, since more money gets spent on items sold for more money.

![image](https://github.com/kellianneyang/food-sales-predictions/blob/main/images/item%20price%20correlation%20with%20sales.png)

However, do higher-priced items make up more sales just because they are priced higher? Or do higher-priced items actually sell less than lower-priced items?

A good guess is that higher-priced items do sell less than lower-priced items, because people have limited money and so are likely to buy lower-priced items more frequently. 

However, this is not the case with our data. In the pie chart below, we can see that high-priced items make up 39% of the overall sales. This means that high-priced items are actually selling more than low-priced items, not just making more sales in dollar amount because they are priced higher.

![image](https://github.com/kellianneyang/food-sales-predictions/blob/main/images/proportion%20of%20sales%20pie%20chart.png)

## 2. What is the relationship between item type and sales?

![image](https://github.com/kellianneyang/food-sales-predictions/blob/main/images/sales%20across%20item%20types.png)

# **Machine Learning Preprocessing**

The data was preprocessed before fitting and testing machine learning models with the following steps:

- Validation was performed (75% of data retained for training; 25% reserved for testing)
- Missing numerical values were imputed with the mean
- Numerical features were scaled
- Missing nominal vlues were imputed with the most frequent value
- Nominal features were one-hot encoded

# **Machine Learning Model 1: Linear Regression**

A linear regression model was fit on the training data and then tested on the reserved testing data. 

The linear regression model was able to account for about 57% of the variation in the testing data (coefficient of determination / R2). In monetary terms, the model was able to predict the sales amount within a range of +/-804 rupees from the actual sales amount on the testing data (mean absolute error). 

![image](https://github.com/kellianneyang/food-sales-predictions-project/blob/main/Images/linear_regression_top_3_coefficients.png)

The three largest coefficients plotted above are all one-hot encoded categorical features. This means their coefficients can be interpreted as how much the target changes if the observation belongs to that category. For the three categories above (largest to smallest):

If the observation belongs to 'Outlet_Type_Supermarket Type 1', then the target sales will increase by 1006.523 rupees.

If the observation belongs to 'Outlet_Type_Supermarket Type 2', then the target sales will increase by 849.785 rupees.

If the observation belongs to 'Outlet_Identifier_OUT027', then the target sales will increase by 849.785 rupees.



# **Machine Learning Model 2: Random Forest Regression**

A random forest regression model was fit on the training data and then tested on the reserved testing data. 

The random forest regression model was able to account for about 60% of the variation in the testing data (coefficient of determination / R2). In monetary terms, the model was able to predict the sales amount within a range of +/-728 rupees from the actual sales amount on the testing data (mean absolute error). 

![image](https://github.com/kellianneyang/food-sales-predictions-project/blob/main/Images/random_forest_regression_top_5_feature_importances.png)

For an interpretation, we keep in mind that built-in feature importances are biased toward valuing features with high-cardinality (like 'Item_MRP') over others. We also don't know the relationship of the feature to the target unless we do more EDA or employ additional tools.

With those caveats aside, we can see that the most important feature for our random forest regressor model is 'Item_MRP', with a feature importance of about 0.5. Because feature importances add up to 1, this means that the model heavily relied on 'Item_MRP' to make its decisions to split nodes (about half the time).

Our other top features were 'Outlet_Type_Supermarket Type 1', 'Outlet_Identifier_OUT027', 'Outlet_Type_Supermarket Type 2', and 'Outlet_Establishment_Year'. Three of these four (all save 'Outlet_Establishment_Year') were in the top 3 largest coefficients for our linear regression model.

Here are the SHAP summary plots (bar and dot versions), which show the most important features to the model's prediction and a deeper dive into those features and how the values of those features are driving the model's predictions.

![image](https://github.com/kellianneyang/food-sales-predictions-project/blob/main/Images/shap_rf_bar_summary_plot.png)

The SHAP bar summary plot identified the same top five features that the random forest model relied on. 

![image](https://github.com/kellianneyang/food-sales-predictions-project/blob/main/Images/shap_rf_dot_summary_plot.png)

The top three most important features identified by the SHAP dot plot above are:

1. **'Item_MRP':** High prices (identified in red) are driving the SHAP values for rows up, meaning that they are pushing up the total sales number in that row. The reverse is true for low prices (in blue). There looks to be a very orderly spread of high-, mid-, and low-value items directing the final sales.

2. **'Outlet_Type_Supermarket Type1':** The rows that belong to this categorical variable (in red) all show up on the positive side, meaning they are driving up final sales numbers. The rows that belong to other values in this category contribute to lower final sales.

3. **'Outlet_Identifier_OUT027':** This particular outlet, with rows identified in red, is associated with higher final sales. Other outlets have lower sales.


# **Final Recommendation**

The random forest regression machine learning model is better able to predict sales data than the linear regression model, so going forward the random forest regression model should be used. 

# **Next Steps for Further Insights**

More data will be the best way to refine the model's predictions and increase its ability to accurately predict sales outcomes. 

# **Questions and Comments**

For any additional questions or comments, please contact kellianne(dot)yang(at)gmail(dot)com.
