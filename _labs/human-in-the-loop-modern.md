---
layout: lab
module: human-in-the-loop-modern
title: "Expense Claims Agent with Workflow Approvals (Human-in-the-Loop)"
order: 245
duration: 45
difficulty: 200
lab_type: local
section: intermediate_labs
journeys: ["business-user", "developer"]
description: "Build an expense claims solution with an agent powered by the GitHub Copilot harness that extracts receipt data into JSON and a workflow that evaluates each claim against a policy document with an inline agent, routes higher-value claims to a human reviewer, and emails the requestor the final outcome."
---

# Expense Claims Agent with Workflow Approvals (Human-in-the-Loop)

Build an end-to-end expense claims solution with an agent powered by the GitHub Copilot harness. The employee attaches a receipt, the agent reads the date and amount from it, asks only for the category and business reason, and sends the extracted receipt content to a workflow. The workflow evaluates the claim against a policy document with an inline agent, routes higher-value claims to a human reviewer, and emails the employee with the final outcome.

---

## Lab Details

| Level | Persona | Duration   | Purpose                                                                                                                                                              |
| ----- | ------- | ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 200   | Maker   | 45 minutes | Build an agent powered by the GitHub Copilot harness that invokes a workflow with AI policy evaluation, conditional routing, human review, and outcome notification. |

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
  - [Use Case #1: Build the Expense Claims Approval Workflow](#use-case-1-build-the-expense-claims-approval-workflow)
  - [Use Case #2: Create the Expense Claims Agent](#use-case-2-create-the-expense-claims-agent)
  - [Use Case #3: Complete the Workflow Response Paths](#use-case-3-complete-the-workflow-response-paths)
  - [Test the End-to-End Solution](#test-the-end-to-end-solution)
- [Summary of Learnings](#summary-of-learnings)

---

## Why This Matters

Expense claims are a classic high-volume, high-friction process:

- Employees want fast decisions and clear outcomes.
- Finance teams want consistent policy enforcement.
- Managers want to review only the claims that require human judgment.

This lab demonstrates how agents and workflows can divide those responsibilities. The user-facing agent gathers the information and sends notifications. The workflow provides predictable orchestration, while an inline agent makes the policy decision and a human reviewer handles higher-value claims.

---

## Introduction

In this lab, you create two components in the new Microsoft Copilot Studio experience:

1. An **Expense Claims Approval Workflow** that receives expense data and the receipt content as JSON, evaluates the claim with an inline agent that has the reimbursement policy as knowledge, and requests human review when required.
2. An **Expense Claims Agent** powered by the GitHub Copilot harness that extracts the uploaded receipt into JSON and uses the workflow and Office 365 Outlook as tools.

After both components are published, you return to the workflow and add two Agent nodes. These nodes call the published agent with either an approved or rejected outcome, allowing the agent to use its email tool to notify the requestor.

> [!NOTE]
> Agents and workflows powered by the GitHub Copilot harness use Copilot Credits for usage-based billing. Building, testing, evaluating, and running the components in this lab might consume Copilot Credits.

---

## Core Concepts Overview

| Concept                    | Why it matters                                                                                                                             |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| **GitHub Copilot harness** | Provides the enhanced orchestration and reasoning runtime used by the new agent and workflow experiences.                                   |
| **Workflow**               | Coordinates deterministic steps, AI reasoning, branching, human review, and downstream actions in a visual designer.                        |
| **Inline agent**           | Performs a task that is specific to one workflow. Its instructions, knowledge, inputs, and output stay with the workflow.                   |
| **Inline agent knowledge** | Grounds the policy decision in an uploaded policy document instead of retrieving that document with a separate connector step.              |
| **Multimodal extraction**  | The agent reads the uploaded receipt image directly, so the user only supplies what the receipt doesn't contain, and no file storage step is required. |
| **Skill**                  | A portable ZIP package or Markdown file that gives an agent a repeatable procedure. This lab uploads a skill that extracts receipts into a consistent JSON shape. |
| **Human review**           | Pauses the workflow, requests information from an assigned reviewer through Outlook, and makes the response available to later steps.       |
| **Tools**                  | Give the agent access to external actions. This lab adds a workflow and Office 365 Outlook **Send an email (V2)** as tools.                 |
| **Agent node**             | Calls an inline agent or an existing published agent directly from a workflow.                                                              |

---

## Documentation and Additional Training Links

- [Microsoft Copilot Studio documentation](https://learn.microsoft.com/microsoft-copilot-studio/)
- [Agents powered by the GitHub Copilot harness](https://learn.microsoft.com/microsoft-copilot-studio/agents-experience/overview)
- [Workflows overview](https://learn.microsoft.com/microsoft-copilot-studio/workflows-experience/flows-overview)
- [Add an agent node to a workflow](https://learn.microsoft.com/microsoft-copilot-studio/workflows-experience/agent-node-workflow)
- [Request information from human review in workflows](https://learn.microsoft.com/microsoft-copilot-studio/workflows-experience/flows-request-for-information)
- [Add a workflow to your agent as a tool](https://learn.microsoft.com/microsoft-copilot-studio/agents-experience/tools-add-workflow)
- [Add a tool to an agent](https://learn.microsoft.com/microsoft-copilot-studio/agents-experience/add-tools-custom-agent)
- [Add an existing skill to an agent](https://learn.microsoft.com/microsoft-copilot-studio/agents-experience/skills-add-existing)
- [Office 365 Outlook connector](https://learn.microsoft.com/connectors/office365/)

---

## Prerequisites

- Access to Microsoft Copilot Studio and the new agents and workflows experience
- An environment with permission to create agents, workflows, connections, and tools
- Copilot Credits available for GitHub Copilot harness usage
- The following files from the lab assets folder, downloaded to your machine:
    - [Contoso_Expense_Claims_Policy.pdf](assets/Contoso_Expense_Claims_Policy.pdf) — the reimbursement policy the inline agent uses as knowledge
    - [receipt-json-extraction.zip](assets/receipt-json-extraction.zip) — the skill that turns an attached receipt into structured JSON

    Download the **raw** file for the skill package — GitHub's file preview won't give you a usable archive, and a `.zip` your browser has already unpacked won't import.
- The sample receipt images from the lab assets folder:
    - [57.78 EUR receipt](assets/human-in-the-loop-receipt-57.78.png)
    - [327.24 EUR receipt](assets/human-in-the-loop-receipt-327.24.png)
- Access to Office 365 Outlook
- The name and email address of a reviewer in your tenant

> [!IMPORTANT]
> Human review requests are sent through Outlook. The assigned reviewer must belong to your tenant.

---

## Summary of Targets

By the end of this lab, you will be able to:

- Create a workflow that an agent can call as a tool
- Upload a skill that extracts an attached receipt into a consistent JSON document
- Let an agent read an attached receipt and pass its content to a workflow as JSON
- Add a policy document as knowledge to an inline workflow agent
- Configure an inline agent to approve or reject a claim against explicit policy criteria
- Use structured agent output to route the workflow
- Send claims over 300 EUR to a human reviewer
- Create a minimal agent powered by the GitHub Copilot harness
- Add workflow and Outlook actions as agent tools
- Call the published agent from approved and rejected workflow paths
- Test automated approval, human review, and rejection scenarios

---

## Use Cases Covered

| Step | Use Case                                                                                              | Value added                                                                            | Effort |
| ---- | ----------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- | ------ |
| 1    | [Build the Expense Claims Approval Workflow](#use-case-1-build-the-expense-claims-approval-workflow) | Combines inline AI evaluation grounded in policy knowledge, conditional routing, and human review | 20 min |
| 2    | [Create the Expense Claims Agent](#use-case-2-create-the-expense-claims-agent)                       | Provides a conversational interface with a receipt extraction skill and reusable workflow and email tools | 10 min |
| 3    | [Complete the Workflow Response Paths](#use-case-3-complete-the-workflow-response-paths)             | Returns approved and rejected outcomes to the agent for email notification              | 10 min |
| 4    | [Test the End-to-End Solution](#test-the-end-to-end-solution)                                        | Verifies the automated, human-reviewed, and rejected workflow paths                     | 5 min  |

---

## Instructions by Use Case

---

## Use Case #1: Build the Expense Claims Approval Workflow

Create a workflow that receives expense details and the receipt content from an agent, evaluates the claim with an inline agent that uses the reimbursement policy as knowledge, and requests human review for an approved claim over 300 EUR.

| Use case                                   | Value added                                                                 | Estimated effort |
| ------------------------------------------ | --------------------------------------------------------------------------- | ---------------- |
| Build the Expense Claims Approval Workflow | Automates policy evaluation and routes only higher-value claims to a person | 20 minutes       |

### Create the workflow and trigger

1. Go to [copilotstudio.microsoft.com](https://copilotstudio.microsoft.com/).
2. Confirm that you are in the correct environment.
3. In the left navigation, select **Workflows**.
4. Select **New workflow**.
5. Name the workflow `Expense Claims Approval Workflow`.
6. Select the **Start** node to open its configuration pane.
7. Under **Trigger type**, select **When an agent calls the workflow**.

> [!NOTE]
> After you select the trigger, the node on the canvas might display **When an agent calls the flow**. This is the trigger required for exposing the published workflow as an agent tool.

![Select When an agent calls the workflow as the trigger type](images/workflow-trigger.png)

8. Select the automatically added **Respond to the agent** node, select **...** (**More commands**), and then select **Delete**.
9. Under **Trigger inputs**, select **Add an input** and create the following inputs:

   | Type   | Name                 | Description                                                     |
   | ------ | -------------------- | --------------------------------------------------------------- |
   | Date   | `Date`               | Date of the expense                                             |
   | Number | `Amount`             | Total claim amount in EUR                                       |
   | Text   | `Category`           | Expense category                                                |
   | Text   | `Reason`             | Business reason for the expense                                 |
   | Text   | `ReceiptJsonContent` | Receipt content extracted from the attachment, in JSON format   |
   | Text   | `Name`               | Requestor's display name                                        |
   | Text   | `Email`              | Requestor's email address                                       |

### Return immediately to the calling agent

10. Select **Add a step** after the trigger.
11. Add **Respond to the agent** and leave the response empty.

> [!TIP]
> The workflow returns control to the calling agent before it starts the potentially long-running review process. The remaining workflow steps continue after the response.

### Save the workflow

12. Select **Save**.

### Add the inline policy evaluation agent

13. Select **Add a step** after **Respond to the agent**.
14. On the Add panel, select **Agent**.
15. Under **Agent**, select **New agent for this workflow**.
16. Open the **Model** selector for the new workflow agent and select **Claude Sonnet 5**.
17. In **Instructions**, enter the following content. For each value in square brackets, use the dynamic content picker to insert the corresponding workflow trigger input.

```text
Review the following inputs:

Date: [Date]
Amount: [Amount]
Category: [Category]
Reason: [Reason]
Receipt: [ReceiptJsonContent]

Reject the expense claim when any of these criteria is met:

1. Effective period validation: Reject the claim if any expense date falls outside the policy's defined effective period.
2. Allowed category validation: Reject the claim if any expense isn't within the policy's approved expense categories.
3. Receipt validation: Reject the claim if a receipt is missing or if required receipt details defined in the policy are incomplete or unclear.
4. Budget and limit enforcement: Reject the claim if any category limit, monthly limit, or total reimbursement limit defined in the policy is exceeded.

Approve the claim only when none of the rejection criteria is met. Return a decision of Approve or Reject and a concise rationale. If rejecting the claim, identify every criterion that caused the rejection.
```

18. Under **Knowledge**, select **+** and upload [Contoso_Expense_Claims_Policy.pdf](assets/Contoso_Expense_Claims_Policy.pdf) from the lab assets folder.

> [!TIP]
> The inline agent reads the policy from its own knowledge, so the workflow doesn't need a connector step to retrieve the policy document.

19. Under **Output**, select **Custom structured output**.
20. Use the following JSON schema:

```json
{
  "type": "object",
  "properties": {
    "decision": {
      "type": "string",
      "enum": ["Approve", "Reject"],
      "description": "The final policy evaluation decision."
    },
    "rationale": {
      "type": "string",
      "description": "A concise explanation of the decision and all rejection criteria that were met."
    }
  },
  "required": ["decision", "rationale"],
  "additionalProperties": false
}
```

21. Rename the node to `Evaluate Expense Claim`.

![Configure the inline agent to evaluate the expense claim](images/workflow-inline-agent.png)

### Route the inline agent decision

22. Select **Add a step** after **Evaluate Expense Claim** and add **If/Else**.
23. Rename the node to `AI Decision`.
24. Configure the **If** branch: - **Property**: `decision` from **Evaluate Expense Claim** - **Operator**: **Is equal to** - **Value**: `Approve`

![Configure the AI decision condition for approved claims](images/workflow-ai-decision-condition.png)

The **If** branch handles policy-compliant claims. The **Else** branch handles rejected claims and will be connected to the rejection response in Use Case #3.

### Route higher-value claims to human review

25. On the **If** output of **AI Decision**, add another **If/Else** node.
26. Rename the node to `Amount Requires Review`.
27. Configure its **If** branch: - **Property**: `Amount` from the workflow trigger - **Operator**: **Greater than** - **Value**: `300`

The **If** branch requires human review. The **Else** branch is automatically approved and will be connected to the approved response in Use Case #3.

![Route claims over 300 EUR to the human review path](images/workflow-amount-condition.png)

28. On the **If** output of **Amount Requires Review**, select **Add a step**.
29. Select **Human review**.
30. Create or select a **Human review** connection.
31. Configure the node: - **Title**: `Expense Report Pending Approval: {Amount} EUR` - **Message**:

```md
**Details:**
{Name} has submitted an expense report totaling {Amount} EUR for your review.

**Reason:** {Reason}

**AI policy evaluation:** {rationale}
```

32. Replace each `{...}` placeholder with dynamic content: - `Amount`, `Name`, and `Reason` from the workflow trigger - `rationale` from **Evaluate Expense Claim**

> [!TIP]
> If the visual input for **Message** isn't working, select the code input option (**</>**) and use this alternative message:
>
> ```text
> **Details:**
> @{triggerOutputs()?['body/text_2']} has submitted an expense report totaling @{triggerOutputs()?['body/number']} EUR for your review.
>
> **Reason:** @{triggerOutputs()?['body/text_1']}
>
> **AI policy evaluation:** @{body('Evaluate_Expense_Claim')?['structuredOutput/rationale']}
> ```

33. In **Assigned to (first to respond)**, enter the name or email address of your reviewer.
34. For **Channel**, select **Outlook**.
35. Under **Inputs**, select **Add an input** and configure: - **Type**: **Yes/No** - **Name**: `Approve?` - **Description**: `Approve this expense claim?`

![Configure the Outlook human review request](images/workflow-human-review.png)

### Save and publish the initial workflow

36. Select **Save**.
37. Select **Publish**.

> [!IMPORTANT]
> A workflow must be published and must contain both **When an agent calls the flow** and **Respond to the agent** before it can be added to an agent as a workflow tool.

The workflow's final branches are intentionally incomplete. You finish them after creating and publishing the agent in Use Case #2.

---

### Congratulations! You've completed Use Case #1!

---

## Use Case #2: Create the Expense Claims Agent

Create a new agent powered by the GitHub Copilot harness. Keep its instructions focused and give it only the skill and tools required to submit expense claims and notify requestors. The agent reads the attached receipt first, so it asks the user for the category and business reason only.

| Use case                        | Value added                                                                             | Estimated effort |
| ------------------------------- | ----------------------------------------------------------------------------------------- | ---------------- |
| Create the Expense Claims Agent | Reads the receipt, collects the missing details, runs the workflow, and emails the outcome | 10 minutes       |

### Create the agent

1. Return to the Copilot Studio **Home** page.
2. Select the option to create a new **Agent** in the new experience.
3. Name the agent `Expense Claims Agent`.
4. Create the agent.
5. On the **Build** tab, open the **Model** selector and select **Claude Sonnet 5**.
6. Replace the agent's instructions with:

```text
When a user wants to submit an expense, ask for the receipt attachment if the user hasn't already provided one.

From the attachment, extract all information in json format (do not show the json back to the user).

Tell the user the date and the total amount you read from the receipt, and ask only for the expense category and the business reason.

Use the date and the total amount from the receipt for the workflow's Date and Amount inputs.

For the workflow's Name and Email inputs, use the display name and email address of the currently signed-in user. Do not ask the user to provide these values.

After the workflow is submitted, end the conversation.

---

When you receive a new expense approval status, send out the status email.
```

### Add the receipt extraction skill

7. In the components panel on the right, locate **Skills** and select **+**.
8. In **Add skill**, select **Upload a skill**.
9. Drag [receipt-json-extraction.zip](assets/receipt-json-extraction.zip) into the upload area, or select the upload area and browse to the file you downloaded.
10. Wait for Copilot Studio to validate the package, and confirm that the **receipt-json-extraction** chip appears in the **Skills** section.

> [!TIP]
> The skill defines how a receipt becomes JSON, so `ReceiptJsonContent` has the same shape on every run and carries the details the policy check depends on, such as guest counts, alcohol line items, and the tax split. Extraction stays in the skill, and the policy verdict stays in the workflow's inline agent.

> [!NOTE]
> A skill package must contain `SKILL.md` at the top level. If the upload is rejected, confirm that you downloaded the raw ZIP and that your browser didn't unpack it.

### Add the email tool

11. In the components panel on the right, select **Tools**.
12. Select **Add**, and then select **Connectors**.
13. Search for **Office 365 Outlook**.
14. Select **Send an email (V2)** and add it to the agent.
15. Sign in with your training account if prompted.
16. On the **Details** tab, set **Description** to:

```text
Use this tool to notify the expense claim requestor of the final approval outcome (approved or rejected).
```

17. Set **Authentication mode** to **Maker** and select your Office 365 Outlook connection.

![Configure Send an email V2 with maker authentication](images/agent-email-tool.png)

18. On the **Inputs** tab, configure the action:

    | Input       | How to fill         | Configuration                                                                                                                                                                  |
    | ----------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
    | **To**      | Dynamically with AI | `The email address of the expense claim requestor.`                                                                                                                            |
    | **Subject** | Custom value        | `Expense claim request outcome`                                                                                                                                                |
    | **Body**    | Dynamically with AI | `Write a concise email in HTML that states whether the expense claim was approved or rejected, includes the total amount, and includes the supplied rationale when available.` |

19. Select **Done**.

### Add the workflow tool

20. In the components panel on the right, select **Tools**.
21. Select **Add**, and then select **Workflow**.
22. Select **Expense Claims Approval Workflow**.
23. Select **Add**.
24. Open the workflow tool details and set its description to:

```text
Use this workflow to submit an expense claim for policy evaluation and approval. Before running it, take the date and amount from the receipt and collect the category and reason from the user. Use the signed-in user's name and email address for the requestor.
```

25. Select **Done**.

### Remove the default web search tool

26. In the agent's **Tools** section, remove **Search all websites** if it appears.
27. Confirm that the agent's configuration contains only:

- **Skills**: receipt-json-extraction
- **Tools**: Send an email (V2) and Expense Claims Approval Workflow

Use the following image as a reference for the instructions and tool layout.

![Review the agent instructions and its workflow and email tools](images/agent-tools-and-instructions.png)

28. Select **Save**.
29. Select **Publish**.

> [!IMPORTANT]
> The agent must be published before it appears under **An existing agent** in a workflow Agent node.

---

### Congratulations! You've completed Use Case #2!

---

## Use Case #3: Complete the Workflow Response Paths

Return to the workflow, add two Agent nodes that use the published Expense Claims Agent, and connect every approval and rejection path.

| Use case                             | Value added                                                                   | Estimated effort |
| ------------------------------------ | ----------------------------------------------------------------------------- | ---------------- |
| Complete the Workflow Response Paths | Sends the final workflow decision to the agent so it can notify the requestor | 10 minutes       |

### Add the approved response Agent node

1. Go to **Workflows** and open **Expense Claims Approval Workflow**.
2. Open the **Build** tab.
3. From the Add panel, select **Agent**.
4. Under **Agent**, select **An existing agent**.
5. Select **Expense Claims Agent**.
6. Rename the node to `Run Agent Approved`.
7. In **Message**, enter the following text and replace each `{...}` placeholder with dynamic content from the workflow trigger or review step:

```text
Send the expense claim requestor a final status email.

Final outcome of the approval: Approved
Human reviewer response: {Approve?}
Expense claim requestor: {Name} ({Email})
Expense claim amount: {Amount} EUR
Policy evaluation: {rationale}
```

![Configure the published agent with the approved outcome message](images/workflow-agent-response.png)

### Add the rejected response Agent node

8. Right-click **Run Agent Approved** and select **Duplicate**.
9. Rename the duplicated node to `Run Agent Rejected`.
10. Replace its **Message** with the following text, preserving the dynamic content values from the duplicated node:

```text
Send the expense claim requestor a final status email.

Final outcome of the approval: Rejected
Human reviewer response, when available: {Approve?}
Expense claim requestor: {Name} ({Email})
Expense claim amount: {Amount} EUR
Policy evaluation: {rationale}
```

### Route the human review response

11. Add an **If/Else** node after **Human review**.
12. Rename it to `Human Review Decision`.
13. Configure its **If** branch: - **Property**: `Approve?` from **Human review** - **Operator**: **Is equal to**
    - **Value**: `Yes`

### Connect all outcome paths

14. Connect the workflow nodes as follows:

    | Source                     | Output                                  | Destination                |
    | -------------------------- | --------------------------------------- | -------------------------- |
    | **AI Decision**            | **If** (`decision` is `Approve`)        | **Amount Requires Review** |
    | **AI Decision**            | **Else**                                | **Run Agent Rejected**     |
    | **Amount Requires Review** | **If** (`Amount` is greater than `300`) | **Human review**           |
    | **Amount Requires Review** | **Else**                                | **Run Agent Approved**     |
    | **Human review**           | Completion                              | **Human Review Decision**  |
    | **Human Review Decision**  | **If** (`Approve?` is `Yes`)            | **Run Agent Approved**     |
    | **Human Review Decision**  | **Else**                                | **Run Agent Rejected**     |

Only one of the two final Agent nodes runs for each claim.

![Complete workflow with approved and rejected agent response paths](images/workflow-routing.png)

15. Select **Save**.
16. Select **Publish**.

---

### Congratulations! You've completed Use Case #3!

---

## Test the End-to-End Solution

Run three tests. Each one uses a receipt image from the lab assets folder and exercises a different path: policy rejection, automatic approval, and human review.

- [57.78 EUR receipt](assets/human-in-the-loop-receipt-57.78.png) — dinner for two guests, no alcohol
- [327.24 EUR receipt](assets/human-in-the-loop-receipt-327.24.png) — a larger client dinner for four people, over the 300 EUR review threshold

The agent reads the date and total from the attachment, so you only supply the category and the business reason.

> [!TIP]
> Tests 1 and 2 use the same receipt and differ only in the reason you supply when the agent asks for it. That difference shows how a single documentation requirement in the policy decides between rejection and approval.

### Test 1: Rejected because attendee names are missing

1. Open **Expense Claims Agent**.
2. Select the **Preview** tab.
3. Enter `Submit an expense claim` and attach [57.78 EUR receipt](assets/human-in-the-loop-receipt-57.78.png) to the message.
4. Confirm that the agent loads the **receipt-json-extraction** skill, answers with the values it read from the receipt — a date of `16.06.2026` and a total of `57.78` EUR — and asks only for the expense category and the business reason. The agent must not print the extracted JSON.
5. Reply with the category and reason:

```text
Category: Business Meals (Client / Team). Reason: Customer workshop dinner.
```

6. Confirm that the agent invokes **Expense Claims Approval Workflow** with `Date` set to `2026-06-16` and `Amount` set to `57.78` from the receipt, and that its `ReceiptJsonContent` input contains two Caesar salads for `24.00` EUR, grilled salmon for `22.00` EUR, and cheesecake for `7.50` EUR, with a subtotal of `53.50` EUR, tax of `4.28` EUR, a total of `57.78` EUR, and a guest count of `2`.
7. In the workflow run inputs, confirm that **Name** and **Email** match the user signed in to the agent session. If any of `Date`, `Amount`, `ReceiptJsonContent`, **Name**, or **Email** is missing or incorrect, stop the test and refine the corresponding workflow input description before continuing.
8. Confirm that **Evaluate Expense Claim** returns `Reject`. The policy requires attendee names for the **Business Meals (Client / Team)** category, and neither the claim nor the receipt names them. Expect a rationale similar to:

```text
Receipt validation: FAIL - The Business Meals (Client / Team) category has an explicit
documentation condition: "Names of attendees and business purpose must be documented."
The business purpose is documented ("Customer workshop dinner"), but the receipt only shows
a guest count of 2 with no attendee names recorded anywhere in the claim or receipt. This
required detail is missing, making the documentation incomplete per policy. (The receipt
itself does otherwise contain vendor name, date, itemised items, subtotal, tax, and total.)
```

9. Confirm that **AI Decision** follows its **Else** output to **Run Agent Rejected**.
10. Confirm that no human review request is sent.
11. Confirm that the requestor receives a rejected **Expense claim request outcome** email for **57.78 EUR** that contains the policy rationale.

### Test 2: Automatically approved after adding attendee names

12. Start another expense claim and attach the same [57.78 EUR receipt](assets/human-in-the-loop-receipt-57.78.png).
13. When the agent asks for the category and reason, name the attendees this time:

```text
Category: Business Meals (Client / Team). Reason: Customer workshop dinner. Attendees: Sofia Ricci (Northwind Traders) and me. Business purpose: follow-up on the customer workshop.
```

14. Confirm that **Evaluate Expense Claim** now returns `Approve`. Every criterion passes: the expense date is inside the policy's effective period, the category is eligible, the receipt is itemised and the attendees and business purpose are documented, and `28.89` EUR per person stays below the `EUR 250 / person` limit with no alcohol on the receipt.
15. Confirm that **Amount Requires Review** follows its **Else** output because `57.78` isn't greater than `300`.
16. Confirm that no human review request is sent.
17. Confirm that **Run Agent Approved** runs and the requestor receives an approved **Expense claim request outcome** email for **57.78 EUR**.

### Test 3: Human review for an approved claim over 300 EUR

18. Start a third expense claim and attach [327.24 EUR receipt](assets/human-in-the-loop-receipt-327.24.png).
19. Confirm that the agent reports a date of `07.06.2026` and a total of `327.24` EUR, and then reply with a category and a reason that name all four attendees:

```text
Category: Business Meals (Client / Team). Reason: Customer workshop dinner. Attendees: Sofia Ricci (Northwind Traders), Marco Bianchi (Northwind Traders), Lena Weber (Contoso) and me. Business purpose: closing dinner for the customer workshop.
```

20. Confirm that the workflow receives `Date` `2026-06-07` and `Amount` `327.24`, and that `ReceiptJsonContent` contains four Caesar salads for `60.00` EUR, two grilled salmon for `104.00` EUR, two cheesecakes for `15.00` EUR, four sparkling waters for `12.00` EUR, and two beef tartars for `112.00` EUR, with a subtotal of `303.00` EUR, tax of `24.24` EUR, and a total of `327.24` EUR.
21. Confirm that **Evaluate Expense Claim** returns `Approve`: the four attendees and the business purpose are documented, `81.81` EUR per person stays well below the `EUR 250 / person` limit, and the receipt contains no alcohol.

> [!TIP]
> The receipt's printed guest counter still reads `2`, while the itemised lines and the documented attendee list describe four people. The policy asks for attendee names, not for the guest counter, so the names you supply in the reason are what makes this claim checkable.

> [!NOTE]
> The amount threshold doesn't override the policy evaluation. A claim rejected by the inline agent goes directly to **Run Agent Rejected**, as in Test 1. Only a policy-compliant claim over 300 EUR reaches **Human review**.

22. Confirm that **AI Decision** follows its **If** output and that **Amount Requires Review** follows its **If** output because `327.24` is greater than `300`.
23. Open Outlook and locate the human review request.
24. Verify that the request shows **327.24 EUR**, the requestor, the business reason, and the inline agent's policy rationale.
25. Set **Approve?** to **Yes**, and then submit the response.
26. Confirm that the workflow resumes through **Human Review Decision** and **Run Agent Approved**, and that the requestor receives an approved outcome email for **327.24 EUR**.
27. Repeat Test 3 and set **Approve?** to **No**. Confirm that **Human Review Decision** follows its **Else** output to **Run Agent Rejected** and that the requestor receives a rejected outcome email.

### Review activity and tool use

28. Open the agent's activity trace and verify for each submission that **Expense Claims Approval Workflow** receives `ReceiptJsonContent` with the extracted receipt and an `Amount` of `57.78` or `327.24`, matching the uploaded receipt.

29. Open the workflow run details and inspect the inputs and outputs for:

- **Evaluate Expense Claim**
- **AI Decision**
- **Amount Requires Review**
- **Human review**, when applicable
- The final approved or rejected Agent node

30. Verify that one final outcome email was sent for each test.

---

## Summary of Learnings

In this lab, you built an expense claims solution by combining a GitHub Copilot harness agent with a workflow:

- The agent uses an uploaded skill to read the attached receipt into a consistent JSON document, asks only for the details the receipt doesn't contain, and passes the extracted content to the workflow.
- The workflow handles branching and long-running human review without any file retrieval steps.
- An inline agent grounded in a policy document applies explicit policy criteria and returns a structured, auditable decision.
- Claims over 300 EUR receive an additional human decision through Outlook.
- Approved and rejected paths call the same published agent with different status messages.
- The agent uses its Outlook tool to close the loop with the requestor.

### Conclusions and Recommendations

- Use workflows for repeatable orchestration, system integration, conditions, and review steps.
- Use an inline agent when the reasoning task belongs to one workflow and doesn't need to be shared elsewhere.
- Keep extraction in a portable skill and the policy verdict in the workflow, so each part can change without breaking the other.
- Attach reference documents as inline agent knowledge instead of retrieving them with connector steps when the content is static.
- Let the agent extract structured data from attachments and pass it forward, so the workflow doesn't need file storage or retrieval, and the user types less.
- Return structured output from AI decisions when downstream logic must branch reliably.
- Keep agent instructions and tool descriptions specific so the orchestrator can select the correct tool and collect the required inputs.
- Route unmatched, rejected, and negative human responses explicitly. Never leave an outcome path unconnected.
- Respond to the calling agent before long-running review steps, and keep the final notification inside the automated process.
- Test automatic approval, human approval, human rejection, and policy rejection before publishing the solution for wider use.

By separating conversation, deterministic orchestration, AI judgment, and human oversight, you create an expense process that is faster for employees and easier for finance teams to govern.
