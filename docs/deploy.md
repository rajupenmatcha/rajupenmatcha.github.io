# 4. Deploy the model

* **Key topics or concepts:** Create an endpoint.
* **Estimated time:** 12 minutes

Here is a 3 minutes video of this step:

<iframe src="https://broadcast.amazon.com/embed/140014" width="682" height="384" style="border:0" allowfullscreen></iframe>

We will now deploy the model we just trained behind a real-time hosted endpoint.

Note: This step can only be run after the previous step is successfully run. Otherwise it fails.

```python
xgb_predictor = xgb_model.deploy(initial_instance_count=1,
                                instance_type='ml.m4.xlarge',
                                )
```

When this is running successfully, you will see the dotted line under the cell, until it is complete. Once it is complete, the cell will have a number next to it.

![image](https://drive.corp.amazon.com/view/Engineering%20Excellence/DYOC-Courses/images/sage-maker/deploy.png)

You can switch back to the console and see under "Endpoints" that a new endpoint is being created where the model is hosted.
