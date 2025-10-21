# AI Ready Governance hands-on exercise: Enable threat protection for AI services

## Background information

The threat protection plan for AI services in Microsoft Defender for Cloud protects AI services on an Azure subscription by providing insights to threats that might affect your generative AI applications. With the AI services threat protection plan enabled, you can control whether alerts include suspicious segments directly from your user's prompts, or the model responses from your AI applications or resources. Enabling user prompt evidence helps you triage, classify alerts and your user's intentions. User prompt evidence consists of prompts and model responses. Both are considered your data. Evidence is available through the Azure portal, Microsoft Defender portal, and any attached partner integrations.

To supplement the protection offered by Microsoft Defender for Cloud, consider also enabling Data Security for Azure AI with Microsoft Purview. This allows you to access, process, and store prompt and response data (including associated metadata) from Azure AI Services, with support for key data security and compliance scenarios such as:
- Sensitive information type (SIT) classification
- Analytics and Reporting through Microsoft Purview DSPM for AI
- Insider Risk Management
- Communication Compliance
- Microsoft Purview Audit
- Data Lifecycle Management
- eDiscovery

These capabilities help your organization manage and monitor AI-generated data in alignment with enterprise policies and regulatory requirements. Note that Data Security for Azure AI requires a Microsoft Purview license, which isn't included with Microsoft Defender for Cloud's Defender for AI Services plan.

## Scenario
Your company is developing a customer engagement platform that supports multilingual virtual agents, automated document processing, and recommendation systems for e-commerce personalization. To protect these AI-driven services, your company plans to enable the AI services threat protection plan in Microsoft Defender for Cloud. Security operations teams will configure monitoring rules so that alerts can include prompt and response evidence when unusual behavior is detected in the virtual agents. For workloads that will handle sensitive customer data (such as payment information extracted from documents) your company is also considering the use of Data Security for Azure AI with Microsoft Purview to classify sensitive information, enforce data lifecycle policies, and meet compliance requirements across regulated industries.

## Prerequisites
- **Azure subscription**: If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.
- **Permissions**: To create Azure AI Services resources, you should have the Owner role assigned at the Azure subscription level.
- **Familiarity with Microsoft Defender for Cloud**: To learn more, refer to [Microsoft Defender for Cloud documentation](https://learn.microsoft.com/en-us/azure/defender-for-cloud/).

**Note**: You should ensure that Prompt Shields-based alerts for Azure OpenAI content filtering are enabled. Disabling this capability can affect Microsoft Defender for Cloud's ability to monitor and detect corresponding attacks.

## Estimated duration
5 minutes

### Task 1: Enable threat protection for AI services and user prompt evidence

1. Start a web browser, navigate to the Azure portal at [https://portal.azure.com](https://portal.azure.com) and sign in by providing the credentials of a user account which has the Owner role assigned at the Azure subscription level.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **Microsoft Defender for Cloud** and, in the list of results, select **Microsoft Defender for Cloud**.
1. On the **Microsoft Defender for Cloud \| Overview** page, from the left side menu, select **Management**, then select **Environment settings**, and then, on the right side, in the list of Azure subscriptions, select the name of the Azure subscription you are using for this exercise. 
1. On the **Settings \| Defender plans** page, in the **Cloud Workload Protection (CWPP)** section, in the **AI Services** plan entry, set the toggle to **On** and then select the corresponding **Settings** link.
1. On the **Settings & monitoring** page, verify that the toggle in the **Status** column of the **Enable suspicious prompt evidence** entry is set to **On**.

### Task 2: Enable Data Security for Azure AI with Microsoft Purview

1. In the web browser displaying the Azure portal, on the **Settings & monitoring** page, set the toggle in the **Status** column of the **Enable data security for AI interactions** entry to **On**.
1. On the **Settings & monitoring** page, select **Continue**.
1. Back on the **Settings \| Defender plans** page, select **Save**.

   **Note**: For additional information regarding Data Security for Azure AI, refer to [Microsoft Purview data security and compliance protections for generative AI apps](https://learn.microsoft.com/en-us/purview/ai-microsoft-purview?WT.mc_id=Portal-Microsoft_Azure_Security).

### Task 3: Perform cleanup

1. Start a web browser, navigate to the Azure portal at [https://portal.azure.com](https://portal.azure.com) and sign in by providing the credentials of a user account which has the Owner role assigned at the Azure subscription level.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **Microsoft Defender for Cloud** and, in the list of results, select **Microsoft Defender for Cloud**.
1. On the **Microsoft Defender for Cloud \| Overview** page, from the left side menu, select **Management**, then select **Environment settings**, and then, on the right side, in the list of Azure subscriptions, select the name of the Azure subscription you are using for this exercise. 
1. On the **Settings \| Defender plans** page, in the **Cloud Workload Protection (CWPP)** section, in the **AI Services** plan entry, set the toggle to **Off** and then select **Save**.
