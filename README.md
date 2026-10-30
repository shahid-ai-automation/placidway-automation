# PlacidWay Daily Social Media Content Automation

[![n8n](https://img.shields.io/badge/Workflow-n8n-EA4B71?logo=n8n&logoColor=white)](https://n8n.io/)
[![Source tests](https://img.shields.io/badge/Source--to--draft-Dry%20Run%20Passed-success)](#test-evidence)
[![Facebook](https://img.shields.io/badge/Facebook-Photo%20Post%20Test%20Verified-success?logo=facebook&logoColor=white)](#test-evidence)
[![Project](https://img.shields.io/badge/Project-Prototype-blue)](#current-status)

An n8n automation prototype that discovers public PlacidWay articles, prepares social-media drafts with an LLM, validates the copy, supports a review/approval path, publishes to a candidate-owned Facebook test Page, and records workflow activity.

> **Test summary:** Two separate test milestones are documented below: (1) source-to-draft generation and validation using real PlacidWay articles in dry-run executions, and (2) a successful live Facebook photo-post test on the candidate-owned test Page. The Facebook photo-publishing API path was verified; a complete scheduled end-to-end run and the text-only publishing branch have not yet been independently verified.

## Contents

- [Workflow overview](#workflow-overview)
- [Current status](#current-status)
- [Test evidence](#test-evidence)
- [Example generated drafts](#example-generated-drafts)
- [Architecture](#architecture)
- [Configuration](#configuration)
- [Setup and operation](#setup-and-operation)
- [Security and responsible use](#security-and-responsible-use)
- [Known limitations](#known-limitations)
- [Repository files](#repository-files)

## Workflow overview

```text
Daily Schedule / Manual Trigger
            |
      Public Sitemap
            |
  Parse, Filter & Deduplicate
            |
   Fetch Selected Article
            |
       LLM Draft
            |
 Rule-based Validation
            |
 Optional Second-pass Review
            |
   Approval / Test Publish
            |
  Content Store + Run Log
```

### Included capabilities

- Daily schedule configured for 9:00 AM in the Asia/Karachi timezone, plus a manual trigger.
- Public PlacidWay article sitemap as the discovery source.
- Candidate filtering, URL normalization, and persistent duplicate prevention.
- Selected article-page enrichment with a 10-second delay before the page fetch.
- LLM-generated social copy with a configurable character limit.
- Deterministic content checks and an optional second LLM review.
- Approval-oriented publishing path and Facebook test-Page integration.
- Separate n8n Data Tables for content/deduplication state and execution logging.
- Retry and failure-handling paths included in the workflow.

## Current status

| Component | Status | Notes |
|---|---|---|
| Schedule and manual trigger | Configured | Daily schedule and manual execution trigger are included. |
| Public sitemap source | Configured | Uses the public PlacidWay article sitemap. |
| Article fetch and content preparation | Included | Fetches and enriches the selected article. |
| LLM draft generation | **Dry-run tested** | Successful draft-generation executions using real source articles. |
| Rule-based validation | **Sample run passed** | Validation path is included and has passed a sample run. |
| Second-pass LLM review | Included | Optional review step is enabled in the current configuration. |
| Content store and execution log | Configured in source instance | Uses two n8n Data Tables; destination instances must map their own tables. |
| Approval-oriented path | Included; setup incomplete | Reviewer channel/destination needs environment configuration. |
| Facebook dry-run mode | Disabled in current export | `FACEBOOK_DRY_RUN=false`. |
| Facebook live photo publishing | **Successfully tested** | A photo post was created on the candidate-owned test Page and visually checked. |
| Facebook text-only publishing | Not independently verified | The text-post node is present, but a separate live test has not been recorded. |
| Full scheduled end-to-end run | Not independently verified | Source/draft and Facebook publishing were tested as separate milestones. |
| Reviewer and alert destinations | Not configured | Webhook destinations remain blank in the current setup. |

## Test evidence

The project has two distinct pieces of test evidence. They should not be conflated:

### Test 1 — Source-to-draft dry run

- Executed against real public PlacidWay article sources.
- Sitemap discovery, selected-article content preparation, LLM draft generation, and validation were exercised in dry-run executions.
- Successful draft outputs returned `status: ok` and included validation notes.
- This verifies the source-to-draft path; it does **not** mean those drafts were published.

### Test 2 — Live Facebook photo-post test

- Target: candidate-owned Facebook test Page (not PlacidWay's official Page).
- The `Publish Photo Post (TEST PAGE)` node successfully created a live post.
- The returned Facebook response included a photo ID and a post ID.
- The post was opened on the test Page and visually checked for its caption, article link, hashtags, and featured image.
- This verifies the Page credential and photo-publishing API path for that test.

**What these tests do not prove:** The two milestones were not one uninterrupted scheduled execution. The full scheduled daily workflow, separate text-only publishing path, and long-term operational reliability still require testing.

## Example generated drafts

These examples were captured from successful source-to-draft workflow executions. They are **draft-generation evidence**, not published-post evidence or independent clinical verification. Review medical and healthcare-related wording against the original source before approval.

### 1. Stem cell facial rejuvenation in Japan

**Source:** [Stem Cell Facial Rejuvenation in Japan: A Patient's Five-Entry Diary](https://www.placidway.com/article/9807/Stem-Cell-Facial-Rejuvenation-in-Japan-A-Patients-Five-Entry-Diary)  
**Workflow result:** `ok` · **Validation notes:** present · **Test type:** dry run

> Considering stem cell facial rejuvenation in Japan? Explore the questions behind the journey.
>
> This five-entry diary covers expectations, recovery, safety and key considerations.
>
> The composite case describes cultured adipose-derived cells, Tokyo and two trips about five weeks apart.
>
> It also highlights checking treatment plans, clinic registration and licensed cell-processing labs under Japan's framework.
>
> Read the full article for the diary and planning considerations.
>
> #MedicalTravel #Japan #StemCellResearch #FacialRejuvenation

### 2. Single-port robotic liver surgery in Saudi Arabia

**Source:** [Single-Port Robotic Liver Surgery in Saudi Arabia for Liver Tumors](https://www.placidway.com/article/9836/Single-Port-Robotic-Liver-Surgery-in-Saudi-Arabia-for-Liver-Tumors-Smaller-Incisions-Renewed-Hope)  
**Workflow result:** `ok` · **Validation notes:** present · **Test type:** dry run

> A milestone in single-port robotic liver surgery is being reported in Saudi Arabia.
>
> KFSHRC in Riyadh introduced robotic liver transplantation in 2023 and has completed over 165 robotic liver transplants.
>
> The single-port system uses one small incision for surgical instruments and a 3D high-definition camera.
>
> Read the full article to learn more about this approach and KFSHRC.
>
> #RoboticSurgery #LiverTumors #MedicalTravel #SaudiArabia

### 3. Robot-assisted deep brain stimulation (DBS) in Saudi Arabia

**Source:** [Robot-Assisted Deep Brain Stimulation (DBS) Surgery in Saudi Arabia](https://www.placidway.com/article/9837/Robot-Assisted-Deep-Brain-Stimulation-DBS-Surgery-in-Saudi-Arabia-Restoring-Movement-and-Quality-of-Life)  
**Workflow result:** `ok` · **Validation notes:** present · **Test type:** dry run

> Exploring robot-assisted DBS surgery in Saudi Arabia?
>
> KFSHRC in Riyadh uses an integrated robotic guidance system for severe movement disorders.
>
> DBS is evaluated for Parkinson's disease, essential tremor, dystonia, and medication-refractory motor fluctuations.
>
> High-resolution imaging and real-time navigation support electrode placement with submillimeter accuracy.
>
> Read the full article for more information about the centre and procedure.
>
> #DeepBrainStimulation #DBS #Neurosurgery #SaudiArabia

**Content-safety note:** Workflow validation notes provide source traceability. They are not independent clinical fact-checking or medical advice. A qualified human should review healthcare claims before publication.

## Architecture

1. **Trigger:** daily schedule or manual execution.
2. **Discover:** fetch the configured public sitemap.
3. **Parse and normalize:** extract article URLs, normalize source fields, reject invalid records, and limit candidate volume.
4. **Deduplicate and select:** consult the persistent content store and select an eligible article.
5. **Enrich:** wait before fetching the selected article page and extract available page metadata/content.
6. **Generate:** request social copy from the configured LLM.
7. **Validate and review:** apply deterministic checks and optionally request a second LLM safety review.
8. **Approve and publish:** follow the configured approval path and, after approval, route to the candidate-owned Facebook test Page. The current export has Facebook dry-run disabled.
9. **Persist and observe:** update content state, write a run-log entry, and route failures through the workflow's retry/error handling.

## Configuration

The **Workflow Config** node centralizes the main settings.

| Setting | Current value |
|---|---|
| `APPROVAL_MODE` | `true` |
| `SOURCE_MODE` | `live` |
| `SOURCE_URL` | `https://www.placidway.com/1/sitemap_articles.xml` |
| `MAX_CANDIDATES` | `20` |
| `USE_MOCK_LLM` | `false` |
| `ENABLE_LLM_REVIEW` | `true` |
| `MAX_POST_CHARS` | `500` |
| `FACEBOOK_DRY_RUN` | `false` |
| `FB_TEST_PAGE_ID` | Candidate-owned test Page configured |
| `APPROVAL_TIMEOUT_HOURS` | `24` |
| `MAX_RETRIES` | `3` |

The Facebook Page access token is stored in n8n credentials and is not included in this repository. The workflow references two environment-specific n8n Data Tables: one for content/deduplication state and one for execution logs. Recreate or map these resources when importing the workflow elsewhere.

## Setup and operation

1. Download [the n8n workflow export](./placidway-social-media-automation.json).
2. Import the JSON into your n8n instance.
3. Review the **Workflow Config** node and confirm the source, schedule, approval, and publishing settings.
4. Create or map the two required n8n Data Tables in the destination instance.
5. Configure the required LLM credentials through n8n's credential manager.
6. Configure your own Facebook test Page, Meta Graph API permissions, Page ID, and n8n credential. Never reuse credentials from another environment.
7. For a first-time import, keep approval enabled and use dry-run mode until the destination environment has been checked.
8. Run manually and inspect node outputs, validation/review decisions, Data Table updates, and execution logs before enabling live publishing.

Configuration details may vary with n8n version and Meta app permissions. The current repository export reflects the tested candidate-owned Page configuration; importing it does not transfer credentials or Data Tables.

## Security and responsible use

- Never commit API keys, access tokens, passwords, private webhook URLs, or exported credential values.
- Store credentials in n8n's credential manager or an appropriate secrets manager.
- Inspect workflow exports for secrets and environment-specific identifiers before publishing them publicly.
- Publish only to Pages you own or are explicitly authorized to manage. Do not publish to PlacidWay's official accounts without authorization.
- Human-review healthcare content for factual accuracy and appropriate wording; do not make unsupported treatment claims, guarantees, or promises.
- Respect source-site terms, access policies, and reasonable request rates.

## Known limitations

- The successful Facebook verification covers the **photo-post node** on the candidate-owned test Page only.
- The separate text-only publishing branch has not been independently live-tested.
- A complete scheduled daily run from source discovery through Facebook publishing has not been independently verified.
- Reviewer and alert webhook destinations are blank and require environment-specific configuration.
- Data Tables and LLM credentials must be recreated or mapped when importing into another n8n instance.
- Approval, failure, retry, and longer-running reliability scenarios still need broader verification.

## Repository files

| File | Description |
|---|---|
| [`placidway-social-media-automation.json`](./placidway-social-media-automation.json) | Importable n8n workflow export, including nodes and workflow configuration. |
| [`README.md`](./README.md) | Project documentation, test evidence, setup, configuration, and limitations. |

## License

No license is currently provided. Contact the repository owner before reusing or redistributing this project.
