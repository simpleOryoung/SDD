# AI 原生开发

AI 充当研发活动的执行者，人类负责审批和决策。通过标准化的技能驱动 AI 完成需求分析、架构设计、模块详设等工作，产出结构化规约文档。

套件遵循 **SDD（Specification-Driven Development，规约驱动开发）**——先产出规约、经人类审批，再驱动下游活动。规约既是 AI 的工作输入，也是人类的决策载体。

## 文档结构

```
docs/                               # 产品知识库（稳定基线）
├── README.md
├── 00-getting-started/             # 快速开始：本地开发启动指南
├── 01-planning/                    # 规划：总体规划与方案 + 演进路线图 + 执行指南
├── 02-requirements/                # 需求基线：产品全景 + 功能域 PRD
├── 03-design/                      # 设计基线：system/（系统级架构，跨元素时）+ HLD + ER 全景 + 模块详设（含数据模型）
├── 04-development/                 # 开发知识（任何编码动作必须先加载此 README 再按需加载相关知识）
├── 05-testing/                     # 测试基线：策略 + Schema + 执行器规格 + 规范
└── 06-deployment/                  # 部署（待建设）

specs/                              # 规约工作区（迭代过程文档）
├── {阶段}/                         # 迭代需求清单 + 以需求为单位的过程文档
│   ├── 需求清单.md
│   ├── REQ-{编号}-{名称}/
│   │   ├── requirement/            # 需求阶段产出
│   │   ├── design/                 # 设计阶段产出
│   │   ├── tasks.md                # 任务清单
│   │   ├── testing/                # 测试阶段产出
│   │   └── bugs/                   # 关联本需求的 Bug
│   │       └── BUG-{编号}/
│   │           └── bug-record.md
│   └── bugs/                       # 独立来源的 bug
│       └── BUG-{编号}/
│           └── bug-record.md
└── archive/                        # 归档（含 REQ + 关联 bug + 独立 bug）
```

`docs/` 为产品知识库（已审批的稳定规约基线），`specs/` 为规约工作区（迭代中的规约过程文档），迭代关闭后沉淀到 `docs/`。

导航与索引规范见 `context-routing.md`；产出文档的表头规范见 `doc-header.md`。

## 划分单位约定

docs 是按"结构"组织的稳定基线，specs 是按"变更"组织的迭代过程。结构进一步分为业务结构与代码结构，对应两个视角。

| 维度 | docs（存量基线·按结构） | specs（增量过程·按变更） |
|:---|:---|:---|
| PRD | 按**功能域**（业务能力分区） | 按 **REQ**（一次需求） |
| 设计 | 按**模块**（代码组织单元） | 挂 REQ 下、按模块做变更 |

- **PRD 以功能域为单位**：PRD 表达对外业务能力，属业务视角；功能域是业务能力的自然分区，覆盖完整、相对稳定，是承接增量沉淀的归档维度。
- **设计以模块为单位**：模块详设表达代码组织，属实现视角；模块是可独立维护与构建的代码单元。
- **功能域与模块为多对多**：一个功能域通常由多个模块协作实现，一个模块也可能服务多个功能域，二者通过架构层的模块—功能域映射对齐。横切关注点（认证、日志、监控等）不归属任何功能域，统一在架构层描述。
- **增量沉淀**：specs 中以 REQ 为单位的增量规约，迭代关闭后沉淀合并回 docs 中对应的功能域 PRD 与模块设计。

## 技能列表

| 阶段 | 技能 |
|:---|:---|
| 规划 | `planning` |
| 需求 | `req-global` → `req-feature` × N |
| 设计 | 系统级（仅需求跨系统元素时）：`system-global` → `system-feature` × N；软件级：`design-global` → `design-feature` × N |
| 任务拆解 | `tasks` × N |
| 编码 | `implement` × N |
| 测试 | `test-global` → `test-impl`（基础设施） → `test-feature` × N → `test-run` × N |
| 环境 | `dev-start`（任意阶段可用） |
| 缺陷修复 | `bug-fix`（横切支持过程，可在任意顺发阶段下游触发） |
| 版本协作 | `git-workflow`（Git 协作：拉取/提交/推送/MR·PR，横切工具，任意阶段可用） |

技能由 Claude Code 通过 SKILL.md 注册，AI 可根据用户意图自动匹配。当用户明确提到技能名称时，读取对应的 SKILL.md 并严格遵循其中的流程执行。

## 阶段流程

生命周期（参照 ISO/IEC 12207）：规划 → 需求 → 设计（系统级 → 软件级；系统级仅需求跨系统元素时）→ 任务拆解 → 编码/测试设计（并行） → 测试执行 → 部署。

| 场景 | 流水线 |
|:---|:---|
| 新项目 | planning → req-global → req-feature × N → system-global（跨系统元素时）→ design-global → test-global → test-impl（基础设施） → design-feature × N → [tasks → implement ∥ test-feature] × N → test-run × N |
| 存量接入 | planning → req-global → req-feature × N → system-global（跨系统元素时）→ design-global → design-feature × N → 基线就绪 |
| 增量迭代 | req-global → req-feature × N → system-feature × N（跨系统元素时）→ design-feature × N → [tasks → implement ∥ test-feature] × N → test-run × N |

`dev-start` 与 `git-workflow` 均为横切技能，可在任意阶段按需执行，不受流水线顺序约束。

## 开发知识（docs/04-development）

项目特有的开发知识，用户渐进维护，**README 路由 + 按需加载**（先读 `docs/04-development/README.md` 再按"建议加载场景"拉取）。含两类：

**① 固定知识**：

| 文件 | 作用 | 建议加载场景 |
|:---|:---|:---|
| `coding-standards.md` | 编码规范：命名、分层、错误处理、日志、API 风格等 | 涉及代码编写的任务（R10）|
| `environments.md` | 环境指南：各环境地址、连接、日志位置、凭据 | 环境诊断 / bug 定位 / 连 shell 查日志（`bug-fix`、`dev-start`）|

**② 项目积累知识**（项目按需沉淀、数量不限）：新增时须在 `04-development/README.md` 登记，并且可以据此进行识别和路由。

## 行为约束

- **R1 产出不回改**：编码不改设计，设计不改需求，测试不改需求/设计/代码。发现上游问题 → 报告用户。
- **R2 门控先行**：阶段转换前检查门控条件，未通过不推进。
- **R3 单源事实**：同一信息只在一个地方定义。跨阶段引用用编号或链接，不复制原文。
- **R4 变更标注**：增量产出必须标注 `[新增]` / `[修改]` / `[删除]`。
- **R5 范围控制**：增量设计不超出影响分析确定的模块。需扩展时先补充影响分析或回退到需求阶段。
- **R6 人类最终决策权**：🔒 为门控点。采集类（收集用户输入）信息充分时可跳过；审批类（AI 产出待确认）必须由用户确认，不可跳过。
- **R7 技能资产直读**：`assets/` 路径解析为 `.claude/skills/{技能名}/assets/{文件名}`，优先使用 Read 直接读取。
- **R8 策略先行**：首次进入测试阶段前，`test-global` 必须已产出项目级测试基线（`docs/05-testing/`）。
- **R9 Schema 契约**：`test-feature` 产出的 JSON 必须符合 `test-case-schema.json`，这是生成端与执行端的唯一契约。
- **R10 编码规范**：编写任意项目代码(正式代码、单元测试、升级脚本等)前，必须先加载 `docs/04-development/README.md`，再按需加载项目编码规范、经验。
- **R11 任务清单生命周期**：`tasks.md` 仅服务于编码阶段，下游阶段（测试/部署/运维/复盘）不作为事实依据；事实源为"设计文档 + 代码 + git log"。
- **R12 BUG 生命周期**：每个缺陷由 `bug-fix` 维护单文件 `bug-record.md`；bug-fix 不直改下游 spec/代码，按归因分发到原产出阶段技能修订。
