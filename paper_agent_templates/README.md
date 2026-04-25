# paper_agent_templates 使用说明

## 目标

让任意用户仅使用以下三类材料即可跑通论文多agent流程：
- Rule_list（可机读规则卡）
- 本目录中的处理模板文档
- 本目录中的prompt模板

## 一次启动顺序

1. 先给经理agent：10_prompt_经理Agent启动.md
2. 经理给模块agent：11_prompt_模块Agent写作任务.md
3. 经理给审查agent：12_prompt_审查Agent审查任务.md
4. 经理给模块agent返修：13_prompt_模块Agent返修任务.md
5. 遇到证据缺口：14_prompt_证据缺口补全请求.md
6. 遇到新增细化要求：15_prompt_新增细化需求注入.md

## 文档清单

- 00_流程总览.md：全流程定义
- 01_经理Agent操作指南.md：经理执行规范
- 02_模块Agent操作指南.md：模块执行规范
- 03_审查Agent操作指南.md：审查执行规范
- 04_模板_任务注册表.md：文件定位与状态追踪模板

## 推荐实践

- 每轮先更新任务注册表，再发审查任务。
- 审查结果优先按模块拆分返修prompt，提升并行效率。
- 对 must 与 forbidden 问题设置硬门槛，不达标不进入摘要与总结阶段。
