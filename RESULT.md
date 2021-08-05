**Results**

![](https://github.com/audicar/MSA_AI_Heart_Failure_Death_Prediction/blob/main/images/APR.png) ![](https://github.com/audicar/MSA_AI_Heart_Failure_Death_Prediction/blob/main/images/confusion.png)

![](https://github.com/audicar/MSA_AI_Heart_Failure_Death_Prediction/blob/main/images/ROC.png) ![](https://github.com/audicar/MSA_AI_Heart_Failure_Death_Prediction/blob/main/images/PRC.png) ![](https://github.com/audicar/MSA_AI_Heart_Failure_Death_Prediction/blob/main/images/Lift.png)

The training model&#39;s accuracy is 0.833 which is fairly high. Its precision is 0.731, recall 0.704, F1 0.717 and AUC 0.832. These figures indicate that the model does help with the prediction of death by heart failure. With data from less than 300 patients being used the build the model, its performance is already moderately high. With even more patients using this classifier, their data could help to strengthen the model to perform even better.

To improve the performance of the model even further, additional research for important factors for heart failure could be undertaken. This may add additional features and/or decrease less important ones. Moreover, evaluation techniques such as k-fold cross-validation could be employed instead of splitting the dataset 7:3. Furthermore, it would be interesting to view how the results may change if the dataset contains those without heart failure.

To improve scalability, the data preprocessing stage could be modified to accommodate for imperfect datasets such as null value cases. A more user-friendly interface could be implemented such that the client would not have to write any Python code and instead, upload their files and maybe click one button. It would also help to have a user ID column in the dataset in cases where the client would like to classify a large set of data. This would make it more convenient to figure out which results belong to which patients.

