# MSA AI Project: Heart Failure Death Prediction

<h3>Introduction</h3>

Cardiovascular diseases (CVDs) are the amongst the most common causes of death globally, with approximately 17.9 million deaths annually. Nationally, CVDs affect more than 4 million Australians and cause 1 in 4 deaths in Australia [1][2]. Also referred to as heart diseases, CVDs are disorders of the blood vessels and the heart and may present heart failures. The most important behavioural factors for contributing to CVDs are imbalanced diet, physical inactivity and smoking. These factors may also result in high blood pressure, high blood glucose and obesity.

<h3>Problem statement</h3>

With the prevalence of death due to heart failure, it is crucial that this can be predicted early on. This calls for a need to be able to predict death caused by heart failure. Artificial Intelligence (AI) can be used to solve this problem by examining past patient data and identifying the patterns in order to predict the chances for future patients. Using AI rather than manually finding the patterns ourselves means that we may find elements that may otherwise be too difficult to see by humans. Moreover, using AI would make the solution scalable as larger datasets are used. It will also be less prone to errors.

<h3>Solution Design &amp; Implementation</h3>

<h4>Data phase</h4>

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

<h4>Model Phase</h4>

Two-Class Logistic Regression is selected as the machine learning algorithm to be used as this is a classification problem of predicting death or survival. All the features in the training portion of the dataset (not including DEATH\_EVENT as it is the label) are used to train the model. The Score Model and the Evaluate Model modules are then added to evaluate the performance of the model. See the picture below for the full training pipeline. The performance of the model is discussed in RESULT.md.

| ![alt text](https://github.com/audicar/MSA_AI_Heart_Failure_Death_Prediction/blob/main/images/training_pipeline.png) |
|:--:|
| *Training Pipeline* |

<h4>Production Phase</h4>

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

<h3>References</h3>

**[1]** Australian Bureau of Statistics 2018

**[2]** National Health Survey 2017-18

**[3]** Chicco, D., &amp; Jurman, G. (2020). Machine learning can predict survival of patients with heart failure from serum creatinine and ejection fraction alone. _BMC medical informatics and decision making_, _20_(1), 1-16.



