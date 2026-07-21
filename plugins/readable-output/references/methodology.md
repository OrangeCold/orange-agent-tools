# 易读性方法论：权威出处与边界

本文件是 `SKILL.md` 中每条优化规则的**出处、可信度与边界**。日常输出优化直接用 SKILL.md 即可；当需要向他人解释「为什么这么改」、或判断某条规则是否适用于边界场景时，再读本文件。

> 方法论来源规模：5 个搜索角度 · 25 个来源被抓取 · 121 条声明提取 · 25 条进入对抗式验证 · **23 条确认 / 2 条被反驳**。
>
> 每条来源都标注了「**机构名 + 一句话核心结论**」，并保留裸 URL 作溯源。即使链接失效，按机构名也能检索到原文；结论本身已内联，不依赖点开链接。

---

## 一、Plain Language 的官方定义（最权威的验收标准）

美国《Plain Writing Act of 2010》（Public Law 111-274）对「plain language」的官方三维度定义：读者能否 **Find（找到）→ Understand（理解）→ Use（使用）**。这三步直接作为 SKILL.md 输出前自检的验收标准。

- **plainlanguage.gov**（美国政府 plain language 官方站）：上述 Find→Understand→Use 三维度定义的官方出处。 <https://www.plainlanguage.gov/about/definitions/>
- **美国国会 Public Law 111-274**（Plain Writing Act of 2010，法律全文）：要求联邦机构面向公众的文件使用 plain language，为该定义提供法律背书。 <https://www.govinfo.gov/link/plaw/111/public/274>

## 二、国家档案馆 10 条写作原则（机械可检查）

写给读者 · 要点先行 · 一段一旨 · 主动语态 · 短句 · 日常用词 · 删冗词 · 主谓相邻 · 用标题/列表/表格 · 校对。

对应 SKILL.md 的：第 3 点（一段一旨 / 主谓相邻）、第 4 点（主动语态删冗词）、第 6 点（标题/列表）。

- **美国国家档案馆 NARA**（10 条写作原则页）：上述 10 条原则的官方出处。 <https://www.archives.gov/open/plain-writing/10-principles.html>

## 三、信息结构与信息架构（证据最强）

### 倒金字塔（Inverted Pyramid）

结论/最重要信息前置，细节按重要性递减。可观察益处：快速建立心理模型、降低交互成本、支持扫读、可从底部裁剪而不丢要点。5 步操作法：识别关键点 → 排序次要信息 → 简洁写作 → 每个元素前置重要信息 → 补充摘要/要点列表。前置规则贯彻到每个层级（标题描述性、正文以要点开头、每段首句最重要、每句开头词承载信息并预示后续）。

- **NN/g（Nielsen Norman Group）**：倒金字塔的益处（利于扫读、可从底部裁剪不丢要点）与 5 步操作法的出处。 <https://www.nngroup.com/articles/inverted-pyramid/>
- **Purdue OWL（普渡大学在线写作实验室）**：倒金字塔在新闻/说明写作中的落地操作指引。 <https://owl.purdue.edu/owl/subject_specific_writing/journalism_and_journalistic_writing/the_inverted_pyramid.html>

### 信息分块（Chunking）

源自 George Miller 1956（工作记忆 7±2），现代研究 Cowan 精化为 ~4。拆成小块更易理解、处理、记忆。不只是视觉分隔，而是表达内容关系与层级的手段；组织成 schema（图式）后，一个高层级图式只占一个工作记忆位。

- **NN/g**：分块原理（Miller 7±2、Cowan 精化为 ~4）与「分块即表达信息关系与层级、非单纯视觉分隔」的出处。 <https://www.nngroup.com/articles/chunking/>

### 渐进式披露（Progressive Disclosure）⚠️ 本 skill 不依赖

首层只展示少数最重要信息，次层按需展开，同时满足「功能丰富」与「简洁」。超文本是其天然实现，对应文档的「主文档 → 子页面/折叠区」层级组织。

**为什么本 skill 不依赖它**：其严格定义偏向交互模式（show more / accordion），与静态排序的倒金字塔**并非同一概念**。本 skill 定位是纯文本/markdown 输出润色，不产生交互载体，因此主要用倒金字塔与分块，不依赖交互式披露。

- **NN/g**：渐进式披露的定义（偏向交互模式 show more / accordion）出处，也是「它 ≠ 倒金字塔」这一判断的依据。 <https://www.nngroup.com/articles/progressive-disclosure/>

### 信息架构四组件（Rosenfeld & Morville）

组织结构、标注系统、导航系统、检索系统——可作为文档检查的四个维度参考。本 skill 主要落在「组织结构」与「标注系统（术语一致）」。

- **Rosenfeld & Morville《Information Architecture for the World Wide Web》**（IA 经典教材，书）：IA 四组件（组织 / 标注 / 导航 / 检索）的提出者。（教材，无单一公开 URL）

## 四、Google / Microsoft 技术写作风格（工业级可机械检查规则）

- **Microsoft**：信息前置、关键词前置（支持扫读）；核心方法「Break it up. Step it out. Layer. Short sentences and fragments.」印证了 chunking。
- **Google**：第二人称（you 而非 we）、主动语态、对话式但不轻浮、避免 jargon / 陈词滥调 / 文化特定引用（利于全球读者与翻译）。
- **一致性方法论**：多级参考源优先级 = 项目特定风格 → Google 风格指南 → 第三方参考（Merriam-Webster 拼写、Chicago Manual 第 17 版、Microsoft 技术风格）。即「单一事实来源 + 局部覆盖」。
- **排版规则**：标题用 sentence case、顺序步骤用编号列表、其他用项目符号、相关数据对用描述性列表、强制 serial comma。
  > 注：serial comma / sentence case 主要针对英文；中文输出遵循对应的中文排版习惯，但规则背后的精神——**一致性**——是通用的。

- **Microsoft Writing Style Guide**（微软官方技术写作风格指南）：本节 Microsoft 规则（信息前置、Break it up. Step it out. Layer.、sentence case、serial comma）的权威出处。 <https://learn.microsoft.com/en-us/style-guide/welcome/>
- **Google Developer Documentation Style Guide**（谷歌官方开发者文档风格指南）：本节 Google 规则（第二人称、主动语态、避免 jargon）与「参考源优先级体系」的权威出处。 <https://developers.google.com/style>

## 五、⚠️ 被反驳的声明（本 skill 刻意避开）

| 被反驳声明（0–3 票） | 原因 | 本 skill 的应对 |
|---|---|---|
| 「联邦指南规定了固定的撰写流程顺序：受众→组织→写作原则→网页适配→测试，可作 Skill 流水线模板」 | plainlanguage.gov 指南是**维度并列**的组织，不是强制线性流程，不能套成流水线 | SKILL.md 明确「不把维度当线性流水线」，6 点是并行体检项 |
| 「Microsoft 风格以『先扫读后精读』为首要原则，为 F/Z 型阅读提供落地规则」 | 引文未提及 F/Z 型，**归因不成立** | 本 skill 不断言 F 型 / Z 型阅读模式 |

## 六、⚠️ 证据薄弱、本 skill 暂不采用的维度

- **F 型 / Z 型阅读模式**：本轮未通过对抗式验证进入确认结论，且主要针对网页眼动，对长文档适用性存疑。本 skill 不以此作为规则。（原始研究来自 NN/g 眼动实验，但本轮未纳入确认结论，故不附链接以免暗示可引用。）
- **Sweller 认知负荷三分法（内在 / 外在 / 相关）**：未获直接原文证据，仅通过 chunking（Miller 1956）间接覆盖。
- **排版视觉阈值**（行宽 50–75 字符、字号、对比度）：主要针对网页渲染，纯文本 / markdown 输出场景不适用，本 skill 不采用。

## 七、可读性公式为什么不采用

Flesch-Kincaid、Gunning Fog、SMOG、Coleman-Liau 等公式**依赖音节计数与词长机制**，对中文（表意文字、无明确词边界、分词多样）**无效**——MDPI《Algorithms》2025 同行评审论文称其为 "irrelevant tools for the task"。此外公式只测表层特征，忽略连贯性、信息组织、读者先验知识（Ginny Redish 批判：同一文本不同公式可相差 3–5 个年级）。

**中文若要度量**，应改用语言学特征：类符形符比 TTR（词汇多样性）、平均句长、总词数、词汇难度等级（仅这四类特征 SVM 三分类准确率即达 83.23%，加词性特征达 86.45%）。但本 skill 定位是「输出前快速优化」，不引入度量公式——靠**结构化特征**（长句、要点堆叠、术语漂移、结论位置）定位难读点即可。公式只能当辅助体检指标，不能当唯一判据。

- **MDPI《Algorithms》2025**（同行评审开放获取论文，CC BY）：「传统可读性公式对中文 irrelevant」「中文应改用 TTR / 平均句长 / 词汇难度等语言学特征（仅四类特征 SVM 三分类准确率 83.23%）」的出处。 <https://www.mdpi.com/1999-4893/18/12/777>
- **Ginny Redish**（可读性公式批判，effortmark.co.uk）：「公式只测表层特征、忽略连贯性与信息组织，同一文本不同公式可相差 3–5 个年级」的出处。 <https://www.effortmark.co.uk/readability-formulas-seven-reasons-to-avoid-them-and-what-to-do-instead/>

## 八、留白与开放问题（暂未固化进 skill）

1. Sweller 认知负荷三分法如何直接对接文档结构规则？
2. F-pattern / Z-pattern 原始证据对**长文档**（非网页）的适用性？
3. 中文排版阈值（行宽含全角字符、字号、对比度、中英混排）的实证规则？
4. 更多评估方法（SUM、readability heuristics、Cloze 测试、用户测试协议）哪些可半自动化集成？

这些待补研究后再考虑纳入；当前版本以已确认证据为准。
