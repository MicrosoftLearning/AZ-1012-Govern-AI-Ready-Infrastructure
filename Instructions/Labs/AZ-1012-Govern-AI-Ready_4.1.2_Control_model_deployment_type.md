# AI Ready Governance hands-on exercise: Control model deployment type with a custom Azure policy definition

## Background information
When using models from Azure AI Foundry and Azure OpenAI with Azure AI Foundry, you have the option to implement custom policies to control which type of deployment options are available to users. This ability is available for both an Azure AI Foundry project and hub-based project.

## Scenario
Your company configures custom policies in Azure AI Foundry to disable global deployments and restrict AI workloads to approved regions or data zones. This will ensure compliance with regulatory and corporate requirements by keeping sensitive data within the designated residency boundaries.

## Prerequisites
- **Azure subscription**: If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.
- **Permissions**: To create and assign policies, you should have the Owner or Resource Policy Contributor role assigned at the Azure subscription level.
- **Familiarity with Azure Policy**: To learn more, refer to [What is Azure Policy?](https://learn.microsoft.com/azure/governance/policy/overview)

## Estimated duration
10 minutes

### Task 1: Create the custom policy

1. Start a web browser, navigate to the Azure portal at [https://portal.azure.com](https://portal.azure.com) and sign in by providing the credentials of a user account which has the Owner or Resource Policy Contributor role assigned at the Azure subscription level.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **Policy** and, in the list of results, select **Policy**.
1. On the **Policy** page, from the left side menu, select **Authoring**, then select **Definitions**, and then select **+ Policy definition**.
1. On the **Basics** tab of the **Policy definition** page, perform the following tasks:

   - Select the **ellipsis** button next to the **Definition location** text box and select the subscription you are using in this exercise. This selection determines where the policy definition will be stored.
   - In the **Name** text box, enter a descriptive name of the new policy definition (we will use **[Custom] Allowed Azure AI services deployment types**).
   - In the **Description** text box, enter an arbitrary description of the policy definition.
   - In the **Category** text box, you can either create a new category (such as `AI model governance`) or use an existing one (such as `Azure Ai Services).
   - In the **Policy rule** text box, enter the JSON-formatted definition of the policy which denies creating deployments with global processing types:

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
                  "field": "Microsoft.CognitiveServices/accounts/deployments/sku.name",
                  "equals": "GlobalStandard"
               }
            ]
         },
         "then": {
            "effect": "deny"
         }
      }
   }
   ```

    **Note**: Azure AI services were originally named Azure Cognitive Services. Azure OpenAI is part of Azure AI services, so this policy also applies to Azure OpenAI models.

1. Select **Save** to save the policy definition. Once the initiative is created, the web browser should display the policy definition's overview page.

### Task 2: Assign the custom policy

1. In the web browser displaying the Azure portal, from the policy definition's overview page, select **Assign policy**.
1. On the **Basics** tab of the **Assign policy** page, specify the following settings and select **Next**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Exclusions|none|
   |Policy definition|**[Custom] Allowed Azure AI services deployment types**|
   |Assignment name|**[Custom] Allowed Azure AI services deployment types**|
   |Description|An arbitrary description of the policy definition assignment|
   |Policy enforcement|**Enabled**|
 
1. On the **Parameters** tab of the **Assign policy** page, select **Next** (this policy has no parameters).
1. On the **Remediation** tab, select **Next** (this policy implements the **deny** effect, so it's not suitable for remediation).
1. On the **Non-compliance message**, select **Review + create** and then, on the **Review + create** tab, select **Create**.

### Task 3: Verify the policy assignment

1. In the web browser displaying the Azure portal, navigate back to the **Policy** page.
1. On the **Policy** page, from the left side menu, select **Authoring** and then select **Assignments**. 
1. On the **Policy \| Assignments page**, in the search box, enter the name of the policy assignment (**[Custom] Allowed Azure AI services deployment types**).
1. Verify that the new custom policy definition assignment is listed in the search results.

### Task 4: Review the policy compliance

1. In the web browser displaying the Azure portal, on the **Policy** page, from the left side menu, select **Compliance**.
1. On the **Policy \| Compliance page**, in the search box, enter the policy assignment name (**[Custom] Allowed Azure AI services deployment types**).
1. In the list of search results, select **[Custom] Allowed Azure AI services deployment types**.
1. On the **[Custom] Allowed Azure AI services deployment types** page, review the compliance status details.

   **Note**: You might need to wait in order for the compliance status to be evaluated and displayed in the Azure portal. Check the **Compliance state** value on the **[Custom] Allowed Azure AI services deployment types** page to determine whether evaluation has started.

### Task 5: Perform cleanup

1. In the web browser displaying the Azure portal, navigate back to **Policy** page.
1. On the **Policy** page, from the left side menu, select **Authoring** and then select **Assignments**.
1. On the **Policy \| Assignments page**, in the search box, enter the name of the policy assignment (**[Custom] Allowed Azure AI services deployment types**).
1. In the search results, select the ellipsis on the right side of the **[Custom] Allowed Azure AI services deployment types** entry, in the drop-down menu, select **Delete assignment**, and, when prompted for confirmation, select **Yes**.
1. On the **Policy** page, from the left side menu, select **Authoring** and then select **Definitions**.
1. On the **Policy \| Definitions page**, in the search box, enter the name of the policy assignment (**[Custom] Allowed Azure AI services deployment types**).
1. In the search results, select the ellipsis on the right side of the **[Custom] Allowed Azure AI services deployment types** entry, in the drop-down menu, select **Delete definition**, and, when prompted for confirmation, select **Yes**.