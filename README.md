# MSA AI Project: Heart Failure Death Prediction

<h3>**Introduction**</h3>

Cardiovascular diseases (CVDs) are the amongst the most common causes of death globally, with approximately 17.9 million deaths annually. Nationally, CVDs affect more than 4 million Australians and cause 1 in 4 deaths in Australia [1][2]. Also referred to as heart diseases, CVDs are disorders of the blood vessels and the heart and may present heart failures. The most important behavioural factors for contributing to CVDs are imbalanced diet, physical inactivity and smoking. These factors may also result in high blood pressure, high blood glucose and obesity.

**Problem statement**

With the prevalence of death due to heart failure, it is crucial that this can be predicted early on. This calls for a need to be able to predict death caused by heart failure. Artificial Intelligence (AI) can be used to solve this problem by examining past patient data and identifying the patterns in order to predict the chances for future patients. Using AI rather than manually finding the patterns ourselves means that we may find elements that may otherwise be too difficult to see by humans. Moreover, using AI would make the solution scalable as larger datasets are used. It will also be less prone to errors.

**Solution Design &amp; Implementation**

**Data phase**

In order to build the solution, Chicco and Jurman&#39;s dataset [3] has been chosen as it contains information on 299 patients with heart failure in 2015. The dataset contains the mortality label of each patient.

The following are the features in the dataset:

- Age of patient
- Whether patient has anemia
- Level of creatinine phosphokinase (mcg/L)
- Whether patient has diabetes
- Ejection fraction (%)
- Whether patient has hypertension
- Platelet concentration (kiloplatelets/mL)
- Level of serum creatinine (mg/dL)
- Level of serum sodium (mEq/L)
- Sex of patient
- Whether patient smokes
- Follow-up period (days)

These features are predicted to be good indicators of mortality as they are important contributing factors to heart diseases.

The Azure service chosen to perform this project is the Azure Machine Learning designer. It facilitates all stages of the machine learning life cycle for the user.

Firstly, the dataset is inserted into the Machine Learning pipeline. It must then undergo data preprocessing. As the features are of different scales, the data must first be normalized. The transformation method selected is MinMax as it is a standard procedure for converting features of different scales to the same scale between 0 and 1. Normalization was applied to the features which have values outside of the 0-1 range: age, creatinine\_phosphokinase, ejection\_fraction, platelets, serum\_creatinine, serum\_sodium and time.

Although in typical scenarios it is important to handle null values, it is not included here mainly as there are no null values in the dataset. Categorical variables also do not need to be handled specifically as they have all been recorded as Boolean values.

Next, the data is split into two parts with a ratio of 7:3. 70% of the dataset is used to train the model with the rest being used to evaluate the model.

**Model Phase**

Two-Class Logistic Regression is selected as the machine learning algorithm to be used as this is a classification problem of predicting death or survival. All the features in the training portion of the dataset (not including DEATH\_EVENT as it is the label) are used to train the model. The Score Model and the Evaluate Model modules are then added to evaluate the performance of the model. See the picture below for the full training pipeline. The performance of the model is discussed in RESULT.md.

![](RackMultipart20210805-4-15nmv5w_html_f435cd6b3497bc72.png)

Training Pipeline

**Production Phase**

The training pipeline is deployed to produce the following inference pipeline (see below). Real-time endpoint has now been deployed. The inference pipeline is a copy of the training pipeline and is modified in the following ways: the raw training dataset module is replaced with the Enter Data Manually and Web Service Input modules. The former was used to enter our input data without the DEATH\_EVENT column. The Web Service Input module is used so that input data can be inserted from elsewhere. The Evaluate Model module is removed as it is not necessary for deployment (since it is simply assessing our model).

The Execute Python Script is added to define what will be the output for the users. The Web Service Output is then used to deliver the outcome.

![](RackMultipart20210805-4-15nmv5w_html_ae3b517e95f595e7.png)

Inference Pipeline

In order for the solution to be used by a client, they may follow the following instructions:

- Open Azure Machine Learning studio
- Create a new file in the Notebooks page
- Paste in the following code into the rectangular cell

Code:

    endpoint = 'http://9a351d82-3e2d-4879-a09a-453e31578f77.australiaeast.azurecontainer.io/score' #Replace with endpoint

    key = 'EN0sO1vLAGsxMlcLLWYlegOWWfEAyvA8' #Replace with key

    import urllib.request

    import json

    import os

    data = {

      "Inputs": {

        "WebServiceInput0":

        [

            {

                    'age': 77,

                    'anaemia': 1,

                    'creatinine_phosphokinase': 342,

                    ';diabetes': 1,

                    'ejection_fraction': 20,

                    'high_blood_pressure': 1,

                    'platelets': 212000,

                    'serum_creatinine': 1.1,

                    'serum_sodium': 125,

                    'sex': 1,

                    'smoking': 0,

                    'time': 21,

            },

        ],

      },

      GlobalParameters:  {
      }

    }

    body = str.encode(json.dumps(data))

    headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ key)}

    req = urllib.request.Request(endpoint, body, headers)

    try:

        response = urllib.request.urlopen(req)

        result = response.read()

        json_result = json.loads(result)

        output = json_result['Results']['WebServiceOutput0'][0]

        print('Prediction: {}\nProbability: {:.2f}'.format(output['HeartFailurePrediction'],

                                                            output['Probability']))

    except urllib.error.HTTPError as error:

        print('The request failed with status code: ' + str(error.code))

        # Print the headers to help debug

        print(error.info())

        print(json.loads(error.read().decode('utf8&quot;, 'ignore')))

- Inside of WebServiceInput0, insert the data the client would like to classify. In the code, synthetic information has already been entered for a patient.
- Hit the double-arrow button to run the code
- Prediction and Probability should appear underneath the cell. Prediction of 1 indicates death, whilst 0 indicates survival. Probability signifies the probability of death.

For scalability, if the client has one or more files of input patient data, these files can instead be read in the python code (rather than entered inside the code).

References

[1] Australian Bureau of Statistics 2018

[2] National Health Survey 2017-18

[3] Chicco, D., &amp; Jurman, G. (2020). Machine learning can predict survival of patients with heart failure from serum creatinine and ejection fraction alone. _BMC medical informatics and decision making_, _20_(1), 1-16.

RESULT.md

**Results**

The training model&#39;s accuracy is 0.833 which is fairly high. Its precision is 0.731, recall 0.704, F1 0.717 and AUC 0.832. These figures indicate that the model does help with the prediction of death by heart failure. With data from less than 300 patients being used the build the model, its performance is already moderately high. With even more patients using this classifier, their data could help to strengthen the model to perform even better.

To improve the performance of the model even further, additional research for important factors for heart failure could be undertaken. This may add additional features and/or decrease less important ones. Moreover, evaluation techniques such as k-fold cross-validation could be employed instead of splitting the dataset 7:3. Furthermore, it would be interesting to view how the results may change if the dataset contains those without heart failure.

To improve scalability, the data preprocessing stage could be modified to accommodate for imperfect datasets such as null value cases. A more user-friendly interface could be implemented such that the client would not have to write any Python code and instead, upload their files and maybe click one button. It would also help to have a user ID column in the dataset in cases where the client would like to classify a large set of data. This would make it more convenient to figure out which results belong to which patients.

![](RackMultipart20210805-4-15nmv5w_html_fa06ac46610e9e90.png) ![](RackMultipart20210805-4-15nmv5w_html_66e8faeb06e22e05.png)

![](RackMultipart20210805-4-15nmv5w_html_7b694fa4ebc71098.png) ![](RackMultipart20210805-4-15nmv5w_html_bacece8f8f979b1a.png) ![](RackMultipart20210805-4-15nmv5w_html_406d870d4b3bde25.png)

LEARNING\_SUMMARY.md

For MSLearn Journey, I have chosen the coding approach for the learning path (despite having done low code in the project. The Q&amp;A session clarified that this is acceptable).

Automated ML in Azure ML: chooses best model for you

Azure ML can be used for regression, classification and clustering. The techniques differ by selecting different modules. Eg classification can use 2-class linear regression.

The ML lifecycle:

- Train model
- Package model
- Validate model
- Deploy model
- Monitor model
- Retrain model

Benefit of ML pipeline: independent steps allow multiple people to work on the same pipeline concurrently

Recommended approach for monitoring ML models: combo of automatic &amp; manual spot checks

Model registry = central place to save every version of every model

Reasons for inaccurate predictions could include:

- Inadequate training
- Issues with the live data that the model evaluates
- Model drifts over time
- World changes

Azure notebooks:

- Can be integrated with Azure Machine Learning, Azure compute source
- Azure Notebooks Website has sample notebooks eg Discover Sentiments in Tweets (sentiment analysis on tweets), Intorduction to Cognitive Toolkit (notebooks showing how to use Microsoft Cognitive Toolkit)
- Import data using curl
- Pandas for cleaning and preparing data
- Scikit-learn to build ML model
- Matplotlib to visualize results

Keras = API wrapper on top of DL libraries eg TensorFlow &amp; MS Cognitive Toolkit

- Model.summary() = produces summary of layers for compiled model
- (x\_train, y\_train),(x\_test,y\_test) = load\_data(…) where x\_train and y\_train are the values of features, and x\_test and y\_test are the label values

Benefits of OOP:

- Data encapsulation
- Simplicity
- Easy to modify
- Maintainability
- Reusability

Encapsulation:

- Protect data
- Don&#39;t and should not need to know the internals

Flask = framework for building web apps in Python

- 5000 default port number
- Jinja template files stored in templates subdirectory by default
- Core: app.py
- Code calls Flask funcs eg render\_template()