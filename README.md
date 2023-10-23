# deep-learning-funding
Create a binary classifier that can predict whether applicants will be successful if funded

## About the data
### Source
The data is provided by Monash University: https://static.bc-edx.com/data/dla-1-2/m21/lms/starter/charity_data.csv

### Columns in the dataset CSV
* `EIN` and `NAME` — Identification columns
* `APPLICATION_TYPE` — Alphabet Soup application type
* `AFFILIATION` — Affiliated sector of industry
* `CLASSIFICATION` — Government organisation classification
* `USE_CASE` — Use case for funding
* `ORGANIZATION` — Organisation type
* `STATUS` — Active status
* `INCOME_AMT` — Income classification
* `SPECIAL_CONSIDERATIONS` — Special considerations for application
* `ASK_AMT` — Funding amount requested
* `IS_SUCCESSFUL` — Was the money used effectively

## Report
### Overview of the analysis
The purpose of the analysis is to optimise a neural network to classify applications for crowdfunding and predict whether they will be succesful.

### Results
* Using bulleted lists and images to support your answers, address the following questions:

**Data Preprocessing**
* What variable(s) are the target(s) for your model?
> We classify the `IS_SUCCESSFUL` column: `1` for succesful and `0` for unsuccesful

* What variable(s) should be removed from the input data because they are neither targets nor features?
> The identification columns `EIN` and `NAME` are removed as they provide no valuable information

* What variable(s) are the features for your model?
> We use all the other columns in the CSV as presented above

**Compiling, Training, and Evaluating the Model**
* How many neurons, layers, and activation functions did you select for your neural network model, and why?
* Were you able to achieve the target model performance?
* What steps did you take in your attempts to increase model performance?

### Summary
Summarise the overall results of the deep learning model. Include a recommendation for how a different model could solve this classification problem, and then explain your recommendation.