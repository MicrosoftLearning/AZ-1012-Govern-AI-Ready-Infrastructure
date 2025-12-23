# AI Ready Governance hands-on exercise: Enforce token limits with AI Gateway

## Background information
You can use AI Gateway in Microsoft Foundry to enforce tokens-per-minute (TPM) rate limits and total token quotas for model deployments at the project scope. This integration uses Azure API Management (APIM) and applies limits per project to prevent runaway token consumption and align usage with organizational guardrails for models deployments.

In this architecture, an AI Gateway sits between client applications and model deployments created in a Foundry project. All inference requests flow through the associated APIM instance once the gateway is enabled. Limits apply at the project level (each project can have its own TPM and quota settings) and can target specific model deployments. Supported features for this release are TPM rate limits and total token quotas only. No other APIM policy types are enforced.

As part of setting up this control layer, when you create a new AI Gateway, you need to decide whether to create a new APIM instance (isolated governance, predictable usage boundary), or reuse an existing APIM instance (centralized management, shared cost). You can use any existing APIM instance in the same Azure region as the Microsoft Foundry resource hosting your project.

If you choose to create a new instance from the Foundry portal flow, the APIM SKU defaults to Basic v2, which is required for gateway integration.

Once the gateway is in place, token limits are enforced through two complementary dimensions:

- Tokens per minute (TPM) rate limit: Evaluated on a rolling 60-second window. Each request’s token usage is aggregated, and once the rolling window total exceeds the configured TPM value, subsequent requests within that window receive 429 Too Many Requests responses until usage falls below the threshold.
- Total token quota: Aggregates tokens consumed across the defined quota window (for example, daily or monthly allocation). When cumulative usage reaches the configured quota, further requests receive 403 Forbidden until the window resets. The quota counter automatically resets at the start of the next window boundary.

In addition to these gateway-level controls, the limits are enforced alongside any underlying model or regional limits (such as per-deployment TPM or RPM quotas defined for Azure OpenAI models within a subscription and region).

Because enforcement is based on accumulated usage, adjusting a quota or TPM value mid-window affects only subsequent decisions; it does not retroactively clear already consumed tokens. To effectively "reset" a quota before the natural window boundary, you can temporarily increase the quota value or remove and recreate the limit.

Taken together, these capabilities make AI Gateway useful in a number of governance-focused scenarios, including:

- Multi-team token containment (prevent one project from monopolizing model capacity).
- Cost control by capping aggregate usage of deployed models.
- Compliance boundaries for regulated workloads (enforce predictable usage ceilings across projects and regions).

As mentioned earlier, the AI Gateway in Microsoft Foundry is built on top of Azure API Management (APIM), which provides the underlying infrastructure for enforcing token limits and quotas, routing requests, and enabling project-level governance. It is worth noting that, while Foundry exposes a simplified interface for managing limits per project and deployment, the gateway itself leverages APIM’s broader functionality, inheriting its scalability, security, and monitoring features.

At the APIM level, the AI Gateway provides a unified set of capabilities to manage, secure, scale, monitor, and govern AI endpoints across a wide range of deployments, including Microsoft Foundry and Azure OpenAI models, Azure AI Model Inference API deployments, remote MCP servers, OpenAI-compatible endpoints, and self-hosted models. It helps organizations authenticate and authorize access, load balance requests, monitor interactions, manage token usage and quotas across applications, and enable self-service for developer teams, providing the foundation for effective enterprise-scale AI governance.

For more information, refer to [AI gateway in Azure API Management](https://learn.microsoft.com/en-us/azure/api-management/genai-gateway-capabilities).

## Scenario
Your company is planning to develop a global customer engagement and knowledge automation platform that integrates virtual support agents and document analysis workflows to streamline operations such as customer service, compliance reviews, and internal knowledge discovery.

To prepare for this initiative, the company plans to use Microsoft Foundry to create a project, deploy a chat completion model, and configure an AI Gateway to explore how token limits, quotas, and capacity allocation can be applied at the project level. This evaluation will help understand how to control usage across different projects, prevent individual workloads from exhausting shared capacity, and guide decisions on governance, scaling, and regional deployment as the platform grows.

## Prerequisites
- **Azure subscription**: If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.
- **Permissions**: To create AI Gateway, you should have the Owner or Contributor role assigned at the Azure subscription level.
- **Familiarity with Microsoft API Management**: To learn more, refer to [What is Azure API Management?](https://learn.microsoft.com/en-us/azure/api-management/api-management-key-concepts).

## Estimated duration
15 minutes

### Task 1: Create a Microsoft Foundry project

1. Start a web browser, navigate to the **All resources** page of the Azure AI Foundry portal at [https://ai.azure.com/allResources](https://ai.azure.com/allResources) and sign in by providing the credentials of a user account which has the Owner or Contributor role assigned at the Azure subscription level.
1. On the **All resources** page of the Azure AI Foundry portal, select **Create new**.
1. On the **Create project** pane, ensure that the **Microsoft Foundry resource** option is selected and then select **Next**.
1. On the **Create a project** pane, expand the **Advanced options** section and perform the following tasks:

   - In the **Project** text box, enter an arbitrary project name (we will name it **foundry-gateway-project-new**).
   - In the **Subscription** drop-down list, select the subscription you are using in this exercise.
   - Select **Create new resource group** link, in the **Create new resource group** text box, enter **foundry-gateway-project-new-RG** and select **OK**.
   - Accept the default value of the **Microsoft Foundry resource** (**foundry-gateway-project-new-resource**).
   - In the **Region** drop-down list, select the Azure region in which you intend to provision the project resource.

   **Note**: Use one of the regions where you will be able to provision the AI Gateway, such as East US.

   - Select **Create**.

   **Note**: Wait until the resource is provisioned. This might take about one minute. Once the project is created, the web browser should display the **Home** page of the Microsoft Foundry portal. If needed, turn the **New Foundry** switch at the top of the page to the **On** position and verify that the project name appears in the upper left corner of the page.

### Task 2: Deploy a model in the project and explore deployment quota allocation

1. On the **Home** page of the Microsoft Foundry portal, in the upper right corner, select **Discover**.
1. On the **Discover** page of the Microsoft Foundry portal, in the vertical menu on the left side, select **Models**.
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

### Task 3: Create an AI Gateway

1. On the **gpt-4.1-mini** deployment page of the Microsoft Foundry portal, in the upper right corner, select **Operate**.
1. On the **Overview** page, in the vertical menu on the left side, select **Admin**.
1. On the **Admin** page, select the **AI Gateway** tab and then select **Add AI Gateway**.
1. In the **Add AI Gateway** pop-up window, specify the following settings and then select **Add**:

   |Setting|Value|
   |---|---|
   |AI Foundry resource|**foundry-gateway-project-new**|
   |AI Gateway (Powered by API Management)|**Create new**|
   |AI Gateway name|**foundry-gateway-new-*xxxxxx*** where *xxxxxx* is a string of random alphanumeric characters|
   |AI Gateway region|an Azure region you selected previously when creating the project **foundry-gateway-project-new**|

1. Back on the **AI Gateway** tab of the **Admin** page, select the entry representing the newly provisioned gateway to display its properties
1. Verify that the pricing tier is set to **BasicV2**, review the **Projects** tab to confirm that it includes the *foundry-gateway-project-new** entry and its **Gateway status** is **Enabled**.

### Task 4: Configure token per minute limits

1. On the **AI Gateway** tab of the **Admin** page, select the **Token management** tab.
1. In the list of projects, select the pencil icon on the right side of the **foundry-gateway-project-new** entry. 
1. In the **Set token limit to a project** pop-up window, specify the following settings and then select **Set limit**:

   |Setting|Value|
   |---|---|
   |Project|**foundry-gateway-project-new**|
   |Model deployment|**gpt-4.1-mini**|
   |Limit (Token-per-minute)|Any value lower than the total allocated token limit for this model deployment|

### Task 5: Perform cleanup

1. Open another tab in the web browser displaying the Microsoft Foundry portal, navigate to the Azure portal at [https://portal.azure.com](https://portal.azure.com) and, if prompted, sign in by providing the same credentials you have been using throughout this exercise.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **foundry-gateway-project-new-RG** and, in the list of results, select **foundry-gateway-project-new-RG**.
1. On the **foundry-gateway-project-new-RG** page, select **Delete resource group**, on the **Delete a resource group** pane, in the **Enter resource group name to confirm deletion** text box, enter **foundry-gateway-project-new-RG**, select **Delete**, and, when prompted for confirmation, select **Delete** again.