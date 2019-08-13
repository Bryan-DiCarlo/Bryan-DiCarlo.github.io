---
layout: post
title:      "Machine Learning and Deep Learning Comparison"
date:       2019-08-13 21:45:11 +0000
permalink:  machine_learning_and_deep_learning_comparison
---

![](https://www.universetoday.com/wp-content/uploads/2009/05/neutron.jpg)

# Classification of Pulsar Stars
This data set was obtained from Kaggle.  HTRU2 is a data set which describes a sample of pulsar candidates collected during the High Time Resolution Universe Survey. Pulsars are a rare type of Neutron star that produce radio emission detectable here on Earth. They are of considerable scientific interest as probes of space-time, the inter-stellar medium, and states of matter. As pulsars rotate, their emission beam sweeps across the sky, and when this crosses our line of sight, produces a detectable pattern of broadband radio emission. As pulsars rotate rapidly, this pattern repeats periodically. Thus, pulsar search involves looking for periodic radio signals with large radio telescopes. Each pulsar produces a slightly different emission pattern, which varies slightly with each rotation. Thus, a potential signal detection known as a 'candidate', is averaged over many rotations of the pulsar, as determined by the length of an observation. In the absence of additional info, each candidate could potentially describe a real pulsar. However, in practice almost all detections are caused by radio frequency interference (RFI) and noise, making legitimate signals hard to find. This data set has 17,898 examples. 1639 positive examples and 16,259 negative examples. We will use a combination of Machine Learning and Deep learning techniques to find the best classifier for this problem.

# The Data Set
- Mean of the integrated profile.
- Standard deviation of the integrated profile.
- Excess kurtosis of the integrated profile.
- Skewness of the integrated profile.
- Mean of the DM-SNR curve.
- Standard deviation of the DM-SNR curve.
- Excess kurtosis of the DM-SNR curve.
- Skewness of the DM-SNR curve.
- Class (0 for negative 1 for positive)

# Exploratory Data Analysis and Interesting Findings
As with many data sets one of the target classes often has fewer observations than your other target class or classes.  This is to be expected for rare events.  Pulsar stars are rare events and only represented roughly 10% of the data set.  This poses a problem for Machine and Deep Learning models. Models will have plenty of examples of non-pulsar star instances to train. However, models will struggle to classify the Pulsar stars because there were not enough training examples to build an effective Model.  I used synthetic minority over-sampling to balance the classes before training.
```
from imblearn.over_sampling import SMOTE

#Prepare the Data for SMOTE
X_a = df_a.drop(['target_class'], axis=1)
y_a = df_a.target_class
print(X_a.shape, y_a.shape)

# SMOTE resampling
X_resampled, y_resampled = SMOTE(random_state=10).fit_sample(X_a, y_a)

# Return the features and target back into a Pandas DataFrames
df_X = pd.DataFrame(X_resampled, columns=X_a.columns)
df_y = pd.DataFrame(y_resampled, columns=['target_class'])

# Concatenate the resampled features and target into one DataFrame
df = pd.concat([df_X, df_y], axis=1)
df.head(5)
```

This up-sampled my data and equally balanced the two target classes.  I learned this the hard way. So, it should be noted, when creating new examples SMOTE adds them to the end of your data frame.  In my case this meant that the second half of my data frame consisted entirely of target values equal to 1.  The newly created instances of Pulsar stars.  If you are using scikit learn’s train, test split method you can have it do the heavy lifting and shuffle the deck for you.  In my deep learning models, I used traditional slicing methods for train, test splits.  A simple but effective work around to shuffle your data frame is shown below.  This method randomly shuffles the rows of your data frame leaving all the information intact

```
df_shuffled = df.sample(frac=1, random_state=10)
```

# Deep Learning Modeling

Rather than bore you with the details of the Keras models I built, I would rather pass along some of the interesting methods I discovered in the process.  Before starting this project, I was much more familiar and comfortable with scikit learn’s methods.  And much less familiar with those of Keras.  Along the way I learned how to build and use Keras models, but perhaps more useful, I also learned you can use many of scikit learn’s built in functions along with Keras modeling.  I discovered you can use scikit learn’s Grid Search and Cross Validation with Keras models.  The process involves building a function that contains the Keras model architecture. You then wrap that function is a Keras classifier and then pass it into the scikit learn function.  I included the code I used for Grid Search; however, the process is very similar for cross validation and can easily be found in online resources.

```
from keras.wrappers.scikit_learn import KerasClassifier
from sklearn.model_selection import GridSearchCV

# Create a Function for your Keras Model

def create_network(optimizer='rmsprop'):
    
    # Start neural network
    network = models.Sequential()

    # Add fully connected layer with a ReLU activation function
    network.add(layers.Dense(units=50, activation='relu', input_dim=8))

    # Add fully connected layer with a ReLU activation function
    network.add(layers.Dense(units=25, activation='relu'))

    # Add fully connected layer with a sigmoid activation function
    network.add(layers.Dense(units=2, activation='sigmoid'))

    # Compile neural network
    network.compile(loss='binary_crossentropy', # Cross-entropy
                    optimizer=optimizer, # Optimizer
                    metrics=['accuracy']) # Accuracy performance metric
    
    # Return compiled network
    return network

# Wrap Keras model so it can be used by scikit-learn

neural_network = KerasClassifier(build_fn=create_network, verbose=0)

# Create hyperparameter space

epochs = [50, 60, 70]
batches = [16, 32, 64]
optimizers = ['rmsprop', 'adam']

# Create hyperparameter options

hyperparameters = dict(optimizer=optimizers, epochs=epochs, batch_size=batches)

# Create grid search

grid = GridSearchCV(estimator=neural_network, param_grid=hyperparameters)

# Fit grid search

grid_result = grid.fit(feature, target2)

# View hyperparameters of best neural network

grid_result.best_params_

```

# Conclusions
I did 7 iterations of deep learning Keras models with the requisite regularization and hyperparameter optimization.  I constructed a similar number of optimized regular machine learning models.  I also used feature reduction and feature engineering in both machine and deep learning models.  The results were quite good in all cases.  But also, a little surprising.  Models basically ranged from 95% to just below 97% accuracy and F1 scores for all models.  This is good news for the astrophysicists! Machine learning can be a very useful tool for Pulsar star classification. The surprising part is that this level of classification can be achieved with the most basic models.  Also, good news.  Just interesting and surprising.  For instance, I could use a decision tree with little optimization and only two features and achieve results of 96% accuracy and F1.  Four or 5 features seemed to be optimal for the ML Models. DL models were attempted with fewer features and engineered features. However, they performed best with all features included.  Through EDA I think I found the answer.  I used a seaborn pairplot colored by the target classes.  The distributions for each target class (by feature) are very different.  Very separable.  A perfect candidate for machine learning. Except…wait for it…a small region of overlap of these distributions where the two classes are just not different.  My guess is this region represents that 3-4% of cases where it is just a coin flip.  I found this to be true in my modeling.  I could achieve 96% classification rates with fairly straight forward models.  More complex models did not produce significantly higher overall results.  What happened is the proportion of false positives and false negatives just shifted.  The overall accuracy and F1 score of the model remained roughly the same.  The misclassifications were just shifted from one category to the other. The data set also describes that radio telescope readings from Earth are also subject to a substantial amount of noise.  Each observation in the data set is an average of many readings of the same star to combat this.  I think this mysterious 3-4% just happens to be the remaining noise level for the observations.  Like I said not bad news.  On the contrary.  The classification rates of 96+% I observed are very reproducible.  This was extremely interesting. I enjoyed working with this data set.









