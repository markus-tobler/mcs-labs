# Canvas App with AI Data Extraction

Import a Dataverse table, build a prompt that turns unstructured customer feedback into strict JSON, and wire it into a Power Apps canvas app so the form fills itself as the user types.

---

## 🧭 Lab Details

| Level | Persona          | Duration   | Purpose                                                                                                                                                                                                                     |
| ----- | ---------------- | ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 200   | Maker, Developer | 60 minutes | After completing this lab, participants will be able to import a Dataverse solution, author a prompt that returns a strict JSON schema, add that prompt as a data source in a canvas app, and use Power Fx `Predict()` and `Patch()` to write extracted values into a record. |

---

## 📚 Table of Contents

---

## 🤔 Why This Matters

**Convinced AI belongs only in chat?** The highest-value AI in a line-of-business app is usually invisible: it fills in the fields nobody wants to type.

Think of a support inbox:

- **Without extraction**: An agent reads a 200-word email, then re-types the customer's name, email, phone, a summary title, and a category into five form fields. Six times an hour. With typos.
- **With extraction**: The agent pastes the email. The five fields populate themselves. The agent's job becomes reviewing, not transcribing.

**Common challenges solved by this lab:**

- "Our team re-types the same details out of emails into a system all day."
- "I can get an AI to summarise, but I can't get it to return something my app can actually parse."
- "I don't know how to call AI from Power Fx or what it hands back."
- "Free-text categorisation is inconsistent between people."

**One hour here converts a copy-paste job into a review job — the pattern behind most practical AI-in-app value.**

---

## 🌐 Introduction

Structured extraction is the workhorse of business AI. A model reads unstructured text and returns a predictable object: named fields, known types, a closed set of category values. Once the output is predictable, ordinary low-code logic can act on it.

**Real-world example:** A consumer goods company receives several hundred feedback emails a week across praise, complaints, product questions, and safety incidents. Today they are triaged by hand, and the safety-relevant ones surface late. With extraction in the intake app, every message arrives already tagged with a customer, contact details, a one-line title, and a category — so incidents can be routed the moment they land.

In this lab you build that intake path end to end. You import a Dataverse table, write a prompt that is explicit about its JSON output schema, generate a canvas app from the table, and add an `OnChange` formula that calls the prompt and patches the result into the current record.

---

## 🎓 Core Concepts Overview

| Concept                     | Why it matters                                                                                                                                                    |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Solution**                | The package that carries the Dataverse table, its columns, and its choice sets between environments. Importing one gives every participant an identical schema.    |
| **Prompt**                  | A named, reusable AI task with typed inputs. Because it lives outside the app, the same extraction logic can be called from flows and agents too.                 |
| **Strict output schema**    | Telling the model exactly which fields to return, with which types and which allowed values, is what makes the response safe to consume programmatically.          |
| **`Predict()`**             | The Power Fx function that calls a prompt from a canvas app and returns its response, including `StructuredOutput` when the prompt returns JSON.                  |
| **`Patch()`**               | Writes values into a Dataverse record — either an existing one or a new one built from `Defaults()`.                                                              |
| **Choice column mapping**   | The model returns a string such as `"complaint"`; Dataverse expects a choice value. A `Switch()` translates between the two vocabularies.                          |
| **`OnChange`**              | Fires when the user leaves the text input, which makes it the natural trigger for "extract as soon as the feedback has been pasted".                              |

---

## 📄 Documentation and Additional Training Links

* [AI Builder prompts overview](https://learn.microsoft.com/ai-builder/prompts-overview)
* [Use prompts in Power Apps](https://learn.microsoft.com/ai-builder/use-a-custom-prompt-in-powerapps)
* [Power Fx `Patch` function](https://learn.microsoft.com/power-platform/power-fx/reference/function-patch)
* [Power Fx `Switch` function](https://learn.microsoft.com/power-platform/power-fx/reference/function-if)
* [Import solutions in Power Platform](https://learn.microsoft.com/power-apps/maker/data-platform/import-update-export-solutions)
* [Create a canvas app from Dataverse](https://learn.microsoft.com/power-apps/maker/canvas-apps/data-platform-create-app)

---

## ✅ Prerequisites

* Access to a Dataverse environment with permission to import solutions. A trial prompt may appear during the lab — accept it.
* AI Builder available in the same environment, so prompts can be created and consumed.
* Permission to create new connections when the app adds the prompt as a data source.
* A solution ZIP containing the **Customer Feedback** table, provided with your lab materials.

> [!IMPORTANT]
> The Dataverse table, column, and choice names in the Power Fx formula below reflect the provided solution. If your environment names them differently, adjust the formula accordingly — the logic is unchanged.

---

## 🎯 Summary of Targets

In this lab, you'll take unstructured customer feedback all the way to a populated Dataverse record without typing a field. By the end of the lab, you will:

* Import a solution and review the **Customer Feedback** table schema.
* Author a prompt that returns a strict JSON object with five named fields.
* Test the prompt against a realistic sample email before integrating it.
* Generate a canvas app from the table and seed it with sample records.
* Add the prompt as a data source and call it from Power Fx with `Predict()`.
* Patch extracted values into the current or a new record, including a mapped choice column.
* Validate the behaviour across praise, complaint, incident, and question samples.

---

## 🧩 Use Cases Covered

| Step | Use Case                                                                        | Value added                                                                                        | Effort |
| ---- | ------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ | ------ |
| 1    | [Import the Table and Build the Prompt](#-use-case-1-import-the-table-and-build-the-prompt) | Establishes the data schema and the extraction contract before any UI work begins.                | 25 min |
| 2    | [Wire the Prompt into a Canvas App](#-use-case-2-wire-the-prompt-into-a-canvas-app)          | Turns the prompt into a working self-filling form and validates it against four feedback categories. | 35 min |

---

## 🛠️ Instructions by Use Case

---

## 🧱 Use Case #1: Import the Table and Build the Prompt

Bring in the Dataverse schema, then define the extraction contract as a prompt that always returns the same JSON shape.

| Use case                              | Value added                                                                        | Estimated effort |
| ------------------------------------- | -------------------------------------------------------------------------------------- | ---------------- |
| Import the Table and Build the Prompt | Establishes the data schema and the extraction contract before any UI work begins. | 25 minutes       |

**Summary of tasks**

In this section, you'll import a solution containing the **Customer Feedback** table, create a prompt named **Customer Complaint Data Extraction**, give it a typed text input, and test it against a sample email.

**Scenario:** Support wants inbound feedback captured consistently. Before building any screen, you need a table to write into and a reliable way to turn an email into the fields that table expects.

### Objective

Produce a prompt whose output is predictable enough for an app to consume without post-processing.

---

### Step-by-step instructions

#### Import the solution

1. Open the maker portal at [https://make.powerapps.com](https://make.powerapps.com).

    *Why?* This is the entry point to Power Apps and solution management.

2. Download the solution ZIP provided with your lab materials.

    *Why?* The solution contains the **Customer Feedback** Dataverse table used by the app.

3. Go to **Solutions › Import solution**.

    *Why?* Importing adds the table and its components to your environment.

4. Select the downloaded ZIP, then choose **Next** and **Import**.

5. After the import finishes, open the solution and review the **Customer Feedback** table.

    *Why?* Familiarise yourself with the columns you will populate through AI extraction — in particular the **Feedback Type** choice column and its four options.

#### Create the prompt

6. In the maker portal, go to **More › Discover all** and scroll to **Prompts**.

    *Why?* Prompts let you operationalise AI tasks such as text extraction independently of any one app.

7. Select **Build your own prompt**.

8. Name the prompt `Customer Complaint Data Extraction`.

    *Why?* Clear naming matters — this is the exact string you will reference in Power Fx.

9. Paste the following into the prompt editor:

    ````markdown
    You are tasked with extracting structured data from customer feedback text. The goal is to identify and extract the following:

    1. Customer Name
    2. E-mail Address
    3. Phone Number
    4. A concise Title summarising the feedback
    5. Type Categorisation of the feedback: one of praise, complaint, question, or incident.

    ### Instructions:

    1. Carefully analyse the provided customer feedback text.
    2. Extract the Customer Name exactly as mentioned.
    3. Extract the E-mail Address and Phone Number if present.
    4. Create a brief and clear Title that summarises the essence of the feedback.
    5. Determine the Type Categorisation based on content and tone (choose exactly one: praise, complaint, question, incident).
    6. If any required fields are missing, set them to null.

    ### Output Format:

    Respond with a valid JSON object:

    ```json
    {
      "CustomerName": string | null,
      "EmailAddress": string | null,
      "PhoneNumber": string | null,
      "Title": string,
      "Type": "praise" | "complaint" | "question" | "incident"
    }
    ```
    ````

    Add these guidelines to the prompt as well:

    - Extract only from the provided feedback text.
    - Ensure the Title is concise (a few words) and captures the main point.
    - Use null for any missing information.

    *Why?* This prompt specifies the fields, the output schema, and the guardrails, so results are structured and reliable rather than differently shaped on every run.

10. Add an input for the customer text: choose **+ Add content › Text**.

    - **Name:** `Customer Feedback`
    - **Sample value:** paste the sample email below.

    ```text
    Subject: Excellent Service Experience
    From: Sarah Klein <sarah.klein@email.com>
    To: support@yourcompany.com

    Dear Team,
    I just wanted to take a moment to thank you for the outstanding support I received last week. I had an issue with my account login, and your representative, Daniel, resolved it within minutes. He was patient, professional, and explained everything clearly.

    It's rare these days to find such responsive and helpful customer service. Please pass along my appreciation to Daniel and the entire team. Keep up the great work!
    Best regards,
    Sarah Klein
    +41 79 555 23 11
    ```

    *Why?* Defining a text input lets the app pass user-entered content into the prompt.

11. Select **Test** to view the model response.

    *Why?* This validates that the prompt produces the expected JSON shape before you integrate it into the app.

    > [!TIP]
    > Check the response for all five keys, correct types, and a `Type` value drawn from the four allowed strings. If the model returns prose around the JSON, tighten the output-format section of the prompt before continuing — a downstream `Patch()` cannot recover from an unparseable response.

---

### 🏅 Congratulations! You've completed Use Case #1!

---

### Test your understanding

**Key takeaways:**

* **Schema first** – Deciding which fields exist, and which values a category may take, is a design decision. The model only enforces what you specify.
* **Prompts are reusable assets** – The same prompt can serve a canvas app, a Power Automate flow, and a Copilot Studio agent.
* **Null is a valid answer** – Instructing the model to return null for missing data prevents it from inventing a phone number to fill the field.

**Lessons learned & troubleshooting tips:**

* If the test output is wrapped in explanation text, restate the output format instruction and add "respond with the JSON object only".
* If the category drifts outside the four allowed values, list them again in the instructions and in the schema.
* If a trial prompt appears when creating the prompt, accept it — AI Builder capacity is required.

**Challenge: Apply this to your own use case**

* Which document your team receives regularly could be described as a five-field JSON object?
* What would you add — sentiment, urgency, product SKU — and which of those is actually reliable to extract?

---

---

## 🔄 Use Case #2: Wire the Prompt into a Canvas App

Generate an app from the table, add the prompt as a data source, and call it from `OnChange` so the form fills itself.

| Use case                          | Value added                                                                                         | Estimated effort |
| --------------------------------- | ------------------------------------------------------------------------------------------------------- | ---------------- |
| Wire the Prompt into a Canvas App | Turns the prompt into a working self-filling form and validates it against four feedback categories. | 35 minutes       |

**Summary of tasks**

In this section, you'll generate a canvas app from the **Customer Feedback** table, make the feedback field multiline, add the prompt as a data source, and write the Power Fx that calls `Predict()` and patches the extracted values.

**Scenario:** The extraction contract works. Now the people doing intake need a screen where pasting an email is the only manual step.

### Step-by-step instructions

#### Generate the app

1. In [https://make.powerapps.com](https://make.powerapps.com), go to **Apps › Start with data**.

    *Why?* This scaffolds a data-connected app quickly.

2. Choose **Select existing tables**, search for **Customer Feedback**, select it, and choose **Create app**.

    *Why?* The generated app provides browse, view, and edit screens bound to the table.

3. Select **Save** and set the app name to `Customer Feedback Management`.

4. Select **Play** in the upper left, explore the app, and add at least **two** example records.

    *Why?* Seed data helps validate the extraction and patch logic later.

#### Enable AI extraction

You will now extend the **Original Feedback** field to accept multi-line text, connect the prompt as a data source, and add an `OnChange` formula that calls it.

5. On the form, select the **Original Feedback** data card and expand it as needed.

    *Why?* This gives room to paste full emails or long comments.

6. Select the inner **Text input** for **Original Feedback** and set **Mode** to **Multiline** in the right pane.

7. In the left tree, select **Data › + Add data**, then search for and add your prompt `Customer Complaint Data Extraction`. Start a free trial if prompted.

    *Why?* Adding the prompt as a data source is what makes `Predict()` available.

8. With the **Text input** still selected, switch the property dropdown in the top-right to **OnChange**.

    *Why?* Extraction should run whenever the pasted text changes.

9. Paste the following Power Fx formula into the **fx** bar:

    ```javascript
    UpdateContext({
      lastExtractionResult: 'Customer Complaint Data Extraction'.Predict(Self.Text)
    });

    If(
      Not(IsBlank(lastExtractionResult.StructuredOutput)),
      Patch(
        'Customer Feedbacks',
        If(
          newMode,
          Defaults('Customer Feedbacks'),
          RecordsGallery1.Selected
        ),
        {
          'Customer Name': lastExtractionResult.StructuredOutput.CustomerName,
          'Feedback Type':
            Switch(
              lastExtractionResult.StructuredOutput.Type,
              "praise",   'Feedback Type (Customer Feedbacks)'.Praise,
              "complaint",'Feedback Type (Customer Feedbacks)'.Complaint,
              "question", 'Feedback Type (Customer Feedbacks)'.Question,
              "incident", 'Feedback Type (Customer Feedbacks)'.Incident
            ),
          'Phone Number': lastExtractionResult.StructuredOutput.PhoneNumber,
          Title:          lastExtractionResult.StructuredOutput.Title,
          Email:          lastExtractionResult.StructuredOutput.EmailAddress,
          'Original Feedback': Self.Text
        }
      );
      Refresh('Customer Feedbacks');
      ResetForm(Form1);
      UpdateContext({ editMode: false, newMode: false });
      ,
      Notify("The AI prompt call failed. Check the prompt response and try again.")
    );
    ```

    *Why?* `Predict(Self.Text)` sends the feedback to your prompt. If a structured result comes back, `Patch()` writes the extracted values to the current or a new record, refreshes the table, and resets the form; otherwise it notifies the user.

    > [!IMPORTANT]
    > Table, control, and choice names may differ in your environment. `RecordsGallery1` and `Form1` are the names the generator produced here — check the left tree and adjust if yours differ, or the formula will not resolve.

#### Try it out

10. Select **Play**, select a record, paste a sample into **Customer Feedback**, then select anywhere else on the form to trigger `OnChange`.

    *Why?* This runs the end-to-end extraction and populates the fields automatically.

11. Work through the samples below and confirm the extracted values for each.

##### Praise samples

*Sample 1* — validates extraction of name, email, phone, title, and `Type=praise`.

```text
Subject: Excellent Service Experience
From: Sarah Klein <sarah.klein@email.com>
To: support@yourcompany.com
Dear Team,
I just wanted to take a moment to thank you for the outstanding support I received last week. I had an issue with my account login, and your representative, Daniel, resolved it within minutes. He was patient, professional, and explained everything clearly.
It's rare these days to find such responsive and helpful customer service. Please pass along my appreciation to Daniel and the entire team. Keep up the great work!
Best regards,
Sarah Klein
+41 79 555 23 11
```

*Sample 2* — another praise scenario with a different structure and an inline email address.

```text
Subject: Kudos to Your Delivery Team
Hi there,
I received my order yesterday, and I'm beyond impressed! The packaging was perfect, and the delivery was even faster than expected. Honestly, I was worried because I've had bad experiences with other companies, but you guys nailed it.
Please keep doing what you're doing. I'll definitely recommend your services to my friends and colleagues.
Cheers,
Michael Bauer
michael.bauer@bluewin.ch
```

##### Complaint samples

*Sample 3* — exercises `Type=complaint` and a labelled phone pattern.

```text
Subject: Extremely Disappointed with My Recent Order
Dear Customer Service,
I'm writing to express my frustration with my recent purchase (Order #45892). The product arrived late, and when I opened the box, it was damaged. I paid extra for express shipping, and this is unacceptable.
I've tried calling your hotline twice, but no one picked up. Please let me know how you plan to resolve this issue. If I don't hear back within 48 hours, I'll have to escalate this matter.
Regards,
Anna Müller
Phone: +41 44 123 45 67
```

*Sample 4* — a complaint without a phone number; expects null for `PhoneNumber`.

```text
Subject: Wrong Item Delivered
Hello,
I ordered a black leather wallet (SKU: WLT-102), but I received a brown one instead. This is the second time this has happened, and I'm honestly losing patience.
Please arrange for the correct item to be sent immediately and let me know how to return the wrong one.
Thank you,
David Steiner
d.steiner@gmail.com
```

##### Incident samples

*Sample 5* — classifies as `incident` and confirms extraction under an urgent tone.

```text
Subject: Safety Concern with Your Product
Dear Team,
I need to report a serious issue. I purchased your electric kettle (Model EK-300) last month, and yesterday it started sparking while plugged in. This could have caused a fire!
Please advise on how to return this product and whether you plan to issue a recall. I'm very concerned about the safety of your customers.
Sincerely,
Laura Meier
Phone: +41 76 888 99 00
```

*Sample 6* — a security-related `incident` with an email address only.

```text
Subject: Data Breach Suspected
Hi,
I received an email claiming to be from your company asking for my login details. It looked suspicious, so I didn't click any links. However, I'm worried my account might be compromised.
Can you confirm if there's been a security breach? Please advise on what steps I should take.
Thanks,
Thomas Keller
thomas.keller@protonmail.com
```

##### Question samples

*Sample 7* — a `question` with no email or phone, verifying null handling.

```text
Subject: Clarification on Warranty Policy
Dear Support,
Could you please clarify your warranty terms? I purchased a laptop from your store six months ago, and I'm wondering if accidental damage is covered.
I checked your website, but the information wasn't very clear. A quick response would be appreciated.
Best regards,
Julia Weber
```

*Sample 8* — a location-oriented `question`, also likely null for the contact fields.

```text
Subject: Do You Ship to Austria?
Hello,
I'm interested in ordering some products from your online store, but I live in Vienna. Do you ship to Austria, and if so, what are the shipping costs and estimated delivery times?
Thank you in advance for your help!
Kind regards,
Peter Hoffmann
```

---

### 🏅 Congratulations! You've completed Use Case #2!

---

### Test your understanding

* Sample 4 and Sample 7 have no phone number. What did the app write into that column, and what would have happened if the prompt had not been told to return null?
* Why does the formula use `Switch()` for **Feedback Type** but assigns the other fields directly?
* What does the user see today if `Predict()` succeeds but returns a `Type` value outside the four expected strings?

**Challenge: Apply this to your own use case**

* Add a confidence check: only patch when the extracted `Title` is non-empty, and notify otherwise.
* Move the extraction into a Power Automate flow triggered by a shared mailbox, so the app is no longer the only entry point.

---

## 🏆 Summary of learnings

True learning comes from doing, questioning, and reflecting — so let's put your skills to the test.

To get the most out of AI extraction in low-code apps:

* **Specify the schema, not the vibe** – Name the fields, name the types, and enumerate the allowed category values. Everything downstream depends on that contract holding.
* **Make missing data explicit** – Instructing the model to return null is what stops it from inventing plausible values.
* **Test the prompt before the app** – A prompt that fails in isolation will fail in the app too, but with far less useful error information.
* **Map vocabularies deliberately** – The model speaks strings, Dataverse speaks choices. Keep the translation in one visible place.
* **Keep the human in the loop** – Extraction fills the form; a person still confirms it. That review step is what makes the automation safe to deploy.

---

### Conclusions and recommendations

**AI-in-app extraction golden rules:**

* Define the output schema before writing a single instruction sentence.
* Give every prompt input a realistic sample value, so the prompt is testable on its own.
* Handle the failure branch explicitly — a `Notify()` beats a silently empty form.
* Reference controls by their actual names; generated apps do not always name things as the documentation assumes.
* Keep the original unstructured text in the record alongside the extracted fields, so a wrong extraction can be audited.
* Re-test after any prompt edit; app formulas depend on the shape of the response, not just its content.

By following these principles, you'll turn transcription work into review work without giving up traceability.

---
