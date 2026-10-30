# PlacidWay Daily Social Media Content Automation

[![n8n](https://img.shields.io/badge/Workflow-n8n-EA4B71?logo=n8n&logoColor=white)](https://n8n.io/)
[![Mode](https://img.shields.io/badge/Publishing-Live%20Test%20Page%20Verified-success)](#current-status)
[![Status](https://img.shields.io/badge/Project-Prototype-blue)](#current-status)

An n8n workflow for discovering public PlacidWay articles, drafting social media copy with an LLM, validating the draft, optionally running a second review pass, publishing approved content to a candidate-owned Facebook test Page, and logging each execution.

> **Current scope:** Source-to-draft generation has passed dry-run testing. A live Facebook photo post was also successfully published to and visually verified on the candidate-owned test Page. The workflow remains in approval mode by default; this does not mean unattended daily end-to-end publishing has been verified.

## Repository contents

| File | Purpose |
|---|---|
| [placidway-social-media-automation.json](./placidway-social-media-automation.json) | Importable n8n workflow export |
| [README.md](./README.md) | Project overview, setup, configuration, and known limitations |

## Workflow at a glance

**Daily schedule / Manual trigger → Sitemap fetch → Candidate filtering & deduplication → Page enrichment → LLM draft → Rule-based validation → Optional LLM review → Approval / dry-run path → Content store & run log**

### Key capabilities

- **Scheduled and manual execution:** configured for 9:00 AM in the Asia/Karachi timezone, with a manual test trigger.
- **Source discovery:** reads the public PlacidWay article sitemap and limits processing to a bounded set of candidates.
- **Responsible crawl behavior:** includes a 10-second delay before fetching a selected article page.
- **Duplicate prevention:** uses an n8n Data Table to track content and avoid processing the same canonical URL repeatedly.
- **LLM-assisted drafting:** generates concise social copy with a configurable character limit.
- **Quality checks:** rule-based validation followed by an optional second LLM review.
- **Operational visibility:** writes run outcomes to a separate n8n Data Table; retry and alert paths are included in the workflow.
- **Controlled publishing:** approval mode remains enabled. The current export is configured for live publishing to the candidate-owned test Page after approval; the Page access token is held in n8n credentials, not in this repository.

## Current status

| Component | Status |
|---|---|
| Schedule and manual trigger | Configured |
| Public sitemap source | Configured |
| Live article fetch and content preparation | Included in workflow |
| LLM draft generation | Successful dry run recorded |
| Rule-based validation and second-pass review | Included; sample run passed |
| Content store and execution log | Configured in the source n8n instance |
| Approval-oriented path | Included; reviewer channel needs environment configuration |
| Facebook dry run | Enabled |
| Live Facebook publishing | **Not yet configured or verified** |
| Reviewer and alert webhook destinations | Blank / require configuration |

The source-to-draft path was validated in dry-run executions using real source articles and LLM generation. Separately, the Facebook photo-post node successfully created a live post on the candidate-owned test Page; the caption, article link, hashtags, and featured image were visually checked. This confirms that the Page credential and photo-publishing API path worked for that test. It does not establish that a complete scheduled daily run or the text-only publishing branch has been independently verified.

## Example generated drafts

The following three outputs were captured from existing successful workflow executions. Each returned `status: ok` and included validation notes linking the draft's main statements to its source article. These are examples of generated copy, **not** proof that the medical claims have been independently verified or that any post was published.

### Example 1 — Stem cell facial rejuvenation in Japan

**Source:** [Stem Cell Facial Rejuvenation in Japan: A Patient's Five-Entry Diary](https://www.placidway.com/article/9807/Stem-Cell-Facial-Rejuvenation-in-Japan-A-Patients-Five-Entry-Diary)  
**Workflow result:** `ok` · **Validation notes:** present

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

### Example 2 — Single-port robotic liver surgery in Saudi Arabia

**Source:** [Single-Port Robotic Liver Surgery in Saudi Arabia for Liver Tumors](https://www.placidway.com/article/9836/Single-Port-Robotic-Liver-Surgery-in-Saudi-Arabia-for-Liver-Tumors-Smaller-Incisions-Renewed-Hope)  
**Workflow result:** `ok` · **Validation notes:** present

> A milestone in single-port robotic liver surgery is being reported in Saudi Arabia.
>
> KFSHRC in Riyadh introduced robotic liver transplantation in 2023 and has completed over 165 robotic liver transplants.
>
> The single-port system uses one small incision for surgical instruments and a 3D high-definition camera.
>
> Read the full article to learn more about this approach and KFSHRC.
>
> #RoboticSurgery #LiverTumors #MedicalTravel #SaudiArabia

### Example 3 — Robot-assisted deep brain stimulation (DBS) in Saudi Arabia

**Source:** [Robot-Assisted Deep Brain Stimulation (DBS) Surgery in Saudi Arabia](https://www.placidway.com/article/9837/Robot-Assisted-Deep-Brain-Stimulation-DBS-Surgery-in-Saudi-Arabia-Restoring-Movement-and-Quality-of-Life)  
**Workflow result:** `ok` · **Validation notes:** present

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

**Review note:** The validation notes explain which source details support each draft. They are traceability aids, not independent clinical fact-checking. A qualified human should verify medical statements, wording, and source accuracy before approval or publication.
## Architecture

1. **Trigger:** daily schedule or manual execution.
2. **Discover:** fetch the configured public sitemap.
3. **Parse and normalize:** extract URLs, normalize fields, reject invalid records, and limit candidate volume.
4. **Deduplicate and select:** check the persistent content store and select an eligible item.
5. **Enrich:** wait before fetching the selected article page and extract available page metadata/content.
6. **Generate:** request a social post from the configured LLM.
7. **Validate and review:** apply deterministic checks and optionally request a second LLM safety review.
8. **Approval / publishing:** route through the configured approval path; the current export targets the candidate-owned test Page with Facebook dry-run disabled. The dry-run simulation remains available for mock/test scenarios.
9. **Persist and observe:** update content state, write a run-log entry, and route failures through configured error/alert handling.

## Configuration reference

The workflow's **Workflow Config** node centralizes the main settings.

| Setting | Current value |
|---|---|
| APPROVAL_MODE | true |
| SOURCE_MODE | live |
| SOURCE_URL | https://www.placidway.com/1/sitemap_articles.xml |
| MAX_CANDIDATES | 20 |
| USE_MOCK_LLM | false |
| ENABLE_LLM_REVIEW | true |
| MAX_POST_CHARS | 500 |
| FACEBOOK_DRY_RUN | false (live test-Page publishing path) |\n| FB_TEST_PAGE_ID | Candidate-owned test Page configured |
| APPROVAL_TIMEOUT_HOURS | 24 |
| MAX_RETRIES | 3 |

The export references two n8n Data Tables: one for content/deduplication state and one for run logging. These are environment-specific n8n resources; they must exist and be mapped correctly when importing into another instance.

## Run it in your own n8n instance

1. Download [placidway-social-media-automation.json](./placidway-social-media-automation.json).
2. Import the JSON into n8n.
3. Inspect the workflow and review the **Workflow Config** node.
4. Recreate or map the required n8n Data Tables in your environment.
5. Configure the required LLM credentials using n8n's credential manager; credentials are not supplied in this repository.
6. Keep APPROVAL_MODE=true and FACEBOOK_DRY_RUN=true during initial tests.
7. Execute the workflow manually and inspect node outputs, validation/review decisions, and the run-log record.
8. Before enabling any live publishing, configure a Facebook test Page you control, valid Meta Graph API permissions/credentials, the correct Page ID, and a tested human approval process.

Configuration details may vary with n8n version and Meta app permissions.

## Security and responsible use

- Never commit API keys, access tokens, passwords, private webhook URLs, or exported credential values.
- Store credentials in n8n's credential store or an appropriate secrets manager.
- Inspect workflow exports for secret values and environment-specific identifiers before publishing them publicly.
- Do not publish to PlacidWay's official social accounts without explicit authorization.
- Review medical and healthcare-related content for accuracy and appropriate wording before publication.
- Respect source-site terms, access policies, and reasonable request rates.

## Known limitations and next steps

- The live photo-post test succeeded on the candidate-owned test Page. The full scheduled daily workflow, text-only publishing branch, and long-running operational reliability should still be verified separately.
- Reviewer and alert webhooks are not configured in this export.
- Data Tables and LLM credentials need to be configured in the destination n8n instance.
- Capture at least three reviewed examples from real source articles and preserve safe execution evidence.
- Record a short walkthrough of source extraction, draft generation, validation/review, and dry-run behavior.
- Test approval, failure, and retry paths before considering production use.

## License

No license is currently provided. Please contact the repository owner before reusing or redistributing this project.
