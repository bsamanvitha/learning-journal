# Mistakes to avoid in ML (LinkedIn Learning Course)

_Mistakes_

_- how to avoid them_

Assuming data is good
- visualize the data: describe() in pandas, pandas-profiling
- check for duplicates: duplicated function in pandas, drop_duplicates()
- beware of missing values: isnull().sum() --> .dropna(), .fillna(), Scikit-learn's Simpleimputer

Neglecting to consult subject matter experts
- solicit feedback from SMEs
  - known issues
  - prior issues with modeling
  - additional concerns
 
Overfitting models
- Regularization: introducing a penalty for overly complex features that reduces or eliminates their weight in our model. Two types include Lasso or L1 regularization and Ridge or L2 regularization

Not standardizing data
- ML techniques will incorrectly assign a higher weight to features of a higher magnitude
- MinMaxScaler (0 to 1) and StandardScaler (standard dev from mean for that feature)

Focusing on the wrong factors
- sometimes rather that first trying to improve the model through the ML technique, one can try incorporating more data sources into the model
- make a wish list -> find available data sources and incorporate relevant new variables
- reach out to other data teams
- more data is always not better

Data Leakage
- when information from outside your training set enters your model
- use corr() to check the correlation between the data
- when scaling, fit your scaler to your training group only, then transform both training and test group

Forgetting traditional statistics tools
- Regression approach
- A/B testing - randomized experiments, causal

Assuming deployment is a breeze
- plan your deployment strategy at the beginning of the project
- will you be scheduling batch predictions or predicting in real time?
- what are the compute requirements
- how will you monitor performance over time
- will you be updating and re-deploying your model

Assuming ML is the answer
- Do you have a large and diverse set of data to start with
- Do you have a clear outcome you're trying to predict
- Do you have a hypothesis
- Do you need ad-hoc analysis or full-fledged ML
- Will results drive meaningful action

Developing in a silo
- invite others to look at your code
- collaborate with data scientists
- version and document code
- communicate regularly with SMEs

Not treating for imbalance sampling


















