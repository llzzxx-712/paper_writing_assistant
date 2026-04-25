# Prompt 模板：证据缺口补全请求（给用户或数据脚本agent）

当前写作遇到证据缺口，请先完成数据/图表补全再继续写作。

缺口描述：
- module_id: {module_id}
- section: {section}
- 关联rule_id: {rule_id}
- 缺失证据类型: {metric/table/figure/ablation/visualization}
- 当前阻塞结论: {blocked_claim}

请执行：
1. 编写或运行脚本生成所需证据。
2. 输出结果文件路径、关键指标、图表说明。
3. 给出可复现实验参数（数据划分、随机种子、环境版本）。

回传格式：
- [Artifacts] 文件路径清单
- [Metrics] 指标表
- [Method] 脚本与参数
- [ConclusionReady] 可回填论文的结论句
