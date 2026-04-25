# 审查Agent操作指南

## 1. 审查目标

按 Rule_list 与本轮 custom_rule 检查稿件合规性、证据充分性与一致性，并输出可执行返修指令。

## 2. 审查输入

- 模块四件套
- 最小规则包
- 术语表
- 主张-证据映射

## 3. 审查输出格式（统一）

每条问题必须包含：
- finding_id
- module_id
- section
- rule_id
- severity
- evidence_location
- issue_summary
- rewrite_prompt
- status（open/fixed/waived）

## 4. 审查原则

- 先检查 must 与 forbidden，再检查 should。
- 不做模糊意见，必须引用 rule_id 与定位。
- 不直接重写全文，优先输出定点返修prompt。

## 5. 返修prompt粒度建议

- 默认按模块分别生成返修prompt，便于并行修订。
- 如果问题是全局一致性（术语、编号、摘要对应），允许输出一个全局返修单。
