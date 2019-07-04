# 1. Create a Notebook

* **Key topics or concepts:** Create a notebook instance to successfully complete this tutorial.
* **Estimated time:** 8 minutes

Here is a 5 minute video of this part:
<iframe width="560" height="315" src="https://www.youtube.com/embed/CFIYMzqAyNk" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Open the AWS console and search for SageMaker under the services.

1. In the left hand pannel, click on Notebook Instances. Then Create Notebook.
1. Provide a name for the SageMaker notebook for example: sagemaker. Leave the rest default in this section
1. In the next section Under IAM role -choose an IAM role - create a new role
1. In the popup window, select "Any S3 bucket" then click "Create Role"
1. Leave the other defaults in the configuration, then click create
1. Once the notebook is "InService" status(will take upto 4 minutes), click "Open Jupyter"
1. On the right corner, click new, then select Conda Python3. This will open a new ipython notebook
1. (optional) click on the Untitled, in the top and give the notebook a name

You are now ready to proceed working on this notebook for the rest of the tutorial.
