# Create a CI/CD process with Azure DevOps

## End Goal Scenario

1) Commit your notebooks to Azure DevOps (Master branch of the repo).
2) Once this commit is successful, the notebooks will automatically be deployed into a production (**PROD**) workspace.
![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/ProdNote.PNG)


## Prerequisites
Before compelting the steps below to deploy your notebooks into PROD you need to have created your Databricks Workspace and uploaded your notebooks as detailed [here](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/README.md)—this will be your development (**DEV**) Azure Databricks Workspace. 

## Create the required resources

To deploy your notebooks to a PROD Azure Databricks Workspace, you will need to deploy two Azure Databricks workspaces in your Azure subscription. You have already created your DEV workspace when you built your predictive maintenance model.

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

1. When your Azure Databricks PROD workspace creation is complete, select the link to go to the resource.

2. Select **Launch Workspace** to open your Databricks workspace in a new tab.

3. In the left-hand menu of your Databricks workspace, select **Clusters**.

4. Select **Create Cluster** to add a new cluster.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/CreateCluster.PNG)

5. Enter a name for your cluster. Use your name or initials to easily differentiate your cluster from your coworkers.

6. Select the Databricks RuntimeVersion. We recommend the latest runtime and Scala.

7. Select the default values for the cluster configuration.

8. Select Create Cluster.

### High Level Steps

At a high level, setting up CI/CD on Azure Databricks with Azure DevOps consists of 4 steps:

1. Setting up Azure DevOps Repo
2. Have your Azure Workspace and notebook configured to use Azure DevOps
3. Azure DevOps - Create a build pipeline (CI)
4. Azure DevOps - Create a release pipeline (CD)


### Step 1: Set up Azure DevOps Repo

1. Go to https://aex.dev.azure.com. Make sure that you are logged in under the correct account.

2. If your account has access to multiple Azure subscriptions, make sure that you're in the same directory as your Databricks workspaces.

3. Create a project.

4. Complete the new project form by providing a unique project name. Set visibility to **Private**, expand the Advanced section on the bottom, then select **Git** for version control. The work item process can remain at its default setting.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/NewProject.PNG)

5. Once Project is created, go to **Repos**, then **initialize** your repo. 

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/InitializeRepo.PNG)

### Step 2: Set up Azure Databricks Workspace and your notebook

Go to your DEV Azure Databricks workspace and setup your Git integration provider to Azure DevOps Services by following these steps:

1. Select the user icon on the top-right of the workspace, then select **User Settings.**


![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/UserSettings.PNG)

2. Select the **Git Integration** tab. Make sertin the Git provider is set to **Azure DevOps Services**.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/GitIntegration.PNG)


3. Navigate to the **Notebooks** folder containing all the notebooks used for the predictve maintenance in your Workspace. 

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/NotebooksDir.PNG)

4. Click the first notebook—`1_Data_Ingestion`, select **Revision history** on the right-hand side above the notebook.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/RevisionHistory.PNG)

5. Select **Git:Not linked** at the top of the revision history. This opens a Git configuration dialog. 

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/NotLinked.PNG)

6. In Git Preferences, use the URL scheme https://dev.azure.com/<myOrg>/<myProject>/_git/<myRepo> in the Link field to link Azure DevOps and Azure Databricks to the same Azure AD tenant.
  
![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/GitPreferences.PNG)

If your Azure DevOps organization is `org.visualstudio.com`, open `dev.azure.com` in your browser and navigate to your repository. Copy the URL from the browser and paste that URL in the Link field.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/FormatURL.PNG)

7. Select **Save** to finish linking your notebook. You should see that the GIT repository is now synced. 
![](https://docs.microsoft.com/en-us/learn/wwl-data-ai/implement-ci-cd-azure-devops/media/git-synced.png)

8. To test Close the Revision History sidebar, then scroll down to the bottom of the notebook. Add a new cell and add a comment such as `# Hello World"

9. Open the Revision History sidebar once again. When prompted to Save Notebook Revision, enter a revision description, then **Save**.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/SaveNotebookRevision.PNG)

10. Repeat steps 4-7 for each notebook you want to deploy to your **PROD** workspace.

11. Go back to your repo in Azure DevOps and refresh the files list. You should see your notebook in the repo. If you look under Commits, you should see your commit message as well. 

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/notebookRepo.PNG)


### Step 3: Retrieve Access token from PROD workspace

Go to PROD Azure Databricks workspace and generate a user access token for the Azure DevOps release pipeline you will create later, by following these steps:

1. Navigate to your production (PROD) Azure Databricks workspace.

2. When you launch and sign in to the workspace, take note of the URL. It is in the form of `https://<location>.azuredatabricks.net`. Copy the location portion of the URL (up to `.net`) and save it to a text editor for later reference.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/location.png)

3. Select the user icon on the top-right of the workspace, then select User Settings.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/usersetting.png)

4. Under the Access Tokens tab, select **Generate New Token**. In the Generate New Token dialog, add `Azure DevOps` for the Comment, then select **Generate**.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/GenerateNewToke.png)

5. Copy the new token and save it to a text editor for later reference. This is only displayed once.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/NewToken.png)

### Step 4: Azure DevOps - Create a Build Pipeline (CI)

A Build pipeline provides the **CI** portion of CI/CD.

1. Navigate back to Azure DevOps. Within your project, expand **Pipelines** in the left-hand menu, then select **Pipelines**. Select **Create Pipeline**.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/BuildPipeline.PNG)

2. Select **Use the classic editor** link under "Where is your code?"

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/youCode.png)

3. Select your project, repository, and the `master` branch for manual and scheduled builds, then select **Continue**.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/Source.PNG)

4. Under Select a template, select the start with an **Empty job** link.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/Template.PNG)

5. Select the + link on `Agent job 1` to add a task.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/AddTask.PNG)


6. Search for the **"Publish Build Artifacts"** task and add it to a Agent job.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/BuildArtifact.PNG)

7. Select added task, enter `notebooks` for the **Path to publish** and enter `DEV build` for the **Artifact name**.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/ArtifactName.PNG)

8. Select the **Triggers** tab and check **Enable continuous integration**. This will automatically trigger a build whenever you commit your code to the repo.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/Trigger.PNG)

9. Select **Save & queue** to continue.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/SaveAndQueue.PNG)

10. In the Run pipeline dialog that appears, enter a save comment, then select **Save** and **run**.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/InitialSetup.PNG)

11. Verify that your build pipeline was created and successfully run.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/VerifyBuild.PNG)


### Step 5: Azure DevOps- Create a release pipeline (CD)

A release pipeline provides the **CD** portion of CI/CD.

1. Within your Azure DevOps project, expand **Pipelines** in the left-hand menu, then select **Releases**. Select **New pipeline**.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/ReleasePipeline.PNG)

2. As you did when creating the previous pipeline, select the start with an **Empty job** link.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/EmptyJob.PNG)

3. Select **Add an artifact**. Set the source type to **Build**, then select your build pipeline you created in the previous step as the **Source**. Select **Add** to apply your changes.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/AddArtificat.PNG)

4. View the tasks for Stage 1 by selecting the `1 job, 0 task` link.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/SelectJobLink.PNG)

5. Select the + link on `Agent job` to add a task. Search "Databricks", then add **Databricks Deploy Notebooks**.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/AddDatabricksTask.PNG)

6.Once the task is added, select it and then fill the required parameters, then **Save:**

* **Azure Region**: Enter the region of your production (PROD) Azure Databricks workspace that you obtained from the URL in a previous step.
* **Source files path:** Browse to and select the **Users** subfolder.
* **Target files path:** Enter `/Users.`
* **Databricks bearer token:** Paste the Azure Databricks Access Key you copied in step 3.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/DatabricksToken.PNG)

7. Select the **Pipeline** tab. Select the **Continuous deployment trigger** on the artifact, then **Enable** the continuous deployment trigger. This will create a release every time a new build is available.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/EnableDeploymentTrigger.PNG)

8. Select **Save** to save your pipeline changes.

9. Finally, create a release by selecting **Create release** at the top of the pipeline blade. When the `Create a new release form` displays, select **Create**.

![](https://docs.microsoft.com/en-us/learn/wwl-data-ai/implement-ci-cd-azure-devops/media/create-release.png)

10. Navigate back to **Releases** under the Pipelines section of the left-hand menu. Select the release you just created. When it opens, you should see that it is either in progress or completed.

![](https://github.com/felicity-borg/BatchSparkScoringPredictiveMaintenance/blob/master/images/ReleaseProgress.PNG)

11. Navigate back to your production (PROD) Azure Databricks workspace. If it is already open, refresh the page. Navigate to your user folder under the workspace. You should see your notebook. This was saved to your workspace by your release pipeline.


CI/CD setup is now completed. If you commit your code from the DEV workspace to the repo (master branch), the same notebook should be available in PROD.

Experiment with making changes to your notebook in DEV, then committing those changes. You will be able to see your build and release pipelines execute and the notebook in the PROD workspace automatically update to reflect those changes.
