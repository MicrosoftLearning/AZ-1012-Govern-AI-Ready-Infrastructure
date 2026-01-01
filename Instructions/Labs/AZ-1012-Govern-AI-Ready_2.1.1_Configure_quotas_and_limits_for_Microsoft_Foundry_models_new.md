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
Your company plans to develop a global analytics platform that will integrate conversational agents and batch inference workflows for tasks such as financial forecasting and scientific research. The current focus is on exploring quotas, limits, and capacity allocation in Microsoft Foundry to understand how resources can be managed and scaled effectively. This exploration will provide insights to guide decisions about platform structure, regional rollout, and cost management as the platform evolves toward broader adoption.

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
   - In the **Region** drop-down list, select the Azure region in which you intend to provision the **Foundry** resource and the corresponding project.
   - Select **Create**.

   **Note**: Wait until the resource and its default project are provisioned. This might take about one minute. Once the project is created, the web browser should display its **Home** page in the Microsoft Foundry portal. If needed, turn the **New Foundry** switch at the top of the page to the **On** position and verify that the project name appears in the upper left corner of the page.

### Task 2: Deploy a model in the project

1. On the project's **Home** page in the Microsoft Foundry portal, in the upper right corner, select **Discover**.
1. On the project's **Discover** page in the Microsoft Foundry portal, in the vertical menu on the left side, select **Models**.
1. In the **Search** text box, enter **gpt-4.1-mini** and, in the list of results, select **gpt-4.1-mini**.

   **Note**: If the **gpt-4.1-mini** model is not available, select another GPT chat completion model

1. On the **gpt-4.1-mini** page, select **Deploy** and, in the drop-down menu, select **Custom settings**.
1. On the **gpt-4.1-mini** pane, perform the following tasks:

   - Accept the default deployment name (**gpt-4.1-mini**)
   - Ensure that the deployment type is set to **Global Standard**
   - Identify the default value of **Tokens per Minute Rate Limit** and note that you can modify it by moving the horizontal slider (up to the quota allocation threshold displayed on the right side of the slider).

   **Note**: For Azure OpenAI models, tokens per minute (TPM) and requests per minute (RPM) limits are defined per region, per subscription, and per model or deployment type.

   - Accept the default **Guardrails** setting (**DefaultV2**)
   - Select **Deploy**.

   **Note**: Wait until the deployment is completed. This should take just a few seconds. Once the deployment is completed, the web browser should display the **gpt-4.1-mini** deployment page.

### Task 3: Explore options for requesting quota increase

1. On the **gpt-4.1-mini** deployment page, select the **Detail** tab and identify the values of the **Tokens per minute Rate Limit** and **Requests per Minute Rate Limit**.
1. Select the **Edit** button in the upper left corner of the page, on the **Update gpt-4.1-mini deployment** pane, note that you can adjust the **Tokens per Minute Rate Limit**, and select **Cancel** without making any changes.
1. Select the **Request quota** button in the upper left corner of the page. 

   **Note**: This will open another tab in the web browser window and display the **Azure AI Foundry Service: Request for Quota Increase** page.

1. Review the information required to complete the **Azure AI Foundry Service: Request for Quota Increase** page and close the newly opened tab.

### Task 4: Explore shared and provisioned quota allocation

1. Back on the **gpt-4.1-mini** deployment page, in the opper right corner, select **Operate** and, in the vertical menu on the left side, select **Quota**.
1. On the **Quota** page, on the **Token per minute** tab, review the listing of the model deployments. Each entry in the listing includes the model name, deployment name, the target Azure region, deployment type, shared allocation, rate limiting, and deployment allocation.

   **Note**: Foundry provides a pool of shared quota (shared allocation) that different deployments can use concurrently. Depending on availability, a deployment can temporarily access quota from the shared pool and use the quota to perform testing for a limited amount of time. The specific time duration depends on the use case. By temporarily using quota from the quota pool, you do not necessarily need to submit a request for a quota increase or wait for your quota request to be approved before you can proceed with running your workloads. You can use the shared quota pool for testing inferencing for Foundry Models from the model catalog. Billing for shared quota is usage-based. You should consider using the shared quota only for creating temporary test endpoints, not production endpoints. For endpoints in production, it is recommended to request dedicated quota.

1. In the listing of models, select the **gpt-4.1-mini** entry, which should display a pane titled **gpt-4.1-mini** on the right side of the page.
1. Review the **gpt-4.1-mini** pane and identify the values of **Deployment quota**, **Total shared quota**, **Total Allocated**, and **Remaining**. 
1. On the **gpt-4.1-mini** pane, examine the **Weekly rate limiting** graph, providing the rate of throttling over the last 7 days.
1. On the **gpt-4.1-mini** pane, note the **Affiliated deployments using shared quota** section, providing a list of deployments using the shared quotas. 
1. In the **Affiliated deployments using shared quota** section, in the **gpt-4.1-mini** section, in the **Actions** column, select the pencil icon to display the **Edit quota allocation** pop-up window. 
1. In the **Edit quota allocation** pop-up window, identify the current quota allocation, note the three allocation options (**Customize quota allocation**, **Use all available quota**, and **Minimize to 1K TPM**) and, with the first option selected, use the slider to set **New quota allocation** to an arbitrary value. 
1. In the **Edit quota allocation** pop-up window, select **Cancel** to return to the **gpt-4.1-mini** pane on the **Quota** page without saving your changes.
1. On the **Quota** page, select the **Privisioned throughput unit** and then select **Show all** to review any provisioned type deployments in your Azure subscription.

   **Note**: Provisioned deployment types include **Global Provisioned Managed**, **Data Zone Provisioned Managed**, and **Provisioned Managed**.

### Task 5: Perform cleanup

1. Open another tab in the web browser displaying the Microsoft Foundry portal, navigate to the Azure portal at [https://portal.azure.com](https://portal.azure.com) and, if prompted, sign in by providing the same credentials you have been using throughout this exercise.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **foundry-quotas-project-new-RG** and, in the list of results, select **foundry-quotas-project-new-RG**.
1. On the **foundry-quotas-project-new-RG** page, select **Delete resource group**, on the **Delete a resource group** pane, in the **Enter resource group name to confirm deletion** text box, enter **foundry-quotas-project-new-RG**, select **Delete**, and, when prompted for confirmation, select **Delete** again.