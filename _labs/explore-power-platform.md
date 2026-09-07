---
layout: lab
module: explore-power-platform
title: "Explore the Power Platform"
order: 400
duration: 30
difficulty: 100
lab_type: local
section: core_learning_path
journeys: ["quick-start", "business-user"]
description: "Take a guided tour through Power Apps, the Plan Designer, Power Automate, AI Builder, and Copilot Studio to learn where each capability lives before you build anything real."
---

# Explore the Power Platform

Take a guided tour through Power Apps, the Plan Designer, Power Automate, AI Builder, and Copilot Studio to learn where each capability lives before you build anything real.

---

## Lab Details

| Level | Persona | Duration   | Purpose                                                                                                                                                                                                                       |
| ----- | ------- | ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 100   | Maker   | 30 minutes | After completing this lab, participants will be able to identify the starting points for app development, automation, AI models, and agents, navigate the most important maker portals with confidence, and describe how the pieces of the Power Platform fit together. |

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
  - [Use Case #1: Power Apps and the Plan Designer](#use-case-1-power-apps-and-the-plan-designer)
  - [Use Case #2: Automation, AI Builder, and Copilot Studio](#use-case-2-automation-ai-builder-and-copilot-studio)
- [Summary of learnings](#summary-of-learnings)

---

## Why This Matters

**New to the Power Platform?** The hardest part is rarely the building — it is knowing which of the five maker portals you are supposed to open.

Think of it like moving into a new office building:

- **Without a tour**: You know your desk exists somewhere. You waste the first week asking where the printer, the meeting rooms, and the coffee machine are.
- **With a tour**: You know the layout on day one, so every later task starts with "I know exactly where that lives."

**Common challenges solved by this lab:**

- "I keep landing on the wrong maker portal and can't find the feature I was told about."
- "I don't know the difference between a prebuilt AI model and a custom one."
- "I've heard of the Plan Designer but have no idea what it actually produces."
- "I can't tell where an app ends and a flow or an agent begins."

**Half an hour of deliberate exploration now saves days of guesswork across every later lab in the course.**

---

## Introduction

The Power Platform is not a single product. It is a family of makers' tools — Power Apps for interfaces, Power Automate for automation, AI Builder for models and prompts, Dataverse for data, and Copilot Studio for agents — that share one environment, one security model, and one solution system.

**Real-world example:** A finance team wants to modernise expense handling. Today an employee emails a scanned receipt to a shared mailbox, someone re-types the amounts into a spreadsheet, and a manager approves by replying "ok". The Power Platform version of that same process is an app for submission, a flow for the approval routing, an AI model to read the receipt, and an agent to answer "where is my reimbursement?". Each of those four pieces lives in a different part of the platform.

This lab walks the whole building before you pick a room. You will not ship anything to production here. You will build a mental map that makes every later lab faster.

---

## Core Concepts Overview

| Concept              | Why it matters                                                                                                                                          |
| -------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Environment**      | The container for all your apps, flows, models, and data. Working in the wrong environment is the single most common cause of "my flow can't see my table". |
| **Power Apps**       | Where user-facing interfaces are built, either from a template, from data, or from scratch.                                                              |
| **Plan Designer**    | Turns a plain-language business scenario into user roles, user stories, a data model, and suggested apps and flows — a starting point, not a final design. |
| **Power Automate**   | Where triggers and connectors turn events (a new file, a new email, a schedule) into automated actions.                                                  |
| **AI Builder**       | Prebuilt and custom AI models plus prompts, usable directly from apps and flows without writing model code.                                              |
| **Copilot Studio**   | Where conversational agents are authored, given instructions and knowledge, and published to channels.                                                   |
| **Connector**        | The reusable authentication and API wrapper that lets any of the above talk to Outlook, OneDrive, SharePoint, ServiceNow, and hundreds of other systems. |

---

## Documentation and Additional Training Links

* [Power Platform documentation](https://learn.microsoft.com/power-platform/)
* [Power Apps documentation](https://learn.microsoft.com/power-apps/)
* [Plan Designer overview](https://learn.microsoft.com/power-apps/maker/plan-designer/plan-designer)
* [Power Automate documentation](https://learn.microsoft.com/power-automate/)
* [AI Builder overview](https://learn.microsoft.com/ai-builder/overview)
* [Microsoft Copilot Studio documentation](https://learn.microsoft.com/microsoft-copilot-studio/)

---

## Prerequisites

* Access to a Power Platform environment where you may create apps, flows, and agents.
* A Microsoft 365 account with OneDrive for Business, used by the Power Automate use case.
* Permission to create new connections when a connector asks for authentication.
* A browser that allows pop-ups for the maker portals — connector authentication opens in a pop-up window.

> [!TIP]
> For a clean lab experience, use a separate browser profile in Microsoft Edge. This keeps lab work and personal data apart, and it prevents an already signed-in personal account from hijacking the sign-in prompt.
>
> Select the profile icon in the top-right corner of Edge, choose **More profiles** > **Add Work or School Profile**, follow the prompts, and switch to that profile whenever you work on the lab.

---

## Summary of Targets

In this lab, you'll walk the five entry points of the Power Platform and build a working mental map of the toolset. By the end of the lab, you will:

* Create an app from a template in Power Apps and describe how its screens and navigation are structured.
* Generate a plan from a natural-language business scenario and edit one of its user stories.
* Generate a flow from a natural-language description and identify its trigger, connectors, and actions.
* Distinguish a prebuilt AI model from a custom one and locate the receipt-extraction model.
* Create a simple agent in Copilot Studio with instructions and sample questions.
* Name the right starting portal for a given business requirement.

---

## Use Cases Covered

| Step | Use Case                                                                                | Value added                                                                                       | Effort |
| ---- | --------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- | ------ |
| 1    | [Power Apps and the Plan Designer](#use-case-1-power-apps-and-the-plan-designer)       | Shows both ways to start an app: from a ready-made template and from a described business scenario. | 15 min |
| 2    | [Automation, AI Builder, and Copilot Studio](#use-case-2-automation-ai-builder-and-copilot-studio) | Connects events to actions, surfaces the AI models available without code, and creates a first agent. | 15 min |

> [!NOTE]
> The sections within each use case are independent. Work through them in any order and plan roughly 5 to 7 minutes each. The goal is to **get to know the interfaces — you do not need to build anything productive yet**, so move on as soon as you can describe what a screen is for.

---

## Instructions by Use Case

---

## Use Case #1: Power Apps and the Plan Designer

Create an app from a template, then let the Plan Designer turn a described business scenario into roles, stories, a data model, and suggested components.

| Use case                         | Value added                                                                                       | Estimated effort |
| -------------------------------- | --------------------------------------------------------------------------------------------------- | ---------------- |
| Power Apps and the Plan Designer | Shows both ways to start an app: from a ready-made template and from a described business scenario. | 15 minutes       |

**Summary of tasks**

In this section, you'll create an app from a template, explore the generated screens and controls in the Studio, and then generate and edit a plan from a plain-language scenario.

**Scenario:** Your organisation has asked you to evaluate the Power Platform for an expense management solution. Before designing anything, you want to see what the platform generates on its own from a template and from a described requirement.

### Objective

Locate the two Power Apps starting points and understand what each one produces.

---

### Step-by-step instructions

#### App from a template

1. Open the maker portal at [https://make.powerapps.com/](https://make.powerapps.com/) and sign in.

2. Confirm the environment shown in the top-right corner. Use the same environment for every section of this lab.

3. Select **Apps** in the left menu.

4. In the top menu, select **+ New app** > **Start with an App Template**.

5. Select the **Book a Room** template, give it a name such as `Room Booking App`, and select **Next**.

6. When prompted to allow connectors, select **Allow**.

   > [!TIP]
   > You might need to allow pop-ups in the browser for the connector authentication window to appear.

7. Open the app in the **Studio** and explore the interface. Note which elements — screens, controls, galleries, forms — are included, and how the navigation between screens is structured.

#### Scenario with the Plan Designer

The Plan Designer takes a business scenario described in natural language and generates **user roles and user stories**, a **data model**, and suggestions for **apps and flows**. You then edit the user stories directly in the designer.

8. Select **Home** in the left menu, then **Start with a Plan** > **Create a plan**.

9. Describe your scenario. Use this text:

    ```text
    We need an expense management solution. Roles: Employees and Managers. Processes: Submit expenses with receipt (image/PDF), approve or reject with comments, trigger payment. Data: Expenses (amount, category, date, project), Receipt, Employee, Approvals. Please create user stories in English with acceptance criteria. Generate apps and at least one flow for notifications.
    ```

10. Select **Generate** and wait for the plan to be created.

11. In the **Requirements Agent**, select **Edit** and add this user story:

    ```text
    As an employee, I want to capture expenses with a receipt photo via mobile.
    ```

12. Select **Keep**, then **Looks good**.

13. Read the suggested processes and confirm with **Looks good**.

14. Explore the data model by selecting **Show Details** in the top right, next to **Data model**. Select **Back** when you are done, then **Looks good**.

15. Explore the **Technology** section and confirm with **Looks good**.

16. **Save the plan**. The generated artifacts are stored in a solution.

    > [!IMPORTANT]
    > The Plan Designer produces a starting point, not a finished design. Treat the generated data model and user stories as a first draft to review with the business, not as a specification to build against directly.

17. *Bonus*: Hover over the suggested model-driven app and select the **plus sign** to create the app. Open it in the Studio and explore what was generated.

---

### Congratulations! You've completed Use Case #1!

---

### Test your understanding

**Key takeaways:**

* **Two starting points, two purposes** – A template gives you a working app to modify. The Plan Designer gives you a described solution to refine before any app exists.
* **Environments are the boundary** – Everything you create in this lab lands in the environment shown in the top-right corner. Components in different environments cannot see each other.
* **Generated is not final** – Both paths produce editable artifacts. Reviewing what was generated is part of the work, not an optional extra.

**Lessons learned & troubleshooting tips:**

* If the template app fails to open, check that pop-ups are allowed and that you selected **Allow** on the connector prompt.
* If the plan generation appears stuck, refresh the page — the plan is saved server-side as it is generated.
* If you cannot find the app you created later, you were most likely in a different environment.

**Challenge: Apply this to your own use case**

* Which process in your own organisation would you describe to the Plan Designer first?
* Which of the generated user stories would your business stakeholders reject, and why?

---

---

## Use Case #2: Automation, AI Builder, and Copilot Studio

Generate a flow from a description, browse the prebuilt AI models and prompts, and create a first conversational agent.

| Use case                                   | Value added                                                                                           | Estimated effort |
| ------------------------------------------ | ------------------------------------------------------------------------------------------------------- | ---------------- |
| Automation, AI Builder, and Copilot Studio | Connects events to actions, surfaces the AI models available without code, and creates a first agent. | 15 minutes       |

**Summary of tasks**

In this section, you'll generate a Power Automate flow from natural language, inspect its trigger and actions, explore AI Builder prompts and models, and create a simple agent in Copilot Studio.

**Scenario:** Having seen how apps get built, you now want to understand what happens between them — the automation that reacts to events, the AI that reads documents, and the agent that answers questions about the process.

### Step-by-step instructions

#### Power Automate

1. Open [https://make.powerautomate.com/](https://make.powerautomate.com/).

2. Confirm you are in the same environment used in Use Case #1 — check the top-right corner.

3. Select **Home** in the left menu.

4. In the *Describe in detail how you want your automation to work* box, enter:

    ```text
    Send me an email when a new file is created in my OneDrive folder.
    ```

5. Select **Generate** and wait for the flow suggestions.

6. Choose **Keep it and continue** > **Create flow**.

7. Inspect the generated flow. Identify which **trigger**, **connectors**, and **actions** are used, and open each one to see its configuration.

#### AI Builder

8. Return to [https://make.powerapps.com/](https://make.powerapps.com/) and select **AI hub**.

9. Explore the **Prompts** and **AI models** sections.

10. Under **AI models** > **Documents**, select **Extract information from receipts** and consider how it would apply to receipt recognition in the expense solution from Use Case #1.

    > [!TIP]
    > Note the difference between a **prebuilt model**, which is trained by Microsoft and usable immediately, and a **custom model**, which you train on your own documents. Prebuilt models cost less setup effort; custom models handle formats the prebuilt ones do not recognise.

#### Copilot Studio (optional)

11. Open [https://copilotstudio.microsoft.com/](https://copilotstudio.microsoft.com/).

12. Select **+ New Agent** and enter a name such as `Expense FAQ Agent`.

13. For **Instructions**, enter:

    ```text
    Answer questions about the expense process, submission deadlines, and approval responsibilities.
    ```

14. Add these **sample questions**:

    ```text
    How do I submit an expense?
    Who approves my expenses?
    ```

15. Test the agent in the test pane and observe how it answers without any knowledge source attached yet.

---

### Congratulations! You've completed Use Case #2!

---

### Test your understanding

* Which trigger did Power Automate select for the OneDrive scenario, and what would change if you had asked for a scheduled flow instead?
* When would you reach for a prebuilt AI model, and when would training a custom model be worth the effort?
* Your agent answered questions without a knowledge source. Where did those answers come from, and why is that a problem for a real expense policy?

**Challenge: Apply this to your own use case**

* Which recurring manual step in your own work is a candidate for a trigger-based flow?
* Which document type does your team re-type by hand today that an AI model could read instead?

---

## Summary of learnings

True learning comes from doing, questioning, and reflecting — so let's put your skills to the test.

To get the most out of the Power Platform:

* **Start from the environment** – Confirm which environment you are in before you create anything. Most "my component disappeared" problems are environment problems.
* **Pick the right entry point** – Interfaces belong in Power Apps, event-driven automation in Power Automate, document and text intelligence in AI Builder, and conversation in Copilot Studio.
* **Treat generation as a draft** – The Plan Designer and natural-language flow authoring accelerate the first 80%; the review is where the design actually happens.
* **Prefer prebuilt before custom** – A prebuilt AI model that covers 90% of your documents beats a custom model you have to maintain.
* **Connectors are the glue** – Nearly every integration question resolves to "is there a connector, and do I have permission to create the connection?"

---

### Conclusions and recommendations

**Power Platform orientation golden rules:**

* Always verify the environment selector before creating or troubleshooting a component.
* Allow pop-ups in your lab browser profile — most authentication failures trace back to a blocked window.
* Name every artifact you create, immediately. Untitled apps and flows become unfindable within a week.
* Read what the generator produced before accepting it; the data model is the part most worth challenging.
* Keep lab work in a dedicated browser profile so personal and work identities never collide.
* When a requirement arrives, ask "is this an interface, an automation, a model, or a conversation?" before opening any portal.

By following these principles, you'll spend your time on the business problem instead of hunting for the feature that solves it.

---

### Reflection

- Which tool did you find most intuitive?
- Where did you have difficulties?
- What idea for a business use case came to you while exploring?

---
