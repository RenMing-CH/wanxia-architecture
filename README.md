# 小说剧情连续化提取维护架构

> **多模型、多轮次、分步骤路由协作，而不是让一个模型从头到尾独自承担全部工作。**
>
> 按任务性质拆分工作，明确每个阶段的输入、输出、职责和边界；让不同模型承担适合自己的任务，通过受控交接、阶段检查和作者审核完成长篇小说资料的连续提取与维护。
>
> **这不是一个模型的单兵作战，而是一套多角色协作的工作体系。**

本项目面向长篇小说创作，关注的不是单章摘要，而是随着章节持续推进，如何提取、整理、更新、维护可追溯的创作资料，并让作者在后续写作中按需调用。

本仓库公开的是**工作原理、抽象架构、模块边界和产品方向**，不是产品源代码，也不披露内部技术实现。

## 核心原则

1. **任务拆分：** 复杂任务按目标拆分，不把所有工作压给一个模型。
2. **职责清晰：** 各阶段有明确输入、输出和责任边界。
3. **结果可审阅：** 模型输出先作为候选资料，不自动成为正式事实。
4. **作者最终确认：** 作者可以修改、接受或拒绝候选结果。
5. **连续维护：** 后续章节带来的资料变化，应能被识别、检查、更新并保留历史。
6. **正文边界：** 资料提取与维护不能擅自修改小说正文，也不能在作者确认后绕过已确认资料去正文中重新裁定事实。

## 一、小说剧情连续化提取

~~~mermaid
flowchart TD
    A[作者创作或导入章节] --> B[识别本轮任务]
    B --> C[按职责进行分步路由]
    C --> D[多模型协作处理]
    D --> E[形成结构化候选资料]
    E --> F[阶段检查与整理]
    F --> G[作者审核与修订]
    G -->|接受| H[进入已确认资料]
    G -->|修改| E
    G -->|拒绝| I[不进入正式资料]
    H --> J[关联来源与历史]
    J --> K[后续章节按规则维护]
    K --> L[供创作工作台按需参考]
~~~

### 工作流程

- **输入：** 作者创作或导入的章节内容。
- **任务路由：** 识别当前任务的性质，将不同工作拆分到相应阶段。
- **阶段协作：** 不同模型在明确边界内完成各自任务，传递受控的中间结果。
- **候选资料：** 输出进入作者审核环节，不因模型生成而自动获得事实权威。
- **作者审核：** 作者检查、修改、接受或拒绝候选资料。
- **连续维护：** 后续章节产生的变化，围绕已确认资料进行更新与追溯。
- **按需引用：** 创作工作台读取作者已确认或主动维护的资料，不代替作者决定剧情。

具体模型名单、数量、轮次、路由条件、提示词、内部字段、身份分配和后端实现均不在公开范围内。

## 二、白金作家助手

白金作家助手是面向作者的创作管理工作台。它围绕正文、已确认资料、作者手记和世界构建工具组织工作，而不是把所有功能压缩成一个聊天窗口。

### 已通过本机功能验收

- **正文与资料联动：** 作者编辑正文时，可以按需查看相关档案、手记、地图、数值或已保存的时间线信息。
- **正文批注直达作者手记：** 作者选中正文片段后，可添加下划线、突出显示或批注，并选择将批注保存到作者手记；保留章节来源和正文定位信息。
- **作者控制联动：** 支持关闭、悬停或固定联动结果，减少编辑过程中的界面干扰。

### 已有界面或持续完善中的能力

- **作者手记：** 管理便签、提醒、优先级和正文来源。
- **数值管理器：** 展示数值、单位、主体、来源和确认状态；未经作者核对的内容不作为有效数值。
- **世界位面与时间线：** 将空间、时间、地图、阵营及画布式组织能力整合到统一的世界构建工作流中。
- **正文返工辅助：** 为作者提供可控的修改范围与要求，作者决定是否采用结果。

界面截图和本机验收不代表所有模块均已完成云端部署或正式商业发布。

## 三、未来规划

### 人设与角色辅助

帮助作者维护人物设定、关系和状态变化，重点是资料一致性与可追溯性。人物动机、行为和命运仍由作者决定。

### 文字游戏

将世界设定与互动叙事结合，提供作者可编辑、可调整的文字游戏创作与管理能力。

### 一句话生成自己的文字游戏

作者用一句话描述题材、玩法或体验目标，系统协助形成可继续编辑的文字游戏雏形。该能力属于未来规划，不代表当前版本已具备完整生成、运行或发布能力。

## 四、数据与权限边界

| 数据类别 | 定义 | 边界 |
|---|---|---|
| 小说正文 | 作者创作或导入的原始文本 | 作者拥有编辑决定权，资料提取不擅自改写正文 |
| 模型候选 | 模型分析后提出的候选信息 | 未经作者审核不成为正式资料 |
| 已确认资料 | 作者接受或修订后确认的资料 | 可供后续创作参考，并保留来源与历史 |
| 作者手记 | 批注、提醒、灵感和个人备注 | 不自动转化为剧情事实 |
| 数值参考 | 数量、单位、归属主体等记录 | 需作者核对并确认 |
| 世界位面与时间线 | 作者维护的空间、事件与时间表达 | 展示和组织已保存资料，不自动推进剧情 |

## 五、公开范围

**公开：** 高层工作流程、模块职责、作者审核原则、数据边界、产品方向和经处理的界面演示。

**不公开：** 产品源代码、内部提示词、私有字段与模板、内部身份分配逻辑、数据库结构、内部 API 契约、模型路由细则、私有测试数据、真实小说正文和部署配置。

本仓库不授予开源许可证。公开可见不等于允许复制、改编或商业使用；相关权利仍归各自权利人所有。

---

**抄袭前端没JJ！**

**抄袭前端没JJ！**

**抄袭前端没JJ！**

---

# English

## Continuous Novel Story Extraction and Maintenance Architecture

This project explores an author-controlled workflow for continuous information extraction and maintenance across long-form fiction.

### Multi-model collaboration

Complex work is divided into stages with explicit responsibilities, inputs, outputs, and boundaries. Multiple model stages collaborate through controlled handoffs and checks instead of assigning every task to a single model.

### Continuous story information workflow

1. The author writes or imports a chapter.
2. The system identifies the current task and routes work by purpose.
3. Multiple model stages process bounded tasks.
4. Candidate story information is organized and checked.
5. The author reviews, edits, accepts, or rejects the candidates.
6. Approved information is maintained with source and revision traceability.
7. The authoring workspace references saved information when needed.

### Platinum Author Assistant

The workspace brings together the writing editor, approved information, author notes, numeric references, and world-building tools. It supports contextual information panels and annotations that can be saved to author notes with source references.

The author remains in control. Unapproved model output is not treated as fact, annotations do not silently rewrite the manuscript, and numeric references require author review.

### Roadmap

Planned directions include character/persona support, interactive text games, and a one-sentence entry point for creating an editable text-game concept. These are roadmap items, not claims of currently released functionality.

### Scope and exclusions

This repository publishes high-level architecture principles and product boundaries only. It does **not** publish source code, internal prompts, private schemas, identifier allocation logic, database structures, API contracts, private test data, deployment configuration, or manuscript content.

No open-source license is granted. All applicable rights remain with their respective rights holders unless separately authorized.
