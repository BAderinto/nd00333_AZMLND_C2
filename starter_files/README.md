*NOTE:* This file is a template that you can use to create the README for your project. The *TODO* comments below will highlight the information you should be sure to include.


# Operationalizing Machine Learning

This project involves working with the Bank Marketing dataset on Azure to:
1. Configure a cloud-based machine learning production model
2. Deploy and consume the model. 
3. Create, publish, and consume a pipeline. 
4. Document all steps taken in a README file and a screencast video.

## Architectural Diagram
*TODO*: Provide an architectual diagram of the project and give an introduction of each step. An architectural diagram is an image that helps visualize the flow of operations from start to finish. In this case, it has to be related to the completed project, with its various stages that are critical to the overall flow. For example, one stage for managing models could be "using Automated ML to determine the best model". 



## Key Steps
*TODO*: Write a short discription of the key steps. Remeber to include all the screenshots required to demonstrate key steps. 
The following steps were taken to complete the project as required

1. Authentication
Authentication for this project was achieved using the login credentials provided on the Udacity Project 2 Lab outside the virtual lab on portal.azure.com through a web browser and on the Git Bash terminal. 

```
az login
```

![az login](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Authentication/az_login.png)

In this step, the Azure Machine Learning Extension was installed on the terminal which allows interaction with Azure Machine Learning Studio by running the following line of code.

```
az extension add -n azure-cli-ml
```
![az ml extension](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Authentication/az_ml_extension.png)
The Service Principal account can be created and associated with the workspace by running the code below at the terminal

```
az ad sp create-for-rbac --sdk-auth --name ml-auth
```
![az service principal account](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Authentication/az_service_principal_account.png)

The details of the account can be viewed running the following code

```
az ad sp show --id b3026462-166c-4051-8f62-74cc5a24bc0d
```
![az sp acount information](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Authentication/az_sp_account_info.png)

The role, "owner" was assigned to the service principal account for the given Workspace, Resource Group and User objectId using the code below

```
$ az ml workspace share -w udacity-project-workspace -g udacity-rg --user 65d2756f-d07b-44e7-914d-b7e411d2e06c --role owner
```
![role owner to sp account](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Authentication/role_owner_to_sp_account.png)

2. Automated ML Experiment
The automated machine learning experiment used for this project was created in Azure ML studio and a compute cluster is configured to run the experiment using the [bank marketing dataset](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv)

![registered dataset](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/registered_datasets.png)

![registered dataset](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/experiment_completed.PNG)

![best model](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/best_model.png)

3. Deploy the best model
At this stage, the experiment run has been completed and a summary of all the models and their metrics are shown as well as their explanations. 

![registered dataset](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/registered_datasets.png)

The Best Model is shown in the Details tab while it appears first in the Models tab at the top. 

![best model](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/best_model.png)

![best model](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/best_model_first.png)

The best model is deployed which is allowed to interact with the HTTP API service and interact with the model by sending data over POST requests.

![deployed model](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/deployed_model_app_i_false.png)


4. Enable logging

Application Insights is enabled on the deployed best Model and now logs can be retrieved. Although this feature is configurable at deploy time with a check-box, this is achieved in this experiment by running the [logs python script](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/logs.py). 

![logs showing by running provided logs.py script](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/logs_by_logs_python_script_1.png)

![logs showing by running provided logs.py script](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/logs_by_logs_python_script_2.png)

![application insights enabled on deployed model](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/deployed_model.png)

5. Swagger Documentation

In this step, the deployed model which is available in the Endpointd section, is consumed using Swagger. That is, the model documentation which Azure provides as a Swagger JSON file for deployed models will be available locally by Swagger container which has been hosted locally on docker.

This is achieved as follows:

a. [swagger.sh file](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/swagger/swagger.sh) contains a command line which is used to download the latest Swagger container on to docker when run as follows 

```
docker run -p 80:8080 swaggerapi/swagger-ui
```

After executing the command line, Swagger is now running on port 80.

[swagger ui](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Swagger_Doc/swagger_ui.png)

b. running [serve.py script](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/swagger/serve.py) starts a Python server on port 8000 because it is required as input in the Swagger UI page. This script runs by making reference to the downloaded swagger.json file of the deployed model endpoint which must be in the same folder as the python script.

![swagger ui](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Swagger_Doc/swagger_ui.png)

![swagger ui](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Swagger_Doc/bm_swagger_1.png)

![swagger ui](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Swagger_Doc/bm_swagger_2.png)

![swagger ui](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Swagger_Doc/bm_swagger_3.png)

![swagger ui](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Swagger_Doc/bm_swagger_4.png)

6. Consume model endpoints

The [endpoint.py script](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/endpoint.py) provided is used to interact with the deployed model in the Endpoints section. The endpoint.py script is updated by modifying both the scoring_uri and the key to match the key and the URI that was generated after deployment. The value for the URI and the key can be found in the Consume tab of the deployed model in the Endpoints section.

![json output](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Consume_Model/json_output.png)

7. Create and publish a pipeline

This part of the project was completed using the Jupyter Notebook, [aml-pipelines-with-automated-machine-learning-step](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/aml-pipelines-with-automated-machine-learning-step.ipynb) provided in the starter files. The notebook was updated to have the same keys, URI, dataset, cluster, and model names already created. 

![pipeline created](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Pipeline/pipeline_created.png)

![pipeline endpoint](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Pipeline/pipeline_endpoint.png)

![Bankmarketing dataset](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Pipeline/Bankmarketing_dataset.png)

![pipeline run overview](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Pipeline/pipeline_run_overview.png)

![use rundetails widget](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Pipeline/rundetails_widget_1.png)

![use rundetails widget](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Pipeline/rundetails_widget_2.png)

![scheduled run](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Pipeline/pipeline_created.png)


## Screen Recording
*TODO* Provide a link to a screen recording of the project in action. Remember that the screencast should demonstrate:
[screen recording](https://youtu.be/rwwZotqhCl0)

## Standout Suggestions
*TODO (Optional):* This is where you can provide information about any standout suggestions that you have attempted.
Although this project did not utilize the full capabilities of pipelines other than training the model, I look forward to using pipelines to perform automation which combines several other tasks, or steps like:

Data Preparation
Validation
Deployment
Combined tasks
