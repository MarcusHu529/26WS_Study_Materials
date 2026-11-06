## Definition
It is a **supervised** machine learning algorithm used for classfication problems. 
- It's used for binary classification where the output can be one of two possible categories.
- It uses sigmoid function to convert inputs into a probability value between 0 and 1.
## Types of Logistic Regression
1. **Binomial Logistic Regression**: Used when the dependent variable has only two possible categories for binary classification tasks. Most commonly used.
2. **Multinomial Logistic Regression**: Used when the dependent variable has three or more unordered categories. It extends logistic regression to handle multiple classes.
3. **Ordinal Logistic Regression**: Used when dependent variable has three or more categories with a natural order, such as Low, Medium, and High. It considers the ranking of categories during prediction.
## Assumptions
1. **Independent Observations**: Each data point should be independent of the other
2. **Binary Dependent Variable**: The target variable is typically binary and can take only two values, such as 0/1 or Yes/No. For multiclass problems, extensions such as Softmax-based (Multinomial) logistic regression are used.
3. **Linearity of Log-Odds**: The independent variables should have a linear relationship with the log-odds of the dependent variable.
4. **No Extreme Outliers**: Extreme outliers can distort coefficient estimates and negatively affect model performance.
5. **Large Sample Size**: A sufficiently large dataset helps produce more reliable and stable predictions.
