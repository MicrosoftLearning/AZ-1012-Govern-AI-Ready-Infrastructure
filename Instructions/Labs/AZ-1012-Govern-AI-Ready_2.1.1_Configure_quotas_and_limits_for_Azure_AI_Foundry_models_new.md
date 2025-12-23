# AI Ready Governance hands-on exercise: Configure quotas and limits for Microsoft Foundry models

## Background information
Quotas and limits in Microsoft Foundry are inherently tied to model deployments, so understanding deployment options is essential to identifying the most suitable approach to optimizing utilization and billing of Microsoft Foundry services. In particular, Microsoft Foundry offers the following deployment options depending on the type of models and resources you want to provision:
- Standard deployment in Microsoft Foundry resources
- Deployment to serverless API endpoints
- Deployment to managed computes

### Standard deployment in Microsoft Foundry resources
Microsoft Foundry resources represent the preferred deployment option in Microsoft Foundry. It offers the widest range of capabilities, including regional, data zone, or global processing, and it offers standard and provisioned throughput (PTU) options. All flagship models in Microsoft Foundry support this deployment option. You can use it in combination with:
- Microsoft Foundry resources
- Azure OpenAI resources
- Azure AI hub, when connected to a Microsoft Foundry resource

As part of deployments, you set token utilization rate limits, up to a global limit determined by quotas. Quotas can be assigned to your subscription on a per-region, per-model basis in units of Tokens-per-Minute (TPM). When you onboard a subscription to Azure OpenAI, you'll receive default quota for most available models. Then, you'll assign TPM to each deployment as it is created, and the available quota for that model will be reduced by that amount. You can continue to create deployments and assign them TPM until you reach your quota limit. Once that happens, you can only create new deployments of that model by reducing the TPM assigned to other deployments of the same model (thus freeing TPM for use), or by requesting and being approved for a model quota increase in the desired region.

When a deployment is created, the assigned TPM will directly map to the tokens-per-minute rate limit enforced on its inferencing requests. A Requests-Per-Minute (RPM) rate limit will also be enforced. Its value is set proportionally to the TPM assignment using ratios published in [Introduction to quota](https://learn.microsoft.com/en-us/azure/ai-foundry/openai/how-to/quota?tabs=rest#introduction-to-quota).

Tokens per minute (TPM) and requests per minute (RPM) limits are defined per region, per subscription, and per model or deployment type, including standard, batch, and provisioned. The highest level of quota restrictions is scoped at the Azure subscription level. 

#### Azure OpenAI quotas and limits
There are also default quotas and limits that apply to Azure OpenAI, which define, for example, maximum number of Azure OpenAI resources per subscription and per region, maximum standard deployments per resource, or maximum fine-tuned model deployments. You can find more details at [Quotas and limits reference](https://learn.microsoft.com/en-us/azure/ai-foundry/openai/quotas-limits?tabs=REST#quotas-and-limits-reference).

### Serverless API endpoints
This deployment option is available only when using Azure AI hub resources. It allows you to create dedicated endpoints to host a model, accessible through an API. It is limited to a subset of Microsoft Foundry Models and it supports only regional deployments.

### Managed computes
This deployment option is also available only when using Azure AI hub resources. It allows you to create a dedicated endpoint to host the model in a dedicated compute. You need to have sufficient compute quota in your subscription to host the model and you're billed per compute uptime. Managed compute deployment is required for model collections that include:
- Hugging Face
- NVIDIA inference microservices (NIMs)
- Industry models (Saifr, Rockwell, Bayer, Cerence, Sight Machine, Page AI, SDAIA)
- Databricks
- Custom models

## Scenario
Your company is developing a global analytics platform that integrates conversational agents, batch summarization pipelines, and specialized models for tasks such as financial forecasting and scientific research. To balance performance with cost efficiency, your company decides to explore quota limits available for resource-based projects when using Microsoft Foundry for agent development.

## Prerequisites
- **Azure subscription**: If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.
- **Permissions**: To create Azure AI Services resources, you should have the Owner role assigned at the Azure subscription or the target resource group level.
- **Familiarity with Microsoft Foundry model deployment**: To learn more, refer to [Deploy Microsoft Foundry Models in the Foundry portal](https://learn.microsoft.com/en-us/azure/ai-foundry/foundry-models/how-to/deploy-foundry-models?view=foundry&preserve-view=true).

## Estimated duration
15 minutes

### Task 1: Create a Microsoft Foundry project

1. Start a web browser, navigate to the **All resources** page of the Azure AI Foundry portal at [https://ai.azure.com/allResources](https://ai.azure.com/allResources) and sign in by providing the credentials of a user account which has the Owner role assigned at the Azure subscription level.
1. On the **All resources** page of the Azure AI Foundry portal, select **Create new**.
1. On the **Create project** pane, ensure that the **Microsoft Foundry resource** option is selected and then select **Next**.
1. On the **Create a project** pane, expand the **Advanced options** section and perform the following tasks:

   - In the **Project** text box, enter an arbitrary project name (we will name it **foundry-quotas-project-new**).
   - In the **Subscription** drop-down list, select the subscription you are using in this exercise.
   - Select **Create new resource group** link, in the **Create new resource group** text box, enter **foundry-quotas-project-new-RG** and select **OK**.
   - Accept the default value of the **Microsoft Foundry resource** (**foundry-quotas-project-new-resource**).
   - In the **Region** drop-down list, select the Azure region in which you intend to provision the project resource.
   - Select **Create**.

   **Note**: Wait until the resource is provisioned. This might take about one minute. Once the project is created, the web browser should display the **Home** page of the Microsoft Foundry portal. If needed, turn the **New Foundry** switch at the top of the page to the **On** position and verify that the project name appears in the upper left corner of the page.




1. Start a web browser, navigate to the **All resources** page of the Microsoft Foundry portal at [https://ai.azure.com/allResources](https://ai.azure.com/allResources) and sign in by providing the credentials of a user account which has the Owner role assigned at the Azure subscription level.
1. On the **All resources** page of the Microsoft Foundry portal, select **Create new**.
1. On the **Create project** pane, ensure that the **Microsoft Foundry resource** option is selected and then select **Next**.
1. On the **Create a project** pane, expand the **Advanced options** section and perform the following tasks:

   - In the **Project** text box, enter an arbitrary project name (we will name it **foundry-quotas-project-new**).
   - In the **Subscription** drop-down list, select the subscription you are using in this exercise.
   - Select **Create new resource group** link, in the **Create new resource group** text box, enter **foundry-quotas-project-new-RG** and select **OK**.
   - Accept the default value of the **Microsoft Foundry resource** (**foundry-quotas-project-new-resource**).
   - In the **Region** drop-down list, select the Azure region in which you intend to provision the project resource.
   - Select **Create**.

   **Note**: Wait until the resource is provisioned. This might take about one minute. Once the project is created, the web browser should display the **Home** page of the Microsoft Foundry portal. If needed, turn the **New Foundry** switch at the top of the page to the **On** position and verify that the project name appears in the upper left corner of the page.

### Task 2: Deploy a model in the project

1. On the **Home** page of the Microsoft Foundry portal, in the upper right corner, select **Discover**.
1. On the **Discover** page of the Microsoft Foundry portal, in the vertical menu on the left side, select **Models**.
1. In the **Search** text box, enter **gpt-4.1-mini** and, in the list of results, select **gpt-4.1-mini**.

   **Note**: If the **gpt-4.1-mini** model is not available, select another GPT chat completion model

1. On the **gpt-4.1-mini** page, select **Deploy** and, in the drop-down menu, select **Default settings**.





### Task 2: Explore Microsoft Foundry hub-based quota settings

1. In the web browser displaying the Microsoft Foundry portal, on the **Overview** page of **foundry-quotas-project-new**, in the vertical menu on the left side, select **All resources**. 
1. On the **Manage your AI Foundry resources** pane, select **quotas-hub**.
1. On the **quotas-hub** pane, in the vertical menu on the left side, select **Compute**.
1. On the **Manage compute resources in this hub** pane, on the **Compute instances** tab, select **+ New**.
1. In the **Required settings** section of the **Create compute instance** pane, review the listing of the virtual machine sizes available for the deployment of managed computes. Note the **Available quota** column, which displays the number of cores available for the corresponding VM family. This considers the quota set for the VM family as well as the overall VM quota for your subscription. 
1. Select **Cancel** to return to the **Manage compute resources in this hub** pane without making any changes.
1. Back on the **Manage compute resources in this hub** pane, select the **Serverless instances** tab.
1. In all likelihood, the tab will not display any serverless instances unless you deployed them prior to this hands on exercise. The tab allows you to identify all serverless instances being used at this point in time, including those generated during prompt flow compute sessions.
1. In the vertical menu on the left side, select **Quota**.
1. On the **Monitor and track your quota usage** pane, select the **VM Quota** tab.
1. Ensure that the subscription you are using in this exercise is listed in the **Subscription** drop-down list and, in the **Region** drop-down list, select the Azure region where you want to examine VM quota limits.
1. Review the resulting listing of VM quotas for the selected subscription and region.
1. Select any of the virtual machine family entries in the listing and select the **Request quota** button. 
1. On the **Request quota** pane, note the option to specify the new cores limit.
1. Select **Cancel** to return to the **Monitor and track your quota usage** pane without making any changes.
1. Note the **Standard + batch**, **Provisioned throughput (classic)**, and **Provisioned throughput** tabs, which allow you to manage quotas for Standard deployments.

### Task 3: Explore Microsoft Foundry project-based quota settings

1. In the web browser displaying the Microsoft Foundry portal, on the **Monitor and track your quota usage** pane, in the vertical menu on the left side, select **All resources**. 
1. On the **Manage your AI Foundry resources** pane, select **foundry-quotas-project-new**.
1. On the project's resource properties page, in the vertical menu on the left side, select **Go to project**.
1. On the **foundry-quotas-project-new** page, in the vertical menu on the left side, select **Models + endpoints**.
1. On the **Model deployments** pane, select **+ Deploy model** and then, from the drop-down menu, select **Deploy base model**.
1. On the **Select a model** pane, select one of the available Azure OpenAI models (e.g. **gpt-5-mini**) and then select **Confirm**.
1. On the **Deploy gpt-5-mini** pane, perform the following tasks and then select **Deploy**.

   - Accept the default deployment name (**gpt-5-mini**)
   - Ensure that the deployment type is set to **Global Standard**
   - Expand the **Deployment details** section and note the **Tokens per Minute Rate Limit** slider, with the information about total quota available for your deployment directly above it.

   **Note**: Wait until the deployment is completed. This should take just a few seconds. Once the deployment is completed, the web browser should display the deployment's **Detail** page.

1. On the deployment's **Detail** page, select the **Request quota** button. This will open another web browser tab displaying the page **Microsoft Foundry Service: Request for Quota Increase**.
1. Review the content of the **Microsoft Foundry Service: Request for Quota Increase** page and close the newly opened web browser tab without making any changes.
1. Back on the web browser tab displaying the deployment's **Detail** page, in the vertical menu on the left side, select **Management center**. This should display the project's resource properties page.
1. On the project's resource properties page, in the vertical menu on the left side, select **Quota**.
1. Note that you are back on the **Monitor and track your quota usage** pane, but the **VM Quota** tab is no longer present.
1. On the **Monitor and track your quota usage** pane, with the **Standard + batch** tab selected, expand the **GlobalStandard** section and note that it includes the new deployment. 
1. Expand the deployment entry to review its details, including the current quota allocation and the link to the **Request quota** page you examined earlier in this task.
1. Select the pencil icon next to the current quota allocation entry to display the **Edit quota allocated to this deployment** pane.
1. On the **Edit quota allocated to this deployment** pane, select **Cancel** to return to the **Monitor and track your quota usage** pane without making any changes.
1. Back on the **Monitor and track your quota usage** pane, select the **Provisioned Throughput** tab.
1. Note that the tab includes the **Purchase a reservation** button via which you can initiate the procurement process and the **Capacity calculator** button via which you can calculate the estimated number of PTUs suitable for your workload. The **Provisioned Throughput** tab also lists the current PTU quotas.

### Task 4: Perform cleanup

1. Open another tab in the web browser displaying the Microsoft Foundry portal, navigate to the Azure portal at [https://portal.azure.com](https://portal.azure.com) and, if prompted, sign in by providing the same credentials you have been using throughout this exercise.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **foundry-quotas-project-new-RG** and, in the list of results, select **foundry-quotas-project-new-RG**.
1. On the **foundry-quotas-project-new-RG** page, select **Delete resource group**, on the **Delete a resource group** pane, in the **Enter resource group name to confirm deletion** text box, enter **foundry-quotas-project-new-RG**, select **Delete**, and, when prompted for confirmation, select **Delete** again.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **quotas-hub-RG** and, in the list of results, select **quotas-hub-RG**.
1. On the **quotas-hub-RG** page, select **Delete resource group**, on the **Delete a resource group** pane, in the **Enter resource group name to confirm deletion** text box, enter **quotas-hub-RG**, select **Delete**, and, when prompted for confirmation, select **Delete** again.