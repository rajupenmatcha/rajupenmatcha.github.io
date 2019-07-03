# 5. Validate the model

* **Key topics or concepts:** Understand what you built
* **Estimated time:** 5 minutes

Now that we have a hosted endpoint running, we can make real-time predictions from our model very easily, simply by making an http POST request. But first, we'll need to setup serializers and deserializers for passing our test_data NumPy arrays to the model behind the endpoint.

```python
xgb_predictor.content_type = 'text/csv'
xgb_predictor.serializer = csv_serializer
xgb_predictor.deserializer = None
```
Now, we'll use a simple function to:

Loop over our test dataset
Split it into mini-batches of rows
Convert those mini-batchs to CSV string payloads
Retrieve mini-batch predictions by invoking the XGBoost endpoint
Collect predictions and convert from the CSV output our model provides into a NumPy array

```python
def predict(data, rows=500):
    split_array = np.array_split(data, int(data.shape[0] / float(rows) + 1))
    predictions = ''
    for array in split_array:
        predictions = ','.join([predictions, xgb_predictor.predict(array).decode('utf-8')])

    return np.fromstring(predictions[1:], sep=',')

dtest = test_data.as_matrix()
predictions = []
for i in range(dtest.shape[0]):
    predictions.append(predict(dtest[i:i+1, 1:]))
predictions = np.array(predictions).squeeze()

pd.crosstab(index=test_data.iloc[:, 0], columns=np.round(predictions), rownames=['actual'], colnames=['predictions'])
```

You will see a confusion matrix as shown below showing true positives,true negatives, false positives, and false negatives


