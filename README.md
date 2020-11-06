# Batch scoring of Spark machine learning models 

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

One solution only uses the Azure Databricks service, whilst the latter which details how to deploy the model as a web service uses both databricks and AMLS. The first solution is comprised of 4 notebooks, whilst the second consists of 3. 

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

  * [Ingest Data](https://github.com/Azure/BatchSparkScoringPredictiveMaintenance/blob/master/notebooks/1_data_ingestion.ipynb). Open the `1_data_ingestion` notebook on the Azure Databricks workspace. You can either `Run All` cells, or execute cells individually. This notebook downloads the example data into your Azure Databricks Data storage.
  * [Model Training Pipeline](https://github.com/Azure/BatchSparkScoringPredictiveMaintenance/blob/master/notebooks/2_Training_Pipeline.ipynb) Open the `2_Training_Pipeline` notebook on the Azure Databricks workspace. You can either `Run All` cells, or execute cells individually. This notebook will run two external notebooks. 
      1. Create a training data set with `2a_feature_engineering`. The training data is written to the Azure Databricks Data store. Once this notebook is run, you can optionally examine the data created with the `2a_feature_exploration` notebook.
      2. Create a machine learning model with `2b_model_building`. Once this notebook is run, you can optionally examine the model with the `2b_model_testing` notebook with other data created with the `2a_feature_engineering` notebook. The model is stored on the Azure Databricks file system in parquet format.

  * [Data Scoring Pipeline](https://github.com/Azure/BatchSparkScoringPredictiveMaintenance/blob/master/notebooks/3_Scoring_Pipeline.ipynb) Open the `3_Scoring_Pipeline` notebook on the Azure Databricks workspace. You can either `Run All` cells, or execute cells individually. This notebook will also run two external notebooks. 

    1. Create a scoring data set with `2a_feature_engineering` with different input parameters than in the training dataset. The scoring data is written to the Azure Databricks Data store. Once this notebook is run, you can again optionally examine the data created with the `2a_feature_exploration` notebook. 
    2. `3a_model_scoring` will score the data with the machine learning model created with `2b_model_building`. The results data is written to the Azure Databricks Data store. Once this notebook is run, you can optionally examine the scored results data with the `3a_model_scoring_evaluation` notebook.
  * (optional) Instructions to create the batch scoring Databricks Job using the Databricks CLI are documented at https://github.com/Azure/BatchSparkScoringPredictiveMaintenance/blob/master/BatchScoringJob.md.

The scenario is constructed as a pipeline flow, where each notebook is optimized to perform in a batch setting for each of the ingest, feature engineering, model building, model scoring operations. To accomplish this, the feature engineering notebook is designed to be used to generate a general data set for any of the training, calibrate, test or scoring operations. In this scenario, we use a temporal split strategy for these operations, so the notebook parameters are used to set date range filtering.

# Conclusion

This scenario demonstrates how to automate the batch scoring of a predictive maintenance solution. The actual work of the "batch scoring a spark model" scenario is done through an Azure Databricks job. The job executes the `3_Scoring_Pipeline` notebook, which depends on a machine learning model existing on the Azure Databricks file storage. We created the model using the `2_Training_Pipeline` notebook which used the data downloaded with the `1_data_ingestion` notebook.

# Cleaning up

The easiest way to cleanup this work is to delete the resource group containing the Azure Databricks instance.

  1. Through the Azure portal (https://portal.azure.com) search for `databricks`.
  1. Open your Azure Databricks service and select the *Resource Group* link.
  1. *Delete resource group*  will remove the Azure Databricks service and all associated resources including the notebooks and data artifacts used in this scenario.

You may also want to remove the Databricks CLI from your python environment with

```{shell}
pip uninstall databricks-cli
```

# References

This scenario has been developed using a similar predictive maintenance use case published at following reference locations:

 * [Predictive Maintenance Solution Template](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance)
 * [Predictive Maintenance Modeling Guide](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1)
 * [Predictive Maintenance Modeling Guide using SQL R Services](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
 * [Predictive Maintenance Modeling Guide Python Notebook](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
 * [Predictive Maintenance using PySpark](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-using-PySpark)

We have also built an additional reference architecture leveraging spark for building real-time recommendation systems with off-line, pre-computed scores. These recommendation systems are common scenarios where scores are batch-processed. Relevant information about this related architecture is available here:

* [Build a real-time recommendation API on Azure](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/ai/real-time-recommendation)
* [Technical details for recommendation systems](https://github.com/Microsoft/Recommenders)

# Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us the rights to use your contribution. For details, visit https://cla.microsoft.com.

When you submit a pull request, a CLA-bot will automatically determine whether you need to provide a CLA and decorate the PR appropriately (e.g., label, comment). Simply follow the instructions provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

## Author

John Ehrlinger <john.ehrlinger@microsoft.com>
