# 4. Training

* **Key topics or concepts:** After setting training parameters, we kick off training, and poll for status until training is completed.
* **Estimated time:** 5 minutes

Here is a 6 minute video on this part

<iframe width="560" height="315" src="https://www.youtube.com/embed/q1g_a1mWyn4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

We split original dataset into train, validate, and test subsets

We set training parameters - location for S3 to store data and model are provided here. 
Along with the role information, we also provide the information for training job with an instance type and instance count.

And now let's split the data into training, validation, and test sets. This will help prevent us from overfitting the model, and allow us to test the models accuracy on data it hasn't already seen.

```python
train_data, validation_data, test_data = np.split(model_data.sample(frac=1, random_state=1729), [int(0.7 * len(model_data)), int(0.9 * len(model_data))])
train_data.to_csv('train.csv', header=False, index=False)
validation_data.to_csv('validation.csv', header=False, index=False)
```

Now we'll upload these files to S3.

```python
boto3.Session().resource('s3').Bucket(bucket).Object(os.path.join(prefix, 'train/train.csv')).upload_file('train.csv')
boto3.Session().resource('s3').Bucket(bucket).Object(os.path.join(prefix, 'validation/validation.csv')).upload_file('validation.csv')
```

We'll get access to xgboost container 

```python
from sagemaker.amazon.amazon_estimator import get_image_uri
container = get_image_uri(boto3.Session().region_name, 'xgboost')
```

Then, because we're training with the CSV file format, we'll create s3_inputs that our training function can use as a pointer to the files in S3.

```python
s3_input_train = sagemaker.s3_input(s3_data='s3://{}/{}/train'.format(bucket, prefix), content_type='csv')
s3_input_validation = sagemaker.s3_input(s3_data='s3://{}/{}/validation/'.format(bucket, prefix), content_type='csv')
s3_output_location = 's3://{}/{}/{}'.format(bucket, prefix, 'output')
```

Create an instance of the sagemaker.estimator.Estimator class, set hyperparameters, and call the fit method to start training.

```python
xgb_model = sagemaker.estimator.Estimator(container,
                                         role, 
                                         train_instance_count=1, 
                                         train_instance_type='ml.m4.xlarge',
                                         train_volume_size = 5,
                                         output_path=s3_output_location,
                                         sagemaker_session=sagemaker.Session())

xgb_model.set_hyperparameters(max_depth = 5,
                              eta = .2,
                              gamma = 4,
                              min_child_weight = 6,
                              subsample=0.8,
                              silent = 0,
                              objective = 'binary:logistic',
                              num_round = 100)
                              
xgb_model.fit({'train': s3_input_train, 'validation': s3_input_validation}) 
```

If it is successful, you will see something like the following, which indicates that the training job process has kicked in.

```python
2019-07-02 18:48:55 Starting - Starting the training job...
2019-07-02 18:48:57 Starting - Launching requested ML instances......
2019-07-02 18:49:58 Starting - Preparing the instances for training...
2019-07-02 18:50:50 Downloading - Downloading input data...
2019-07-02 18:51:23 Training - Downloading the training image...
2019-07-02 18:51:54 Uploading - Uploading generated training model
2019-07-02 18:51:54 Completed - Training job completed
```

If there is any red text in the output of the cell, ignore it, that is expected. If the cell is successful, in the end of execution, you will see text similar to the following:

```python
[98]#011train-error:0.025289#011validation-error:0.067568
[18:51:43] src/tree/updater_prune.cc:74: tree pruning end, 1 roots, 0 extra nodes, 14 pruned nodes, max_depth=0
[99]#011train-error:0.024861#011validation-error:0.066066

Billable seconds: 64
```

You can go to the sagemaker console and click on the Training Jobs section in the left to view the job you have just created.
