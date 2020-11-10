# Create a CI/CD process with Azure DevOps

## End Goal Scenario

1) Commit your notebooks to Azure DevOps (Master branch of the repo).
2) Once this commit is successful, the notebooks will automatically be deployed into PROD workspace.
![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/ProdNote.PNG)


## Prerequisites
Before compelting the steps detailed befor you need to have created your Databricks Workspace and uploaded your notebooks as detailed [here](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/README.md); this will be your **DEV** Azure Databricks Workspace. 

## Create the required resources

To deploy your notebooks to a production (**PROD**) Azure Databricks Workspace, you will need to deploy two Azure Databricks workspaces in your Azure subscription. You have already created your DEV workspace when you built your predicive maintenance model.

### Deploy a PROD Azure Databricks Workspace

1. Click the following button to open the Azure Resource Manager template in the Azure portal. [Deploy Databricks from the Azure Resource Manager Template](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-databricks-workspace%2Fazuredeploy.json)

2. Provide the required values to create your Azure Databricks workspace:

* **Subscription:** Choose the Azure Subscription in which to deploy the workspace; this should be the same subscription you have used for your DEV workspace. 
* **Resource Group:** Leave at Create new and provide a name for the new resource group; this can also be the same resource group you have used for your DEV workspace.
* **Location:** Select a location near you for deployment. For the list of regions supported by Azure Databricks, see Azure services available by region; but you can use the same location used for your DEV workspace. 
* **Workspace Name:** Provide a name for your workspace that includes the word PROD in the name.
* **Pricing Tier:** Ensure premium is selected.

3. Accept the terms and conditions.

4. Select Purchase.

The workspace creation takes a few minutes. During workspace creation, the portal displays the Submitting deployment for Azure Databricks tile on the right side. You may need to scroll right on your dashboard to see the tile. There is also a progress bar displayed near the top of the screen. You can watch either area for progress.


### Create a Cluster

**OPTIONAL:** Creating a cluster is optional—it is only needed if you plan on running the sample notebook. The notebook is only used for demonstration purposes of the CI/CD process.

1. When your Azure Databricks **PROD** workspace creation is complete, select the link to go to the resource.

2. Select **Launch Workspace** to open your Databricks workspace in a new tab.

3. In the left-hand menu of your Databricks workspace, select **Clusters**.

4. Select **Create Cluster** to add a new cluster.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/CreateCluster.PNG)

5. Enter a name for your cluster. Use your name or initials to easily differentiate your cluster from your coworkers.

6. Select the Databricks RuntimeVersion. We recommend the latest runtime and Scala.

7. Select the default values for the cluster configuration.

8. Select Create Cluster.







