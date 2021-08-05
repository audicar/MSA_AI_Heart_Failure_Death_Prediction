For my MSLearn Journey, I have chosen the coding approach for the learning path (despite having done low code in the project. The Q&amp;A session clarified that this is acceptable).

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