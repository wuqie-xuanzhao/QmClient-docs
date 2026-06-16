# QmClient Docs 资料库

本目录是历史调研、现状说明和后续规划的整理区。当前入口优先使用 `index.html`。

## 分区

- `current/`：仍适合维护为当前代码现状说明的 HTML 文档。同名 Markdown 原文已移入归档区，只作为来源材料。
- `planning/`：需求草案、方案稿、候选 backlog。优先使用 `planning/index.html`，这里的内容不能直接视为已实现功能。
- `research/`：调研材料。优先使用 `research/index.html`，可作为背景资料，但应优先核对当前代码和 `docs/ai-workflow/`、`qmclient_scripts/` 下的权威说明。
- `archive/`：已明显过时、错误或不再适用的历史报告。只保留追溯价值。

## 维护规则

1. 当前事实优先放进 `current/`，并在文首标注审查日期、代码范围和可信度。
2. 未实现或未复核内容放进 `planning/` 或 `research/`，不要混进当前说明。
3. 已知错误结论移入 `archive/`，不要继续作为实现依据。
4. 新的 HTML 文档复用 `../../qmclient-html-template.html` 的视觉语言。
5. 维护规则、归档边界和验证口径见 `maintenance-status.html`。
