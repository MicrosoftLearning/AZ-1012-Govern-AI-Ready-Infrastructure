# AI Ready Governance hands-on exercise: Configure content filters with prompt shields (new)

## Background information
Microsoft Foundry includes built-in safety and security guardrails that apply to both core models, including image generation models, and agents developed using the Foundry Agent Service (agent guardrails are in preview as of December 2025). These guardrails run alongside models and agents to detect and mitigate harmful or undesirable behavior using classification models designed to identify risks such as violence, hate, sexual content, self-harm, prompt attacks, and protected material in text or code.

Guardrails define how and where risks are evaluated and what action is taken when a risk is detected. Risks can be assessed at multiple intervention points, including user input and model output for both models and agents, and additionally at tool call and tool response stages for agents. When a risk is detected, the guardrail can annotate or block the output, depending on the configured action and the applicability of the control to models or agents.

By default, model deployments use the Microsoft.DefaultV2 guardrail. Agents inherit the guardrail of the underlying model unless a specific guardrail is assigned to the agent. When an agent-level guardrail is applied, it fully overrides the model's guardrail, and all risk detection for that agent is based on the agent's configuration. Guardrail behavior may vary depending on API configuration and application design, and agent guardrails currently apply only to agents built in the Foundry Agent Service.

## Scenario
Your company is building an AI-powered training and simulation platform using Microsoft Foundry agents for professionals in domains such as law enforcement, healthcare, and social work. These simulations require agents to generate and reason over realistic situations that may include references to violence, hate, sexual content, or self-harm in order to help trainees recognize, respond to, and de-escalate high-risk scenarios. To support this goal, the organization configures agent-level guardrails with lower severity thresholds so that only high-risk content is blocked, while contextually appropriate material is allowed to pass through for training purposes.

Because the platform is designed to evaluate how users may attempt to manipulate or stress-test agent behavior, guardrails are configured to detect and block direct user prompt attacks while allowing certain indirect attack patterns to surface during controlled exercises. Agent guardrails are applied at the user input and output intervention points, and selectively at tool call and tool response stages where tools are used to enrich simulations. To maintain compliance and protect intellectual property, protected material checks for text and code are enabled to prevent the agent from returning copyrighted or publicly available content in its responses, while still allowing realistic, policy-compliant training interactions.

## Prerequisites
- **Azure subscription**: If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.
- **Permissions**: To create Azure AI Services resources, you should have the Owner role assigned at the Azure subscription or the target resource group level.
- **Familiarity with guardrails and controls in Microsoft Foundry**: To learn more, refer to [Guardrails and controls in Microsoft Foundry](https://learn.microsoft.com/en-us/azure/ai-foundry/guardrails/guardrails-overview?view=foundry).

## Estimated duration
20 minutes

### Task 1: Create a Microsoft Foundry project

1. Start a web browser, navigate to the **All resources** page of the Azure AI Foundry portal at [https://ai.azure.com/allResources](https://ai.azure.com/allResources) and sign in by providing the credentials of a user account which has the Owner role assigned at the Azure subscription level.
1. On the **All resources** page of the Azure AI Foundry portal, select **Create new**.
1. On the **Create project** pane, ensure that the **Microsoft Foundry resource** option is selected and then select **Next**.
1. On the **Create a project** pane, expand the **Advanced options** section and perform the following tasks:

   - In the **Project** text box, enter an arbitrary project name (we will name it **content-filter-project-new**).
   - In the **Subscription** drop-down list, select the subscription you are using in this exercise.
   - Select **Create new resource group** link, in the **Create new resource group** text box, enter **content-filter-project-new-RG** and select **OK**.
   - Accept the default value of the **Microsoft Foundry resource** (**content-filter-project-new-resource**).
   - In the **Region** drop-down list, select the Azure region in which you intend to provision the project resource.
   - Select **Create**.

   **Note**: Wait until the resource is provisioned. This might take about one minute. Once the project is created, the web browser should display the **Home** page of the Microsoft Foundry portal. If needed, turn the **New Foundry** switch at the top of the page to the **On** position and verify that the project name appears in the upper left corner of the page.

### Task 2: Deploy a model in the project

1. On the **Home** page of the Microsoft Foundry portal, in the upper right corner, select **Discover**.
1. On the **Discover** page of the Microsoft Foundry portal, in the vertical menu on the left side, select **Models**.
1. In the **Search** text box, enter **gpt-4.1-mini** and, in the list of results, select **gpt-4.1-mini**.

   **Note**: If the **gpt-4.1-mini** model is not available, select another GPT chat completion model

1. On the **gpt-4.1-mini** page, select **Deploy** and, in the drop-down menu, select **Default settings**.

### Task 3: Create a Microsoft Foundry agent

1. On the **gpt-4.1-mini** page of the Microsoft Foundry portal, in the vertical menu on fthe left side, select **Agents**.
1. On the **Agents** page, select **Create agent**.
1. In the **Create an agent** dialog box, in the **Agent name** text box, enter **content-filter-project-new-agent** and select **Create**.

### Task 4: Review predefined guardrails and blocklists

1. On the **Agents** page of the Microsoft Foundry portal, in the vertical menu on fthe left side, select **Guardrails**.
1. On the **Guardrails** page of the Microsoft Foundry portal, review the content of the **Guardrails** tab and note that it contains two automatically generated guardrails named **Microsoft.Default** and **Microsoft.DefaultV2**.
1. Select the **Microsoft.Default** entry and, on the **Microsoft.Default** pane on the right side, expand the **Content safety** section.
1. In the **Content safety** section, review the entries in the **Risk type** (all set to **Medium blocking**), **Intervention point** (all set to **User input, Output**), and **Action** (all set to **Block**) columns.
1. Select the **Microsoft.DefaultV2** entry and, on the **Microsoft.DefaultV2** pane on the right side, expand the **Jailbreak** and **Content safety** sections.
1. Note that the **Content safety** section is the same as that for **Microsoft.Default**, but the **Jailbreak** section mitigates the **Jailbreak** risk type, applying **Block** at the **User input** **Intervention point**.
1. On the **Guardrails** page, select the **Blocklists** tab.
1. On the **Blocklists** tab, note the built-in **Profanity** filter blocklist.

### Task 5: Create a guardrail

1. On the **Guardrails** page of the Microsoft Foundry portal, select the **Guardrails** tab.
1. On the **Guardrails** tab, select **Create**.
1. On the **Create guardrail controls** pane, in the **Add controls** section, perform the following tasks:

   - On the **Controls** pane, in the **Jailbreak** section, select the checkbox next to the **Jailbreak** entry and then select **Delete**.
   - Repeat the previous step to delete the pre-populated entries in the **Content safety** and **Protected materials** sections.
   - On the **Controls** pane, in the **Add controls** section, in the **Risk** drop-down list, select **Jailbreak**, ensure that the **Intervention point** is set to **User input**, set **Action** to **Annotate**, and then select **Add control**.

   **Note**: Annotations are supported only for models, not agents.

   - On the **Controls** pane, in the **Add controls** section, in the **Risk** drop-down list, in the **Content safety** subsection, select **Hate**, set the **Severity level** slider to **Lowest blocking**, ensure that the **User input** and **Output** **Intervention points** are included, set **Action** to **Block**, and then select **Add control**.
   - Repeat the previous step to set the **Lowest blocking** **Severity level** for other risk categories, including **Sexual**, **Self-harm**, and **Violence**.
   **Note**: Besides using **Jailbreak** and **Content safety** controls, you can also include controls applicable to **Indirect prompt injections**, **Sensitive data leakage**, and **Protected materials**.

   - At the bottom of the **Add controls** section, select **Next**.

1. In the **Select agents and models** section, perform the following tasks:

   - Select **Add agents**. 
   - In the **Agents** dialog box, select **content-filter-project-new-agent** and then select **Save**.
   - Select **Add models**. 
   - In the **Model deployments** dialog box, select **gpt-4.1-mini** and then select **Save**.
   - At the bottom of the **Select agents and models** section, select **Next**.

1. In the **Review** section, perform the following tasks:

   - In the **Guardrail name** text box, enter **GuardrailContentSafetyLowestBlocking**.
   - Leave the streaming mode set to **Default**.

   **Note**: Streaming mode is not supported for the Responses API.  Effectively, guardrail testing in the Foundry playground might not reflect the actual streaming behavior because of its use of Responses API.

   - Select **Submit**.

### Task 6: Perform cleanup

1. Open another tab in the web browser displaying the Microsoft Foundry portal, navigate to the Azure portal at [https://portal.azure.com](https://portal.azure.com) and, if prompted, sign in by providing the same credentials you have been using throughout this exercise.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **content-filter-project-new-RG** and, in the list of results, select **content-filter-project-new-RG**.
1. On the **content-filter-project-new-RG** page, select **Delete resource group**, on the **Delete a resource group** pane, in the **Enter resource group name to confirm deletion** text box, enter **content-filter-project-new-RG**, select **Delete**, and, when prompted for confirmation, select **Delete** again.

