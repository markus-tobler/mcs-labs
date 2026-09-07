---
layout: lab
module: agent-skills
title: "Skill Creation Agent"
order: 296
duration: 45
difficulty: 200
lab_type: local
section: intermediate_labs
journeys: ["developer"]
description: "Build a Microsoft Copilot Studio agent that inspects its runtime capabilities and generates a portable skill, then run that generated skill in a second agent."
---

# Skill Creation Agent

Build a Microsoft Copilot Studio agent that can inspect its available runtime capabilities and create reusable skills for that environment. You will equip the agent with two supporting skills, web search, an Office 365 Outlook mail tool, and memory, then use the agent to generate a company-research skill and run that skill in a second agent that emails the result.

---

## Lab Details

| Level | Persona | Duration   | Purpose                                                                                                                                                            |
| ----- | ------- | ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 200   | Maker   | 45 minutes | Create a GitHub Copilot harness agent that generates portable skills aligned with its available knowledge, tools, and memory, then consume a generated skill in a second agent. |

---

## Table of Contents

- [Why This Matters](#why-this-matters)
- [Introduction](#introduction)
- [Core Concepts Overview](#core-concepts-overview)
- [Documentation and Additional Training Links](#documentation-and-additional-training-links)
- [Prerequisites](#prerequisites)
- [Summary of Targets](#summary-of-targets)
- [Use Cases Covered](#use-cases-covered)
- [Instructions by Use Case](#instructions-by-use-case)
  - [Use Case #1: Create the Agent and Add Its Skills](#use-case-1-create-the-agent-and-add-its-skills)
  - [Use Case #2: Configure the Agent's Capabilities](#use-case-2-configure-the-agents-capabilities)
  - [Use Case #3: Generate and Download a Skill](#use-case-3-generate-and-download-a-skill)
  - [Use Case #4: Consume the Generated Skill in a Second Agent](#use-case-4-consume-the-generated-skill-in-a-second-agent)
- [Test the End-to-End Solution](#test-the-end-to-end-solution)
- [Summary of Learnings](#summary-of-learnings)

---

## Why This Matters

Agent skills are most useful when they match the environment in which they will run. A generic skill might assume tools, data sources, or state-management features that the target agent doesn't have.

This lab demonstrates a practical meta-agent pattern: one skill defines how to create skills, while another inspects the available agent harness. The resulting agent can design a skill around capabilities that actually exist in its runtime — and because skills are portable files, the generated skill can then be dropped into a different agent that has those same capabilities.

---

## Introduction

In this lab, you will create **Skill Creator**, an agent powered by the GitHub Copilot harness in Microsoft Copilot Studio. The agent combines two uploaded skills:

- **Skill Creator** provides the process for creating a new skill.
- **Agent Harness Explorer** identifies what the target harness can support so the generated skill can use appropriate capabilities.

You will then configure the agent with open web search, the **Send an email (V2)** action from the **Office 365 Outlook** connector, and **Memory**. Next, you will ask it to create a company-research skill that finds recent public activity and is designed to return only information that is new since the previous run.

Finally, you will build a second agent, **Company Researcher**, upload the generated skill package into it, give it the same mail tool, web search, and memory, and watch it load the skill and act on a real request.

**Real-world example:** A maker needs a reusable account-research skill that monitors public news and publications without repeating previously delivered updates, and mails a weekly digest to the account team. Instead of manually authoring the skill package, the maker describes the outcome, lets the configured agent generate a downloadable starting point, and then runs that package in the agent that will actually serve the account team.

---

## Core Concepts Overview

| Concept                        | Why it matters                                                                                                                                 |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| **GitHub Copilot harness**     | Provides the reasoning-focused Copilot Studio runtime used by the Skills, Model, and Memory features in this lab.                               |
| **Skill**                      | A portable Markdown file or ZIP package that contains YAML front matter, instructions, and optional supporting resources.                       |
| **Agent Harness Explorer**     | Helps the Skill Creator understand the capabilities available in the target runtime before designing a skill.                                   |
| **Tool**                       | Gives an agent an external capability. In this lab, the Office 365 Outlook connector supplies the mail capability.                              |
| **Connector action**           | A single operation from a Power Platform connector. **Send an email (V2)** is one action of the Office 365 Outlook connector.                   |
| **Search all websites**        | Enables open web search as an agent knowledge source for current public information.                                                            |
| **Memory**                     | Maintains separate context for each user so an agent can use relevant details across interactions — the basis for "only what's new" reporting.  |
| **Model selection**            | Sets the reasoning model for the agent. Model choice affects response quality, speed, and Copilot Credit consumption.                           |
| **Preview**                    | Provides an interactive test surface for verifying instructions, skills, knowledge, and tools before publishing.                                |

---

## Documentation and Additional Training Links

- [Agents powered by the GitHub Copilot harness overview](https://learn.microsoft.com/microsoft-copilot-studio/agents-experience/overview)
- [Create a new agent](https://learn.microsoft.com/microsoft-copilot-studio/agents-experience/build-new-agent)
- [Add an existing skill to an agent](https://learn.microsoft.com/microsoft-copilot-studio/agents-experience/skills-add-existing)
- [Manage and download skills](https://learn.microsoft.com/microsoft-copilot-studio/agents-experience/skills-manage)
- [Add knowledge sources to an agent](https://learn.microsoft.com/microsoft-copilot-studio/agents-experience/knowledge-add-existing-copilot)
- [Manage knowledge sources and web search](https://learn.microsoft.com/microsoft-copilot-studio/agents-experience/knowledge-edit-source#turn-web-search-on-or-off)
- [Add a tool to an agent](https://learn.microsoft.com/microsoft-copilot-studio/agents-experience/add-tools-custom-agent)
- [Available tools for agents (connectors, MCP servers, workflows)](https://learn.microsoft.com/microsoft-copilot-studio/agents-experience/tools-available)
- [Office 365 Outlook connector reference](https://learn.microsoft.com/connectors/office365/)
- [Select a model for an agent](https://learn.microsoft.com/microsoft-copilot-studio/agents-experience/authoring-select-agent-model)
- [Memory in Copilot Studio](https://learn.microsoft.com/microsoft-copilot-studio/agents-experience/memory-overview)
- [Test an agent in Preview](https://learn.microsoft.com/microsoft-copilot-studio/agents-experience/authoring-test-bot)
- [Data, privacy, and security for web search](https://learn.microsoft.com/microsoft-copilot-studio/data-privacy-security-web-search)

---

## Prerequisites

- Access to [Microsoft Copilot Studio](https://copilotstudio.microsoft.com/) in an environment where you can create agents
- Access to the **GitHub Copilot harness** agent experience
- A work or school account with an Exchange Online mailbox, and permission to create or use an **Office 365 Outlook** connection
- The following skill packages, bundled with this lab under [`assets`](assets):

  | Package                                                              | Skill                     | Purpose                                                              |
  | --------------------------------------------------------------------- | -------------------------- | ------------------------------------------------------------------- |
  | [`skill-creator.zip`](assets/skill-creator.zip)                     | **Skill Creator**         | Provides the process for creating a new skill                       |
  | [`agent-harness-explorer.zip`](assets/agent-harness-explorer.zip)   | **Agent Harness Explorer** | Identifies what the target harness can support before designing a skill |

  Download the **raw** file for each — GitHub's file preview will not give you a usable archive, and a `.zip` your browser has helpfully unpacked will not import.
- Approval to use open web search and mail capabilities under your organization's data policies

> [!IMPORTANT]
> Skills, Model selection, and Memory in this lab use the GitHub Copilot harness. Building and testing these agents can consume Copilot Credits.

> [!NOTE]
> The two skill packages ship with this lab as ready-to-upload ZIP packages. Confirm that you have downloaded both before starting. A ZIP package must contain a `SKILL.md` file; a standalone Markdown skill must contain YAML front matter with a name and description, followed by its instructions.

---

## Summary of Targets

By the end of this lab, you will:

- Create a **Skill Creator** agent in Copilot Studio
- Upload the **Skill Creator** and **Agent Harness Explorer** skills
- Configure concise instructions that keep generated skills aligned with the available harness
- Enable **Search all websites**, add the Office 365 Outlook **Send an email (V2)** action, and turn on **Memory**
- Generate and download a company-research skill from the **Preview** tab
- Create a second agent, **Company Researcher**, and run the generated skill in it end to end

---

## Use Cases Covered

| Step | Use Case                                                                                                            | Value added                                                                        | Effort |
| ---- | --------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ | ------ |
| 1    | [Create the Agent and Add Its Skills](#use-case-1-create-the-agent-and-add-its-skills)                             | Gives the agent a repeatable skill-authoring process and awareness of its runtime  | 10 min |
| 2    | [Configure the Agent's Capabilities](#use-case-2-configure-the-agents-capabilities)                                | Grounds generated skills in web search, mail, and persistent user context          | 10 min |
| 3    | [Generate and Download a Skill](#use-case-3-generate-and-download-a-skill)                                         | Turns a natural-language requirement into a portable skill artifact                | 10 min |
| 4    | [Consume the Generated Skill in a Second Agent](#use-case-4-consume-the-generated-skill-in-a-second-agent)         | Proves the generated skill is portable and runs against the capabilities it assumed | 15 min |

---

## Instructions by Use Case

---

## Use Case #1: Create the Agent and Add Its Skills

Create the Skill Creator agent and give it the two reusable skills demonstrated in the recording.

| Use case                            | Value added                                                         | Estimated effort |
| ----------------------------------- | ------------------------------------------------------------------- | ---------------- |
| Create the Agent and Add Its Skills | Combines a skill-authoring method with runtime capability discovery | 10 minutes       |

**Summary of tasks**
Create the agent, upload both skill packages, and verify that Copilot Studio accepts them.

**Scenario**
You want generated skills to fit the capabilities of the agent that will run them.

**Objective**
Prepare an agent that understands both how to create a skill and how to inspect its available harness.

### Step-by-step instructions

1. Go to [copilotstudio.microsoft.com](https://copilotstudio.microsoft.com/).
2. Confirm that you are working in the intended Power Platform environment.
3. In the left navigation, select **Agents**.
4. Select **New agent**.
5. In the agent designer, enter the name:

   ```text
   Skill Creator
   ```

6. Select **Save** if Copilot Studio prompts you to save the new agent before adding components.
7. On the **Build** tab, locate **Skills** in the components panel and select **+**.
8. In **Add skill**, select **Upload a skill**.

   ![The Add skill dialog with Upload a skill selected](images/upload-skill-dialog.jpg)

9. Download [`skill-creator.zip`](assets/skill-creator.zip), then drag it into the upload area, or select the upload area and browse to it.
10. Wait for Copilot Studio to validate and add the skill.
11. In **Skills**, select **+** again, choose **Upload a skill**, download [`agent-harness-explorer.zip`](assets/agent-harness-explorer.zip), and upload it.
12. Wait until both skill chips appear in the **Skills** section.

![The Skill Creator agent with Agent Harness Explorer and Skill Creator installed](images/installed-skills.jpg)

> [!TIP]
> If an upload is rejected, confirm that the artifact is either a Markdown skill file with valid YAML front matter or a ZIP package containing `SKILL.md`. The skill must have a nonempty name and description.

**Completion check:** The **Skills** section shows both **Agent Harness Explorer** and **Skill Creator**.

---

### Congratulations! You've completed Use Case #1!

---

## Use Case #2: Configure the Agent's Capabilities

Tell the agent what to create, expose the capabilities that generated skills can rely on, and enable user-specific memory.

| Use case                           | Value added                                                    | Estimated effort |
| ---------------------------------- | -------------------------------------------------------------- | ---------------- |
| Configure the Agent's Capabilities | Aligns generated skills with the target agent's actual runtime | 10 minutes       |

**Summary of tasks**
Set the agent instructions, enable web search, add the Office 365 Outlook **Send an email (V2)** action, turn on Memory, and save.

**Scenario**
The first generated skill will research public company activity, can send mail, and should account for information delivered in earlier runs.

**Objective**
Expose the relevant runtime capabilities to the Skill Creator before asking it to generate a package.

### Step-by-step instructions

1. On the **Build** tab, replace the default instructions with:

   ```text
   Create skills for copilot studio, focusing on the capability of the existing harness.
   Provide the skill as zip download <skill-name>.zip.
   Make sure, the zip file contains the skill.md on top level and does not contain the skill itself in a subfolder.
   ```

   ![The Skill Creator agent with its harness-focused instruction](images/agent-instructions.jpg)

2. In the **Knowledge** section, confirm that the **Search all websites** chip is present.
3. If **Search all websites** is absent, select **+** next to **Knowledge** and add **Search all websites**.

> [!IMPORTANT]
> Open web search sends a focused query derived from the user's request to Bing. Enable it only when that behavior complies with your organization's data and privacy policies.

4. In the **Tools** section, select **+**.
5. In **Add a tool**, select **Office 365 Outlook** on the **Featured** tab. If it isn't listed, select the **Connectors** filter or search for `Outlook`.

   ![The Add a tool dialog on the Featured tab with the Office 365 Outlook connector listed](images/add-tool-dialog-featured.jpg)

6. Under **Run a single action**, select **Send an email (V2)**.

   ![The Office 365 Outlook tool pane showing MCP servers above the Run a single action list, with Send an email (V2) available](images/office365-outlook-send-an-email-v2.jpg)

> [!IMPORTANT]
> The same pane also offers **Mail MCP** under **Use an MCP server**. This lab deliberately uses the connector action **Send an email (V2)**, not the MCP server, so the generated skill targets a single, well-defined send-mail operation.

7. Review the action details — the pane describes it as *This operation sends an email message* — and select **Add**.
8. If Copilot Studio asks for a connection, select an existing authorized **Office 365 Outlook** connection, or create one with an account that has a mailbox.
9. Confirm that **Send an email (V2)** appears in the **Tools** section.
10. Turn on the **Memory** toggle.
11. Confirm the final configuration includes:
    - **Skills**: Agent Harness Explorer and Skill Creator
    - **Tools**: Send an email (V2)
    - **Knowledge**: Search all websites
    - **Memory**: On
12. Select **Save**.

> [!NOTE]
> Memory is a production-ready preview feature. Each user has a private memory store for this agent. Microsoft currently deletes that user's memories after 28 days without interaction, and Memory is disabled in group chats and Microsoft Teams channels.

**Completion check:** The required skills, tool, and knowledge chip are visible, the Memory toggle is on, and the agent saves without an error.

---

### Congratulations! You've completed Use Case #2!

---

## Use Case #3: Generate and Download a Skill

Use the configured agent to create a reusable skill from a natural-language requirement.

| Use case                      | Value added                                                        | Estimated effort |
| ----------------------------- | ------------------------------------------------------------------ | ---------------- |
| Generate and Download a Skill | Produces a portable starting point for repeatable company research | 10 minutes       |

**Summary of tasks**
Submit the demonstrated prompt in Preview, review the generated design, and download the skill artifact.

**Scenario**
An account team wants recent public activity for a company without receiving the same items on every run.

**Objective**
Generate a skill that combines public web research with a memory-aware requirement.

### Step-by-step instructions

1. Select the **Preview** tab.
2. In the chat input, enter:

   ```text
   Create a skill that is capable of doing research on a company, providing recent public activities based on news and publications. Remember the last run and provide only new information.
   ```

   ![The company-research skill request entered in Preview](images/preview-skill-prompt.jpg)

3. Press **Enter** or select the send button.
4. Allow the agent to evaluate its available harness, tools, knowledge, and memory before it generates the skill.
5. Review the response. Confirm that it:
   - Identifies a company-research use case
   - Uses recent public news and publications as inputs
   - Accounts for previously returned information in the skill design
   - Provides a downloadable skill artifact
6. Select the generated skill link or download control and save the artifact.
7. Note the skill's name and the file you downloaded — you need both in Use Case #4.

> [!NOTE]
> In the recording, the agent produced `company-research-skill.zip` containing a skill named `company-watch`. Your generated skill may use a different name; use whatever your run produces.

> [!WARNING]
> Treat generated skills as implementation drafts. Review their instructions, tool assumptions, privacy behavior, and output rules before using them with production data.

**Completion check:** You can download the generated skill and identify its `SKILL.md` content or standalone Markdown skill file.

---

### Congratulations! You've completed Use Case #3!

---

## Use Case #4: Consume the Generated Skill in a Second Agent

Build a separate agent that uses the skill you just generated, so the skill runs against the capabilities it was designed for.

| Use case                                      | Value added                                                                         | Estimated effort |
| --------------------------------------------- | ------------------------------------------------------------------------------------- | ---------------- |
| Consume the Generated Skill in a Second Agent | Proves the generated skill is portable and works against a real request and mailbox  | 15 minutes       |

**Summary of tasks**
Create the **Company Researcher** agent, select a model, upload the generated skill, add the same mail tool, web search, and memory, set short instructions, and test it in Preview.

**Scenario**
The account team needs a working agent — not a skill file. The research and the weekly report must come from an agent they can actually chat with.

**Objective**
Run the generated skill inside an agent whose runtime matches the capabilities the Skill Creator assumed.

### Step-by-step instructions

1. In the left navigation, select **Agents** and confirm that you are in the same Power Platform environment.
2. Select **New agent**.
3. Enter the agent name:

   ```text
   Company Researcher
   ```

4. In the components panel, open the **Model** list and select **Claude Sonnet 5**.

   ![The Company Researcher agent with the model list open and Claude Sonnet 5 available under Anthropic models](images/company-researcher-model.jpg)

> [!TIP]
> New agents default to a deeper reasoning model. A more capable model is a valid choice for demanding production work, but this lab uses **Claude Sonnet 5** to keep Copilot Credit consumption down.

5. In the **Skills** section, select **+**, choose **Upload a skill**, and upload the skill package you downloaded in Use Case #3.
6. Wait for Copilot Studio to validate the package, then confirm the skill chip appears — in the recording it was named `company-watch`.
7. In the **Tools** section, select **+**, select **Office 365 Outlook**, and under **Run a single action** select **Send an email (V2)**.
8. Select **Add**, and select or create the Office 365 Outlook connection if prompted.
9. In the **Knowledge** section, confirm that the **Search all websites** chip is present, and add it if it is missing.
10. Turn on the **Memory** toggle.
11. Replace the default instructions with:

    ```text
    Research the provided company and send out the weekly report.
    ```

12. Select **Save**.
13. Confirm the configuration matches the target state:
    - **Model**: Claude Sonnet 5
    - **Skills**: your generated research skill
    - **Tools**: Send an email (V2)
    - **Knowledge**: Search all websites
    - **Memory**: On

    ![The configured Company Researcher agent showing the uploaded skill, the Send an email (V2) tool, web search, and Memory on](images/company-researcher-configured.jpg)

> [!NOTE]
> The instructions are deliberately short. The behavior — how to research, what to include, and how to suppress previously delivered items — lives in the uploaded skill, not in the agent instructions.

14. Select the **Preview** tab.
15. In the chat input, enter a research request naming a real company. The recording used:

    ```text
    Research isolutions AG in Switzerland
    ```

16. Press **Enter** and watch the agent's reasoning trail. A correct run shows the agent:
    - Matching the request to the uploaded skill and reporting `Loading skill: <skill-name>`
    - Identifying the company and its domain
    - Running **Searched knowledge** against open web search
    - Reporting that no prior ledger exists, so the run is a *baseline run*
    - Calling the **Send an email (V2)** tool to deliver the report

    ![The Preview transcript showing the agent loading the generated skill and starting a baseline research run](images/company-researcher-preview-run.jpg)

> [!IMPORTANT]
> The agent sends real email through your Office 365 Outlook connection. Use a recipient address you control for the first run.

> [!NOTE]
> The recording ends while the agent is still gathering research, so it does not show the delivered email. Confirm delivery in the recipient mailbox yourself.

**Completion check:** The Preview transcript shows the generated skill being loaded by name, the research proceeding as a baseline run, and the agent invoking the mail tool.

---

### Congratulations! You've completed Use Case #4!

---

## Test the End-to-End Solution

Use this verification to confirm the generated skill is structurally valid and behaves as designed.

### 1. Verify the artifact structure

1. Open the downloaded artifact.
2. If it is a ZIP package, confirm that it contains `SKILL.md` at the top level, not inside a subfolder.
3. Open `SKILL.md`, or open the downloaded Markdown file if the skill was delivered as a single file.
4. Confirm that the file contains YAML front matter with at least:

   ```yaml
   ---
   name: <skill-name>
   description: <one-line description>
   ---
   ```

5. Confirm that the Markdown instructions describe:
   - How to identify the company to research
   - How to find recent public news or publications
   - How to use or update prior-run state so previously delivered items can be excluded
   - What the final research output should contain

### 2. Verify the skill runs in the second agent

6. Open the **Company Researcher** agent from Use Case #4 and select the **Preview** tab.
7. Send a research request for a company you can verify, for example a well-known public organization.
8. Confirm that the transcript names the loaded skill and shows a web-knowledge search.
9. Confirm that the agent calls **Send an email (V2)**, then check the recipient mailbox for the report.

### 3. Verify the "only what's new" behavior

10. Select **New chat** and send the same research request again.
11. Confirm that the second run refers to the stored prior run instead of treating the request as a baseline, and that the delivered report contains only items that are new since the first run.

> [!NOTE]
> The recording demonstrates generation, download, upload into a second agent, and the start of a research run. It does not show a delivered email or a second run. Steps 9 and 11 are the checks that actually prove the mail path and the "only new information" requirement — run them before relying on the skill.

---

## Summary of Learnings

You created a Skill Creator agent that:

- Uses one skill to author reusable skills and another to inspect the target harness
- Grounds skill designs in open web search and the Office 365 Outlook **Send an email (V2)** connector action
- Has Memory enabled for designs that need continuity across interactions
- Converts a natural-language requirement into a downloadable skill artifact

You then created a Company Researcher agent that:

- Consumes the generated skill package without editing it
- Provides the same runtime capabilities the skill was designed against — model, mail tool, web search, and memory
- Keeps its own instructions to a single sentence, letting the skill carry the procedure
- Loads the skill on a matching request and runs it end to end

The central design principle is simple: define the desired outcome, expose only the capabilities the target runtime can actually use, and validate every generated skill by running it before relying on it.
