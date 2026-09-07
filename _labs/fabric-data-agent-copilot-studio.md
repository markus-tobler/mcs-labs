---
layout: lab
module: fabric-data-agent-copilot-studio
title: "Fabric Data Agent with Copilot Studio"
order: 430
duration: 55
difficulty: 300
lab_type: local
section: advanced_labs
journeys: ["developer"]
description: "Provision a Power BI semantic model, build and publish a Fabric Data Agent that answers natural-language questions over selected tables, inspect the DAX it generates, and consume it from a Copilot Studio agent."
---

# Fabric Data Agent with Copilot Studio

Provision a Power BI semantic model, build and publish a Fabric Data Agent that answers natural-language questions over selected tables, inspect the DAX it generates, and consume it from a Copilot Studio agent.

---

## Lab Details

| Level | Persona          | Duration   | Purpose                                                                                                                                                                                                                                |
| ----- | ---------------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 300   | Maker, Developer | 55 minutes | After completing this lab, participants will be able to scope a Fabric Data Agent to the right tables, write AI instructions that steer natural-language-to-DAX generation, publish and validate the agent, and connect it to a Copilot Studio agent as a data-answering capability. |

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
  - [Use Case #1: Build and Publish the Fabric Data Agent](#use-case-1-build-and-publish-the-fabric-data-agent)
  - [Use Case #2: Consume the Data Agent from Copilot Studio](#use-case-2-consume-the-data-agent-from-copilot-studio)
- [Summary of learnings](#summary-of-learnings)

---

## Why This Matters

**Worried that "chat with your data" is a demo trick?** It is — right up until you scope the data and state your assumptions. Then it becomes a reporting layer.

Think of a new analyst joining your team:

- **Without context**: They can see every table in the warehouse. Their first revenue number is wrong because they picked the wrong date column, and nobody notices for a week.
- **With context**: You tell them which five tables matter, what "revenue" means, and that the default period is the last twelve months. Their first answer is right, and it says which assumption it used.

**Common challenges solved by this lab:**

- "Our natural-language answers are plausible but silently use the wrong table."
- "I can't tell how the answer was calculated, so I can't defend it in a meeting."
- "Every question needs a report request, and reports take days."
- "I have a data agent in Fabric but no way to put it in front of the people who ask the questions."

**Fifty-five minutes here gets you from an unscoped semantic model to a governed, inspectable answer inside a conversational agent.**

---

## Introduction

A Fabric Data Agent sits between a natural-language question and a semantic model. It selects the relevant tables, generates a query — DAX, in the case of a Power BI semantic model — runs it, and narrates the result. The quality of that chain depends almost entirely on two things you control: which tables it is allowed to see, and what you tell it about them.

**Real-world example:** A sales leadership team asks the same five questions every Monday — top products, win rate, pipeline by owner, largest losses, month-over-month trend. Each one becomes a request to the BI team. With a scoped data agent, those questions get answered in the chat where they are asked, with the generated query available for anyone who wants to check the arithmetic.

In this lab you provision a sample semantic model, create a data agent over exactly five tables, write instructions that define both domain vocabulary and default assumptions, publish it, validate it by inspecting generated DAX, and finally add it to an existing Copilot Studio agent so the capability reaches end users.

---

## Core Concepts Overview

| Concept                    | Why it matters                                                                                                                                                             |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Semantic model**         | The structured source the agent queries. Table and column names are part of the prompt surface — cryptic names produce cryptic reasoning.                                   |
| **Fabric Data Agent**      | The preview artifact that turns a natural-language question into a generated query against selected sources, then narrates the result.                                     |
| **Data-source scoping**    | Restricting the agent to relevant tables is the single highest-leverage accuracy lever. Fewer candidates means fewer wrong joins.                                           |
| **AI instructions**        | System-level guidance that carries domain definitions, tone, and default assumptions. For semantic models this is the only steering mechanism available in preview.        |
| **Generated query (DAX)**  | The audit trail. Inspecting it is how you confirm the agent used the tables and filters you intended rather than arriving at the right number by accident.                 |
| **Publish state**          | An agent must be published and **Running** before anything outside Fabric can consume it.                                                                                  |
| **Copilot Studio agent node** | Lets a conversational agent delegate data questions to the Fabric agent, so users never have to know which system answered.                                              |

---

## Documentation and Additional Training Links

* [Fabric data agent concept](https://learn.microsoft.com/fabric/data-science/concept-data-agent)
* [Create a Fabric data agent](https://learn.microsoft.com/fabric/data-science/how-to-create-data-agent)
* [Fabric data agent tenant settings](https://learn.microsoft.com/fabric/data-science/data-agent-tenant-settings)
* [Consume a Fabric data agent in Copilot Studio](https://learn.microsoft.com/fabric/data-science/data-agent-microsoft-copilot-studio)
* [Microsoft Copilot Studio documentation](https://learn.microsoft.com/microsoft-copilot-studio/)
* [Power BI semantic models](https://learn.microsoft.com/power-bi/connect-data/service-datasets-understand)

---

## Prerequisites

Fabric Data Agent is currently in **preview**. Confirm the following before you start:

* A Fabric capacity: **F2 or higher**, or a **Power BI Premium per capacity (P1+)** with Fabric enabled.
  *Why?* The capacity enables the preview feature and the underlying AI workload.
* Tenant switches enabled in the Fabric admin portal: **Fabric Data Agent**, **Copilot**, **Cross-geo processing**, **Cross-geo storing**, and **XMLA endpoints** for semantic models.
  *Why?* These govern AI features, data locality, and model metadata access.
* A workspace where you have **Build + Write** permissions.
  *Why?* You must add a semantic model as a source and publish the agent.
* A Power BI semantic model with relevant tables — this lab uses the **Artificial Intelligence Sample**.
  *Why?* The agent needs structured data sources; it supports up to five in total across warehouses, lakehouses, KQL databases, and semantic models.
* Access to Microsoft Copilot Studio with permission to edit the destination agent.
  *Why?* Required to add and invoke the Fabric Data Agent inside Copilot Studio.
* An existing Copilot Studio agent to extend. This lab uses an "Ask Me Anything" agent; any agent you can edit will do.

> [!IMPORTANT]
> If any tenant switch is missing, coordinate with a Fabric or Power BI admin before proceeding. Preview features change — if the UI does not match these steps, consult the current documentation.

---

## Summary of Targets

In this lab, you'll build a governed natural-language interface over sales data and put it in front of users. By the end of the lab, you will:

* Locate and copy the **Artificial Intelligence Sample** semantic model into a workspace you control.
* Create a Fabric Data Agent and attach the semantic model as its data source.
* Scope the agent to five relevant tables and explain why scoping improves accuracy.
* Write AI instructions that define domain vocabulary, tone, and a default time range.
* Publish the agent and confirm it reaches the **Running** state.
* Validate answers in Fabric and inspect the generated DAX.
* Add the published agent to a Copilot Studio agent and test delegation end to end.

---

## Use Cases Covered

| Step | Use Case                                                                                    | Value added                                                                                     | Effort |
| ---- | ------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- | ------ |
| 1    | [Build and Publish the Fabric Data Agent](#use-case-1-build-and-publish-the-fabric-data-agent) | Produces a scoped, instructed, published agent whose answers state their assumptions.          | 35 min |
| 2    | [Consume the Data Agent from Copilot Studio](#use-case-2-consume-the-data-agent-from-copilot-studio) | Puts the capability in the conversation where users actually ask their questions.               | 20 min |

---

## Instructions by Use Case

---

## Use Case #1: Build and Publish the Fabric Data Agent

Prepare the semantic model, create the agent, scope its tables, instruct it, and validate the DAX it generates.

| Use case                                | Value added                                                                            | Estimated effort |
| --------------------------------------- | ------------------------------------------------------------------------------------------ | ---------------- |
| Build and Publish the Fabric Data Agent | Produces a scoped, instructed, published agent whose answers state their assumptions. | 35 minutes       |

**Summary of tasks**

In this section, you'll copy a sample semantic model into your workspace, create a data agent over it, restrict it to five tables, write AI instructions, publish it, and test the answers against the generated DAX.

**Scenario:** Sales leadership asks the same analytical questions every week. You want to answer them conversationally without giving up the ability to show how each number was produced.

### Objective

Create a data agent whose scope, vocabulary, and default assumptions are explicit and auditable.

---

### Step-by-step instructions

#### Prepare and inspect the sample semantic model

1. Open [https://app.powerbi.com](https://app.powerbi.com) and sign in.

    *Why?* You need to locate and reuse an existing sample semantic model quickly.

2. Navigate to **Learn › Samples** — or **Browse › All**, depending on the UI — and open the **Artificial Intelligence Sample** report.

    *Why?* It provides pre-modelled sales and opportunity data for exploration.

3. In the report header, choose **Save a copy** to your workspace if prompted.

    *Why?* This ensures the linked semantic model sits in a workspace you can modify.

4. Go to **My Workspace › Semantic models** — or **Datasets + dataflows** — and locate **Artificial Intelligence Sample**.

    *Why?* Confirms you have the underlying semantic model object, not just the report.

5. Open the model via the ellipsis: **… › Open semantic model**.

    *Why?* Lets you review table names and measures before crafting instructions.

6. Identify these tables — names may vary slightly: **Accounts**, **Opportunities**, **Opportunity Calendar**, **Owners**, **Products**.

    *Why?* You will enable only these tables to improve query precision.

#### Create the data agent

7. In the same workspace, select **New item › Data agent (preview)**.

8. Enter the name `DataAnalysisAgent` and select **Create**.

9. In the **Explorer** pane, choose **+ Data source** and pick the **Artificial Intelligence Sample** semantic model.

    *Why?* This attaches your semantic model as the agent's first data source.

10. In the table selector, check only **Accounts**, **Opportunities**, **Opportunity Calendar**, **Owners**, and **Products**. Deselect everything else.

    *Why?* Limiting scope reduces noise and speeds up natural-language-to-DAX generation.

11. *Optional*: rename cryptic tables or columns in the semantic model before proceeding.

    *Why?* Clear schema names measurably improve the model's ability to map natural language to the right objects.

    > [!NOTE]
    > You can add up to five sources in total, mixing semantic models with lakehouses and warehouses. Keep this lab to one source so the DAX inspection stays focused.

#### Add AI instructions

12. In the agent, select **AI instructions** — or the **Data agent instructions** button.

    *Why?* This provides system-level guidance that influences tool and table selection.

13. Paste the following instruction block:

    ```text
    - Table 'Accounts' describes customer account entities.
    - Table 'Opportunities' lists won and lost opportunities including value and status.
    - Table 'Opportunity Calendar' provides date intelligence for trending.
    - Table 'Products' holds product catalog and categories.
    - Table 'Owners' maps internal sales reps / account managers.

    When a question asks about revenue, won vs lost, or pipeline trends, leverage Opportunities with related calendar for date grouping.
    Summaries must be concise. Answer in a professional, analytical tone.
    If a time range isn't specified, default to the last 12 full calendar months and state that assumption.
    Return aggregated figures with appropriate units (e.g., currency) and a short narrative insight.
    ```

    *Why?* This embeds domain clarification, default assumptions, and tone — which improves reproducibility and user trust.

14. Save the instructions panel.

    > [!IMPORTANT]
    > Per current preview limitations, example query pairs are **not** supported for semantic models; they are available for lakehouses, warehouses, and KQL databases. Instructions are the only steering mechanism here, so make them explicit.

#### Describe and publish

15. Enter a description:

    ```text
    Answers analytical questions regarding accounts, opportunities, and sales figures.
    ```

    *Why?* This helps consumers understand the agent's scope in selection dialogs.

16. Select **Publish**.

    *Why?* Publishing puts the agent into the runnable state required for external consumption.

17. Confirm the status changes to **Running**, refreshing if needed.

#### Validate inside Fabric

18. In the agent chat panel, ask:

    ```text
    What are our top 5 products by won opportunity value in the last quarter?
    ```

    *Why?* This validates basic ranking combined with time filtering.

19. Expand **Steps** or **Generated query**, if available, to view the DAX produced.

    *Why?* This confirms the intended tables and measures were chosen and the assumptions applied correctly.

20. Ask a follow-up:

    ```text
    Break that down month over month.
    ```

    *Why?* Tests conversational continuity and use of the calendar table.

21. Test the default assumption:

    ```text
    Total won revenue?
    ```

    *Why?* Confirms the "last 12 months" default logic and that the response states the assumption it used.

22. If results reference unexpected columns or apply the wrong filters, refine the AI instructions — for example, explicitly naming the revenue measure — and re-test.

    *Why?* Iterative instruction refinement is the available tuning mechanism, since few-shot examples are not supported for semantic models.

---

### Congratulations! You've completed Use Case #1!

---

### Test your understanding

**Key takeaways:**

* **Scope beats prompting** – Deselecting irrelevant tables removes whole classes of wrong answers that no instruction could reliably prevent.
* **Defaults must be stated** – An agent that silently picks a time range produces numbers nobody can reconcile. One that announces "last 12 full calendar months" produces numbers people can check.
* **The generated query is the proof** – A right answer from the wrong table is a coincidence waiting to become an incident.

**Lessons learned & troubleshooting tips:**

* If the agent cannot be created, the capacity or a tenant switch is the usual cause — check both before debugging the model.
* If answers reference tables you deselected, re-open the table selector; scoping changes require a re-publish to take effect.
* If the status stays outside **Running**, refresh the workspace item list before assuming the publish failed.

**Challenge: Apply this to your own use case**

* Which five tables in your own model would you expose first, and which would you deliberately hide?
* What default assumption does your organisation make implicitly today that an agent should state out loud?

---

---

## Use Case #2: Consume the Data Agent from Copilot Studio

Add the published Fabric agent to a Copilot Studio agent so users can ask data questions in the conversation they already use.

| Use case                                  | Value added                                                                       | Estimated effort |
| ----------------------------------------- | ------------------------------------------------------------------------------------- | ---------------- |
| Consume the Data Agent from Copilot Studio | Puts the capability in the conversation where users actually ask their questions. | 20 minutes       |

**Summary of tasks**

In this section, you'll add the Fabric agent to an existing Copilot Studio agent, create the connection, describe the capability, and verify that the host agent delegates data questions correctly.

**Scenario:** The data agent works in Fabric, but the people who ask sales questions live in Teams and talk to an existing assistant. The capability has to reach them there.

### Step-by-step instructions

1. Open [https://copilotstudio.microsoft.com](https://copilotstudio.microsoft.com) and select your existing "Ask Me Anything" agent.

    *Why?* This host agent will route relevant queries to the Fabric data agent.

2. Navigate to **Agents › Add**.

3. Choose **Microsoft Fabric agent**.

4. Under **Connection**, select **Create new connection**, authenticate with the same account you used in Fabric, and finish creating the connection.

    *Why?* This establishes secure delegated access to the published data agent.

5. Select **Next**, pick your `DataAnalysisAgent`, then choose **Add agent**.

    *Why?* This links the published artifact to the Copilot Studio agent runtime.

6. In **Additional details**, set the description:

    ```text
    Answer questions regarding sales figures, accounts and opportunities.
    ```

    *Why?* The description is what the orchestrator reads when deciding whether to route a question here. A vague description produces unreliable routing.

7. Select **Save**.

8. In the test canvas, ask:

    ```text
    What are our largest lost opportunities?
    ```

    *Why?* This verifies that the host agent delegates to the Fabric Data Agent and returns a DAX-driven answer.

9. Review any source or reasoning panels shown.

    *Why?* This validates transparent tool invocation — you should be able to see that the Fabric agent, not the host model, produced the number.

    > [!TIP]
    > If the host agent answers from its own knowledge instead of delegating, the description in step 6 is almost always the cause. Make it name the entities users will ask about — accounts, opportunities, revenue, owners, products.

---

### Congratulations! You've completed Use Case #2!

---

### Test your understanding

* What determines whether the host agent routes a question to the Fabric agent rather than answering itself?
* A user asks "how did we do last month?" — which component supplies the definition of "did"?
* If the underlying semantic model gains a sixth table tomorrow, what has to change and where?

**Challenge: Apply this to your own use case**

* Publish the host agent to a channel your team already uses and watch which questions get misrouted.
* Add a second data source to the Fabric agent and observe how scoping decisions compound.

---

## Summary of learnings

True learning comes from doing, questioning, and reflecting — so let's put your skills to the test.

To get the most out of Fabric Data Agents:

* **Scope first, instruct second** – Table selection removes wrong answers; instructions shape the remaining right ones.
* **Name things for humans** – The agent reads your schema names. Cryptic column names are a prompt-quality problem, not just a documentation problem.
* **State assumptions in the answer** – A response that declares its time range can be checked; one that hides it cannot.
* **Inspect the generated query** – Treat DAX inspection as part of testing, not as a debugging step of last resort.
* **Describe the capability precisely** – In a multi-agent setup, the description is the routing logic. Vague descriptions are the main cause of "it just answered from memory".

---

### Conclusions and recommendations

**Fabric Data Agent golden rules:**

* Verify capacity and tenant switches before you start building — most blocked labs stop here.
* Attach one source at a time and validate it before adding the next.
* Re-publish after every scoping or instruction change; consumers see the published version only.
* Write instructions that define vocabulary ("revenue means…"), not just table purposes.
* Test the assumption path explicitly — ask a question with no time range and check what the agent says it did.
* Give the Copilot Studio agent description the same care as the AI instructions; it decides whether the capability is ever used.

By following these principles, you'll deliver conversational analytics people can trust enough to act on.

---
