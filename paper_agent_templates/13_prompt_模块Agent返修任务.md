# Prompt 模板：模块Agent返修任务

你是模块agent。请根据审查问题单对 {section_scope} 执行定点返修。

输入：
- 原稿四件套：{original_package}
- 审查问题单：{review_findings}
- 本轮新增规则（如有）：{custom_rules}

执行要求：
1. 逐条处理 open 状态问题。
2. 每条问题必须回填“修改位置 + 修改说明 + 对应rule_id”。
3. 若问题依赖新增数据，先提交补证据计划，再提交最终改文。

输出：
- 修订后正文
- 修订后自检报告
- 修订变更日志（finding_id -> action）
- 仍待确认项
