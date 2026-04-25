# 可机读规则卡（Rule Cards）v1

本文件用于多-agent论文写作编排。目标是让经理agent可按章节裁剪规则包，让模块agent仅读取必要规则，同时不遗漏全局硬约束。

## 1. 机器读取约定

字段约定如下：
- id: 规则唯一编号。
- scope: 规则作用域。可取 global、abstract、ch1、ch2、ch3、ch4、ch5、references、formatting。
- severity: must / should / forbidden。
- type: content / structure / language / citation / evidence / format / consistency。
- rule: 规则文本（可直接显示给agent）。
- check_method: auto / manual / hybrid。
- auto_check_hint: 自动检查提示（正则、关键词、计数等）。
- violation_example: 典型违规样例。
- fix_prompt_hint: 返修prompt模板片段。

## 2. 规则卡（YAML）

```yaml
version: 1.0
last_updated: 2026-04-25
scopes:
  - global
  - abstract
  - ch1
  - ch2
  - ch3
  - ch4
  - ch5
  - references
  - formatting

rules:
  - id: G-MUST-001
    scope: [global]
    severity: must
    type: language
    rule: 全文使用第三人称学术表达，避免第一人称“我/我们”。
    check_method: hybrid
    auto_check_hint: 检索关键词 我们|本人|我认为
    violation_example: 我们提出了一种方法。
    fix_prompt_hint: 将主语改为“本文/该研究/本课题”，并保持客观语气。

  - id: G-MUST-002
    scope: [global]
    severity: must
    type: consistency
    rule: 全文术语前后一致，同一概念不可出现多种称呼。
    check_method: manual
    auto_check_hint: 构建术语映射表后做冲突扫描
    violation_example: 同一处分别写“特征提取”和“特征抽取”且未定义区别。
    fix_prompt_hint: 统一为术语表中的标准名称，并批量替换冲突用词。

  - id: G-MUST-003
    scope: [global]
    severity: must
    type: citation
    rule: 所有引用内容与参考文献条目一一对应，不得失配。
    check_method: hybrid
    auto_check_hint: 检查文中引用编号是否均存在于文末文献列表
    violation_example: 文中出现[17]但参考文献仅到[15]。
    fix_prompt_hint: 补齐缺失文献或更正引用编号，确保编号连续且可追溯。

  - id: G-MUST-004
    scope: [global]
    severity: must
    type: language
    rule: 非公知术语或缩写首次出现时给出全称与释义。
    check_method: hybrid
    auto_check_hint: 扫描大写缩写词，检查首次出现附近是否有全称
    violation_example: 首次出现“CSP”但未解释含义。
    fix_prompt_hint: 在首次出现位置补充“中文全称（英文全称，缩写）”。

  - id: G-SHOULD-005
    scope: [global]
    severity: should
    type: structure
    rule: 段落之间使用逻辑连接词，避免硬切换。
    check_method: manual
    auto_check_hint: 检查段首是否存在 然而/因此/鉴于此/具体地
    violation_example: 上下段主题跳转但无过渡。
    fix_prompt_hint: 增加承接句，明确“问题-方法-结果”的连接关系。

  - id: G-MUST-006
    scope: [global]
    severity: must
    type: format
    rule: 图表编号、标题命名和正文引用必须一致且可定位。
    check_method: hybrid
    auto_check_hint: 抽取 图x-y/表x-y 并检查是否被正文引用
    violation_example: 文中“见表3-2”，实际不存在该表。
    fix_prompt_hint: 修正编号或正文引用，保证一一对应。

  - id: G-MUST-007
    scope: [global]
    severity: must
    type: citation
    rule: 参考文献总数不少于15篇，英文文献不少于3-5篇。
    check_method: auto
    auto_check_hint: 计数参考文献条目及英文条目
    violation_example: 文献共11篇，英文仅2篇。
    fix_prompt_hint: 补充近5年高质量文献并更新正文引用。

  - id: G-MUST-008
    scope: [global]
    severity: must
    type: citation
    rule: 优先引用近5年高水平会议或期刊文献。
    check_method: manual
    auto_check_hint: 统计文献年份分布，识别过时条目占比
    violation_example: 大量引用10年前综述，缺少近年成果。
    fix_prompt_hint: 增补近5年代表性工作，并在现状分析中对比其优缺点。

  - id: A-MUST-001
    scope: [abstract]
    severity: must
    type: structure
    rule: 摘要完整覆盖目的、方法、结果、结论四要素。
    check_method: manual
    auto_check_hint: 检查是否出现对应语义句群
    violation_example: 仅写背景和方法，缺少结果与结论。
    fix_prompt_hint: 按“目的-方法-结果-结论”重组摘要内容。

  - id: A-MUST-002
    scope: [abstract]
    severity: must
    type: language
    rule: 中文摘要约400字，不分段，避免“本文/文章/作者/我们”等字样。
    check_method: auto
    auto_check_hint: 字数统计+禁词扫描
    violation_example: 摘要写“本文提出...”且分为两段。
    fix_prompt_hint: 调整到单段并删除禁词，改为客观陈述句。

  - id: A-MUST-003
    scope: [abstract]
    severity: must
    type: language
    rule: 英文摘要避免使用“In the paper”等表达。
    check_method: auto
    auto_check_hint: 检索固定短语 in the paper
    violation_example: In the paper, we design...
    fix_prompt_hint: 改写为被动或客观表达，如“This study proposes...”。

  - id: A-SHOULD-004
    scope: [abstract]
    severity: should
    type: content
    rule: 开发类课题需回答动机、目标、关键问题、价值、主要内容五问。
    check_method: manual
    auto_check_hint: 五问清单覆盖检查
    violation_example: 仅描述实现过程，未交代价值。
    fix_prompt_hint: 补写应用价值和关键问题解决点。

  - id: C1-MUST-001
    scope: [ch1]
    severity: must
    type: content
    rule: 1.1需从宏观背景收敛到具体技术问题，并有量化数据支撑。
    check_method: hybrid
    auto_check_hint: 检查数据表达（百分比/年份/样本量）
    violation_example: 仅写“近年来很重要”，无任何数据。
    fix_prompt_hint: 引入权威数据或政策指标，补齐问题收敛链路。

  - id: C1-MUST-002
    scope: [ch1]
    severity: must
    type: content
    rule: 1.2综述需分类比较，禁止文献罗列式堆砌。
    check_method: manual
    auto_check_hint: 检查是否按方法/时间/技术路线分组
    violation_example: 连续多句“某某提出了...”。
    fix_prompt_hint: 先分组，再写每组核心思想、代表成果和局限。

  - id: C1-MUST-003
    scope: [ch1]
    severity: must
    type: content
    rule: 1.2末段必须明确1-2个技术缺口并衔接1.3。
    check_method: manual
    auto_check_hint: 末段应出现“尚未解决/仍存在挑战”等缺口表达
    violation_example: 结尾仅写“研究较少”。
    fix_prompt_hint: 指出可检验的技术缺口，并引出本文目标。

  - id: C1-MUST-004
    scope: [ch1]
    severity: must
    type: content
    rule: 1.3研究目的需可检验，意义需分理论与工程两层。
    check_method: manual
    auto_check_hint: 检查是否出现可度量目标和分层意义
    violation_example: 仅写“有重要意义”。
    fix_prompt_hint: 增加量化目标，并分别给出理论意义和应用意义。

  - id: C1-MUST-005
    scope: [ch1]
    severity: must
    type: structure
    rule: 1.4研究内容与章节安排必须与目录和后文严格对应。
    check_method: hybrid
    auto_check_hint: 比对章节标题清单一致性
    violation_example: 绪论写了“第六章”，正文无该章。
    fix_prompt_hint: 以最终目录为准重写章节安排，逐章对应。

  - id: C2-MUST-001
    scope: [ch2]
    severity: must
    type: content
    rule: 第二章仅引入与后文直接相关的理论与技术，禁止无关百科式扩写。
    check_method: manual
    auto_check_hint: 每节末需说明“本文如何使用该技术”
    violation_example: 大篇幅讲Java发展史且后文未使用。
    fix_prompt_hint: 删除无关内容，并补充与后文映射关系。

  - id: C2-MUST-002
    scope: [ch2]
    severity: must
    type: format
    rule: 公式需规范编号，变量首次出现必须解释。
    check_method: hybrid
    auto_check_hint: 检查公式编号模式和变量解释句
    violation_example: 出现符号 lambda 但未定义。
    fix_prompt_hint: 在公式后补充变量定义并统一编号。

  - id: C2-MUST-003
    scope: [ch2]
    severity: must
    type: format
    rule: 伪代码必须包含Input/Output，采用4空格缩进。
    check_method: auto
    auto_check_hint: 扫描伪代码块是否含 Input 和 Output
    violation_example: 仅有步骤列表，无输入输出定义。
    fix_prompt_hint: 重写伪代码头部并补齐输入输出说明。

  - id: C34-MUST-001
    scope: [ch3, ch4]
    severity: must
    type: structure
    rule: 章节标题需包含“技术名词+工作类型”，并保持正式风格。
    check_method: manual
    auto_check_hint: 标题匹配“基于XX的XX方法/系统/优化”
    violation_example: 第三章 模型改进。
    fix_prompt_hint: 改为具体技术标题，并体现工作类型。

  - id: C34-MUST-002
    scope: [ch3, ch4]
    severity: must
    type: content
    rule: 章节结构应覆盖问题分析、方法设计、实验设置、结果分析四段主链路。
    check_method: manual
    auto_check_hint: 检查小节标题覆盖度
    violation_example: 只有方法和结果，缺少实验设置。
    fix_prompt_hint: 增补缺失小节并补写过渡句。

  - id: C34-MUST-003
    scope: [ch3, ch4]
    severity: must
    type: evidence
    rule: 主实验必须表格对比全部基线，并给出文字解读。
    check_method: hybrid
    auto_check_hint: 检查“表”后是否跟随解释段落
    violation_example: 只给表格无任何分析。
    fix_prompt_hint: 补写最优/次优对比、提升幅度与原因分析。

  - id: C34-MUST-004
    scope: [ch3, ch4]
    severity: must
    type: evidence
    rule: 必须包含消融实验，验证各模块有效性与贡献。
    check_method: manual
    auto_check_hint: 检查是否存在“去掉模块A/B”对比
    violation_example: 没有任何消融表。
    fix_prompt_hint: 增加模块级消融并解释协同效应。

  - id: C34-MUST-005
    scope: [ch3, ch4]
    severity: must
    type: evidence
    rule: 必须包含可视化分析，且图像为本文模型产出。
    check_method: manual
    auto_check_hint: 图注中声明数据来源/模型版本
    violation_example: 使用他人论文截图。
    fix_prompt_hint: 替换为本文实验生成的可视化并补充解读。

  - id: C34-MUST-006
    scope: [ch3, ch4]
    severity: must
    type: evidence
    rule: 预处理或中间表示改进必须给出“前后对比+定量指标”。
    check_method: manual
    auto_check_hint: 检查是否同时出现 before/after 与数值变化
    violation_example: 仅说“效果明显提升”。
    fix_prompt_hint: 增加同条件对照实验和指标变化值。

  - id: C34-MUST-007
    scope: [ch3, ch4]
    severity: must
    type: content
    rule: 本章问题定义需与1.3研究目的中的至少一条严格对应。
    check_method: manual
    auto_check_hint: 建立“研究目的-章节任务”映射表
    violation_example: 本章讨论的问题在绪论目标中从未出现。
    fix_prompt_hint: 对齐1.3目标并重写本章开头问题定义。

  - id: C34-MUST-008
    scope: [ch3, ch4]
    severity: must
    type: format
    rule: 算法流程图和模块图必须为原创，模块命名与正文一致。
    check_method: manual
    auto_check_hint: 核对图中文字与正文术语表
    violation_example: 图中写Feature Block，正文写特征融合层且未说明。
    fix_prompt_hint: 统一图文命名，并替换非原创图。

  - id: C5-MUST-001
    scope: [ch5]
    severity: must
    type: content
    rule: 总结需给出3-4条核心工作，每条包含“动作+对象+结果(指标)”。
    check_method: manual
    auto_check_hint: 检查每条是否含动词和量化结果
    violation_example: 对模型进行了研究。
    fix_prompt_hint: 改写为可量化结论句，并对应前文章节。

  - id: C5-MUST-002
    scope: [ch5]
    severity: must
    type: consistency
    rule: 总结不得引入正文未出现的新数据、新结论。
    check_method: hybrid
    auto_check_hint: 新数字与正文指标库比对
    violation_example: 结论出现97.8%，正文未出现该值。
    fix_prompt_hint: 删除或补充正文证据后再保留该结论。

  - id: C5-MUST-003
    scope: [ch5]
    severity: must
    type: content
    rule: 展望应基于已验证局限，控制在2-3条且每条给出具体技术方案。
    check_method: manual
    auto_check_hint: 检查“局限->方案”配对关系
    violation_example: 未来将尝试更多算法。
    fix_prompt_hint: 指向明确技术路线与可执行实验计划。

  - id: C5-FORBID-004
    scope: [ch5]
    severity: forbidden
    type: language
    rule: 禁止“完美收尾式”空话，不得用宏大口号替代技术展望。
    check_method: auto
    auto_check_hint: 扫描 相信在不久的将来|必将取得巨大进步 等表达
    violation_example: 相信本领域将迎来跨越式发展。
    fix_prompt_hint: 替换为具体且可验证的未来工作描述。

  - id: F-MUST-001
    scope: [formatting]
    severity: must
    type: format
    rule: 图、表、公式、伪代码格式全篇统一，编号策略一致。
    check_method: manual
    auto_check_hint: 抽样检查编号样式是否混用
    violation_example: 部分按章编号，部分全文连续编号。
    fix_prompt_hint: 统一编号策略并全篇批量修正。

  - id: F-MUST-002
    scope: [formatting]
    severity: must
    type: format
    rule: 章节编号与标题格式遵循统一规范，不使用混杂样式。
    check_method: auto
    auto_check_hint: 检查一级标题是否统一数字样式
    violation_example: 同时出现“第一章”与“1 引言”。
    fix_prompt_hint: 统一为一种编号风格并更新目录。

  - id: F-SHOULD-003
    scope: [formatting]
    severity: should
    type: format
    rule: 图表应具有自明性，图注或表注能脱离正文表达核心信息。
    check_method: manual
    auto_check_hint: 检查图注是否包含对象、条件、结论关键词
    violation_example: 图名仅为“结果图”。
    fix_prompt_hint: 重写图表标题与图注，补齐实验条件与对比对象。

  - id: R-MUST-001
    scope: [references]
    severity: must
    type: citation
    rule: 参考文献格式遵循GB/T 7714-2015。
    check_method: manual
    auto_check_hint: 抽样检查作者、题名、来源、年份、卷期页等要素
    violation_example: 仅有标题和年份，无来源信息。
    fix_prompt_hint: 按GB/T 7714补齐并统一格式。

  - id: Q-MUST-001
    scope: [global]
    severity: must
    type: consistency
    rule: 摘要、绪论、结论三部分不得大面积重复同句。
    check_method: hybrid
    auto_check_hint: 段落相似度阈值检查
    violation_example: 三处完全复用“本文针对...提出...”。
    fix_prompt_hint: 保留主题一致性但重写表达侧重点。

  - id: Q-MUST-002
    scope: [global]
    severity: must
    type: consistency
    rule: 中英文摘要语义应基本对应，不得出现关键结论不一致。
    check_method: manual
    auto_check_hint: 对齐关键词与指标数值
    violation_example: 中文写95.2%，英文写93.1%。
    fix_prompt_hint: 逐句对齐中英文关键结论与指标。

  - id: Q-SHOULD-003
    scope: [global]
    severity: should
    type: format
    rule: 目录标题与正文标题实时同步，避免修改后不一致。
    check_method: auto
    auto_check_hint: 目录标题集合与正文标题集合差集检查
    violation_example: 目录仍是“系统实现”，正文改成“方法验证”。
    fix_prompt_hint: 统一标题并重新生成目录。
```

## 3. 章节下发规则（给经理agent）

- 输入: target_scope, task_type。
- 输出: minimal_rule_pack。
- 生成逻辑:
  - 永远下发: scope 包含 global 的 must + forbidden。
  - 按章节下发: scope 命中 target_scope 的全部 must/forbidden，以及 should 中与 task_type 相关项。
  - 特殊附加:
    - target_scope=ch3/ch4 时，强制附加 evidence 类型全部 must。
    - target_scope=abstract 时，附加 Q-MUST-001 与 Q-MUST-002。
    - target_scope=ch5 时，附加 C5-MUST-002。

## 4. 审查输出结构（给审查agent）

审查agent输出应为结构化列表，每条包括：
- finding_id
- module_id
- section
- rule_id
- severity
- evidence_location
- issue_summary
- rewrite_prompt
- status (open/fixed/waived)

## 5. 版本与扩展说明

- 本版为规则卡v1，已覆盖通用规则与章节关键规则。
- 若后续增加学校模板细则，可继续追加规则卡，不改已有id。
- 建议新增规则按前缀分组（G/A/C1/C2/C34/C5/F/R/Q），避免冲突。
