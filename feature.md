# 3. Feature engineering and selection

* **Key topics or concepts:** Let us engineer some new features and drop any unnecessary features
* **Estimated time:** 5 minutes

Here is the 4 minute video of this part:
<iframe width="560" height="315" src="https://www.youtube.com/embed/DvPremfqTeU" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Feature "phone" can be dropped and Area Code needs to be non-numeric

```python
churn = churn.drop('Phone', axis=1)
churn['Area Code'] = churn['Area Code'].astype(object)
```

Next let's look at the relationship between each of the features and our target variable.
These steps help you understand your data better.

```python
for column in churn.select_dtypes(include=['object']).columns:
    if column != 'Churn?':
        display(pd.crosstab(index=churn[column], columns=churn['Churn?'], normalize='columns'))

for column in churn.select_dtypes(exclude=['object']).columns:
    print(column)
    hist = churn[[column, 'Churn?']].hist(by='Churn?', bins=30)
    plt.show()
```

Let us review how each of these features correlate with each other.

```python
display(churn.corr())
pd.plotting.scatter_matrix(churn, figsize=(12, 12))
plt.show()
```

Interestingly we see that churners appear:

Fairly evenly distributed geographically    
More likely to have an international plan
Less likely to have a voicemail plan
To exhibit some bimodality in daily minutes (either higher or lower than the average for non-churners)
To have a larger number of customer service calls (which makes sense as we'd expect customers who experience lots of problems may be more likely to churn)

In addition, we see that churners take on very similar distributions for features like Day Mins and Day Charge. That's not surprising as we'd expect minutes spent talking to correlate with charges. Let's dig deeper into the relationships between our features.

```python
churn = churn.drop(['Day Charge', 'Eve Charge', 'Night Charge', 'Intl Charge'], axis=1)
```

Let's convert our categorical features into numeric features.

```python
model_data = pd.get_dummies(churn)
model_data = pd.concat([model_data['Churn?_True.'], model_data.drop(['Churn?_False.', 'Churn?_True.'], axis=1)], axis=1)
```