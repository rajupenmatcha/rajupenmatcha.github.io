# 2. Data Preprocessing

* **Key topics or concepts:** Let us see how the data looks like and perform pre-processing
* **Estimated time:** 5 minutes

Here is a 6 minutes video of this step:

<iframe src="https://broadcast.amazon.com/embed/140005" width="682" height="384" style="border:0" allowfullscreen></iframe>

First lets create a notebook and start with data pre-processing. For this example, we'll use the telecom churn dataset from UC Irvine, 
which contains 3,333 records and 21 attributes and labels for each record on customer churn. 
We transfer the data to S3 for use in training, inspect the data, understand the distributions, and determine what type(s) of preprocessing might be needed.

Run the below cell in the notebook. To run it, press Shift + enter. An asterisk appears next to the cell when you are running the cell, when a number appears, the cell was executed. Any error message appears under the cell.

First we setup the linkage and authentication to AWS services. We provide the role to give learning and hosting access to your data and the S3 bucket name that you want to use for training and model data.  Here we extract the role you created earlier for accessing your notebook

```python
import boto3
import re
from sagemaker import get_execution_role
from sagemaker import Session
```

```python
bucket = Session().default_bucket()
role = get_execution_role()
prefix = 'sagemaker/DEMO-xgboost-churn'
```

<p>Next, we'll import the Python libraries we'll need for the remainder of the exercise. </p>

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import io
import os
import sys
import time
import json
from IPython.display import display
from time import strftime, gmtime
import sagemaker
from sagemaker.predictor import csv_serializer
```

Mobile operators have historical records on which customers ultimately ended up churning and which continued using the service. We can use this historical information to construct an ML model of one mobile operator’s churn using a process called training.
The dataset we use is publicly available and was mentioned in the book Discovering Knowledge in Data by Daniel T. Larose. It is attributed by the author to the University of California Irvine Repository of Machine Learning Datasets. Let's download and read that dataset in now:


```python
!wget http://dataminingconsultant.com/DKD2e_data_sets.zip
!unzip -o DKD2e_data_sets.zip
```

Review the dataset

```python
churn = pd.read_csv('./Data sets/churn.txt')
pd.set_option('display.max_columns', 500)
churn
```
By modern standards, it’s a relatively small dataset, with only 3,333 records, where each record uses 21 attributes to describe the profile of a customer of an unknown US mobile operator. The attributes are:

    State: the US state in which the customer resides, indicated by a two-letter abbreviation; for example, OH or NJ
    Account Length: the number of days that this account has been active
    Area Code: the three-digit area code of the corresponding customer’s phone number
    Phone: the remaining seven-digit phone number
    Int’l Plan: whether the customer has an international calling plan: yes/no
    VMail Plan: whether the customer has a voice mail feature: yes/no
    VMail Message: presumably the average number of voice mail messages per month
    Day Mins: the total number of calling minutes used during the day
    Day Calls: the total number of calls placed during the day
    Day Charge: the billed cost of daytime calls
    Eve Mins, Eve Calls, Eve Charge: the billed cost for calls placed during the evening
    Night Mins, Night Calls, Night Charge: the billed cost for calls placed during nighttime
    Intl Mins, Intl Calls, Intl Charge: the billed cost for international calls
    CustServ Calls: the number of calls placed to Customer Service
    Churn?: whether the customer left the service: true/false

The last attribute, Churn?, is known as the target attribute–the attribute that we want the ML model to predict. Because the target attribute is binary, our model will be performing binary prediction, also known as binary classification.

Let's begin exploring the data:

```python
# Frequency tables for each categorical feature
for column in churn.select_dtypes(include=['object']).columns:
    display(pd.crosstab(index=churn[column], columns='% observations', normalize='columns'))

# Histograms for each numeric features
display(churn.describe())
%matplotlib inline
hist = churn.hist(bins=30, sharey=True, figsize=(10, 10))
```

We can see immediately that:

    State appears to be quite evenly distributed
    Phone takes on too many unique values to be of any practical use. It's possible parsing out the prefix could have some value, but without more context on how these are allocated, we should avoid using it.
    Only 14% of customers churned, so there is some class imabalance, but nothing extreme.
    Most of the numeric features are surprisingly nicely distributed, with many showing bell-like gaussianity. VMail Message being a notable exception (and Area Code showing up as a feature we should convert to non-numeric).