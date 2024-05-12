# Machine Learning System Design (Educative course)
## ML Primer
**6 basic steps to approach ML System Design:**
1. Problem statement
    - ask questions
    - be clear on the problem statement
2. Identify metrics
    - can start with the popular metrics like logloss and AUC for binary classification, or RMSE and MAPE for forecast
3. Identify requirements
    - training requirements include components like data collection, feature engineering, feature selection, and loss function
    - inference requirements can include low latency and scaling system to serve millions of users
4. Train and evaluate models
5. Design high-level system
    - In this stage, we need to think about the system components and how data flows through each of them. The goal of this section is to identify a minimal, viable design to demonstrate a working system
6. Scale the system
    - understand system bottlenecks and how to address these bottlenecks

**Feature Selection and Feature Engineering**

1. One hot encoding: converts categorical variables into a one-hot numeric array
    - Common problems: expansive computation and high memory consumption due to high-dimensional feature vectors
    - Best practices: levels/categories that are not important, can be grouped together in the “Other” class
2. Feature hashing: converts text data or categorical attributes with high cardinalities into a feature vector of arbitrary dimensionality
    - Benefits: a way to reduce the increase in dimension and memory by allowing multiple values to be present/encoded as the same value
    - Feature hashing example: First, you chose the dimensionality of your feature vectors. Then, using a hash function, you convert all values of your categorical attribute (or all tokens in your collection of documents) into a number. Then you convert this number into an index of your feature vector.
    - Feature hashing in tech companies: If the hash size is too small, more collisions will happen and negatively affect model performance. On the other hand, the larger the hash size, the more it will consume memory.
3. Crossed feature: usually used with a hashing trick to reduce high dimensions
    - example: using crossed feature between user location and user job title for job recommendation model
4. Embedding: transforming features from original space into a new space to support effective maching learning
    - Benefits: captures semantic meaning of features, similar features will be close to each other in the embedding vector space
    - Examples: store embedding where each store is one word, each user session is one sentence, and vectors for a consumer is achieved by summing the vectors for each store they ordered from in the past; account embedding to recommend relevant content (photos, videos, and stories) to users
5. Numeric features:
   - Normalization: make the mean equal 0, and the values be in the range [-1, 1] (or [0, 1] based on the usecase)
   - Standardization: (feature value - mean of feature value) / (stdev of feature value)



