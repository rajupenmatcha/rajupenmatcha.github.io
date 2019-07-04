# 5. Deploy the model

* **Key topics or concepts:** Create an endpoint.
* **Estimated time:** 12 minutes

Here is a 2 minute video of this part:
<iframe width="844" height="480" src="https://www.youtube.com/embed/zjjggZ0b4iA" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

We will now deploy the model we just trained behind a real-time hosted endpoint.

Note: This step can only be run after the previous step is successfully run. Otherwise it fails.

```python
xgb_predictor = xgb_model.deploy(initial_instance_count=1,
                                instance_type='ml.m4.xlarge',
                                )
```

When this is running successfully, you will see a dotted line under the cell, until it is complete. Once it is complete, the cell will have a number next to it.


You can switch back to the console and see under "Endpoints" that a new endpoint is being created where the model is hosted.
