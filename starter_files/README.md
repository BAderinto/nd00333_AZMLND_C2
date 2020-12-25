# Operationalizing Machine Learning

This project involves working with the Bank Marketing dataset on Azure to:
1. Configure a cloud-based machine learning production model
2. Deploy and consume the model. 
3. Create, publish, and consume a pipeline. 
4. Document all steps taken in a README file and a screencast video.

## Architectural Diagram
1. AutoML Studio
![az ml extension](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/project2.png)

2. Create, Publish and Consume Pipeline

![az ml extension](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/pipeline.png)


## Key Steps
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

### Registered dataset
Bankmarketing dataset from the Datasets section available in the lab environment used for running the automl experiment created in the ML studio.
![registered dataset](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/registered_dataset.png)

### Completed Automated ML Experiment
A new Automated ML run was created in ML Studio using the available Bankmarketing dataset, with an Automated ML experiment in a configured compute cluster(VM Size – Standard_DS2_V2, number of minimum nodes = 1, number of maximum nodes = 5, Exit criterion defaults to 1, Concurrency defaults to 5). The experiment was completed as shown below.
![completed experiment](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/automl-experiment-completed.png)

### Models from the Automated ML Experiment
Below is a screenshot of all models involved in the Automated ML run with the best model, VotingEnsemble, coming first at the top.
![all models](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/all-models.png)

### Best Model from the Automated ML Experiment
![best model](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/best-model.png)

3. Deploy the best model
At this stage, the experiment run has been completed and a summary of all the models and their metrics are shown as well as their explanations. 

![best model](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/all-models.png)

The Best Model is shown in the Details tab while it appears first in the Models tab at the top. 

![best model](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/best-model.png)


The best model is deployed which is allowed to interact with the HTTP API service and interact with the model by sending data over POST requests.

![deployed model](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/healthy-deployed-model-1.png)
![deployed model](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/healthy-deployed-model-2.png)

4. Enable logging

Application Insights is enabled on the deployed best Model and now logs can be retrieved. Although this feature is configurable at deploy time with a check-box, this is achieved in this experiment by running the [logs python script](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/logs.py). 

### logs.py script edited and updated
The logs.py script was updated  by assigning the deployed model’s name to the “name” variable and adding the line of code below to enable the application insights:
`service.update(enable_app_insights = True)` 

![logs.py script updated](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/logs-py-updated-1.png)

![logs.py script updated](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/logs-py-updated-3.png)

### logs.py script enable application insights on deployed model endpoint
To achieve enabling the “Application Insights Enabled” by running the logs.py script, we need to download the config.json file for Azure portal and place it in the same folder as the script. This provides information on the subscription id, resource group and the workspace name which used in script file. 
![logs.py script enable application insights](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/logs-enabled-1.png)

![application insights enabled on deployed model](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/logs-enabled-2.png)
![logs.py script updated](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/app-insights-enabled.png)

5. Swagger Documentation

In this step, the deployed model which is available in the Endpointd section, is consumed using Swagger. That is, the model documentation which Azure provides as a Swagger JSON file for deployed models will be available locally by Swagger container which has been hosted locally on docker.

This is achieved as follows:

a. [swagger.sh file](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/swagger/swagger.sh) contains a command line which is used to download the latest Swagger container on to docker when run as follows 

```
docker run -p 80:8080 swaggerapi/swagger-ui
```

After executing the command line, Swagger is now running on port 80.
### Running swagger.sh and serve.py
![swagger ui](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Swagger_Doc/swagger-serve.png)

![swagger container](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Swagger_Doc/swagger-container.png)

b. running [serve.py script](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/swagger/serve.py) starts a Python server on port 8000 because it is required as input in the Swagger UI page. This script runs by making reference to the downloaded swagger.json file of the deployed model endpoint which must be in the same folder as the python script.

![swagger bankmarketting-model-02](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Swagger_Doc/swagger-bankmarketting-model-02.png)

6. Consume model endpoints

The [endpoint.py script](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/endpoint.py) provided is used to interact with the deployed model in the Endpoints section. The endpoint.py script is updated by modifying both the scoring_uri and the key to match the key and the URI that was generated after deployment. The value for the URI and the key can be found in the Consume tab of the deployed model in the Endpoints section.

### Running endpoint.py and benchmark.sh scripts
![endpoint](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/endpoint-benchmark-1.png)
![endpoint](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/endpoint-benchmark-2.png)
![endpoint](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/endpoint-benchmark-3.png)

### data saved as json file
![data.json output saved](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Deploy_Model/data-json-saved.png)

7. Create and publish a pipeline

This part of the project was completed using the Jupyter Notebook, [aml-pipelines-with-automated-machine-learning-step](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/aml-pipelines-with-automated-machine-learning-step.ipynb) provided in the starter files. The notebook was updated to have the same keys, URI, dataset, cluster, and model names already created. 

### Pipeline Runs
![pipeline created](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Pipeline/pipeline-runs.png)

### Bankmarketing Train Pipeline Endpoint
![pipeline endpoint](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Pipeline/pipeline-endpoint-bankmarketing-train.png)

### Published Pipeline Overview
![Published Pipeline Overview 1](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Pipeline/published-pipeline-overview-1.png)

![Published Pipeline Overview 2](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Pipeline/published-pipeline-overview-2.png)

![Published Pipeline Overview 3](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Pipeline/published-pipeline-overview-3.png)

![Published Pipeline Overview 4](https://github.com/BAderinto/nd00333_AZMLND_C2/blob/master/starter_files/Pipeline/published-pipeline-overview-4.png)


## Screen Recording
[screen recording](https://youtu.be/0Dt-bE0v8PA)

## Standout Suggestions
1. With reference to this project, the pipeline capabilities in data preparation could be better used in validating and cleaning, munging and transformation, normalization, and staging of the dataset used for training the model.

2. Also the training process could be more effecient by including parameterizing arguments, filepaths, and logging / reporting configurations. Efficiency might come from specifying specific data subsets, different hardware compute resources, distributed processing, and progress monitoring.

3. The training iteration process can be improved through fine-tuning around the training loop of the pipeline. That is, when the pipeline is rerun, the run jumps to the steps that need to be rerun in this case, an updated training script while steps that do not need to be rerun are skipped.

