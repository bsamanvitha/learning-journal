# Machine Learning System Design (Educative course) - Part 2
## Feed Ranking

**Problem Statement**

Design a personalized LinkedIn feed to maximize long-term user engagement (example: user frequency, Click Through Rate). LI feed contains connections, informational, profile, opinion, site-specific. Different activities have very different CTR

**Metrics**

Offline metrics
- Click Through Rate (CTR) = number of clicks that feed receives, divided by the number of times the feed is shown
- Normalize cross-entropy and AUC
Online metrics
- Conversion rate (ratio of clicks with number of feeds)

**Requirements**

Training
- retrain the models (incrementally) multiple times per day
- personalization - different users have different tastes and styles for consuming their feed
- data freshness - avoid showing repetitive feed for user

Inference
- scalability - handle 300m users)
- latency - return within 50ms
- data freshness - Feed Ranking needs to be fully aware of whether or not a user has already seen any particular activity. Data pipelines need to run really fast

**Modeling**

Feature engineering:
- user profile (job title, industry, demographic, etc.) —> low cardinality then one-hot encoding else Embedding
- Connection strength between users —> similarity between users or Embedding for users and measure the distance vector
- Activity features —> activity Embedding and measure the similarity between activity and user
- Cross features —>  combine multiple features


Training data:
- select a period of data: last month, last 6 months, etc. (balance training time and model accuracy)
- downsample the negative data to handle the imbalanced data
- ways to collect training data:
    - rank by chronicle order - rank post based on chronological order to collect click/not-click data (serving bias because of user’s attention on the first few posts)
    - random serving - rank post by random order (may lead to bad UX)
    - feed ranking algorithm - ranks the top feeds, permute randomly, use the clicks for data collection (provides randomness and helps in exploring more activities)

Model:
- logistic regression to work well with sparse features
- use distributed learning: Logistic Regression in Spark
- fully connected layers with the Sigmoid activation function applied to the final layer
- CTR is usually very small (less than 1%) —> we would need to resample the training data set to make the data less imbalanced
- split data into training and validation for evaluation. Data until time to t for training the model, data from time t+1 for testing, reorder ranking based on our model during inference. Total match = total clicks

**High-level system design**

Calculations:
- 30 million MAU
- user visits 10 times per month
- user sees 40 activities per visit
- total = 120 billion observations or samples
- if CTR is 1% for 1 month —> 1 billion positive labels, 110 billion negative labels
- storage: if each row takes 500 bytes to store, 120 billion rows will take: 60 Terabytes. To save costs we can keep the last 6 months or 1 year of data in the data lake and archive old data in cold storage

Feature store is feature value storage. Example: MySQL, Cluster, Redis, DynamoDB. Item store stores all activities generated by users. 

![IMG_6E8283D4DB8A-1](https://github.com/bsamanvitha/learning-journal/assets/6962922/87a43318-3e34-4463-95f2-c55596e35a29)

Flow:
- Client sends feed request to Application Server
- Application Server sends feed request to Feed Retrieval Service
- Feed Retrieval Service selects most relevant feeds from Item Store
- Feed Retrieval Service sends feed ranks request to Feed Ranking Service
- Feed Ranking Service gets the latest ML model, gets the right features from Feature Store
- Feed Ranking Service scores each feed and returns to Feed Retrieval Service and Application Server. Application server sorts feeds by rankings and return to client

**Scaling**
- Scale out the Feed Service module
- Scale out the Application Server, put the load balancer in front of the Application Server

## Ads Recommendation

**Problem Statement**

Build a machine learning model to predict if an ad will be clicked

**Metrics**

Offline metrics:
- Normalized Cross-Entropy (NCE) - predictive logloss divided by the cross-entropy of the background CTR. This was NCE is insensitive to background CTR
Online metrics:
- Revenue lift - revenue change % over a time period

**Requirements**

Training
- Imbalance data - CTR is very small in practice (1%-2%), making supervises training difficult. Need a way to train the model that can handle highly imbalanced data
- Retraining freq. - retrain models many times within one day to capture the data distribution shift in the production environment
- Train/validation data split - partition by time

Inference
- Serving - low latency < 100ms for ad prediction
- overspent - if the ad serving model repeatedly serves the same ads, it might end up over-spending the campaign budget and publishers lose money

**Modeling**

Feature engineering
- advertiser ID —> Embedding or feature hashing
- user’s historical behavior —> number of clicks on ads over a period of time (feature scaling / normalization, obe hot encoding)
- cross features —> combine multiple features

Training data
- data about ad clicks
- same as feed ranking
    - period of data
    - downsample negative data to handle imbalanced data

Model
- fully connected layers with the Sigmoid activation function applied to the final layer
- same as feed ranking

**High-level system design**

Calculation
- 40k ad requests per second —> 100 billion ad requests per month
- 500 bytes to store each observation
- data: historical ad click data includes [user, ads, click_or_not]
- 1% CTR —> 1 billion clicked ads
- total 50 PB, —> keep only 1%-10% or use 1 week of data for training and use the next data for validation data
- 100 million users

flow:
- Client sends an ad request to Application Server
- Application Server sends ad request to Ads Candidate Generation service
- Ads Candidate Generation Service generates ad candidates from database
- Ads Candidate Generation Service sends ad ranking request to Ads Ranking service
- Ads Ranking service gets feature values from Feature Store, gets latest model
- Ads Ranking Service scores ads and return ads with ranking to Ads Candidate Generation service. Ads Candidate Generation service returns result to Application server and client

![IMG_3546F5B5B2D0-1](https://github.com/bsamanvitha/learning-journal/assets/6962922/1d39435f-e76d-4c1b-a4cd-0087610c292d)

With scale:
- User visits the homepage and sends an Ad request to the Candidate Generation Service. Candidate Generation Service generates a list of Ads Candidates and sends them to the Aggregator Service
- The Aggregator Service splits the list of candidates and sends it to the Ad Ranking workers to score
- Ad Ranking Service gets the latest model from Model Repos, gets the correct features from the Feature Store, produces ad scores, then returns the list of ads with scores to the Aggregator Service
- The Aggregator Service selects top K ads (For example, k = 10, 100, etc.) and returns to upstream services

**Scaling**
- scale out Model Serving
- put Aggregator Service to spread the load for Model Serving components (It distributes the candidate list to multiple serving instances and collects results)

