![Microsoft Cloud Workshops](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/master/Media/ms-cloud-workshop.png "Microsoft Cloud Workshops")

<div class="MCWHeader1">
Machine Learning
</div>

<div class="MCWHeader2">
Hands-on lab step-by-step
</div>

<div class="MCWHeader3">
June 2019
</div>


Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.

© 2019 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at <https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx> are trademarks of the Microsoft group of companies. All other trademarks are property of their respective owners.

**Contents** 

<!-- TOC -->

- [Machine Learning hands-on lab step-by-step](#machine-learning-hands-on-lab-step-by-step)
  - [Abstract and learning objectives](#abstract-and-learning-objectives)
  - [Overview](#overview)
  - [Solution architecture](#solution-architecture)
  - [Requirements](#requirements)
  - [Before the hands-on lab](#before-the-hands-on-lab)
  - [Exercise 1: Creating a forecast model using automated machine learning](#exercise-1-creating-a-forecast-model-using-automated-machine-learning)
    - [Task 1: Create an automated machine learning experiment](#task-1-create-an-automated-machine-learning-experiment)
    - [Task 2: Review the experiment run results](#task-2-review-the-experiment-run-results)
    - [Task 3: Deploy the Best Model](#task-3-deploy-the-best-model)
  - [Exercise 2: Understanding the automated ML generated forecast model using model explainability](#exercise-2-understanding-the-automated-ml-generated-forecast-model-using-model-explainability)
    - [Task 1: Explore the model using a notebook](#task-1-explore-the-model-using-a-notebook)
  - [Exercise 3: Creating a deep learning model (RNN) for time series data and registering the model](#exercise-3-creating-a-deep-learning-model-rnn-for-time-series-data-and-registering-the-model)
    - [Task 1: Create the model using a notebook](#task-1-create-the-model-using-a-notebook)
  - [Exercise 4: Using a forecast model for scoring of streaming telemetry](#exercise-4-using-a-forecast-model-for-scoring-of-streaming-telemetry)
    - [Task 1: Create the streaming job using a notebook](#task-1-create-the-streaming-job-using-a-notebook)
  - [Exercise 5: Creating a deep learning text classification model](#exercise-5-creating-a-deep-learning-text-classification-model)
    - [Task 1: Create the classification model using a notebook](#task-1-create-the-classification-model-using-a-notebook)
  - [After the hands-on lab](#after-the-hands-on-lab)
    - [Task 1: Clean up lab resources](#task-1-clean-up-lab-resources)

<!-- /TOC -->

# Machine Learning hands-on lab step-by-step 

## Abstract and learning objectives 

In this hands-on lab, you will use Azure Databricks in combination with Azure Machine Learning service to build, train and deploy desired models. You will learn how to train a forecasting model against time-series data, without any code, by using automated machine learning, and how to score data in real-time using Spark Structure Streaming within Azure Databricks.  You will create a recurrent neural network (RNN) model using PyTorch in Azure Databricks that can be used to forecast against time-series data and train a Natural Language Processing (NLP) text classification model using Keras.

At the end of this lab, you will be better able to build solutions leveraging the Azure Machine Learning service and Azure Databricks.

## Overview

Trey Research Inc. delivers innovative solutions for manufacturers. They specialize in identifying and solving problems for manufacturers that can run the range from automating away mundane but time-intensive processes to delivering cutting edge approaches that provide new opportunities for their manufacturing clients. 

Trey Research is looking to provide the next generation experience for connected car manufacturers by enabling them to utilize AI to decide when to pro-actively reach out to the customer thru alerts delivered directly to the car's in-dash information and entertainment head unit. For their proof of concept (PoC), they would like to focus on two maintenance related scenarios.

In the first scenario, Trey Research recently instituted new regulations defining what parts are compliant or out of compliance. Rather than rely on their technicians to assess compliance, they would like to automatically assess the compliance based on component notes already entered by authorized technicians. Specifically, they are looking to leverage Deep Learning technologies with Natural Language Processing techniques to scan through vehicle specification documents to find compliance issues with new regulations. Then each car is evaluated for out compliance components. 

In the second scenario, Trey Research would like to predict the likelihood of battery failure based on the telemetry stream of time series data that the car provides about how the battery performs when the car is started, how it is charging while running and how well it is holding its charge, among other factors. If they detect a battery failure is imminent within the next 30 days, they would like to send an alert.

Upon detection of an out of compliance component or a battery at risk of failure, they would like to be able to send an alert directly to the customer inviting them to schedule a service appointment to replace the part. 

In building this PoC, Trey Research wants to understand how they might use machine learning or deep learning in both scenarios, and standardize the platform that would support the data processing, model management and inferencing aspects of each. 

They are also interested to learn what new capabilities Azure provides that might help them to document and explain the models that are created to non-data scientists or might accelerate their time to creating production ready, performant models. 

In this lab, you will use Azure Databricks in combination with Azure Machine Learning service to build, train and deploy the desired models. 

## Solution architecture

The following diagram summarizes the key components and processing steps in the lab.

![Overall solution diagram, described in the text that follows.](images/lab-architecture.png 'Solution Architecture')

In this lab, models are trained using both Azure Databricks (for deep learning with the PyTorch and Keras frameworks) and Azure Machine Learning compute (for automated machine learning using the user experience in the Azure Portal). Models are registered with the Azure Machine Learning Workspace. The data used for model training is read from Azure Storage. 

The scoring is performed using notebooks running within Azure notebooks, which show how to load and apply the respective models against the data provided. 

## Requirements

1.  Microsoft Azure subscription must be pay-as-you-go or MSDN

    - Trial subscriptions will not work. You will run into issues with Azure resource quota limits.

    - Subscriptions with access limited to a single resource group will not work. You will need the ability to deploy multiple resource groups.

## Before the hands-on lab

Refer to the Before the hands-on lab setup guide manual before continuing to the lab exercises.

## Exercise 1: Creating a forecast model using automated machine learning

Duration: 30 minutes

In this exercise, you will create a model that predicts battery failure from time-series data using the visual interface to automated machine learning in an Azure Machine Learning workspace. 

### Task 1: Create an automated machine learning experiment

1. Navigate to your Azure Machine Learning workspace in the Azure Portal.

2. Select `Automated machine learning` in the left navigation bar.

3. Select  **Create Experiment**.
   
   ![Automated machine learning experiment section in Azure Portal. The image highlights the "Create Experiment" button.](./images/02_CreateExperiment.png 'Create Experiment')

4. Provide the experiment name: `Battery-Cycles` and select **Create a new compute**.
   
   ![The experiment name has "Battery-Cycles" entered into it and "Create a new compute" button is highlighted.](./images/03_NewExperiment_1.png 'Create New Experiment')


5. For the new compute, provide the following values and then select **Create**:
   - Compute name: `auto-ml-compute`
   - VM size: `STANDARD_DS11_V2`
   - Select `Additional Settings` and set Minimum number of nodes and Maximum number of nodes to `1`.
   
      ![Create a New Compute dialog shows the values for various fields, such as Compute name.](./images/04_CreateNewCompute.png 'Create a New Compute')

6. Wait for the Compute to be ready and select **Next**. Now you will upload the training data. Start by downloading the data from https://databricksdemostore.blob.core.windows.net/data/connected-car/daily-battery-time-series.csv. Please ensure that the extension of the downloaded file is **csv**. Select **Upload** and then in the dialog that appears choose the file `daily-battery-time-series.csv` from your local disk.
   
   ![The image highlights the Upload button to start uploading the training data from your local computer.](./images/05_UploadDataFile.png 'Uploading training data')

7. Select **daily-battery-time-series.csv** and review the training data. Be sure to scroll to the right to observe the target column `Daily_Cycles_Used`. Toggle the switch above the following columns so the header reads `Ignored` for the first unlabeled column, `number_of_trips`, `lifetime_cycles_used` and `battery_rated_cycles` columns.
   
   ![The data preview section shows both selected and ignored features from the uploaded dataset.](./images/06_ReviewDataFile.png 'Select Features')

8. Now, you will setup the Auto ML Experiment Basic Settings by providing the following values:

   - Prediction Task: Select **Forecasting**
   - Target column: Select **Daily_Cycles_Used**
   - Time column: Select **Date**
   - Series name column(s): **Battery_ID**
   - Forecast Horizon: Enter `30`. This refers to forecasting out up to 30 days. 

      ![The Auto ML experiment basic settings dialog shows values for various fields such as "Prediction Task", "Target column", etc.](./images/07_SetupExp_1.png 'Configure Experiment - Basic Settings')

9. Select **Advanced Settings** to expand that area. Provide the following settings:

   - Primary metric: **normalized_root-mean_squared_error**
   - Max number of iterations: **10**
   - Number of Cross Validations: **5**
   - Max concurrent iterations: **1**
   
     ![The Auto ML experiment advanced settings dialog shows values for various fields such as "Primary metric", "Max number of iterations", etc.](./images/08_SetupExp_2.png 'Configure Experiment - Advanced Settings')

10. Select **Start** to run the experiment and begin automated machine learning process.
    
### Task 2: Review the experiment run results

1. The experiment will run for about *5-10 minutes*. Once it completes you should examine the chart to observe the model performance for the primary metric for different iterations. 
   
   ![Run details shows Primary Metric verses Iterations graph.](./images/09_ReviewRunDetails_1.png 'Run Details - Graph View')

2. Scroll down to see a table view of different iterations and select the iteration with the best **normalized root mean square error** score. Note that the normalized root mean square error measures the error between the predicted value and actual value. In this case, the model with the lowest normalized root mean square error is the best model. Note that Azure ML Python SDK updates over time and gives you the best performing model at time you run the experiment. Thus, it is possible that the best model you observe can be different than the one shown below.
   
   ![Run details shows a table view of Iteration details and corresponding Primary Metric values.](./images/010_ReviewRunDetails_2.png 'Run Details - Table View')


### Task 3: Deploy the Best Model
1. Scroll below the `ITERATIONS` table and select **Deploy Best Model** as shown.
   
   ![The "Deploy Best Model" button is highlighted.](./images/0141_DeployBestModel.png 'Deploy Best Model Button')
   
2. Provide the `Deployment name`, and `Deployment description`, and then select **Deploy** as shown:

   - Deployment name: **battery-cycles**
   - Deployment description: **Deploying best AutoML model to predict battery cycles.**
   
   ![The Deploy Best Model dialog that shows values for the two fields, deployment name and deployment description.](./images/0142_DeployBestModel.png 'Deploy Best Model Dialog')

3. The model deployment, will register the model, create the deployment image, and deploy it as a scoring webservice in an Azure Container Instance (ACI). To view the deployed model, from the Azure Machine Learning workspace select **Deployments**.
   
   ![Viewing the list of deployed models in the Azure Machine Learning workspace](images/0143_DeployBestModel.png 'Deployed Models')

4. If you see your model deployed in the above list, you are now ready to continue on to the next exercise.

## Exercise 2: Understanding the automated ML generated forecast model using model explainability 

Duration: 15 minutes

### Task 1: Explore the model using a notebook

1. Browse to your Azure Databricks Workspace and open `AI with Databricks and AML \ Model Explainability`. This is the notebook you will step thru executing in this lab.

2. Follow the instructions within the notebook to complete the lab.
3. Note that because of version differences between the local environment of the cluster and the model training environment sometimes you may see warning messages when you first load the best model from the AutoML Run. You should be able to ignore the warning messages and successfully complete the lab. 
4. Note that in this notebook, and the following notebooks, we are using the default storage account in Azure Databricks. However, the best practice in enterprises is to use a shared storage account that you mount to your Azure Databricks. Also, you can use [Secrets in Azure Key Vaults](https://docs.azuredatabricks.net/user-guide/secrets/secrets.html) that can be read by the notebooks to save sensitive data, such as your subscription ID.

## Exercise 3: Creating a deep learning model (RNN) for time series data and registering the model

Duration: 45 minutes

### Task 1: Create the model using a notebook

1. Browse to your Azure Databricks Workspace and open `AI with Databricks and AML \ Deep Learning with Time Series`. This is the notebook you will step thru executing in this lab.

2. Follow the instructions within the notebook to complete the lab.

## Exercise 4: Using a forecast model for scoring of streaming telemetry

Duration: 45 minutes

In this exercise, you will apply the forecast model to a Spark streaming job in order to make predictions against streaming data.

### Task 1: Create the streaming job using a notebook

1. Browse to your Azure Databricks Workspace and navigate to `AI with Databricks and AML \ Stream Scoring`. This is the notebook you will step thru executing in this lab.

2. Follow the instructions within the notebook to complete the lab.

## Exercise 5: Creating a deep learning text classification model

Duration: 45 minutes

In this exercise, you create a model for classifying component text as compliant or non-compliant.

### Task 1: Create the classification model using a notebook

1. Browse to your Azure Databricks Workspace and navigate to `AI with Databricks and AML \ Deep Learning with Text`. This is the notebook you will step thru executing in this lab.

2. Follow the instructions within the notebook to complete the lab.

## After the hands-on lab 

Duration: 5 minutes

To avoid unexpected charges, it is recommended that you clean up all of your lab resources when you complete the lab.

### Task 1: Clean up lab resources

1.  Navigate to the Azure Portal and locate the `MCW-AI-Lab` Resource Group you created for this lab.

2.  Select **Delete resource group** from the command bar.

    ![Screenshot of the Delete resource group button.](images/image71.png 'Delete resource group button')

3.  In the confirmation dialog that appears, enter the name of the resource group and select **Delete**.

4.  Wait for the confirmation that the Resource Group has been successfully deleted. If you don't wait, and the delete fails for some reason, you may be left with resources running that were not expected. You can monitor using the Notifications dialog, which is accessible from the Alarm icon.

    ![The Notifications dialog box has a message stating that the resource group is being deleted.](images/image72.png 'Notifications dialog box')

5.  When the Notification indicates success, the cleanup is complete.

    ![The Notifications dialog box has a message stating that the resource group has been deleted.](images/image73.png 'Notifications dialog box')

You should follow all steps provided _after_ attending the Hands-on lab.

