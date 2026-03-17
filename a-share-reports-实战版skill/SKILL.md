---
name: a-share-reports-portable
description: Portable A-share premarket/intraday/post-close reporting skill for secondary-market workflows. Use when a user wants to复制/迁移一套盘前、盘中、盘后报告规则到另一台 OpenClaw，或要快速搭建“盘前文本 + 盘中截图 + 盘后复盘”流程。 Covers quant/trading-oriented report structure, Wind/WindPy requirements, trading-day gating, Feishu delivery, intraday screenshot rules, and dual event-source enrichment from 知识星球 + 雪球.
---

## Core rules

- Treat this skill as a **portable rule pack**. Replace all placeholder paths/IDs before use.
- Keep intraday output as **PNG screenshot first**; do not downgrade to text unless explicitly requested.
- Use **Wind real-time** for intraday and **WSD close** for post-close.
- Fail closed on missing data: do not fabricate numbers.
- Gate every market task by **trading day + correct session window**.

## Required placeholders to replace

Read `references/portable-setup.md` before first use and replace these values in prompts/scripts/cron jobs:

- `{{WATCHLIST_XLSX}}`
- `{{ZSXQ_STATE_JSON}}`
- `{{FEISHU_USER_ID}}`
- `{{FOCUS_CODE}}` (default can be 688663.SH)
- `{{WORKSPACE_ROOT}}`

## Report standard (all stages)

All reports must use **quant + trading-oriented** structure:

- Give conclusion + threshold + trigger + invalidation + position suggestion.
- Prefer stock **name first, code in parentheses**.
- Add source time and confidence for event-driven content.
- If there is no valid event increment, write `无有效增量` or `无有效共振增量` explicitly.

Also enforce these four fields inspired by the 2026-03-08 public-wechat article:

- Model consistency first
- Market-state layering: `走弱 / 震荡修复 / 走强`
- Main-theme rotation stability
- Risk triad: `环境 / 模型稳定性 / 数据质量`

## Workflow: premarket_0915

Output text report for 09:15.

Required sections:

1. Market temperature score (0-100)
2. Main-theme candidates (>=3), each with:
   - >=2 evidence points
   - trigger condition
   - invalidation condition
3. Liquidity/breadth thresholds
4. T1/T2/T3 trade list (3-8 names)
   - trigger
   - invalidation
   - position hint (`轻/中`)
5. Risk budget / no-trade condition
6. Dual-source event enrichment:
   - 知识星球 recent 24h increment
   - 雪球 热门话题Top10 + 热门榜单/热股Top10 + 热门标的
   - conclude with `星球×雪球共振判断`

## Workflow: intraday_onepager_png

Generate the locked intraday HTML and send full-page PNG.

Hard rules:

- Screenshot must be complete.
- Caption can include event enrichment:
  - `星球×雪球共振`
  - `雪球增量`
  - `星球增量`
- Read dual-source intraday increment before sending:
  - 知识星球 recent 60m
  - 雪球 热门话题/榜单/热股/热门标的 changes

## Workflow: intraday_calibration_1435

Output a 14:35 quant calibration text report.

Required sections:

1. Main-theme strength Top3 (0-100)
2. Deviation vs premarket call
3. T1/T2/T3 add-reduce action
4. Tail-risk score or tail-chasing signal
5. Focus stock single-line execution view
6. `星球×雪球×盘面` three-way validation

## Workflow: postclose_1610

Output post-close text review.

Required sections:

1. Premarket hit-rate
2. Fund-price consistency
3. T1/T2/T3 attribution
4. Error list (>=2)
5. Three next-day verifiable conditions with thresholds
6. Dual-source review:
   - 知识星球 day increment
   - 雪球 day hot-topic / hot-board / hot-stock evolution
   - output hit/deviation judgment with source time + confidence

## Workflow: postclose_1805_png

Generate post-close screenshot using **WSD close** only.

Required card fields:

- Main-theme strength score
- Market breadth (N/A allowed if unavailable)
- Fund consistency (`共振/背离`)
- Three next-day trigger thresholds
- Focus stock trigger/invalidation
- Optional dual-source resonance conclusion

## Cron guidance

Recommended market-time jobs:

- 09:15 premarket text
- every 10 minutes intraday one-pager PNG during trading windows
- 14:35 intraday calibration
- 16:10 post-close text review
- 18:05 post-close WSD-close PNG

Always add trading-day guard before execution.

## What to reuse vs localize

Keep these unchanged:

- report structure
- quant field definitions
- dual-source enrichment logic
- failure behavior

Localize these per deployment:

- Wind script paths
- watchlist path
- Feishu recipient
- focus stock list
- whether knowledge-source names remain the same
