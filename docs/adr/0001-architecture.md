## Architecture
Market data lands raw, moves through bronze, silver and gold Delta tables in Unity Catalog, runs as a Databricks Jobs workflow, and ships from Git through GitHub Actions and Asset Bundles.

## Phases 0-2: foundations to gold tables
The first five weeks get a deployable pipeline running from Git, then fill it with ingestion and transformations. The plan assumes a real-time market data theme, 10-15 hours a week, and 8-10 weeks in total.
## Phase 0: Foundations (week 1)
• Set up the repo skeleton and one Databricks workspace on the Premium tier (for serverless compute), named tidemark-data, with two Unity Catalog catalogs: tidemark_dev and tidemark_prod. - DONE
• Set a monthly budget with alerts in Azure Cost Management before running any job, and keep early runs small: daily or hourly schedules, one or two trading pairs, and the smallest SQL warehouse with a short auto-stop.
• Create the bundle config with dev and prod targets, and a CI workflow that only lints at this point.
• Write ADR-001 on why this architecture, and a README stub with the problem statement.
• Done when: the budget alert is set and an empty "hello" job deploys from Git to the dev catalog.
## Phase 1: Ingestion (weeks 2-3)
• Pick one public market data source, such as an exchange REST or websocket API.
• Land raw events as-is, then load bronze incrementally. Auto Loader and Structured Streaming are the options to compare; the choice is worth an ADR.
• Add a metadata and audit layer: ingestion timestamp, source, and a record of each run.
• Done when: a scheduled job pulls data end to end and bronze grows without duplicates on re-runs.
## Phase 2: Transformations (weeks 4-5)
• Silver: schema enforcement, deduplication, type fixes, and handling late or out-of-order data.
• Gold: analytics-ready tables, such as OHLC candles, volume and volatility metrics.
• Wire these into one multi-task job with dependencies, so a failed task stops the downstream ones.
• Decide whether transformations are PySpark, SQL or dbt-databricks. The lean is dbt for gold, since it also shows modelling skills.
• Done when: one job runs bronze, silver and gold in order, and a failure in one task is visible and recoverable.

## Phases 3-5: reliability, deployment, polish
Weeks 6-9 turn a working pipeline into one that looks senior: tested, automatically deployed and documented. Week 10 is buffer.
## Phase 3: Quality and reliability (week 6)
• Add unit tests for transformation logic and one integration test against a small sample dataset.
• Add data quality checks at the silver and gold boundaries, plus job-level retries, timeouts and failure alerts.
• Add schema evolution handling, which is directly relevant to your day-job work and a good interview story.
• Done when: you can break the data on purpose and show how the pipeline catches it.
## Phase 4: CI/CD and environments (week 7)
• On a pull request: lint, run unit tests and validate the bundle.
• On merge to main: deploy to dev, then promote to prod with approval.
• Use a service principal rather than personal credentials, and document the cost and compute choices.
• Done when: you never deploy by hand.
## Phase 5: Serving and polish (weeks 8-9)
• Build a Databricks SQL dashboard on the gold tables.
• Write the README (architecture diagram, how to run it, design decisions and trade-offs), the ADRs, and a data dictionary.
• Optionally write a short blog post or LinkedIn write-up.
• Done when: a stranger could understand and run the project in 15 minutes.
Week 10: Buffer
Spare time for slipped phases, a demo recording, and tidying commit history.