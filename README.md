# PlacidWay Daily Social Media Content Automation

An n8n workflow prototype that finds source articles from PlacidWay, drafts social copy with an LLM, validates the result, optionally runs a second review pass, and records each run. It is currently configured for **approval-first, dry-run operation**; live Facebook publishing is not configured.

## What it does

- Runs on a daily schedule (9:00 AM, Asia/Karachi) or can be started manually.
- Reads candidate article URLs from the PlacidWay article sitemap.
- Limits candidate processing and applies a crawl delay.
- Generates a concise social post from a source article.
- Validates the generated post and can run a separate LLM review.
- Supports an approval-oriented workflow and a Facebook dry-run mode.
- Stores content/deduplication state in an n8n Data Table and logs run outcomes in a second table.
- Exposes retry and alert configuration points for operational hardening.

## Current status

| Area | Status |
|---|---|
| Scheduled and manual triggers | Configured |
| Live source sitemap | Configured |
| LLM post generation | Tested in a successful dry run |
| Validation and review | Configured; successful sample passed |
| Content store and run log | Configured in n8n Data Tables |
| Approval-first mode | Enabled by configuration |
| Facebook publishing | **Not yet configured** |
| Facebook dry run | Enabled |
| Reviewer/alert webhooks | Optional URLs are currently blank |

The workflow has successfully completed a dry run using a real source article and live LLM generation. That verifies the content-generation path, not live publishing to Facebook.

## Example output

Source article: [Stem Cell Facial Rejuvenation in Japan: A Patient's Five-Entry Diary](https://www.placidway.com/article/9807/Stem-Cell-Facial-Rejuvenation-in-Japan-A-Patients-Five-Entry-Diary)

> Considering stem cell facial rejuvenation in Japan? Explore the questions behind the journey. This five-entry diary covers expectations, recovery, safety and key considerations. The composite case describes cultured adipose-derived cells, Tokyo and two trips about five weeks apart. It also highlights checking treatment plans, clinic registration and licensed cell-processing labs under Japan's framework. Read the full article for the diary and planning considerations.

This is an illustrative generated draft, not medical advice or a claim that a treatment is appropriate for any individual. Review health-related claims and source accuracy before approval or publication.

## Workflow overview

1. **Trigger** — scheduled run or manual execution.
2. **Source discovery** — retrieve candidate URLs from the configured sitemap, subject to candidate limits and crawl delay.
3. **Content preparation** — fetch source material and prepare the article context.
4. **Draft generation** — ask the configured LLM to write a social post.
5. **Quality checks** — run rule-based validation and optional LLM review.
6. **Approval / dry run** — keep the workflow approval-oriented and avoid a live Facebook post while publishing credentials and test-page settings are not configured.
7. **Persistence and observability** — store content state and write run outcomes to the run log.

## Configuration

The workflow uses n8n workflow variables for its operating mode. Review these in the exported workflow before running it in another environment:

| Setting | Current value / purpose |
|---|---|
| `APPROVAL_MODE` | `true` |
| `SOURCE_MODE` | `live` |
| `SOURCE_URL` | `https://www.placidway.com/1/sitemap_articles.xml` |
| `MAX_CANDIDATES` | `20` |
| `USE_MOCK_LLM` | `false` |
| `ENABLE_LLM_REVIEW` | `true` |
| `MAX_POST_CHARS` | `500` |
| `FACEBOOK_DRY_RUN` | `true` |
| `APPROVAL_TIMEOUT_HOURS` | `24` |
| `MAX_RETRIES` | `3` |

The workflow also references two n8n Data Tables for content state and run logging. When importing into another n8n instance, verify that these tables exist and update any environment-specific references.

## Setup

1. Import the exported workflow JSON into your n8n instance.
2. Review node settings, workflow variables, Data Table references, and any disabled nodes.
3. Configure the required LLM connection in n8n credentials.
4. Keep approval mode and Facebook dry run enabled while testing.
5. Run manually and inspect the execution, generated post, validation/review results, and run-log entry.
6. Only enable Facebook publishing after configuring a test Facebook Page, the appropriate Meta Graph API permissions/credentials, page ID, and a deliberate approval step.
7. Test with a page you control before considering production use.

Exact credential setup depends on the n8n and Meta app configuration used by the operator.

## Security notes

- Do not commit API keys, access tokens, passwords, private webhook URLs, or credential exports.
- n8n credential values should be managed through n8n's credential store or environment-specific secret management.
- Review the exported JSON for secret values and personal or environment-specific identifiers before making it public.
- Use a test Facebook Page you control; do not publish to PlacidWay's official social accounts without explicit authorization.

## Known limitations / next steps

- Live Facebook publishing is not complete or demonstrated by the current dry-run result.
- Add and test the human approval interaction and reviewer webhook if required for the intended deployment.
- Configure alert delivery and verify failure handling in the target n8n instance.
- Capture multiple real-source generated examples and execution evidence.
- Record a short walkthrough showing source extraction, generated content, review/approval behavior, and the dry-run result.

## Repository contents

The repository is intended to contain the n8n workflow export, this README, and any safe example artifacts or documentation needed to reproduce the demo. Secrets and live credentials must remain outside version control.

## License

No license has been added yet. Contact the repository owner before reusing this project.
