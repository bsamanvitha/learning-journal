# Machine Learning System Design (Educative course) - Part 3
## Rental Search Ranking

**Problem Statement**
Airbnb users search for available homes at a particular location. We can build a supervised ML model to predict booking likelihood. This is a binary classification model, i.e., classify booking and not-booking. (note: the naive approach is using text similarity and ranking function, but similarity doesn’t guarantee a booking)

**Metrics**
- Discounted Cumulative Gain
- Normalized DCG
- IDCG (Ideal DCG)
- Online metrics: conversion rate (number of bookings divided by number of search results) and revenue lift

**Requirements**

Training
- imbalanced data as an average user might do extensive research before deciding a booking
- we then select a few weeks of data before that date as training data and a few days of data after that date as validation data

Inference

- low latency for search ranking (50ms - 100ms)
- brand new listings might not have enough data for the model to estimate likelihood. As a result, the model might end up under-predicting for new listings. (Ability to avoid under-predicting for new listings)

**Modeling**

Feature Engineering
- listing ID embedding
- listing feature
- location
- text embedding of historical search query
- user-assoc. features
- number of previous bookings
- previous length of stays
- time-related features

Training data
- user search history
- view history
- bookings


Model
- input: user data, search query, listing data
- output: user books a rental or not, binary classification model that outputs the likelihood of booking --> [0, 1]


**High-level system design**

- 100 million MAU
- user books 5 times per year
- user sees 30 rentals from the search
- total = 1.25 billion samples per month (15 billion samples per year)
- each flow takes 500 bytes to store —> 500 bytes * 1.25 *10^9 625 * 10^9 = 625 GB
- keep the last 6 months or 1 year in the data lake and archive old data in the cold storage

![IMG_49C40ADE3C0A-1](https://github.com/bsamanvitha/learning-journal/assets/6962922/9fe926f1-3090-4653-b5f9-81ca8664d597)

flow:
- User searches rentals and request Application Server for result
- Application Server sends search request to Search Service
- Search Service gets rentals from database and sends rental rank request to Ranking Service
- Ranking Service scores each rental results and returns the score to Search Service
- Search Service returns rentals to Application Server and Application Server returns rentals to user

**Scaling**
- Application Servers and Load Balancers
- scale out Search Services and Ranking Services to handle millions of requests from the Application Server
- log all candidates that we recommended as training data, so the Search Service needs to send logs to a cloud storage or send a message to a Kafka cluster

