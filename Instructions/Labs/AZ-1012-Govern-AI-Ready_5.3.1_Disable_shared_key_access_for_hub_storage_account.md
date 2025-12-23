# AI Ready Governance hands-on exercise: Disable shared-key access for a Microsoft Foundry hub storage account

## Background information
Every secure request to an Azure Storage account must be authorized. By default, requests can be authorized with either Microsoft Entra credentials, or by using the account access key for Shared Key authorization. Of these two types of authorization, Microsoft Entra ID provides superior security and ease of use over Shared Key, and is recommended by Microsoft. To require clients to use Microsoft Entra ID to authorize requests, you can disallow requests to the storage account that are authorized with Shared Key.

A Microsoft Foundry hub defaults to use of a shared key to access its default Azure Storage account. With key-based authorization, anyone who has the key and access to the storage account can access data.

To reduce the risk of unauthorized access, you can disable key-based authorization and instead use Microsoft Entra ID for authorization. This configuration uses a Microsoft Entra ID value to authorize access to the storage account. The identity used to access storage is either the user's identity or a managed identity. The user's identity is used to view data in Azure Machine Learning studio or to run a notebook while authenticated with the user's identity. Machine Learning uses a managed identity to access the storage account. An example is when the managed identity runs a training job.

**Important**: As of December 2025, the option to disable shared-key access for your hub's storage account is in public preview.

## Scenario
Your company is building an enterprise-grade AI platform using Microsoft Foundry to support multiple teams working on projects such as natural language processing, predictive analytics, and computer vision. To strengthen governance over sensitive AI data and prevent the risks associated with shared keys being distributed or misused, your company decided to disable key-based authorization on Azure Storage accounts associated with the Microsoft Foundry hubs and use Microsoft Entra ID, leveraging either user identities or managed identities for secure, auditable access to storage when running notebooks, managing datasets, or executing training jobs.

## Prerequisites
- **Azure subscription**: If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.
- **Permissions**: To create Azure AI Services resources, you should have the Owner role assigned at the Azure subscription or the target resource group level.
- **Familiarity with Azure Storage authorization**: To learn more, refer to [Prevent Shared Key authorization for an Azure Storage account](https://learn.microsoft.com/en-us/azure/storage/common/shared-key-authorization-prevent?tabs=portal).
- **Familiarity with Azure Role-Based Access Control (RBAC)**: To learn more, refer to [Azure RBAC documentation](https://learn.microsoft.com/en-us/azure/role-based-access-control/).

## Estimated duration
15 minutes

### Task 1: Create Microsoft Foundry hub

1. Start a web browser, navigate to the Azure portal at [https://portal.azure.com](https://portal.azure.com) and sign in by providing the credentials of a user account which has the Owner role assigned at the Azure subscription level.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **AI Foundry** and, in the list of results, select **AI Foundry**.
1. On the **AI Foundry** page, in the vertical menu on the left side, select **Use with AI Foundry** and then select **AI Hubs**.
1. On the **AI Foundry \| AI Hubs** page, select **+ Create** and, in the drop-down menu, select **Hub**.
1. On the **Basics** tab of the **Azure AI hub** page, specify the following settings and select **Next: Storage**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Resource group|The name of a new resource group **secure-storage-hub-RG**|
   |Region|The name of the Azure region in which you intend to provision the hub|
   |Name|**secure-storage-hub**|
   |Friendly name|**secure-storage-hub**|
   |Default project resource group|**Same as hub resource group**|

1. On the **Storage** tab of the **Azure AI hub** page, specify the following settings and select **Next: Inbound Access**:

   |Setting|Value|
   |---|---|
   |Storage account|The default name of a new storage account that will be automatically provisioned|
   |Credential store|**Azure key vault**|
   |Key vault|The default name of a new key vault that will be automatically provisioned||
   |Application insights|**None**|
   |Container registry|**None**|

   **Note**: You could pre-provision a storage account and use it instead. You have the option of disabling shared-key access when creating a storage account.

1. On the **Inbound Access** tab, accept the default option for **Public network access** (**All networks**) and select **Next: Outbound Access**.
1. On the **Outbound Access** tab, accept the default option of **Public** and select **Next: Encryption**.
1. On the **Encryption** tab, accept the default settings and select **Next: Identity**.
1. On the **Identity** tab, first note that the hub has its identity type set to system assigned identity and this option is not configurable. Next, in the **Storage account access** section, set the **Storage account access type** to **Identity-based access**, select the checkbox **Disable shared key access**, and then select **Review + create**.

   **Note**: The user assigned identity option is supported only when using an existing storage account, key vault, and container registry.

   **Important**: When using identity-based authentication, **Storage Blob Data Contributor** and **Storage File Privileged Contributor** roles must be granted to individual users who need access to the storage account.

1. Once you selected **Review + create**, wait for the validation to complete and then select **Create**.

   **Note**: Wait until the hub and the corresponding resources, including the storage account, are provisioned. This might take about one minute. Once the hub is created, on the deployment page, select **Go to resource** to navigate to the hub's **Overview** page.

### Task 2: Review the default role assignments

1. In the web browser displaying the Azure portal, on the **secure-storage-hub** **Overview** page, in the **Essentials** section, select the link to the newly created storage account.
1. On the storage account page, in the vertical menu on the left side, select **Access Control (IAM)**.
1. On the storage account's **Access Control (IAM)** page, select the **Role assignments** tab.
1. In the search text box, enter **secure-storage-hub** and review the list of results. Verify that there are three entries, representing the following roles assigned to the system assigned managed identity associated with the newly provisioned Microsoft Foundry hub:

   - **Azure AI Administrator** - inherited from the **secure-storage-hub-RG** resource group
   - **Storage Blob Data Contributor** - assigned directly to the hub's storage account
   - **Storage File Privileged Contributor** - assigned directly to the hub's storage account

   **Note**: These role assignments allow the hub to access the default storage account. As mentioned earlier, you would need to grant the same roles to users who need to be able to access the content of the storage account.

### Task 3: Perform cleanup

1. In the web browser displaying the Azure portal, use the **Search** text box at the top of the page to search for **secure-storage-hub-RG** and, in the list of results, select **secure-storage-hub-RG**.
1. On the **secure-storage-hub-RG** page, select **Delete resource group**, on the **Delete a resource group** pane, in the **Enter resource group name to confirm deletion** text box, enter **secure-storage-hub-RG**, select **Delete**, and, when prompted for confirmation, select **Delete** again.