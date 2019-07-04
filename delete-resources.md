# 7. Delete the resources

* **Key topics or concepts:** Delete the resources to stop incurring costs
* **Estimated time:** 3 minutes

Here is a 1 minute video for this last part:
<iframe width="560" height="315" src="https://www.youtube.com/embed/Z8yTQalSSag" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
Finally we will delete our instances.

The below code will only delete the endpoint you created in deploy step. To delete the notebook, you need to go to the "Notebook Instances" section and delete the notebook you created in Step 1.

```python
sagemaker.Session().delete_endpoint(xgb_predictor.endpoint)
```

You are awesome! Thanks for competing the course. Please let us know if there are other topics you want us to cover or if you want to work with us on building similar tutorials
