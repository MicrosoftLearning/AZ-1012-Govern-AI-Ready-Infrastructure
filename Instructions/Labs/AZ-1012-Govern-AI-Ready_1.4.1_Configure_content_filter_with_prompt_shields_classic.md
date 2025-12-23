# AI Ready Governance hands-on exercise: Configure content filters with prompt shields (classic)

## Background information
The content filtering system integrated into Microsoft Foundry runs alongside the core models, including image generation models. It uses a collection of multi-class classification models to detect four categories of harmful content (violence, hate, sexual, and self-harm) at four severity levels (safe, low, medium, and high), and optional binary classifiers for detecting jailbreak risk (incorporated into prompt shields), existing text, and code in public repositories.
The default content filtering configuration is set to filter at the medium severity threshold for all four content harms categories for both prompts and completions. That means that content that is detected at severity level medium or high is filtered, while content detected at severity level low or safe is not filtered by the content filters.
Prompt shields and protected text and code models are optional and on by default. For prompt shields and protected material text and code models, the configurability feature allows all customers to turn the models on and off. The models are on by default and can be turned off to match your requirements.

## Scenario
Your company is developing an AI-powered training and simulation platform for professionals in fields such as law enforcement, healthcare, and social work. In these contexts, realistic scenarios sometimes require references to violence, hate, sexual themes, or self-harm in order to train staff to recognize, respond to, and de-escalate critical situations. Because overly aggressive filtering would block content that is both intentional and necessary for simulation accuracy, the organization decides to lower severity thresholds to block only the most extreme cases. This configuration still prevents harmful misuse but allows the AI to produce controlled, context-appropriate outputs that better align with training objectives.
Additionally, since some scenarios involve exploring how individuals might attempt to manipulate AI systems, direct jailbreak detection should be enabled, while indirect jailbreak shields are turned off to allow testing of those edge cases in a controlled environment. To further protect compliance and intellectual property boundaries, output filters will be used to protect material checks to block copyrighted or publicly available code/text from surfacing in generated content.

## Prerequisites
- **Azure subscription**: If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.
- **Permissions**: To create Azure AI Services resources, you should have the Owner role assigned at the Azure subscription or the target resource group level.
- **Familiarity with Azure AI Content Safety**: To learn more, refer to [Content Safety in the Microsoft Foundry portal](https://learn.microsoft.com/en-us/azure/ai-foundry/ai-services/content-safety-overview).

## Estimated duration
10 minutes

### Task 1: Create a Microsoft Foundry project

1. Start a web browser, navigate to the **All resources** page of the Microsoft Foundry portal at [https://ai.azure.com/allResources](https://ai.azure.com/allResources) and sign in by providing the credentials of a user account which has the Owner role assigned at the Azure subscription level.
1. On the **All resources** page of the Microsoft Foundry portal, select **Create new**.
1. On the **Create project** pane, ensure that the **Microsoft Foundry resource** option is selected and then select **Next**.
1. On the **Create a project** pane, expand the **Advanced options** section and perform the following tasks:

   - In the **Project** text box, enter an arbitrary project name (we will name it **content-filter-project**).
   - In the **Subscription** drop-down list, select the subscription you are using in this exercise.
   - Select **Create new resource group** link, in the **Create new resource group** text box, enter **content-filter-project-RG** and select **OK**.
   - Accept the default value of the **Microsoft Foundry resource** (**content-filter-project-resource**).
   - In the **Region** drop-down list, select the Azure region in which you intend to provision the project resource.
   - Select **Create**.

   **Note**: Wait until the resource is provisioned. This might take about one minute. Once the project is created, the web browser should display the project's **Overview** page.

### Task 2: Create a content filter

1. In the web browser displaying the Microsoft Foundry portal, on the **Overview** page of **content-filter-project**, in the vertical menu on the left side, select **Guardrails + controls**. 
1. On the **Build Trusted AI with guardrails and controls** pane and select **Content filters**.
1. On the **Guardrails + Controls** page, select **+ Create content filter**.
1. On the **Basic information** tab of the **Create filters to allow or block specific types of content** pane, in the **Name** text box, enter **CustomComplianceContentFilter**, and then select **Next**.
1. On the **Input filter** tab, in the **Set input filter** section, configure the following settings and select **Next**:

   |Setting|Value|Rationale|
   |---|---|---|
   |Violence|High blocking|Allows moderate/mild depictions for training, blocks only extreme gore/graphic detail|
   |Hate|High blocking|Permits contextual use of slurs/hostile language in training, blocks only most severe|
   |Sexual|High blocking|Allows discussions of harassment scenarios, blocks explicit/pornographic content|
   |Self-harm|High blocking|Enables simulations (e.g., suicide prevention training), blocks only graphic methods|
   |Prompt shields for jailbreak attacks|Annotate and block|Prevents unsafe bypasses during normal use|
   |Prompt shields for indirect attacks|Off|Intentionally left off to allow controlled research/testing of indirect jailbreaks|

1. On the **Output filter** tab, in the **Set output filter** section, configure the following settings and select **Next**:

   |Setting|Value|Rationale|
   |---|---|---|
   |Violence|High blocking|Allows moderate/mild depictions for training, blocks only extreme gore/graphic detail|
   |Hate|High blocking|Permits contextual use of slurs/hostile language in training, blocks only most severe|
   |Sexual|High blocking|Allows discussions of harassment scenarios, blocks explicit/pornographic content|
   |Self-harm|High blocking|Enables simulations (e.g., suicide prevention training), blocks only graphic methods|
   |Protected material for text|Annotate and block|Ensures copyrighted or public text is not surfaced|
   |Protected material for code|Annotate and block|Ensures copyrighted or public code is never surfaced in outputs, preventing compliance and IP risks|

1. On the **Connection** tab, accept the default settings and select **Next**.

   **Note**: The **Connection** tab allows you to associate the filter you just configured to existing projects, hubs, or workloads, ensuring that any content processed in those contexts adheres to the filter's rules. 

1. On the **Review** tab, review the configuration you defined and select **Create filter**.

### Task 3: Perform cleanup

1. In the web browser displaying the Microsoft Foundry portal, navigate back to the **Build Trusted AI with guardrails and controls** pane and select **Content filters**.
1. On the **Guardrails + Controls** page, select the checkbox next to the **CustomComplianceContentFilter** entry, select **Delete**, and, when prompted for confirmation, select **Delete** again.
1. On the **Guardrails + Controls** page, at the bottom of the navigation menu on the left, select **Management center**.
1. On the **content-filter-project-resource** page, select the **content-filter-project** entry, select **Delete project**, and, when prompted for confirmation, select **Delete** again.
1. Open another tab in the web browser displaying the Microsoft Foundry portal, navigate to the Azure portal at [https://portal.azure.com](https://portal.azure.com) and, if prompted, sign in by providing the same credentials you have been using throughout this exercise.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **content-filter-project-RG** and, in the list of results, select **content-filter-project-RG**.
1. On the **content-filter-project-RG** page, select **Delete resource group**, on the **Delete a resource group** pane, in the **Enter resource group name to confirm deletion** text box, enter **content-filter-project-RG**, select **Delete**, and, when prompted for confirmation, select **Delete** again.