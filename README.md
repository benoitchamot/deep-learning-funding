# deep-learning-funding
Create a binary classifier that can predict whether applicants will be successful if funded, with an accuracy of 75% or better

## File structure
* The `img` directory contains the charts and graphics generated by the Jupyter notebooks and used in this README file
* The `tmp` directory contains the weights saved during training in Step 1
* `AlphabetSoupCharity_Optimisation.h5` is a binary file containing the optimised model from Step 2
* `AlphabetSoupCharity.h5` is a binary file containing the model from Step 1
* The module `ml_classification.py` contains functions commonly used for ML applications
* The `StepN_*.ipynb` files are the Jupyter notebooks used in steps `N = 1-5` detailed below

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

### Research questions

**Data Preprocessing**
> **Question 1**: What variable(s) are the target(s) for your model?

We classify against the `IS_SUCCESSFUL` column: `1` for succesful and `0` for unsuccesful

> **Question 2**: What variable(s) should be removed from the input data because they are neither targets nor features?

The identification columns `EIN` and `NAME` are removed as they are first thought to provide no valuable information. This is revisted in Step 3 (see below) where `NAME` is added back as a feature.

> **Question 3**: What variable(s) are the features for your model?

We use all the columns in the CSV apart from `IS_SUCCESSFUL`, `EIN` and `NAME` (see Step 3)

**Compiling, Training, and Evaluating the Model**
> **Question 4**: What steps did you take in your attempts to increase model performance?

See next Section: `Approach`

> **Question 5**: How many neurons, layers, and activation functions did you select for your neural network model, and why?

See Section: `Results`

> **Question 6**: Were you able to achieve the target model performance (75%)?

Yes, by using the `NAME` as one of the features, we achieve an accuracy of 80%. We do however have some reservations about this approach which is discussed in the Section: `Conclusions`. In general, an accuracy closer to 72% should be expect from the model.

### Approach
We take four main steps to approach the problem, as presented in this section.

#### Step 1: First attempt
The first step is simply to establish a baseline by constructing a simple network to get some preliminary results. The accuracy is used as the metric to optimise (this is the case in the next steps too.)

The data are prepared in the following way:
1. Import complete dataset and load it into a pandas DataFrame
2. Drop the `EIN` and `NAME` columns
3. Group the application types with a number of entries below 500 together into an 'other' type
4. Group the classification types with a number of entries below 1000 together into an 'other' type
5. Create dummies with all categorical columns
6. Split the data into training and test sets
7. Scale the data using a standard scaler

The model is then trained, evaluated and saved in the `AlphabetSoupCharity.h5` binary file.

#### Step 2: Manual and automated tuning exploration
The second step builds on the first one. We change the hyperparamters of the initial model one by one to try and find an optimal:
1. test a number of hidden layer between 1 and 4
2. test different activation functions
3. test different number of neurons in the internal layers, between 5 and 50
4. change the number of epochs, between 100 and 800

For each hyperparamater, we take the best configuration in terms of accuracy before changing the next hyperparameter. This manual approach may seem slow and "brute force"-like but it should help understand the mechanics of the network and identify the main performance drivers. As we show in the results, this is not the case.

Following the manual approach, we use the `keras-tuner` Python module to automatically tune the model and try and improve the accuracy.

In all the steps above, the same data as for Step 1, presented in the previous subsection, are used. In addition to the Step 1 data, we try two additional approaches with `keras-tuner`:
1. We add the square of the 'asked amount' column as a feature
2. We use the 'income' column as an ordinal feature instead of a nominal one

To transform the income, we use the following conversion table:

<table>
    <tr>
        <th>Original (nominal) value</th>
        <th>Ordinal value</th>
    </tr>
    <tr>
        <td>0</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1-9999</td>
        <td>1</td>
    </tr>
    <tr>
        <td>10000-24999</td>
        <td>2</td>
    </tr>
    <tr>
        <td>25000-99999</td>
        <td>3</td>
    </tr>
    <tr>
        <td>100000-499999</td>
        <td>4</td>
    </tr>
    <tr>
        <td>1M-5M</td>
        <td>5</td>
    </tr>
    <tr>
        <td>5M-10M</td>
        <td>6</td>
    </tr>
    <tr>
        <td>10M-50M</td>
        <td>7</td>
    </tr>
    <tr>
        <td>50M+</td>
        <td>8</td>
    <tr>
</table>

#### Step 3: Using `NAME` as a feature
After discussion with some peers, it seems that adding the `NAME` column back as a feature could significantly improves the results. We look at this option in Step 3.

#### Step 4: Pruning and simplification
During this step, we look again at the original data, without using `NAME`. More reading and research went into this step to understand industry's best practices and common approach. We look at a model with as few neurons and epochs as possible to accelerate the training.

We used this source for some guidelines: https://medium.com/geekculture/introduction-to-neural-network-2f8b8221fbd3

This model is saved in the `AlphabetSoupCharity_Optimisation.h5` binary file.

### Results
In Step 1, we use a preliminary model with the following structure:

<img src=img/model_1.png>

The output layer use a `sigmoid` activation function while all the other layers use `relu`. The model achieve an accuracy of 72.34% which is already close to the target of 75%.

In Step 2, we start with the model from Step 1 shown above and get the following accuracy metrics when changing the number of hiddent layers between 1 and 4:

<img src=img/accuracy_vs_number_of_hidden_layers.png>

We fix the number of hidden layers to 3 as it gives the best performance and then change the activation function (the same function is used on all layers but the output one.) The following performance is achieved for the different functions:

<img src=img/accuracy_vs_activation_functions.png>

We select `selu` as our activation function and then change the number of neurons per layer (all layers have the same number of neurons):

<img src=img/accuracy_vs_number_of_neurons.png>

We keep 10 neurons in all the layers and the look at the influence of the number of epochs:

<img src=img/accuracy_vs_number_of_epochs.png>

The final model has the structure shown below:

<img src=img/model_2.png>

The accuracy of the optimised model is only 72.73% at best, which is barely an improvement compared to the model from Step 1 and still a long way from the requirement of 75%. The chart below shows the performance of all the models looked at in this Step 2 compared to the target of 75%:

<img src=img/comparison_between_model_accuracies_and_target.png>

The accuracies are all between 72.26% (min) and 72.73% (max), with an average of 72.57% so no significant driver is found so far that could help improve the performance. Moreover, as some of the steps were repeated during the code development, different results were obtained, indicating that the difference between models could simply come from differences in the random number generators used as part of the model training.

Using `keras-tuner`, we can automate and accelerate the tuning process, while losing some transparency in the performance of the intermediate optimisation steps. The following design space is used by the tuner:

<table>
    <tr>
        <th>Parameter</th>
        <th>Possible values</th>
    </tr>
    <tr>
        <td>Activation</td>
        <td>relu, selu, sigmoid, tanh</td>
    </tr>
    <tr>
        <td>Number of layers</td>
        <td>Between 1 and 6</td>
    </tr>
    <tr>
        <td>Neurons per layer</td>
        <td>Between 1 and 20</td>
    </tr>
    <tr>
        <td>Epochs</td>
        <td>Up to 20</td>
    </tr>
<table>

The best model has the structure shown below:

<img src=img/model_3.png>

An accuracy of 72.85% is achieved, which is still below the requirement.

Changing the features, provide little to no improvement:
- Adding the squared asked amount, leaves the accuracy at 72.85%
- Changing the income from nominal to ordinal, only improves the accuracy by 0.01%

In Step 3, we add the `NAME` column back as a feature. Because it is a nominal column, we create dummies as part of the preprocessing step. We use `keras-tuner` to optimise the model and we eventually get an accuracy of 80.07%.

This exceeds the requirement and the performance of all the other models. This however raises a question:  if the name of a company is a dominant feature in the algorithm to decide whether or not to get funding we would expect either:
1. the model to be overfitted, and it would therfore not perform as well when data about new companies are provided, or
2. the funding campaign decision-makers are biased and influenced by the company names. A strategy for a company requesting funding would basically be to give themselves the right name and to increase their chances of funding.

In Step 4, we use `keras-tuner` on a smaller design space based on guidelines from the data analytics community. The following design space is used by the tuner:

<table>
    <tr>
        <th>Parameter</th>
        <th>Possible values</th>
    </tr>
    <tr>
        <td>Activation</td>
        <td>relu, selu, sigmoid, tanh</td>
    </tr>
    <tr>
        <td>Number of layers</td>
        <td>Between 1 and 2</td>
    </tr>
    <tr>
        <td>Neurons per layer</td>
        <td>Between 2 and 10</td>
    </tr>
    <tr>
        <td>Epochs</td>
        <td>Up to 20</td>
    </tr>
<table>

The best model has the structure shown below:

<img src=img/model_4.png>

An accuracy of 72.86% is achieved, which is still below the requirement.

### Conclusions
#### Summary of the results
The accuracy obtained by the different models is given in the table below:

<table>
    <tr>
        <th>Step</th>
        <th>Model</th>
        <th>Best accuracy</th>
        <th>Remarks</th>
    </tr>
    <tr>
        <td>Step 1</td>
        <td>Baseline</td>
        <td>72.34%</td>
        <td></td>
    </tr>
    <tr>
        <td>Step 2</td>
        <td>Manually optimised</td>
        <td>72.73%</td>
        <td>Using the data as in Step 1</td>
    </tr>
    <tr>
        <td>Step 2</td>
        <td>Optimised with keras-tuner</td>
        <td>72.85%</td>
        <td>Using the data as in Step 1</td>
    </tr>
    <tr>
        <td>Step 2</td>
        <td>Optimised with keras-tuner</td>
        <td>72.85%</td>
        <td>Using the squared amount as a feature</td>
    </tr>
    <tr>
        <td>Step 2</td>
        <td>Optimised with keras-tuner</td>
        <td>72.86%</td>
        <td>Using the income as an ordinal feature</td>
    </tr>
    <tr>
        <td>Step 3</td>
        <td>Optimised with keras-tuner</td>
        <td>80.07%</td>
        <td>Using the NAME as a feature</td>
    </tr>
    <tr>
        <td>Step 4</td>
        <td>Optimised with keras-tuner</td>
        <td>72.86%</td>
        <td>Using the data as in Step 1</td>
    </tr>
</table>

#### Recommendations
- Use the Step 3 model, including the NAME column, only with companies that are included in the training and test sets.
- Use the Step 4 model providing the accuracy of 72.86% with other companies as it relies on more objective data (excluding the company name, which seems more arbitrary)
- Explore ways to include information that could be a by-product of the company name in a more objective way: e.g. number of previous application, number of previous successful campaigns, reputation, market share, number of employees, etc.

#### Alternative model (Step 5)
Because of the large number of dummies columns, it could be that a decision tree could be a better model for this type of data. Instead of defining complex non-linear separator in a large multi-dimensional space, taking decision step by step, in a more transparent way, could be better in this case.

This is tried as an additional step (see `Step5_Tree.ipynb`.) A tree with a maximum depth of 9 can achieve an accuracy of 72.6&, comparable to some of the models presented above with two main advantages:
1. It is much faster to train and execute
2. It is more transparent and helps explain the decision process to the users

