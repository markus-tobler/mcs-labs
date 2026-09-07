---
layout: lab
module: sequential-multi-agent
title: "Sequential Multi-Agent Product Developer"
order: 410
duration: 45
difficulty: 200
lab_type: local
section: intermediate_labs
journeys: ["business-user", "developer"]
description: "Build a Product Developer Agent that chains three specialised AI steps - a content summarizer, a marketing writer, and a proofing agent - into one Copilot Studio topic that turns a raw product description into polished marketing copy."
---

# Sequential Multi-Agent Product Developer

Build a Product Developer Agent that chains three specialised AI steps — a content summarizer, a marketing writer, and a proofing agent — into one topic that turns a raw product description into polished marketing copy.

---

## Lab Details

| Level | Persona | Duration   | Purpose                                                                                                                                                                                                                                    |
| ----- | ------- | ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 200   | Maker   | 45 minutes | After completing this lab, participants will be able to implement a sequential multi-agent pattern inside a single Copilot Studio topic, author AI Builder prompts with typed inputs, chain their outputs through topic variables, and read a topic's YAML definition in Code view. |

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
  - [Use Case #1: Build the Agent and the Topic Skeleton](#use-case-1-build-the-agent-and-the-topic-skeleton)
  - [Use Case #2: Attach the Prompts and Test the Chain](#use-case-2-attach-the-prompts-and-test-the-chain)
- [Summary of learnings](#summary-of-learnings)

---

## Why This Matters

**Tempted to solve everything with one giant prompt?** Most makers start there, and most end up with output they cannot debug.

Think of a marketing team:

- **Without specialisation**: One person researches the product, writes the copy, and proofreads it. When the result is off, nobody can say which of the three jobs went wrong.
- **With specialisation**: An analyst extracts the facts, a copywriter drafts, an editor polishes. Each hand-off is inspectable, and each role can be improved on its own.

**Common challenges solved by this lab:**

- "My single prompt does too much and I can't tell which instruction it ignored."
- "I need the output of one AI step to become the input of the next."
- "I don't understand what the visual topic editor is actually generating underneath."
- "I want repeatable, consistently formatted output rather than a different structure every run."

**Forty-five minutes here gives you a decomposition pattern you will reuse in almost every agent you build afterwards.**

---

## Introduction

A sequential multi-agent pattern splits one complex task into a chain of narrow ones. Each step has a single responsibility, a typed input, and a typed output. The next step consumes what the previous one produced.

**Real-world example:** A product team drops a bullet list of features into a channel and asks marketing for launch copy. Today that takes two days and three revisions. With this pattern, the bullet list goes into an agent: the first step identifies key features, target audience, and unique selling points; the second step drafts around 150 words of copy; the third step corrects grammar, tone, and formatting. What comes back is a consistent draft in seconds, and each stage can be tuned independently when the tone is wrong.

In this lab you build exactly that chain inside one Copilot Studio topic. You will paste the topic's YAML definition directly into Code view, then attach two AI Builder prompts to the two model-invocation nodes, and finally trace how a value flows from `Topic.keyConcepts` to `Topic.draftMessage` to `Topic.formattedMessage` to `Topic.finalMessage`.

---

## Core Concepts Overview

| Concept                        | Why it matters                                                                                                                                                          |
| ------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Sequential agent pattern**   | Splits one hard task into a chain of narrow, individually testable steps, so a bad result can be traced to a specific stage.                                             |
| **Topic**                      | The unit of orchestration. It holds the input variables, the ordered actions, and the output variables that the agent's orchestrator can call.                           |
| **Code view (YAML)**           | The topic's real definition. Editing it directly is faster than clicking, and reading it is the fastest way to understand what the visual designer produces.             |
| **AI Builder prompt**          | A named, reusable model invocation with typed inputs and a saved instruction. Changing the prompt changes every agent that uses it.                                      |
| **Topic variable**             | The wiring between steps. `Topic.draftMessage` written by one action is read by the next, which is what makes the chain sequential rather than parallel.                 |
| **Input and output variables** | Declared in `inputType` and `outputType`, they define the topic's contract — what the orchestrator must supply and what it gets back.                                    |
| **`SearchAndSummarizeContent`**| A generative action that can consult the model and, optionally, the web, and capture the full response into a variable.                                                 |

---

## Documentation and Additional Training Links

* [Microsoft Copilot Studio documentation](https://learn.microsoft.com/microsoft-copilot-studio/)
* [Use topics in Copilot Studio](https://learn.microsoft.com/microsoft-copilot-studio/authoring-create-edit-topics)
* [Edit topics with the code editor](https://learn.microsoft.com/microsoft-copilot-studio/authoring-topic-management)
* [AI Builder prompts overview](https://learn.microsoft.com/ai-builder/prompts-overview)
* [Create a custom prompt](https://learn.microsoft.com/ai-builder/create-a-custom-prompt)
* [Use variables in Copilot Studio](https://learn.microsoft.com/microsoft-copilot-studio/authoring-variables)

---

## Prerequisites

* Access to Microsoft Copilot Studio with permission to create agents and topics.
* An environment with AI Builder available, so prompts can be created and tested.
* Permission to create new connections if prompted during prompt creation.
* Familiarity with creating a basic agent in Copilot Studio is helpful but not required.

---

## Summary of Targets

In this lab, you'll assemble a three-stage AI chain inside one topic and watch a raw product description turn into polished marketing copy. By the end of the lab, you will:

* Create a Product Developer Agent in Copilot Studio.
* Author a topic by pasting a YAML definition into Code view.
* Create two AI Builder prompts — a writer and a proofreader — with typed text inputs and sample data.
* Bind those prompts to the topic's model-invocation actions.
* Trace how a value moves through four topic variables from input to final output.
* Test the chain end to end and explain what each stage contributed.

---

## Use Cases Covered

| Step | Use Case                                                                                    | Value added                                                                                       | Effort |
| ---- | ------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- | ------ |
| 1    | [Build the Agent and the Topic Skeleton](#use-case-1-build-the-agent-and-the-topic-skeleton) | Establishes the full three-stage chain in one paste, so you can focus on what each stage does.   | 20 min |
| 2    | [Attach the Prompts and Test the Chain](#use-case-2-attach-the-prompts-and-test-the-chain)   | Turns the placeholder model calls into working prompts and validates the end-to-end output.       | 25 min |

---

## Instructions by Use Case

---

## Use Case #1: Build the Agent and the Topic Skeleton

Create the agent, then define the whole sequential chain at once by pasting its YAML definition into Code view.

| Use case                               | Value added                                                                                     | Estimated effort |
| -------------------------------------- | ------------------------------------------------------------------------------------------------- | ---------------- |
| Build the Agent and the Topic Skeleton | Establishes the full three-stage chain in one paste, so you can focus on what each stage does. | 20 minutes       |

**Summary of tasks**

In this section, you'll create the agent, add a topic, switch to Code view, and paste the YAML that declares the input variable, the three chained actions, and the output variable.

**Scenario:** Your product team keeps sending raw feature lists and asking for launch copy. You want a single agent that always returns copy in the same shape, built from stages you can adjust one at a time.

### Objective

Create a topic whose YAML defines a summarize → write → proof chain with explicit input and output contracts.

---

### Step-by-step instructions

#### Create the agent

1. Open [Microsoft Copilot Studio](https://copilotstudio.microsoft.com/) and create a new agent.

2. Set the following values:

    - **Name:** `Product Developer Agent`
    - **Description:** `Creates a Product Marketing Message, based on basic Features`

3. Create the agent.

#### Add the topic

4. Navigate to **Topics** and add a new topic.

5. Name the topic `Marketing Writer`.

6. Switch to **Code view** and replace the contents with the following YAML definition:

    ```yaml
    kind: AdaptiveDialog
    inputs:
    - kind: AutomaticTaskInput
        propertyName: productDescription
        description: A product description
        entity: StringPrebuiltEntity
        shouldPromptUser: false
        inputSettings:
        defaultValue: =Blank()

    modelDescription: Extract concepts from product descriptions.
    beginDialog:
    kind: OnRecognizedIntent
    id: main
    intent: {}
    actions:
        - kind: SearchAndSummarizeContent
        id: aRjLMG
        autoSend: false
        variable: Topic.keyConcepts
        userInput: '=$"Product description: {Topic.productDescription}"'
        moderationLevel: Low
        additionalInstructions: |-
            You are a marketing analyst. Given a product description, identify:
            - Key features
            - Target audience
            - Unique selling points
        webBrowsing: true
        responseCaptureType: FullResponse

        - kind: InvokeAIBuilderModelAction
        id: invokeAIBuilderModelAction_44r3KI
        displayName: Writer Agent
        input:
            binding:
            Text_20input: =JSON(Topic.keyConcepts)

        output:
            binding:
            predictionOutput: Topic.draftMessage

        aIModelId: 35839336-d5fc-430a-b1b5-9bf27d17ee53

        - kind: InvokeAIBuilderModelAction
        id: invokeAIBuilderModelAction_2Vk7z2
        displayName: Format Proof Agent
        input:
            binding:
            Message: =Topic.draftMessage.text

        output:
            binding:
            predictionOutput: Topic.formattedMessage

        aIModelId: 2d20cfdf-f5fd-487f-94ae-032b72de0eba

        - kind: SetVariable
        id: setVariable_DQ6gFs
        displayName: Set ouput
        variable: Topic.finalMessage
        value: =Topic.formattedMessage.text

    inputType:
    properties:
        productDescription:
        displayName: productDescription
        description: A product description
        type: String

    outputType:
    properties:
        finalMessage:
        displayName: finalMessage
        description: Final, polished marketing message
        type: String
    ```

    > [!IMPORTANT]
    > The two `aIModelId` values point at prompts that do not exist in your environment yet. You replace them in Use Case #2 by creating new prompts on each node. Until then the topic will not run successfully.

7. Switch back to the visual editor using **Close Code Editor**.

#### Explore what you pasted

8. In the **Details** panel, find the input variable **productDescription**. Note its type (**String**) and its description (**A product description**), and observe whether the topic prompts the user for this input or expects it to be supplied automatically.

9. In the **Output Variables** section, find **finalMessage** and note its description: *Final, polished marketing message*.

10. Follow the sequence of actions and answer these questions for each:

    - **Action 1 — Search and Summarize Content**: What is its purpose? *(Hint: it extracts key features, target audience, and unique selling points.)* Does it allow web browsing? How does it read and write variables?
    - **Action 2 — AI Builder, Writer Agent**: What does it generate? *(Hint: a draft marketing message.)* Which input does it receive?
    - **Action 3 — AI Builder, Format Proof Agent**: What does it do to the draft, and where does its input come from?
    - **Action 4 — Set Variable**: Which variable does it store as the topic's output?

    > [!TIP]
    > Read the chain as a data flow: `productDescription` → `keyConcepts` → `draftMessage` → `formattedMessage` → `finalMessage`. Every arrow is one action, and every variable is a place you can inspect when the output is wrong.

---

### Congratulations! You've completed Use Case #1!

---

### Test your understanding

**Key takeaways:**

* **YAML is the source of truth** – The visual designer is a view over the same definition you just pasted. Reading Code view is the fastest way to understand an unfamiliar topic.
* **Variables are the wiring** – Each action writes to a topic variable that the next action reads. That chain is what makes the pattern sequential.
* **Contracts are explicit** – `inputType` and `outputType` declare what the orchestrator must pass in and what it gets back, independent of the actions in between.

**Lessons learned & troubleshooting tips:**

* If Code view rejects the paste, check the indentation — YAML is whitespace-sensitive and a mixed tab/space paste will fail.
* If the topic saves but does nothing, confirm you switched back with **Close Code Editor** before saving.
* The placeholder `aIModelId` values are expected to be invalid until Use Case #2 replaces them.

**Challenge: Apply this to your own use case**

* Which multi-step task in your own team could be split into three narrow stages like this?
* What would you add as a fourth stage — translation, tone check, channel-specific formatting?

---

---

## Use Case #2: Attach the Prompts and Test the Chain

Replace the two placeholder model calls with real AI Builder prompts, then run the chain end to end.

| Use case                             | Value added                                                                                 | Estimated effort |
| ------------------------------------ | ----------------------------------------------------------------------------------------------- | ---------------- |
| Attach the Prompts and Test the Chain | Turns the placeholder model calls into working prompts and validates the end-to-end output. | 25 minutes       |

**Summary of tasks**

In this section, you'll create the Writer prompt and the Format Proof prompt with typed text inputs and sample data, bind them to the topic's two AI Builder actions, and test the full chain.

**Scenario:** The chain is in place, but the two writing stages are still placeholders. You now define what "write" and "proofread" actually mean, and confirm the whole thing produces usable copy.

### Step-by-step instructions

#### Writer prompt

1. On the **Writer Agent** node, select **Change AI Builder Model** (the two-arrows icon).

2. Choose **New prompt**.

3. Name the prompt `Writer Agent`.

4. Choose the model **GPT-4.1**.

5. Paste this into the instructions:

    ```text
    You are a marketing copywriter. Given a block of text describing features, audience, and USPs, compose a compelling marketing copy (like a newsletter section) that highlights these points.

    Output should be short (around 150 words), output just the copy as a single text block.

    These are the key concepts:
    {Text input}
    ```

6. Select the **{Text input}** placeholder and choose **+ Add content**.

7. Choose the type **Text**.

8. Name the input `Text input` and use this sample data:

    ```text
    An eco-friendly stainless steel water bottle that keeps drinks cold for 24 hours
    ```

9. Test the prompt and save.

    > [!TIP]
    > The input name must match the placeholder in the instructions exactly. `Text input` in the YAML binding appears as `Text_20input` because the space is URL-encoded — that is expected.

#### Format Proof prompt

10. On the **Format Proof Agent** node, select **Change AI Builder Model**.

11. Choose **New prompt**.

12. Name the prompt `Format Proof`.

13. Paste this into the instructions:

    ```text
    You are an editor. Given the draft copy, correct grammar, improve clarity, ensure consistent tone, give format and make it polished. Output the final improved copy as a single text block.

    This is the message:
    """
    {Message}
    """
    ```

14. Select the **{Message}** placeholder and choose **+ Add content**.

15. Choose the type **Text**.

16. Name the input `Message` and use this sample data:

    ```text
    An eco-friendly stainless steel water bottle that keeps drinks cold for 24 hours
    ```

17. Test the prompt and save.

#### Test the chain

18. Select **Test your agent**.

19. Provide a sample product description:

    ```text
    A lightweight, waterproof hiking backpack with 30L capacity, ideal for outdoor enthusiasts.
    ```

20. Observe how the topic processes the input and what the final marketing message looks like.

    > [!IMPORTANT]
    > If the output arrives unformatted or truncated, check each stage in isolation before changing the chain. Test the Writer prompt alone, then the Format Proof prompt alone — the stage that fails on its own is the one to fix.

---

### Congratulations! You've completed Use Case #2!

---

### Test your understanding

* Which stage would you change if the copy is accurate but reads like a spec sheet?
* Which stage would you change if the copy invents a feature the product does not have?
* What happens to `Topic.finalMessage` if the Format Proof prompt returns an empty response?

**Challenge: Apply this to your own use case**

* Replace the Writer prompt's instructions with your own brand voice and rerun the same input. How much did the output change?
* Add a fourth stage that translates the final message, and decide whether it belongs before or after proofreading.

---

## Summary of learnings

True learning comes from doing, questioning, and reflecting — so let's put your skills to the test.

To get the most out of sequential multi-agent patterns:

* **One responsibility per stage** – A stage that both writes and proofreads cannot be debugged. Split until each stage has a single, checkable job.
* **Type your inputs and outputs** – Explicit `inputType` and `outputType` declarations make a topic reusable by an orchestrator instead of only by a person clicking Test.
* **Name variables after their content** – `keyConcepts`, `draftMessage`, `formattedMessage` describe a pipeline at a glance; `var1` through `var4` describe nothing.
* **Test stages in isolation first** – When the chain output is wrong, test each prompt on its own. The failing stage reveals itself immediately.
* **Read the YAML** – Code view is faster to author and far faster to review than the visual designer, and it is what actually gets deployed.

---

### Conclusions and recommendations

**Sequential multi-agent golden rules:**

* Decompose by responsibility, not by length — three narrow stages beat one long prompt.
* Give every prompt a sample input, so the prompt can be tested without running the whole agent.
* Keep the hand-off between stages in a named topic variable rather than re-deriving context.
* Set `moderationLevel` and `webBrowsing` deliberately; both change what the first stage is allowed to do.
* Version prompts separately from topics — a prompt change affects every agent that uses it.
* Re-test the full chain after any single-stage change; downstream stages depend on the shape of upstream output.

By following these principles, you'll build agents whose behaviour you can explain, tune, and hand to someone else without a rebuild.

---
