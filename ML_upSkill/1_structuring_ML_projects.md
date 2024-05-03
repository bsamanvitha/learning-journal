# Structuring ML Projects (Coursera course)

**Setting up goal**
- evaluation metric: given two classifiers, tells which one is better
- precision and recall have trade-offs, can use single number metric evaluation
  - F1 score (“average of precision and recall”)
- To pick the best classifier:
    - Satisficing metric = just needs to be good enough, could be a threshold to meet
    - Optimizing metric = something you want to optimize
- Train/Dev/Test sets
    - ensure they come from the same distribution
    - old way of splits: train=30% test=70% or train=60 dev=20% test=20%
    - modern way of splits (due to much larger dataset sizes): train=98% dev=1% test=1%
    - if doing well on your metric+dev/test set does not correspond to doing well on your application, change your metric and/or dev/test set

**Comparing to human-level performance**
- So long as ML is worse than humans, one can get labeled data from humans, gain insight from manual error analysis, and have better analysis of bias/variance
- bias = underfitting the data where it assumes. To reduce it:
    - train bigger model
    - train longer/better optimization models
    - NN architecture/hyperparameters search
- variance = overfit the data where it learns too much from it. To reduce it:
    - more data
    - regularization
    - NN architecture/hyperparameters search
- Problems where ML significantly surpasses human-level performance (as the computer can leverage structured data and compute quicker)
    - online advertising
    - product recommendations
    - logistics (predicting transit time)
    - loan approvals
- Human-level error can be a proxy to Bayes error. From the screenshot above:
    - the left-side scenario can focus on reducing bias
    - the right-side scenario can focus on reducing variance
 <img width="621" alt="Screen Shot 2024-05-03 at 10 57 46 AM" src="https://github.com/bsamanvitha/learning-journal/assets/6962922/00c15cad-3060-465d-950c-760afe426575">

**Error analysis**
- evaluate multiple ideas in parallel
- DL algorithms are quite robust to random errors in the training set, but not systematic errors
- build your first system quickly, then iterate
    - set up dev/test set and metrics
    - build initial system quickly
    - use bias/variance analysis and error analysis to prioritize next steps
