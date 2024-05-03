# Structuring ML Projects (Coursera course)

Setting up goal:
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
 
  Comparing to human-level performance:
  - 
