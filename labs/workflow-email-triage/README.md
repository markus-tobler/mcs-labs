# Workflow-Based Email Triage

Build a Copilot Studio workflow that reacts to new inbox messages, classifies them by urgency, turns urgent mail into a short markdown alert, and posts it to Teams — then validate the whole path with a real email.

---

## 🧭 Lab Details

| Level | Persona | Duration   | Purpose                                                                                                                                                                                                            |
| ----- | ------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 200   | Maker   | 40 minutes | After completing this lab, participants will be able to configure a connector-based workflow trigger, use a Classify action to route messages by business priority, add a prompt step that reshapes content for a target channel, and validate a published workflow end to end using the Activity view. |

---

## 📚 Table of Contents

---

## 🤔 Why This Matters

Email remains one of the most common entry points for operational work, but not every message deserves the same response. Teams lose time manually reviewing incoming mail, deciding what matters, and creating follow-up tasks only after delays have already occurred.

Think of a shared operations inbox:

- **Without triage**: Every message is read in arrival order. The production outage sits three messages below the kitchen-maintenance notice for forty minutes.
- **With triage**: The outage surfaces in Teams within seconds; the kitchen notice never leaves the inbox.

**Common challenges solved by this lab:**

- "Urgent customer mail gets buried under informational traffic."
- "I can classify with AI, but the raw email body is unreadable in a Teams alert."
- "I don't know how to prove a published workflow actually ran."
- "Everything gets escalated, so nothing gets escalated."

**With a workflow-based triage pattern, you turn inbox traffic into structured action** — and you get a reusable pattern for many other business processes.

---

## 🌐 Introduction

This lab combines two ways of working that are increasingly common in Copilot Studio: generating a workflow from a natural-language description, and then refining the result directly in the visual designer.

**Real-world example:** A mid-sized sports retailer runs its key-account communication through a single mailbox. A blocked purchase order with a same-day deadline and an FYI about office maintenance arrive within minutes of each other and look identical in the inbox list. After this lab, the first one produces a Teams alert with a summary and a next action; the second one produces nothing at all — which is exactly the right outcome.

You will build the trigger, the classification stage, the summarisation prompt, and the Teams notification, and finish by sending yourself a real email and following the run in the Activity view. The workflow is designed to be extended: important messages can become tasks, and other informational mail can be safely ignored.

---

## 🎓 Core Concepts Overview

| Concept                  | Why it matters                                                                                                                                              |
| ------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Connector trigger**    | Turns an external event — a new email arriving — into a workflow run, without polling or manual starts.                                                     |
| **Classify action**      | Assigns an incoming item to one of your defined categories, which is what makes differentiated downstream handling possible.                                |
| **Category description** | The classifier reads your descriptions, not your category names. The description is where urgency is actually defined.                                      |
| **Default Other branch** | Every classification needs an explicit "nothing to do" path, so unmatched items exit cleanly instead of falling through to an action.                        |
| **Prompt action**        | Reshapes content between steps. Here it converts a raw email into a compact markdown alert that reads well in Teams.                                        |
| **Dynamic content**      | Carries trigger outputs — subject, body, sender — into later steps. Getting these bindings right is most of the work.                                        |
| **Activity view**        | The run history. It shows which branch executed and where a run stopped, which is the difference between "it works" and "I tested it".                       |

---

## 📄 Documentation and Additional Training Links

* [Microsoft Copilot Studio documentation](https://learn.microsoft.com/microsoft-copilot-studio/)
* [Workflows overview](https://learn.microsoft.com/microsoft-copilot-studio/workflows-experience/flows-overview)
* [Office 365 Outlook connector reference](https://learn.microsoft.com/connectors/office365/)
* [Microsoft Teams connector reference](https://learn.microsoft.com/connectors/teams/)
* [AI Builder prompts overview](https://learn.microsoft.com/ai-builder/prompts-overview)

---

## ✅ Prerequisites

* Access to Copilot Studio with workflow authoring enabled.
* A Microsoft 365 mailbox connected to Office 365 Outlook.
* Permission to create new connections in Copilot Studio.
* Microsoft Teams access in the same tenant.
* A second mailbox you can send from, for the end-to-end test.
* Optionally, a task management destination such as Microsoft To Do or Planner, for the extension ideas at the end.

---

## 🎯 Summary of Targets

In this lab, you'll build a triage workflow from trigger to Teams alert and prove it runs on real mail. By the end of the lab, you will:

* Create an email triage workflow and refine it in the visual designer.
* Configure an Outlook-based trigger for new inbox messages.
* Classify incoming email into urgent, important, and other categories.
* Add a prompt step that converts urgent emails into short Teams-ready summaries.
* Add a Teams notification step for urgent emails.
* Publish the workflow and validate the full run from incoming email to Teams alert.
* Establish the basis for downstream actions such as task creation and further routing.

---

## 🧩 Use Cases Covered

| Step | Use Case                                                                              | Value added                                                                                       | Effort |
| ---- | ------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- | ------ |
| 1    | [Create the Email Triage Workflow](#-use-case-1-create-the-email-triage-workflow)     | Quickly generates the workflow skeleton and connects it to a real inbox trigger.                  | 10 min |
| 2    | [Add Classification Logic](#-use-case-2-add-classification-logic)                     | Introduces the decision point that enables different follow-up actions based on email priority.  | 10 min |
| 3    | [Add a Prompt for the Urgent Summary](#-use-case-3-add-a-prompt-for-the-urgent-summary) | Turns urgent email content into a concise, action-oriented alert message.                        | 10 min |
| 4    | [Add Teams Alert for Urgent Cases](#-use-case-4-add-teams-alert-for-urgent-cases)      | Sends urgent email notifications directly into Teams for immediate visibility.                   | 10 min |
| 5    | [Run an End-to-End Workflow Test](#-use-case-5-run-an-end-to-end-workflow-test)        | Validates the published workflow with a real email, activity trace, and Teams outcome.           | 10 min |

---

## 🛠️ Instructions by Use Case

---

## 🧱 Use Case #1: Create the Email Triage Workflow

Build the initial workflow and configure the inbox trigger in the workflow designer.

| Use case                         | Value added                                                                               | Estimated effort |
| -------------------------------- | --------------------------------------------------------------------------------------------- | ---------------- |
| Create the Email Triage Workflow | Establishes the automation foundation for inbox monitoring and business-priority routing. | 10 minutes       |

**Summary of tasks**

In this section, you'll create a workflow, rename it, and configure the Office 365 Outlook trigger that starts it when new mail arrives.

**Scenario:** Your organization wants a lightweight email assistant that can distinguish between urgent emails, important but non-urgent messages, and everything else, so that follow-up actions happen consistently without requiring constant inbox monitoring.

### Objective

Create a reusable workflow shell for email triage and connect it to your Outlook inbox.

---

### Step-by-step instructions

1. Open [Copilot Studio](https://copilotstudio.preview.microsoft.com/).

2. In the left navigation, select **Flows**, then select **+ New workflow**.

3. In the upper-left corner, rename **Untitled Workflow** to `Email Triage`.

4. In the workflow canvas, select the **Start** trigger.

5. In the right-side configuration pane, set **Trigger Type** to **Connector**.

6. For the connector, choose **Office 365 Outlook**, then select **When a new email arrives**.

7. In the **Connection** section, select **Create new connection** and complete the Outlook sign-in if prompted.

8. In the **Folder** field, select **Inbox** — or **Posteingang**, depending on your mailbox language.

9. In the top command bar, select the **Save** icon to persist the workflow.

    > [!TIP]
    > Name the workflow before configuring anything else. Untitled workflows are hard to find in the Activity view once several test runs exist.

---

### 🏅 Congratulations! You've completed Use Case #1!

---

### Test your understanding

**Key takeaways:**

* **A workflow shell aligned to a realistic business scenario** – The trigger defines what the automation is about long before any logic exists.
* **A concrete Outlook trigger** – Connector triggers turn external events into workflow runs without polling.
* **A naming and configuration baseline** – Later classification and branching logic build directly on this.

**Lessons learned & troubleshooting tips:**

* If the folder picker is empty, the connection was not completed — reopen it and finish the Outlook sign-in.
* Mailbox language changes the folder name; **Inbox** and **Posteingang** are the same folder.

**Challenge: Apply this to your own use case**

* Which other connector event in your environment would justify the same triage treatment — a new file, a new form response, a new ticket?

---

---

## 🔀 Use Case #2: Add Classification Logic

Extend the workflow with an AI-assisted decision step that separates urgent, important, and other messages.

| Use case                 | Value added                                                                            | Estimated effort |
| ------------------------ | ------------------------------------------------------------------------------------------ | ---------------- |
| Add Classification Logic | Introduces the routing mechanism that turns raw inbox events into prioritized actions. | 10 minutes       |

**Summary of tasks**

In this section, you'll add the classification stage that interprets the incoming email and assigns it to a business category. This is the core intelligence layer of the workflow, because every later action depends on whether the email is considered urgent, important, or other.

**Scenario:** After connecting the workflow to your inbox, you need a consistent way to evaluate message importance so the automation can notify you, create tasks, or deliberately take no action.

### Objective

Insert the classification step that enables downstream branching based on email priority.

---

### Step-by-step instructions

1. Add a **Classify** action directly after **When a new email arrives**.

2. In **Input to classify**, keep the model set to **GPT-4.1 mini**.

3. In **Input to classify**, first enter this text:

    ```text
    Subject: [Subject]
    Body: [Body]
    ```

4. Replace **[Subject]** and **[Body]** with the matching dynamic values from the email trigger output.

5. Under **Categories**, add **Urgent** with this description:

    ```text
    Email is urgent and needs a reaction within the current day because there is clear time pressure, an active issue, or an immediate business impact.
    ```

6. Add **Important** with this description:

    ```text
    Email is highly important. It is evident that not answering could result in damage to the company or loss of opportunity, but there is no same-day time pressure and no immediate reaction is required today.
    ```

7. Do **not** add an **Other** category manually. The **Other** branch is already available by default.

    > [!IMPORTANT]
    > The classifier reads the category descriptions, not the category names. "Urgent" means whatever the description says it means — which is why the two descriptions above draw the line at same-day time pressure rather than at how emphatic the sender sounds.

#### Suggested classifier tests

Use sample emails like these to confirm the classifier separates urgency from importance:

1. **Expected result: Urgent**

    **Subject:** Production webshop checkout is failing right now
    **Body:** Since 08:15, customers cannot complete checkout and orders are blocked. Please investigate immediately and provide an update today.

2. **Expected result: Important**

    **Subject:** Approval needed for strategic customer renewal proposal
    **Body:** We need your feedback on the renewal proposal for Alpine Retail. If we miss this response, we may lose the opportunity, but there is no issue today and your review can happen this week.

3. **Expected result: Other**

    **Subject:** FYI: Office kitchen maintenance on Thursday
    **Body:** This is only an informational update. The kitchen will be unavailable on Thursday morning during routine maintenance. No action is required.

---

### 🏅 Congratulations! You've completed Use Case #2!

---

### Test your understanding

* Sample 2 sounds consequential but is not urgent. Which sentence in your **Important** description made the difference?
* What would happen to the third sample if you had added an explicit **Other** category with your own description?

**Challenge: Apply this to your own use case**

* Write the category descriptions your own team would need. Where exactly does your organisation draw the line between urgent and important?

---

---

## ✍️ Use Case #3: Add a Prompt for the Urgent Summary

Add a prompt step that turns urgent email content into a short, well-formatted Teams-ready message.

| Use case                            | Value added                                                                            | Estimated effort |
| ----------------------------------- | ------------------------------------------------------------------------------------------ | ---------------- |
| Add a Prompt for the Urgent Summary | Converts raw email content into a concise alert message with summary and next action. | 10 minutes       |

**Summary of tasks**

In this section, you'll add a prompt action to the urgent branch so the workflow does not post raw email fields directly into Teams. Instead, the flow first generates a short markdown response that summarizes the email and recommends the next action.

**Scenario:** Your classification logic can already identify urgent emails, but the raw subject and body are not ideal as a Teams alert. You want Copilot Studio to transform the email into a short message that is easier to read and act on.

### Objective

Create a prompt step that formats the urgent email into a compact markdown alert.

---

### Step-by-step instructions

1. In the urgent branch of the workflow, add **Prompt**.

2. Keep the existing connection selected.

3. In **Instructions**, keep the model set to **GPT-4.1 mini**.

4. Enter this instruction text and place the dynamic values for **Subject**, **Body**, and **From** directly into the placeholders:

    ```text
    Write an extremly short message including summarizing the Email and next action to take:

    From: [From]
    Subject: [Subject]
    Body: [Body]

    Use Markdown formatting for the text.
    ```

5. Insert the matching dynamic values from the email trigger for all three placeholders.

6. In **Output**, keep **Text response** selected.

7. Save the workflow.

#### Test step

8. Open **Prompt** and switch to the **Test** tab. Under **When a new email arrives**, enter this urgent sales email:

    - **From:** `Lena Ortiz <lena.ortiz@megamart-sports.example>`
    - **Subject:** `MegaMart Sports order blocked for summer launch`
    - **Body:** `Our biggest retail partner cannot release PO SP-4821 for 6,000 TrailSpark running shoes until we confirm final delivery slots today before 16:00. If we miss the window, AlpenPulse Sports loses the front-of-store launch placement for June.`

9. Run the test and check that **Response** is a short markdown message that includes the sender, a compact summary of the situation, and a clear next action.

    A good result should read like a polished alert, for example:

    ```markdown
    **Urgent email**

    - **From:** Lena Ortiz
    - **Summary:** MegaMart Sports cannot release a 6,000-unit summer launch order until delivery slots are confirmed today.
    - **Next action:** Confirm delivery slots before 16:00 to protect June placement.
    ```

    The exact wording will vary, but it should stay concise, actionable, and clearly better suited for Teams than the raw email body.

    > [!TIP]
    > Keep this formatting layer separate from the notification step. When the alert reads badly, you change one prompt — not every downstream channel action.

---

### 🏅 Congratulations! You've completed Use Case #3!

---

### Test your understanding

**Key takeaways:**

* **A reusable formatting layer** – It sits between classification and notification, so any channel can consume the same summary.
* **A concise markdown summary** – Better suited for Teams than raw email fields.
* **One place for summarization logic** – Downstream actions reuse the prompt output rather than re-deriving it.

**Challenge: Apply this to your own use case**

* Change the instruction to include a deadline field. Does the model reliably find one, and what does it do when there is none?

---

---

## 🔔 Use Case #4: Add Teams Alert for Urgent Cases

Extend the workflow so emails classified as urgent generate a direct Teams notification through the Flow bot.

| Use case                         | Value added                                                                 | Estimated effort |
| -------------------------------- | ------------------------------------------------------------------------------- | ---------------- |
| Add Teams Alert for Urgent Cases | Surfaces urgent emails immediately in Teams so follow-up can start faster. | 10 minutes       |

**Summary of tasks**

In this section, you'll add the first concrete follow-up action. Once an email is identified as urgent, the workflow sends a Teams message to you through the Flow bot so the signal is visible outside the inbox.

**Scenario:** Your classification logic is now in place, and urgent emails should no longer remain buried in the mailbox. Instead, they should trigger a visible alert in Teams that helps you react in time.

### Objective

Configure the urgent path so it posts a Teams message to your own chat via the Flow bot.

---

### Step-by-step instructions

1. In the urgent branch of the workflow, add **Post message in a chat or channel** and set **Trigger Type** to **Connector** if prompted.

2. For the connector action, choose **Microsoft Teams**, then select **Post message in a chat or channel**.

3. Set **Post as** to **Flow bot**.

4. Set **Post in** to **Chat with Flow bot**.

5. In **Recipient**, search for and select your own user account.

6. In **Message**, insert dynamic content and choose the **Response** output from the prompt step.

7. Set **IsAlert** to **Yes**.

8. Set **FeedbackLoopEnabled** to **No**.

#### Test step

9. Open **Post message in a chat or channel** and switch to **Test**. Enter the same urgent sales email under **When a new email arrives**. In the **Prompt** section, paste the markdown response generated in Use Case #3 into **Response**.

10. Run the test and confirm that:

    - the message is posted to your own Teams chat with the **Flow bot**,
    - the posted content matches the prompt output rather than the raw email body,
    - and only the **Urgent** dataset is used for this action.

    > [!IMPORTANT]
    > The **Important** and **Other** test runs from Use Case #2 should not trigger this Teams action, because they belong to different downstream paths. If they do, the action was added outside the urgent branch.

---

### 🏅 Congratulations! You've completed Use Case #4!

---

### Test your understanding

**Key takeaways:**

* **A concrete urgent-response action** connected to the classification result.
* **A direct Teams notification path** that raises visibility for time-sensitive emails.
* **A reusable pattern** for extending other branches with channel-specific actions.

**Challenge: Apply this to your own use case**

* Which channel would your team actually watch — a Teams chat, a channel post, or a mobile push? Would the message need to change shape for it?

---

---

## 🚀 Use Case #5: Run an End-to-End Workflow Test

Publish the workflow and validate complete live execution with a real email, activity monitoring, and Teams delivery.

| Use case                        | Value added                                                                          | Estimated effort |
| ------------------------------- | ---------------------------------------------------------------------------------------- | ---------------- |
| Run an End-to-End Workflow Test | Confirms that the published workflow works end to end with a real trigger and output. | 10 minutes       |

**Summary of tasks**

In this section, you'll stop working with isolated action tests and validate real workflow behavior. You will publish the workflow, send a real email to your lab mailbox, observe the run in **Activity**, and confirm that the urgent summary arrives in Teams.

**Scenario:** Your design is complete, but you still need proof that the published workflow reacts to a real incoming message and moves through classification, prompt generation, and Teams notification without manual test inputs.

### Objective

Verify that one real urgent sales email triggers full workflow execution from Outlook to Teams.

---

### Step-by-step instructions

1. Save the workflow if you still have unpublished changes.

2. In the upper-right corner, select **Publish** and wait until the workflow is published successfully.

3. From another mailbox, send an email to your own lab email address with this content:

    - **To:** your lab email address
    - **From:** any mailbox you can send from
    - **Subject:** `Urgent: MegaMart Sports requests immediate delivery confirmation for summer launch`
    - **Body:**

        ```text
        Hello AlpenPulse Sports team,

        I need your confirmation today for our summer launch order before I release it to distribution.

        MegaMart Sports is ready to proceed with PO SP-4821 for 6,000 TrailSpark running shoes and 2,500 AeroFlex training sets across our Zurich, Bern, and Basel stores. However, our allocation team will only hold the launch placement until 16:00 today.

        Please confirm the final delivery schedule, warehouse split, and whether the in-store display kits will ship with the first wave. If we do not have written confirmation before the deadline, I will have to move the launch window to another supplier.

        This campaign is tied to our early-summer promotion and cannot slip into next week.

        Thanks,
        Lena Ortiz
        Senior Buyer, MegaMart Sports
        ```

4. Return to Copilot Studio and open the **Activity** tab for the workflow.

5. Watch for a new run to appear in the left activity panel. Open the newest item and confirm the execution status is **Succeeded**.

6. Review the run path and confirm the workflow followed the **Urgent** classification path, executed the **Prompt** step, and reached the Teams action.

7. Open Microsoft Teams and check your chat with **Flow bot**.

8. Confirm that the Teams message contains the generated summary and recommended next action from the urgent email.

    > [!TIP]
    > If no run appears, the workflow was saved but not published, or the trigger is watching a different folder. Both show up immediately in the Activity view as "no runs" rather than as an error.

---

### 🏅 Congratulations! You've completed Use Case #5!

---

### Test your understanding

**Key takeaways:**

* **The workflow is published and reacts to real inbound email** – Testing actions in isolation is not the same as testing a run.
* **The Activity view is the reliable place** to inspect run status and execution path.
* **The full urgent path works** from Outlook trigger to Teams notification.

**Challenge: Apply this to your own use case**

* Send the "Other" sample as a real email. Confirm that a run appears, succeeds, and produces no Teams message — a correct no-op is a result worth verifying.

---

## 🏆 Summary of learnings

True learning comes from doing, questioning, and reflecting — so let's put your skills to the test.

To get the most out of workflow-based triage:

* **Classify on consequence, not tone** – Category descriptions should describe business impact and time pressure, because that is what the classifier reads.
* **Reshape before you notify** – A dedicated prompt step keeps channel formatting out of the notification action and out of every future branch.
* **Give "nothing to do" a real path** – The default Other branch is a feature. Most inbox traffic should end there.
* **Test in isolation, then for real** – Action-level tests catch binding errors; a published run with a real email catches everything else.
* **Read the Activity view** – The run path tells you which branch fired, which is the only reliable evidence that classification worked.

---

### Conclusions and recommendations

**Email triage golden rules:**

* Rename the workflow before your first save, so runs are identifiable later.
* Bind dynamic content deliberately — most failed runs trace back to an unbound subject or body placeholder.
* Write category descriptions as decision criteria, not as synonyms of the category name.
* Keep the summarization prompt independent of the destination channel.
* Publish before testing end to end; an unpublished workflow produces no runs and no error.
* Verify that low-priority mail produces no action at all before adding more branches.

By following these principles, you'll build automations that raise the signal without adding noise.

---

## Next expansion ideas

Once the urgent notification path is in place, a natural next iteration is to add a second branch so that important emails create a task while other emails exit without action. You could also enrich the workflow by extracting sender details, detecting customer names, or applying confidence thresholds before high-impact notifications are sent.

---
