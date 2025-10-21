# AI Ready Governance hands-on exercise: Restrict deployable models with a custom Azure initiative

## Background information
When using models from Azure AI Foundry and Azure OpenAI with Azure AI Foundry, you have the option to implement custom policies to restrict which specific models users can deploy. This capability is available for Azure AI Foundry project and hub-based project. In addition, you can leverage a built-in policy definition to apply similar restrictions to ensure that Azure Machine Learning deployments use only approved Registry models. To minimize the number of policy assignments, it is possible to combine multiple policy definitions into initiatives.

## Scenario
Your company plans to implement custom policies in Azure AI Foundry projects to restrict which specific models can be deployed by users. This will ensure that only approved models are available within project and hub-based environments, maintaining governance and consistency across AI solutions. In parallel, your company plans to apply built-in policy definitions to its Azure Machine Learning workspaces to allow deployments of only pre-approved Registry models. By combining these policies into initiatives, the organization will be able to enforce consistent compliance controls across both Azure AI Foundry and Azure Machine Learning workloads.

## Prerequisites
- **Azure subscription**: If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.
- **Permissions**: To create and assign policies, you should have the Owner or Resource Policy Contributor role assigned at the Azure subscription level.
- **Familiarity with Azure Policy**: To learn more, refer to [What is Azure Policy?](https://learn.microsoft.com/azure/governance/policy/overview)

## Estimated duration
15 minutes

### Task 1: Create the custom policy

1. Start a web browser, navigate to the Azure portal at [https://portal.azure.com](https://portal.azure.com) and sign in by providing the credentials of a user account which has the Owner or Resource Policy Contributor role assigned at the Azure subscription level.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **Policy** and, in the list of results, select **Policy**.
1. On the **Policy** page, from the left side menu, select **Authoring**, then select **Definitions**, and then select **+ Policy definition**.
1. On the **Basics** tab of the **Policy definition** page, perform the following tasks:

   - Select the **ellipsis** button next to the **Definition location** text box, select the subscription you are using in this exercise. This selection determines where the policy definition will be stored.
   - In the **Name** text box, enter a descriptive name of the new policy definition (we will use **[Custom] Allowed Azure AI Services models**).
   - In the **Description** text box, enter an arbitrary description of the policy definition.
   - In the **Category** text box, you can either create a new category (such as `AI model governance`) or use an existing one (such as `Azure Ai Services).
   - In the **Policy rule** text box, enter the JSON-formatted definition of the policy which allows you to control which specific models and versions are available for deployment:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "allOf": [
               {
                  "field": "type",
                  "equals": "Microsoft.CognitiveServices/accounts/deployments"
               },
               {
                  "not": {
                     "value": "[concat(field('Microsoft.CognitiveServices/accounts/deployments/model.name'), ',', field('Microsoft.CognitiveServices/accounts/deployments/model.version'))]",
                     "in": "[parameters('allowedModels')]"
                  }
               }
            ]
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {
         "allowedModels": {
            "type": "Array",
            "metadata": {
               "displayName": "Allowed AI models",
               "description": "The list of allowed models to be deployed."
            }
         }
      }
   }
   ```

   **Note**: Azure AI services were originally named Azure Cognitive Services. Azure OpenAI is part of Azure AI services, so this policy also applies to Azure OpenAI models.

1. Select **Save** to save the policy definition. The web browser should display the policy definition's overview page.

### Task 2: Create an initiative by combining a built-in and custom policy definition

1. In the web browser displaying the Azure portal, navigate back to the **Policy** page.
1. On the **Policy** page, from the left side menu, select **Authoring**, then select **Definitions**, and then select **+ Initiative definition**.

1. On the **Basics** tab of the **Policy definition** page, perform the following tasks and then select **Next**:

   - Select the **ellipsis** button next to the **Initiative location** text box and then select the subscription you are using in this exercise. This selection determines where the initiative definition will be stored.
   - In the **Name** text box, enter a descriptive name of the new initiative definition (we will use **[Custom] Allowed Azure AI Services and Azure ML models**).
   - In the **Description** text box, enter an arbitrary description of the policy definition.
   - In the **Category** text box, you can either create a new category (such as `AI model governance`) or use an existing one (such as `Azure Ai Services).

1. On the **Policies** tab of the **Initiative definition** page, select **Add policy definition(s)**.
1. On the **Add policy definition(s)** pane, in the **Search** text box, enter **[Custom] Allowed Azure AI Services models** and, in the list of results, select the checkbox next to the entry **[Custom] Allowed Azure AI Services models**. 

   **Note**: Do not select **Add** yet.

1. On the same pane, in the **Search** text box, enter **[Preview]: Azure Machine Learning Deployments should only use approved Registry Models** and, in the list of results, select the checkbox next to the entry **[Preview]: Azure Machine Learning Deployments should only use approved Registry Models**.
1. On the **Add policy definition(s)** pane, select **Save**.
1. Back on the **Policies** tab of the **Initiative definition** page, select **Next**.
1. On the **Groups** tab of the **Initiative definition** page, select **Next**.

   **Note**: Groups help you organize policy definitions within an initiative.

1. On the **Initiative parameters** tab of the **Initiative definition** page, select **Next**.

   **Note**: Initiative parameters allow parameter values to be re-used across individual policy parameters. They can also be used to specify parameter values at assignment time.

1. On the **Policy parameters** tab, ensure that the drop-down list in the **Value Type** column for the **[Custom] Allowed Azure AI Services models** policy definition contains the **Set value** entry and, in the **Value(s)** text box, enter the comma separated list of models and their versions (within double quotes) to be allowed, enclosed by square brackets (we will use **["gpt-5-mini,0807", "DeepSeek-V3.1"]**). 

   **Note**: You can find the model names and their versions in the [Azure AI Foundry Model Catalog](https://ai.azure.com/explore/models). Select the model to view the details, and then copy the model name and their version in the title.

   **Note**: Policy parameters are inputs into individual policies.​ 

1. Back on the **Policy parameters** tab, select **Review + create** and then, on the **Review + create** tab, select **Create**. Once the initiative is created, the web browser should display the policy initiative's overview page.

### Task 3: Assign the initiative

1. In the web browser displaying the Azure portal, from the policy initiative's overview page, select **Assign initiative**.
1. On the **Basics** tab of the **Assign initiative** page, specify the following settings and select **Next**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Exclusions|none|
   |Initiative definition|**[Custom] Allowed Azure AI Services and Azure ML models**|
   |Assignment name|**[Custom] Allowed Azure AI Services and Azure ML models**|
   |Description|An arbitrary description of the initiative definition assignment|
   |Policy enforcement|**Enabled**|
 
1. On the **Parameters** tab of the **Assign initiative** page, select **Next** (parameters were assigned in the previous task).
1. On the **Remediation** tab, select **Next** (the policies in this initiative implements the **deny** effect, so it's not suitable for remediation).
1. On the **Non-compliance messages**, select **Review + create** and then, on the **Review + create** tab, select **Create**.

### Task 4: Verify the initiative assignment

1. In the web browser displaying the Azure portal, navigate back to the **Policy** page.
1. On the **Policy** page, from the left side menu, select **Authoring** and then select **Assignments**. 
1. On the **Policy \| Assignments page**, in the search box, enter the name of the initiative assignment (**[Custom] Allowed Azure AI Services and Azure ML models**).
1. Verify that the new initiative definition assignment is listed in the search results.

### Task 5: Review the initiative compliance

1. In the web browser displaying the Azure portal, on the **Policy** page, from the left side menu, select **Compliance**.
1. On the **Policy \| Compliance page**, in the search box, enter the initiative assignment name (**[Custom] Allowed Azure AI Services and Azure ML models**).
1. In the list of search results, select **[Custom] Allowed Azure AI Services and Azure ML models**.
1. On the **[Custom] Allowed Azure AI Services and Azure ML models** page, review the compliance status details.

   **Note**: You might need to wait in order for the compliance status to be evaluated and displayed in the Azure portal. Check the **Compliance state** value on the **[Custom] Allowed Azure AI Services and Azure ML models** page to determine whether evaluation has started.

### Task 6: Perform cleanup

1. In the web browser displaying the Azure portal, navigate back to **Policy** page.
1. On the **Policy** page, from the left side menu, select **Authoring** and then select **Assignments**.
1. On the **Policy \| Assignments page**, in the search box, enter the name of the initiative assignment (**[Custom] Allowed Azure AI Services and Azure ML models**).
1. In the search results, select the ellipsis on the right side of the **[Custom] Allowed Azure AI Services and Azure ML models** entry, in the drop-down menu, select **Delete assignment**, and, when prompted for confirmation, select **Yes**.
1. On the **Policy** page, from the left side menu, select **Authoring** and then select **Definitions**.
1. On the **Policy \| Definitions page**, in the search box, enter the name of the policy initiative (**[Custom] Allowed Azure AI Services and Azure ML models**).
1. In the search results, select the ellipsis on the right side of the **[Custom] Allowed Azure AI Services and Azure ML models** entry, in the drop-down menu, select **Delete definition**, and, when prompted for confirmation, select **Yes**.
1. On the **Policy** page, from the left side menu, select **Authoring** and then select **Definitions**.
1. On the **Policy \| Definitions page**, in the search box, enter the name of the policy definition (**[Custom] Allowed Azure AI Services models**).
1. In the search results, select the ellipsis on the right side of the **[Custom] Allowed Azure AI Services models** entry, in the drop-down menu, select **Delete definition**, and, when prompted for confirmation, select **Yes**.