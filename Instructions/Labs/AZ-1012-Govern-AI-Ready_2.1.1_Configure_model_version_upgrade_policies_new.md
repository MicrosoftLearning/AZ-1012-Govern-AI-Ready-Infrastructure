# AI Ready Governance hands-on exercise: Configure model version upgrade policies (new)

## Background information
Microsoft Foundry regularly releases new model versions to incorporate the latest features and improvements from key model providers in the industry. Customers can choose to start with a particular version and stay on it or to automatically update as new versions are released.

The version of a model is set when you deploy it. You can choose an update policy, which can include the following options:
- Deployments set to **Opt out of automatic model version upgrades** require a manual upgrade if a new version is released. When the model is retired, those deployments stop working.
- Deployments set to **Upgrade once the new default version becomes available** use the new default version.
- Deployments set to **Once the current version expires** automatically update when the current version is retired.

Update policies are configured per deployment and vary by model and provider.

## Scenario
Your company is building a global analytics platform that integrates conversational agents, batch summarization pipelines, and specialized models for financial forecasting and scientific research. These solutions rely on standard Microsoft Foundry models, which are regularly updated by the platform. Some components of your applications, such as internal chatbots, can easily adopt new default versions to benefit from the latest improvements. Other components, like financial forecasting models that require strict reproducibility, depend on staying on a fixed version until testing is complete. To ensure each workload behaves reliably, your company must configure appropriate update policies for each deployment.

## Prerequisites
- **Azure subscription**: If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.
- **Permissions**: To create Azure AI Services resources, you should have the Owner role assigned at the Azure subscription or the target resource group level.
- **Familiarity with Microsoft Foundry model deployment**: To learn more, refer to [Deploy Microsoft Foundry Models in the Foundry portal](https://learn.microsoft.com/en-us/azure/ai-foundry/foundry-models/how-to/deploy-foundry-models?view=foundry&preserve-view=true).

## Estimated duration
10 minutes

### Task 1: Create a Microsoft Foundry project

1. Start a web browser, navigate to the **All resources** page of the Azure AI Foundry portal at [https://ai.azure.com/allResources](https://ai.azure.com/allResources) and sign in by providing the credentials of a user account which has the Owner role assigned at the Azure subscription level.
1. On the **All resources** page of the Azure AI Foundry portal, select **Create new**.
1. On the **Create project** pane, ensure that the **Microsoft Foundry resource** option is selected and then select **Next**.
1. On the **Create a project** pane, expand the **Advanced options** section and perform the following tasks:

   - In the **Project** text box, enter an arbitrary project name (we will name it **version-upgrade-project-new**).
   - In the **Subscription** drop-down list, select the subscription you are using in this exercise.
   - Select **Create new resource group** link, in the **Create new resource group** text box, enter **version-upgrade-project-new-RG** and select **OK**.
   - Accept the default value of the **Microsoft Foundry resource** (**version-upgrade-project-new-resource**).
   - In the **Region** drop-down list, select the Azure region in which you intend to provision the project resource.
   - Select **Create**.

   **Note**: Wait until the resource is provisioned. This might take about one minute. Once the project is created, the web browser should display the **Home** page of the Microsoft Foundry portal. If needed, turn the **New Foundry** switch at the top of the page to the **On** position and verify that the project name appears in the upper left corner of the page.

### Task 2: Deploy a model in the project

1. On the project's **Home** page in the Microsoft Foundry portal, in the upper right corner, select **Discover**.
1. On the project's **Discover** page in the Microsoft Foundry portal, in the vertical menu on the left side, select **Models**.
1. In the **Search** text box, enter **gpt-4.1-mini** and, in the list of results, select **gpt-4.1-mini**.

   **Note**: If the **gpt-4.1-mini** model is not available, select another GPT chat completion model

1. On the **gpt-4.1-mini** page, select **Deploy** and, in the drop-down menu, select **Custom settings**.
1. On the **gpt-4.1-mini** pane, perform the following tasks:

   - Accept the default deployment name (**gpt-4.1-mini**)
   - Ensure that the deployment type is set to **Global Standard**
   - Expand the **Model version settings** section
   - Review the entries available in the **Model version** drop-down list but leave the default entry selected.
   - Review the entries available in the **Model version upgrade policy** drop-down list, and ensure that the entry **Upgrade once new default version becomes available** is selected
   - Accept the default value of **Tokens per Minute Rate Limit**
   - Accept the default **Guardrails** setting (**DefaultV2**)
   - Select **Deploy**.

   **Note**: Wait until the deployment is completed. This should take just a few seconds. Once the deployment is completed, the web browser should display the **gpt-4.1-mini** deployment page.

1. On the **gpt-4.1-mini** deployment page, select the **Detail** tab. Verify that the model version and upgrade policy match the choices you made during the deployment.

### Task 3: Perform cleanup

1. Open another tab in the web browser displaying the Microsoft Foundry portal, navigate to the Azure portal at [https://portal.azure.com](https://portal.azure.com) and, if prompted, sign in by providing the same credentials you have been using throughout this exercise.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **version-upgrade-project-new-RG** and, in the list of results, select **version-upgrade-project-new-RG**.
1. On the **version-upgrade-project-new-RG** page, select **Delete resource group**, on the **Delete a resource group** pane, in the **Enter resource group name to confirm deletion** text box, enter **version-upgrade-project-new-RG**, select **Delete**, and, when prompted for confirmation, select **Delete** again.