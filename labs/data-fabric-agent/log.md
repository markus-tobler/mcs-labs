# Lab Setup Log — Data Fabric Agent (cas-ai-for-business-day-5)

Lab: https://markus-tobler.github.io/mcs-labs/labs/data-fabric-agent/?event=cas-ai-for-business-day-5

This log tracks every place where the setup for this repo (Shopkart) diverges from the
lab's own instructions, which assume a pre-built shared "Workshop Demo Workspace"
(existing Lakehouse, semantic model, SQL endpoint, report). We don't have that workspace,
so we build the equivalent from this repo's raw data in our own workspace.

## Environment / tooling

- **Automation tool**: no `fab` (Fabric CLI) or `pip` available locally (minimal Python
  install, no `ensurepip`, no sudo used to fix it). Used **Azure CLI (`az rest`)** instead
  to call the Fabric REST API directly (`https://api.fabric.microsoft.com/v1/...`) —
  functionally equivalent, just lower-level (raw JSON payloads instead of a dedicated CLI).
- **Auth**: interactive `az login --use-device-code`, account `mtodevadmin@qfwq.onmicrosoft.com`,
  tenant `qfwq.onmicrosoft.com` (`0624311a-260f-4019-a9f5-730994a5e99b`).
- **Workspace used**: "CAS AI in Business" (`944ee8b4-a98c-47ef-863a-37ffd0c335bd`),
  capacity `4ba380cb-4919-47a7-a41f-4e0541e6aaaf` (Switzerland North). The lab refers to a
  "Workshop Demo Workspace" provided by instructors — we use the user's own pre-existing
  workspace/capacity instead, per the user's setup.

## Data source

- Lab uses a ready-made "E-commerce Order Dataset" / "E-commerce Lakehouse" already
  populated in the shared workspace. We instead load the **Shopkart** repo's own data
  from `Data/*.txt`.
- Source files are **tab-separated, double-quoted, with `\N` as the NULL marker** — not a
  plain CSV — so the load step needs explicit delimiter/quote/null handling (not a
  drag-and-drop "Load to Tables" default).
- The repo has **6 tables** (`Category`, `Customers`, `Order_Items`, `Orders`, `Payments`,
  `Products`); the lab's semantic-model step only names **5** (no `Category`), even though
  `Products.Category_ID` is a foreign key into it.
  - **Decision (user)**: load `Category` into the Lakehouse *and* include it in the
    semantic model / Data Agent context — 6 tables instead of the lab's 5 — so category
    names/descriptions are resolvable instead of raw `CAT0x` codes.

## Naming

- Mirrored the lab's names where possible: Lakehouse = **"E-commerce Lakehouse"**,
  semantic model = **"ecommerce-order-dataset"**.

## Steps performed (append as we go)

1. **Lakehouse creation** — `POST /v1/workspaces/{id}/items` with `type: Lakehouse`.
   - **Naming deviation**: requested display name `"E-commerce Lakehouse"` was rejected
     (`InvalidInput` / `DisplayName is Invalid for ArtifactType`) — Lakehouse names can't
     contain a hyphen (they double as the underlying SQL database identifier). Used
     **`Ecommerce_Lakehouse`** instead. Item id `a16db79f-14d0-4080-b615-fe247d4c2f7b`,
     workspace `944ee8b4-a98c-47ef-863a-37ffd0c335bd`.
   - Creating the Lakehouse **auto-provisioned its SQL analytics endpoint**
     (`Ecommerce_Lakehouse`, SQLEndpoint item `b8400c27-2a7a-4ace-b47b-db6e50d82811`) —
     matches the lab's "SQL Endpoint" prerequisite with no extra step needed.
   - Note: the target workspace ("CAS AI in Business") already contained unrelated
     pre-existing items (NYC Taxi lakehouse/warehouse/semantic model, dataflows, "Company
     Warehouse") from other work — left untouched.

2. **Raw file upload** — the lab expects files to already be sitting in a shared
   Lakehouse; we uploaded `Data/*.txt` ourselves into `Files/raw/` via the **OneLake DFS
   (ADLS Gen2) REST API** (`onelake.dfs.fabric.microsoft.com`), using an
   `https://storage.azure.com` scoped token (not the Fabric API token) and the standard
   create → append → flush sequence. One quirk: the directory-create (`PUT
   ?resource=directory`) call fails with `MissingRequiredHeader` unless `Content-Length: 0`
   is sent explicitly even for an empty body.

3. **Table load** — the lab's Lakehouse already has Delta tables; ours only has raw `.txt`
   files, so we created a **Fabric Notebook** item (`Load Ecommerce Tables`, id
   `18395f5c-06c6-4357-8275-92fad0f0d273`) containing PySpark that reads each file with
   `sep="\t"`, `quote="\""`, `nullValue="\\N"`, `inferSchema=True`, and writes it as a
   managed Delta table via `df.write.format("delta").save(".../Tables/{name}")` (absolute
   OneLake ABFS path, so no default-lakehouse attachment was needed on the notebook). Ran
   via `POST .../jobs/instances?jobType=RunNotebook` (Fabric job scheduler API) rather than
   manually opening the notebook in the portal.
   All 6 tables materialized as Delta folders under `Tables/` and are visible through the
   auto-provisioned SQL analytics endpoint (`diysibqpeymubkpvomezjjpjtm-wtue5femvhxupbr2g775bqzvxu.datawarehouse.fabric.microsoft.com`,
   provisioning status `Success`).

4. **Semantic model** — the lab's shared workspace already has "ecommerce-order-dataset"
   ready to pick as the Data Agent's data source. Ours had to be built:
   - Fabric's normal behavior is to auto-create a *default* semantic model the first time
     a Lakehouse is opened in the portal. Since everything so far was done purely through
     the REST API (no portal visit), **no default semantic model appeared** even after a
     5-minute wait/poll — confirmed absent via both the Fabric Items API and the Power BI
     `datasets` API.
   - Built one explicitly instead, using Microsoft's **`semantic-link-labs`** Python
     library's `sempy_labs.directlake.generate_direct_lake_semantic_model()`, executed
     *inside* the same Fabric notebook (this library isn't installed locally — the local
     machine has no `pip` — but Fabric's managed Spark/Python environment does, so the
     install (`pip install semantic-link-labs`) and the model generation both run
     notebook-side, triggered from the local `az rest` calls).
   - **Deviation/bug worked around**: the first attempt used the `%pip install` Jupyter
     magic, which triggers a kernel/session restart — this crashed the whole job
     (`System_Cancelled_Session_Statements_Failed`) when run non-interactively via the Job
     Scheduler API. Fixed by installing via `subprocess.run([sys.executable, "-m", "pip",
     "install", ...])` instead of the `%pip` magic, and by having each cell log its
     outcome to a file in `Files/raw/sm_log.txt` (readable back over the OneLake API)
     rather than relying on interactive cell output, which the Job Scheduler API doesn't
     expose.
   - Result: semantic model **`ecommerce-order-dataset`** created (item id
     `cbcfcc7e-97d6-479b-94dd-be8aeac18530`) in **Direct Lake** mode over all 6 tables
     (`Category`, `Customers`, `Order_Items`, `Orders`, `Payments`, `Products` — see the
     Category decision above), confirmed via a `DirectLakeFraming` refresh in its refresh
     history.

## Prerequisite status vs. the lab

| Lab prerequisite | Status |
|---|---|
| Fabric capacity + workspace | Already had (user-provided): "CAS AI in Business" |
| E-commerce Lakehouse | Built: `Ecommerce_Lakehouse` (6 Delta tables from this repo's data) |
| SQL Endpoint | Auto-provisioned with the Lakehouse |
| "ecommerce-order-dataset" semantic model (5 tables) | Built with **6** tables (Category included, per user's decision) |
| E-commerce Dataset Report / reference Data Agent | Not built — these are the shared workspace's own example/demo artifacts, not inputs the lab asks the student to pre-build |
| Copilot Studio access + message packs | **Not verified from this session** — needs manual confirmation by the user; out of scope for Fabric-workspace automation (no CLI/API access used for this) |

## Still to do manually

- Confirm Copilot Studio access/message packs at copilotstudio.microsoft.com before
  attempting Use Case #2 of the lab.
- The lab's own step-by-step (create Data Agent, generate meta-prompt instructions,
  publish, then connect from Copilot Studio) still needs to be done in the Fabric/Copilot
  Studio portals — that part is UI-driven by design and wasn't automated here.

