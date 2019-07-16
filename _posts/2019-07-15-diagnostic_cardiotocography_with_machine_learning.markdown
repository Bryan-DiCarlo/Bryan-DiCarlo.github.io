---
layout: post
title:      "Diagnostic Cardiotocography with Machine Learning"
date:       2019-07-16 00:23:20 +0000
permalink:  diagnostic_cardiotocography_with_machine_learning
---



# Cardiotocography
Cardiotocography (CTG) is a technical means of recording the fetal heartbeat and the uterine contractions during pregnancy.  CTG is commonly used to monitor fetal wellbeing during pregnancy.  Results are interpreted by a physician using the following guidelines set by International Federation of Gynecology and Obstetrics (FIGO)

# Updated 2015 FIGO Intrapartum Fetal Monitoring Guidelines
- **Normal**: No hypoxia/acidosis, no intervention necessary to improve fetal oxygenation state:
    * Baseline 110-160 bpm
    * Variability 5-25 bpm
    * No repetitive decelerations (decelerations are defined as repetitive when associated with > 50% contractions)
    
- **Suspicious**: Low probability of hypoxia/acidosis, warrants action to correct reversible causes if identified, close monitoring or adjunctive methods:
    * Lacking at least one characteristic of normality, but with no pathological features.
    
- **Pathological**: High probability of hypoxia/acidosis, requires immediate action to correct reversible causes, adjunctive methods, or if this is not possible expedite delivery. In acute situations immediate delivery should be accomplished
    * Deceleration > 5 min
    * Baseline <100 bpm
    * Reduced or increased variability or sinusoidal pattern
    * Repetitive late or prolonged decelerations for > 30 min, or > 20 min if reduced variability (decelerations are defined as repetitive when associated with > 50% contractions) [Wikipedia](https://en.wikipedia.org/wiki/Cardiotocography)

# CTG Data Set

- The CTG Dataset has 2126 different CTG test results. Each evaluated and scored by a physician
- [Cardiotocography Data Set](https://archive.ics.uci.edu/ml/datasets/cardiotocography)
- The evaluations are based on the 21 feature observations (listed above) of each CTG test.
- The physicians then classified each test into one of three categories according to FIGO guidelines
    * Normal
    * Suspicious
    * Pathological
 
# Objective and Methodology
* We will develop a machine learning model, as a diagnostic tool, for effectively classifying CTG test results
* We will use a supervised Machine Learning approach. Models will be trained to predict three outcome categories(Normal (1), Suspicious (2), Pathological (3)) using 70%  of the dataset of the target labeled dataset.
* The model will then be evaluated by its ability to predict classes for the remaining 30% of unseen data.
* The most promising models will be further scrutinized using 10-fold cross validation for each of the four most important metrics: F1 score, Precision, Recall and Accuracy.
* The best performing model will have high and consistent results for all 4 of these 10 fold cross validated metrics
* This will give us high confidence that the model performs well across the entire dataset and that our results are not anamalous.

# Data Pre-Processing And EDA
As mentioned, the data set had 21 total features to predict three classes: Normal, Suspect and Pathological.  The data set also contained 2126 instances.  On initial inspection I noticed there was a broad range for feature values.  All were numerical values.  Some features ranged from the hundreds to other features that were close to one and zero.  Scale was a factor that would affect my downstream modeling and needed to be addressed.  I first used the StandardScaler from Scikit-learn.  This did a nice job of scaling the data, but I was still left with features that still had substantial outliers that would also affect my modeling. I did some further research into Scikit-learn’s available scaling methods.  I then used the RobustScaler.  This scaling feature scaled the data as well as removed outliers. All features were scaled.  Two of the 21 features still had outliers to a small degree.  However, since the scale was also reduced, these outliers and these two features turned out to be non-issues in my modeling.


# Class Imbalance
I knew from the beginning of this project that I would be dealing with class imbalance.  I had 1655 cases of the Normal class, 295 cases of the Suspicious class and 176 cases of the Pathological class. From literature searches and the nature of this data set I knew the models I would be investigating.  Decision Trees, Random Forests and the Naïve Bayes Classifier have been predominately used in the published literature. Decision Trees and Random Forests also usually outperformed the Naïve Bayes Classifier.  I decided to use Decision Trees, Random Forests and add Support Vector Machines.  In addition, I would also try the Ensemble methods: Bagging Classifier and Gradient Boosting Classifier. Each of these has the class_weight parameter which you can set to ‘balanced’.  This will give class weights inversely proportional to their frequency during the modeling process.  This produced much better results than not setting this parameter.  However, my results were still not great.  Not at the level of being a legitimate medical diagnostic tool.  I went through the entire process.  Built baseline models for each. Optimized hyperparameters through grid searches and built models for each of the two ensemble methods.  The Bagging Classifier turned out to be the best in this scenario.  But the results were still lackluster.  My best model had overall accuracy, precision, F1 and recall in the low 90% range.  And all models had trouble distinguishing the Suspicious class.  It turns out that even though I was balancing class weights, I just did not have enough representatives in the minority classes to build an accurate model.

# Synthetic Minority Oversampling (SMOTE)
On a tip from my instructor I investigated SMOTE. I was at first hesitant to use simple up-sampling with replacement.  At least from my understanding simple up-sampling would just sample from my existing values to create equal number between classes. In essence just replication.  But I looked in to SMOTE.  Rather than just simple up-sampling SMOTE uses a ML algorithm to create new ‘synthetic’ samples.  Again, a very simplistic view and description.  Say the algorithm is looking at class 1.  It creates ‘lines’ of connection between those similar class values and then creates new values along those lines. In the absence of having new ‘real’ data, it is the closest thing we have to adding new ‘real’ data.  Its synthetic data, but not repeated data.  And the algorithm does its best to ensure the properties of the class, needed for separation, are maintained.  I went back and re-worked my whole Data Frame. The data was still pre-processed using the RobustScaler.  My entire Data Frame went from 2126 instances to 4965.  And more importantly I now have 1655 instances in each class to model.  My models now had enough instances from each class to draw valid predictions from.  The results of all of my models improved dramatically.    

# Final Model: Gradient Boosting Classifier
After addressing my class imbalance issue with SMOTE, I exhaustively re-worked all my models.  Optimized the hyperparameters for each and re- ran all of them.  The results were impressive.  Including my baseline models, I investigated a total of eight models.  When I started, I had one model that had evaluation metrics in the range of 90%, but still had trouble distinguishing the suspicious class.  After re-working my data, I had four models that had average 10-fold cross validation results at or above 95%.  The two best models were SVM-RBF and the Gradient Boosting Classifier.  I focused on those.  I performed 10-fold cross validation for each of the four metrics: Accuracy, Precision, Recall and F1-score.  The SVM model was very good.  It had almost identical mean scores across all for 10-fold cross validated metrics of 97%.  And did well predicting all three classes.  However, when looking at the boxplot of the cross-validated metrics they range from 95-99%.  The Gradient Boosting Classifier did better across the board.   The 10-fold cross validation for each of the four metrics: Accuracy, Precision, Recall and F1-score was 98% across the board.  The training score was also 98%.  The test score was 99.7%.  At first this gave some hesitation that my model might be slightly overfit.  However, when I examined the boxplot of the cross validated metrics the values were tightly grouped in each instance.  The range of all the cross-validated scores was 97.7% -99%. Very close to the train score. The exact same pattern was seen for all four 10-fold cross validated metrics.  We expect to get train scores slightly higher than testing scores.  The tight grouping and closeness of these scores to the train score give me confidence the model is not overfit.  It also performed equally consistently across the three classes. 

# Evaluation of This Model as a Diagnostic Tool
This model performed consistently across all metrics and the predictive value for each class.  The model was evaluated using 10-fold cross validation for each of the four metrics: Accuracy, F1-score, Precision and Recall. The average score for each of these metrics was 98%.  The average for each of these metrics was also 98% for each of the three classes. In the case of this test I would like to examine the critical areas of concern.  It is of less concern if a Pathological case is predicted as Suspect or vice versa.   The pregnancy would continued to be monitored closely in either case. This model only made this misclassification at a rate of roughly 0.5% in each case.  One area of concern, which I will call critical false negatives, is when a Suspicious or Pathological case is classified as Normal.  This could mean that a troubled pregnancy could go untreated.  Our model only made this misclassification at a rate of 1.2%. which is very encouraging.  The other area of concern, which I will call critical false positives, is when a Normal case is classified as either Suspicious or Pathological.  This could lead to unnecessary medical costs and stress to the patient. CTG is non-invasive and low-cost relative to many medical procedures.  Further monitoring will likely identify these misclassifications quickly.  In any event the model made this misclassification at a rate of 2.4%.  All of these are in the tolerance range of a useful medical diagnostic tool.







