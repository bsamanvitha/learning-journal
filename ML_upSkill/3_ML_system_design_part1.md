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

**Training Pipeline**
1. Data partitioning
    - store data in a column-oriented format like Parquet or ORC via time partitioning for efficiency
    - Parquet can reduce the data that is scanned by 99% and speed up the query times to be 30x faster
2. Handle imbalance class distribution
    - use class weights in loss function to penalize the majority class
    - naive resampling (resample the majority class at a certain rate to reduce the imbalance)
    - SMOTE: randomly picking a point from the minority class and computing the k-nearest neighbors for that point
3. Choose the right loss function
    - for binary classification: cross-entropy
    - for CTR (click through rate): normalized cross entropy loss (logloss)
    - for forecast: MAPE and SMAPE
4. Retraining requirements
   - Adtech and recommendation/personalization: important to retrain models to capture changes in user's behavior and trending topics
   - need to run fast and scale well with big data

**Inference**
- inference is the process of using a trained machine learning model to make a prediction
1. Imbalance workload
    - split workloads onto multiple inference servers, send it to workers in the pool, pick workers through an algorithm (Round Robin, etc.)
    - serving logics and multiple models: combine multiple models in inference, route to a different model to get score
3. Non-stationary problem
    - based on how frequently the model performance degrades, we can decide how often it needs to be retrained or updated.
4. Exploration vs. exploitation: Thompson Sampling
    - tradeoff: exploring means to experiment, take risks but it can result in too few Ad conversation, hurting revenue; exploitation means to take the guaranteed reward path, but it may not be the best reward. One common technique for this is Thompson Sampling

**Metrics Evaluation**
- measure model performance in both online and offline environments
1. Offline metrics
   - logloss, MAE (Mean Absolute Error), R2
2. Online metrics
   - evaluate on certain pre-defined metrics and business metrics, then replace the current prod models with new models

## Video Recommendation
**Problem Statement**
- build a recommendation system for YouTube users
- goal: maximize users' engagement and recommend new types of content

**Metrics**
- offline: reasonable precision, high recall, ranking loss, logloss
- online: A/B testing to compare CTR, watch time, and conversion rates

**Requirements**
- training: train many times during the day as user behavior can be unpredictable and videos can become viral during the day. Balance the operation cost vs. the online metrics improvement
- inference: latency needs to be ideally 100 ms (i.e., recommend 100 videos for every user when they visit the homepage), balance between exploration vs. exploitation (fresh new content vs. relevancy)

**Modeling**
1. Candidate generation model: finds the relevant videos based on user watch history
    - feature engineering: each user has a list of video watched, minutes watched
    - training data: use a time period like last 6 months to optimize accuracy and training time
    - model: matrix factorization, collaborative algorithms (low-latency alternatives to collab filtering: Inverted Index, FAISS, Google ScaNN)
2. Ranking model: optimized for the view likelihood
    - feature engineering:
          - watched video IDs -> video embedding
          - past searches --> text embedding
          - location --> geolocation embedding
          - user-features --> normalization or standardization
          - previous impression --> normalization or standardization
          - time-related --> month, week, holiday, week, hour
    - training data: user watched history data
    - model: fully connected neural network, sigmoid activation at the last layer (returns in the range [0, 1], can be used as probability), ReLU as an activation function for hidden layers, cross-entropy loss for loss function

**High-level system design**

Assumptions:
- videos views per month = 150 billion
- 10% are from recommendations
- homepage consists of 100 video recs for a user
- on avg, user watches 2 out of 100 videos
- total number of users = 1.3 billion
- If users do not click or watch some video within a given time frame, i.e., 10 minutes, then it is a missed recommendation
- data size: 15 billion positive labels and 750 billion negative labels
- total size est. = 0.4 Petabytes
- we can keep the last six months or one year of data in the data lake, and archive old data in cold storage
![IMG_B19F63FB407A-1](https://github.com/bsamanvitha/learning-journal/assets/6962922/e4da4794-ac99-42b9-b0ee-90a9b7164d0a)

**Scaling**
- multiple app servers and use load balancers to balance loads
- multiple candidate generation services and ranking services
- (Kubernetes Pod Autoscaler), Kube-proxy - candidate generation service can call ranking service directly, reducing latency even further
