# Predicting Hospital Readmission in Patients with Diabetes

## Goal
Train and compare models that predict hospital readmission within 30 days for patients with diabetes.

## Data
Hospital records on 101,766 hospitalizations among patients with diabetes from 1999-2008 at 130 US hospitals.  Predictors include some demographics (race, gender, age), admission type, discharge type, time in hospital, number of lab procedures and medications given during hospitalization, number of prior visits (outpatient, emergency, inpatient) in the last year, a number of lab values, etc. Data was split into 75% training and 25% test.

## Results
### Models
| Model                            | Best Hyperparameters*              | Accuracy* | Precision | Recall |
| -----------                      | -----------                       | -------- | --------- | ------ |
| Random Forest Classifier         | max_depth: 20, n_estimators: 100  | 88.783%  | 66.667%   | 0.64%  |
| Gradient Boosting Classifier     | learning_rate: 0.1, max_depth: 3, n_estimators: 50 | 88.747% | 50.0% | 0.605% |
| Logistic Regression              | default                           | 88.731% | 47.619% | 1.422% |
| Multilayer Perceptron            | activation: relu, learning_rate: constant | 88.695% | 41.096% | 1.067% |
| K-Neighbors Classifier           | leaf_size: 50, n_neighbors: 7, weights: uniform | 88.447% | 30.366% | 2.063% |
* Hyperparameter combinations were examined using Bayes Search CV from scikit-optimize with 3-fold cross-validation on the training data
* Accuracy, Precision, and Recall were calculated on the test data

### Tuning for precision
Assuming that users of such a model would rather like to prioritize identifying as many people as possible who are going to be re-hospitalized and keep a low false-negative rate, the best model, Random Forest Classifier, was tuned to reduce precision.
  - At threshold: 0.07, predicted readmitted: 19882, predicted not readmitted: 19558, accuracy: 29.737%, precision: 12.916%, recall: 91.323%
  - At threshold: 0.05, predicted readmitted: 24129, predicted not readmitted: 19558, accuracy: 14.414%, precision: 11.509%, recall: 98.755%

### Best predictors
According to logistic regression, the 3 strongest predictors in a linear model are:
  - discharge disposition 
  - number of inpatient visits in past year
  - change in diabetes medication during hospitalization
During data cleaning/exploration, it was noted that many predictors in the dataset did not have a significant linear relationship with the outcome.  These were left out of some models such as logistic regression.  Other models were checked on both datasets (all predictors vs. only predictors with a linear relationships) but including more or fewer predictors did not make a significant difference on results.

## Python Libraries used:
  - [joblib](https://joblib.readthedocs.io/en/stable/)
  - [pandas](https://pandas.pydata.org/)
  - [matplotlib](https://matplotlib.org/)
  - [numpy](https://numpy.org/)
  - [seaborn](https://seaborn.pydata.org/)
  - [scikit-learn](https://scikit-learn.org/stable/)
  - [scikit-optimize](https://scikit-optimize.github.io/stable/)

## Takeaways
  - All models struggled to differentiate between patients destined to be readmitted in 30 days from those who weren't based on predictors available, but Random Forest Classifier came out the strongest.
  - When tuned by default to prioritize accuracy, the highest accuracy rate of 88.8% is little better than a prediction that 0 patients would be re-hospitalized in 30 days, which would be correct 88.7% of the time
  - When the best model was tuned to prioritize precision, we'd have to assume that just over half of all patients are at risk of being re-hosp in order to capture almost all of the 11% who will be re-hosp. 
  - Data cleaning was done to impute missing data (where variables had a relatively small percent of missing data), drop variables with very high amounts of missing data, drop variables unlikely to be related to the outcome like insurance payer code, collapse some categories where variables had a lot of categories, and check linear relationships with outcome. However, more data exploration/preparation could be conducted to reduce skew, bring back categorical diversity among some variables, and work with a medical expert to deturmine what comorbid diagnoses are relevant and create categories around that. This could improve the relationships in the data for future models.

## Dataset Citation: 
Beata Strack, Jonathan P. DeShazo, Chris Gennings, Juan L. Olmo, Sebastian Ventura, Krzysztof J. Cios, and John N. Clore, “Impact of HbA1c Measurement on Hospital Readmission Rates: Analysis of 70,000 Clinical Database Patient Records,” BioMed Research International, vol. 2014, Article ID 781670, 11 pages, 2014.
