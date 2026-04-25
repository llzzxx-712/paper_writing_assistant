# 论文多 Agent 协作模板（本科毕设通用）

### 做好了项目但是不知道怎么写高质量的课设报告/学位论文？这个项目来帮你！

**简单的说这就是一个教AI怎么工作的文档组，你只需要有vibe coding工具、做好的项目，本项目就可以教你的AI写出高质量的报告**

**把它下载到你的项目文件夹下，然后让项目经理agent读《Rule_list.md》和《prompt_经理Agent启动.md》两个文件，后面你就按照项目经理的建议当prompt搬运工即可（详见下方“快速开始”）**

content_standard是作者总结的本科论文规范，Rule_list是将其转化成AI易检索的带标记语言形式。

本项目核心目的就是通过一套标准流水线使得agent产出的文本符合这个“规范”。

工作流程简介（用户只需要当prompt搬运工即可）：
1. 启动阶段：经理agent读取 Rule_list 与模板包，确认章节分工、时间轮次、验收标准。
2. 蓝图阶段：经理agent生成论文蓝图、术语表、主张-证据映射表。
3. 首轮写作：经理agent向各模块agent下发章节任务prompt，模块agent提交四件套（正文草稿、自检报告、证据清单、待确认项）。
4. 审查返修：经理agent向审查agent下发审查prompt，审查agent按 rule_id 生成问题单与返修prompt。
5. 多轮收敛：模块agent按返修prompt定点修改，经理agent评估是否进入下一轮。
6. 收尾阶段：核心章节达标后，经理agent撰写摘要与第5章总结展望，做全局一致性审计。

适用场景：
- 本科毕设
- 课程大作业论文
- 有代码实现但写作经验不足的技术项目
- 多人协作或多 Agent 协作写作

## 你将得到什么

本仓库提供三类核心资产：
1. 规则卡：将论文规范结构化为可机读规则。
2. 处理模板：约定各角色职责、输入输出和轮次流程。
3. Prompt 模板：可直接复制给经理 Agent、模块 Agent、审查 Agent使用。

目标是做到：
- 角色分工清晰
- 审查可追溯
- 返修可执行
- 适配不同学校或不同课题

## 目录结构

- Rule_list.md：可机读规则卡（含 scope、severity、rule_id、检查建议）
- paper_agent_templates/：流程与 Prompt 模板
  - 00_流程总览.md
  - 01_经理Agent操作指南.md
  - 02_模块Agent操作指南.md
  - 03_审查Agent操作指南.md
  - 04_模板_任务注册表.md
  - 10_prompt_经理Agent启动.md
  - 11_prompt_模块Agent写作任务.md
  - 12_prompt_审查Agent审查任务.md
  - 13_prompt_模块Agent返修任务.md
  - 14_prompt_证据缺口补全请求.md
  - 15_prompt_新增细化需求注入.md

## 快速开始（5 步）

### 第 1 步：准备你的项目素材

至少准备以下内容：
- 在vscode/cursor/claude code等vibe coding工具中的项目
- 已有实验结果、日志、截图（若有）

### 第 2 步：让经理 Agent 启动流程

将以下文件交给经理 Agent（随便创建一个对话告诉它你现在是这个项目的经理agent）：
- Rule_list.md
- paper_agent_templates/10_prompt_经理Agent启动.md

经理 Agent 应输出：
- 论文蓝图
- 术语表
- 主张-证据映射
- 模块任务拆分

### 第 3 步：并行下发模块写作任务

经理 Agent 向各模块 Agent 下发：
- paper_agent_templates/11_prompt_模块Agent写作任务.md

每个模块 Agent 必须提交四件套：
1. 正文草稿
2. 自检报告
3. 证据清单
4. 待确认项

### 第 4 步：发起审查与返修

经理 Agent 向审查 Agent 下发：
- paper_agent_templates/12_prompt_审查Agent审查任务.md

审查结束后，经理 Agent 向模块 Agent 下发返修：
- paper_agent_templates/13_prompt_模块Agent返修任务.md

### 第 5 步：收敛后再写摘要与总结

在核心章节（通常第 1-4 章）通过审查后，再由经理 Agent 统一撰写：
- 摘要
- 总结与展望

避免过早写摘要导致反复重写。

## 两个关键机制

### 1) 证据缺口机制（强烈建议开启）

当模块 Agent 发现论文要求有数据支撑，但项目中没有可用数据或图表时：
- 不允许编造结论
- 必须先触发补证据流程
- 使用文件：paper_agent_templates/14_prompt_证据缺口补全请求.md

### 2) 增量规则注入机制

规则卡不可能覆盖所有细节。遇到个性化要求时：
- 用户提出额外需求
- 经理 Agent 生成 custom_rule 并注入本轮任务
- 使用文件：paper_agent_templates/15_prompt_新增细化需求注入.md

如果该规则具有普适性，再回写 Rule_list.md。

## 推荐协作规范

- 所有审查问题都绑定 rule_id，避免口水战。
- 审查意见必须包含定位和可执行返修 Prompt。
- 使用任务注册表跟踪轮次与状态：paper_agent_templates/04_模板_任务注册表.md。
- must 或 forbidden 未通过时，不进入最终收尾阶段。

## 常见问题（FAQ）

### Q1：可以只用一个 Agent 吗？

可以，但推荐至少区分写作与审查两个角色，避免自审偏差。

### Q2：没有实验图怎么办？

先走补证据流程，生成必要图表和指标后再写结论段。

### Q3：只想写某一章可以用吗？

可以。经理 Agent 可按章节 scope 下发最小规则包。

### Q4：不同学校格式要求不同怎么办？

保留现有规则卡框架，在 Rule_list.md 里追加本校 custom_rule 即可。

## 建议的 GitHub 使用方式

- 将本仓库作为模板仓库（Template Repository）
- 每位同学 Fork 后，替换自己的项目资料
- 保留 Rule_list 与模板目录结构，减少上手成本

## 致使用者

这套方案的核心不是让 Agent 代写，而是把写作流程工程化：
- 规则显式化
- 证据可追溯
- 返修可执行

欢迎按你的学校规范继续扩展 Rule_list.md，并提交改进建议。