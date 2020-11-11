# Build a Spark Machine Learning Model for Predictive Maintenance using Azure Databricks and Azure Machine Learning

## Overview

This document walks through how we can build and train a model using Azure Databricks and/or Azure Machine Learning for two different purposes:
* Solution 1: Build and train a model on Azure Databrics and use it to predict the current and future state of the machine
* Solution 2: Build and train a model on Azure Databricks and use the open source MLFlow API to track, run experiments and register the model on Azure Machine Learning. 

A predictive maintenance scenario is used for this use case, where a Random Forest is trained and used to classify a set of four machine components into _healthy_ or _unhealthy requiring maintenance_ states.

For an in-depth description of the scenario, we have documented the operations in each of the supplied Jupyter notebooks contained in the `./notebooks/` directory of this repository.

## Experiment Tracking and Model Deployment with MLFlow and Azure Machine Learning

#### Azure Machine Learning Service <img src=https://www.bluegranite.com/hubfs/Machine%20Learning%20Service%20Workspaces.svg width="30">
Azure Machine Learning Service (AMLS) is Microsoft's homegrown solutions to supporting your end-to-end machine learning lifecycle in Azure. AMLS is a newer service on Azure that's continually getting new features. Currently you can use either the Python SDK or the R SDK to interact with the service or you can use the Designer for a low-code foray into machine learning. 

<!--![](https://www.bluegranite.com/hubfs/Machine%20Learning%20Service%20Workspaces.svg)   -->

AMLS includes functionality to keep track of datasets, experiments, pipelines, models, and API endpoints. Plus, you can easily provision notebook virtual machines, training clusters, and inference clusters right from the site (and, of course, from the SDK). 

![](https://www.bluegranite.com/hs-fs/hubfs/AMLS_process.png?width=788&name=AMLS_process.png_)


<!--![]() -->
<img src= "https://www.bluegranite.com/hs-fs/hubfs/amls_site.png?width=600&name=amls_site.png" height = "700" width="700">
Azure Machine Learning, however, has some limitations in coping with big data—this is where an Azure Databricks compute can help.

#### Azure Databricks <img src=https://www.bluegranite.com/hubfs/Azure%20Databricks-1.svg width = "30">
[Databricks](https://azure.microsoft.com/en-us/services/databricks/) is a unified analytics platform projects to data science solutions using [Apache Spark](https://spark.apache.org/). 
One of the biggest benefits of using Databricks is the interface, which allows for easy cluster creation, data management, and user collaboration while coding—that is, users can work together in the same notebook—but one person can be writing R in one block and another can be switching back and forth between Python and SQL in another cell on the same notebook. This makes it easy for users to work towards a solution using the langage they're most familair with. <br> [](https://www.bluegranite.com/hs-fs/hubfs/dbx_site.png?width=600&name=dbx_site.png).

#### Integrating Databricks with Azure Machine Learning

By using Databricks as a compute to train your models when working with a lot of data and Azure Machine Learning you can benefit from from the parallelization power of Apache Spark and Azure Machine Learning's capability of tracking runs and experiements. As shown in the example detailed in this repository, this can be easily done through the se of MLFlow as the tracking engine inside of Databricks. This is an open source package that come pre-installed and enabled in the ML runtime version in Databricks. 
<img src="https://www.bluegranite.com/hs-fs/hubfs/mlflow-1.png?width=600&name=mlflow-1.png" width = "600">


An additional benefit of MLFlow, is that MLFlow wll autmaticlly track your MLib experiment runs with zero configuration.

<img src="https://www.bluegranite.com/hs-fs/hubfs/experiment_track_mlflow.png?width=334&name=experiment_track_mlflow.png" height="400" width="300">


## Design

One solution only uses the Azure Databricks service, whilst the latter which details how to deploy the model as a web service uses both databricks and AMLS. The first solution is comprised of 6 notebooks, whilst the second consists of 3. 

 1. **Ingest** downloads the simulated data sets from a GitHub site and converts and stores them as Spark dataframes on the Databricks DBFS. “Input Data” in the architecture diagram refers to a set of five simulated data sets related to realistic machine operating conditions.

 2. **Feature engineering** transforms and combines the data sets into an analysis data set. The analysis data set can be targeted for training a model or scoring data for a production pipeline. Each analysis data set is also stored in the Databricks DBFS.

 3. **Training** takes a subset of the complete data and constructs a model we can use to predict future outcomes. For solution 1, The model is stored in the Databricks DBFS for use by the scoring notebook. For solution 2, this notebook also details how to use MLFlow APIs to register the model on Azure Machine Learning and build an Azure Container Image for model deployment.

 4. **Scoring** uses a different subset of the data, including data not yet collected to predict the current and future state of the machine. The model results are stored back onto the Databricks DBFS. 

# Prerequisites

 * We assume you have an Azure subscription. 

## Azure Databricks

This example is designed to run on [Azure Databricks](https://azure.microsoft.com/en-us/services/databricks/). Provision the service through your Azure subscription at the [Azure portal](https://portal.azure.com).

  * Click "+ Create a resource" button, then search for `databricks` and select the Azure Databricks search suggestion. Follow the prompts, and select `Standard pricing tier`. See https://azure.microsoft.com/en-us/pricing/details/databricks/ for information on different pricing tiers.
  
See https://docs.azuredatabricks.net/getting-started/index.html for detailed documentation on using Azure Databricks.

## Databricks cluster

Once your Azure Databricks service has been created, you will need to create a compute cluster to execute the notebooks.

  * From the portal, find your new Azure Databricks service, and `Launch Workspace`.
  * A new window will open in your browser. Select the *Clusters* icon, and click on the `+ Create Cluster` button to provision a new cluster. In the form that pops up, change the value of the `Python Version` dropdown menu to **3**. The remaining defaults values are acceptable.

## Azure Machine Learning

For the second solution you will also need an Azure Machine Learning Workspace. 
Sign into the Azure portal and click on `Create a resource`, start typing `Machine Learning` and select the Machine Learning suggeston. Follow the prompts, and if requested seleced the 'most standard pricing tier'. 

and select `Basic` Workspace edition. 


# Setup

## Link your Databricks Workspace with Azure ML Workspace

Go to your Azure Workspace, click `Link Azure ML Workpace` and follow the prompts.


<img src="https://docs.microsoft.com/en-us/azure/machine-learning/media/how-to-use-mlflow-azure-databricks/link-workspaces.png" width="500">

Once they are linked MLFlow recognized the fact that you want your metrics and runs to be logged both to your Databricks and Azure ML workspace.

## Import Notebooks

1) To import the notebooks navigate to [Notebooks](notebooks) and download each notebook.
2) Go to your Azure Portal, find your Databricks workspace and click `Launch Workspace`. You can see this in the image shown above. Once launched you should be able to view your Workspace. 
3) Click the **Workspace** button or the **Home** button in the sidebar. Next to any folder, click the down arrow icon on the right side of the text and select **Import**. 

<img src="https://docs.databricks.com/_images/import-notebook.png" width = "400"> 

4) Browse to the notebook you want to upload. 

5) Click **Import**

6) Repeat for each notebook. 

Instructions on how to use Jupyter notebooks are at https://docs.databricks.com/user-guide/notebooks/notebook-use.html#run-notebooks.

# Steps

To create the full example scenario, log into your Azure Databricks workspace and run through the following notebooks that are now located there.

These notebooks need to be run sequentially as the latter notebooks depends on data artifacts produced in the previous notebooks' run.

When running the notebooks, you may have to start your Azure Databricks cluster or attach these notebooks to your Azure Databricks cluster. The UI will prompt you if this is required.

  * [Ingest Data](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/notebooks/1_data_ingestion.ipynb). Open the `1_Data_Ingestion` notebook on the Azure Databricks workspace. You can either `Run All` cells, or execute cells individually. This notebook downloads the example data into your Azure Databricks Data storage.
  
  * Model Building and Training:
  
    [Solution 1](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/notebooks/3_Model_Building_And_Training.ipynb) Open the `3_Model_building_And_Training` notebook on the Azure Databricks Workspace. You can either `Run All` cells, or execute cells individually. This notebook will run one external notebook—2_Feature_Engineering to create training and testing data sets which are then written to the Azure Databricks Data store. The rest of the notebook builds and tests a Random Forest model.
    
    [Solution 2](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/notebooks/3_Model_Building_And_Training.ipynb) Open the `3_AzureML_Model_Building_And_Training` notebook on the Azure Databricks Workspace. For this notebook you will need to execute each cell individually. This notebook will run one external notebook—2_Feature_Engineering to create training and testing data sets which are then written to the Azure Databricks Data store. The rest of the notebook creates an experiment to build, train and test a machine learning model using the open source MLFlow API to track. This enables the user to track the experiment such the model's performance metrics on both Azure ML and Databricks' workspace. This notebook also details how to regsiter the model on Azure ML and create a container image for the trained model. `Run All` cells will not work on this notebook as users will need to populate `cell 8` with their workspace details and then follow the instructions provided for authentication. 
   
 * [Data Scoring Pipeline](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/notebooks/4_Scoring_Pipeline.ipynb). Open the 4_Scoring_Pipeline notebook    on the Azure Databricks workspace. You can either Run All cells, or execute cells individually. The main scoring`4_Scoring_Pipeline` notebook runs two external notebooks:
 
   i. [2_Feature_Engineering](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/notebooks/2_Feature_Engineering.ipynb) creates a scoring data set with 2 different input parameters than in the training dataset. The scoring data is written to the Azure Databricks Data        store.

   ii. [4a_Model_Scoring](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/notebooks/4a_Model_Scoring.ipynb) will score the data with the machine learning model created with `3_Model_Building_And_training`. The results data is written to the Azure Databricks   Data    store. Once this notebook is run, you can optionally examine the scored results data with the`4b_Model_Scoring_Evaluation` notebook.
   
  
  
  ## Conclusion 
  
  This use case demonstrates how to build and train a machine learning model on Azure Databricks for two different purposes. The first approach builds the entire solution on Azure Databricks and demosntrated how to build a model on Databricks, save it on DBFS and use to predict the state of the machine. The second approach shows how to build the same model using the MLFlow API so that the model's performance can be tracked both on the Databricks workspace as well as on Azure ML workspace. This approach also demonstrates how to register a model and create a Container Image to deploy the trained model. Registering a model is useful if you want to create and compare the perfomance of various models. 
  
 ## Cleaning up
The easiest way to cleanup this work is to delete the resource group containing the Azure Databricks instance.

Through the Azure portal (https://portal.azure.com) search for databricks.
Open your Azure Databricks service and select the *Resource Group* link.
Delete resource group will remove the Azure Databricks service and all associated resources including the notebooks and data artifacts used in this scenario.

