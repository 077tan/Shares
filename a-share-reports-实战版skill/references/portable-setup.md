# Portable setup

## Replace these placeholders first

- `{{WORKSPACE_ROOT}}`: target OpenClaw workspace root
- `{{WATCHLIST_XLSX}}`: target watchlist Excel path
- `{{ZSXQ_STATE_JSON}}`: 知识星球 state json path
- `{{FEISHU_USER_ID}}`: target Feishu user id
- `{{FOCUS_CODE}}`: primary focus stock

## Recommended local files/scripts on target machine

The target machine should have equivalents for:

- trading day check script
- Wind intraday HTML generator
- Wind post-close generator
- screenshot renderer (Chrome headless or browser fullPage)
- optional event collectors for 知识星球 / 雪球

## Suggested cron layout

1. `09:15` → `premarket_0915`
2. `09:30-11:30, 13:00-15:00 every 10m` → `intraday_onepager_png`
3. `14:35` → `intraday_calibration_1435`
4. `16:10` → `postclose_1610`
5. `18:05` → `postclose_1805_png`

## Minimal prompt templates

### Premarket

`使用技能 a-share-reports-portable 执行 Workflow: premarket_0915。重点 {{FOCUS_CODE}}。无数据不编。`

### Intraday one-pager

`使用技能 a-share-reports-portable 执行 Workflow: intraday_onepager_png。必须发送完整PNG截图。`

### Intraday calibration

`使用技能 a-share-reports-portable 执行 Workflow: intraday_calibration_1435。先做Wind连接校验。`

### Post-close text

`使用技能 a-share-reports-portable 执行 Workflow: postclose_1610。必须按WSD close口径复盘。`

### Post-close PNG

`使用技能 a-share-reports-portable 执行 Workflow: postclose_1805_png。必须按WSD close口径截图发送。`

## Migration notes

- If the target machine has no Wind, keep the structure but replace Wind-specific execution with the local equivalent.
- If the target machine has no 知识星球 or 雪球 collector yet, keep the section names and explicitly output `无有效增量` until collectors are wired.
- If the target machine uses a different messaging channel, keep the report schema unchanged and only swap delivery instructions.
