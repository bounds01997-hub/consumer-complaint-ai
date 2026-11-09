# consumer-complaint-ai

微众消保投诉智能分类与多维归因研究项目。

本仓库用于组织投诉文本分类、多维语义分析与人工复核的研究代码、公开结构模板和实验文档。当前仅完成仓库骨架初始化，尚无可运行的分类模型、数据处理流水线或 Streamlit 页面，也不代表银行官方系统或生产能力。

## 业务标准与当前边界

- **用户提供的六层体系是业务标准**。六层的名称、定义、标签、适用条件及映射关系，必须以用户合规授权后的原始文件为准。
- 公开仓库仅提供六个位置的空结构模板，不包含、推断或代写实际业务标签。不预设六层之间一定存在父子树关系。
- 实际原始文件、标签字典和映射表由用户在合规授权后本地维护，建议放在被 Git 忽略的 `data/` 和 `rules/local/`，或仓库外的受控位置。
- `A/B/C` 是用户提出的分类任务标记，其业务定义和与六层体系的关系待确认；不得将其擅自解释为三类业务标签，也不得与三条实验路线混用。
- 多维归因指基于投诉文本的候选解释，不能直接认定事实、责任或因果关系；结果需人工复核。
- **第一阶段不实现 RAG、Agent、模型微调或业务证据核验**，不接入银行核心系统，不进行自动决策或自动投诉处置。

## 第一阶段研究范围

1. 明确授权数据范围、六层业务标准版本、A/B/C 任务定义和标注规范。
2. 规划本地文本预处理、分类、多维语义分析、输出格式校验和人工复核流程。
3. 在后续获得授权后，对比 SVM、直接 LLM、规则约束 LLM 三种方法；当前目录仅预留入口，不执行任何模型调用。
4. 设计固定数据划分、分类指标、规则一致性检查和人工复核记录；当前不宣称已有实验结果。

详细说明见 [研究计划](docs/research_plan.md)、[路线图](docs/roadmap.md) 和 [架构说明](docs/architecture.md)。

## 目录结构

```text
consumer-complaint-ai/
├── README.md
├── requirements.txt               # 轻量配置依赖
├── .gitignore
├── .env.example                   # 仅占位符
├── docs/
│   ├── research_plan.md
│   ├── roadmap.md
│   └── architecture.md
├── rules/
│   ├── label_schema.yaml          # 六层空结构模板
│   ├── classification_rules.yaml  # 空规则模板
│   └── version_history.md
├── src/
│   ├── __init__.py
│   ├── preprocessing/             # 文本预处理
│   ├── classification/            # A/B/C 任务，业务定义待确认
│   ├── semantic_analysis/         # 可配置的多维语义分析
│   ├── validation/                # 格式与规则一致性校验
│   └── review/                    # 人工复核
├── experiments/
│   ├── baseline_svm/
│   ├── baseline_llm/
│   └── rules_llm/
├── tests/                         # 测试约定，当前无业务测试
├── app/                           # Streamlit 界面预留
├── scripts/                       # 本地辅助脚本预留
├── data/
│   └── README.md                  # 数据管理说明；其他内容不入库
└── outputs/
    └── .gitkeep                   # 本地输出不入库
```

每个核心模块以说明性的 `__init__.py` 占位，其余空目录通过 README 或 `.gitkeep` 保留。模块职责和拟议数据流见 [架构说明](docs/architecture.md)。

## 本地准备

建议使用 Python 3.11 或更新的兼容版本。以下命令适用于 PowerShell：

```powershell
git clone https://github.com/bounds01997-hub/consumer-complaint-ai.git
cd consumer-complaint-ai
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
Copy-Item .env.example .env
```

macOS / Linux 创建虚拟环境后可用 `.venv/bin/python -m pip install -r requirements.txt` 和 `cp .env.example .env`。

`requirements.txt` 只声明 YAML 配置和环境变量读取的基础依赖。SVM、LLM SDK、Streamlit 等依赖应在对应功能开始实现时按需增加；当前没有启动应用或执行实验的命令。`.env.example` 是未来配置约定，目前没有代码读取这些变量。

已克隆仓库同步前先检查本地改动：

```powershell
git status
git pull --ff-only origin main
```

若有本地改动或分支分叉，先妥善提交、保存并处理冲突，不使用强制重置覆盖本地工作。

## 开发原则

- 先确认业务定义，再编写分类规则；模型不能替代六层标准。
- 分类、语义分析、格式校验与人工复核保持职责清晰；`validation` 不承担业务证据核验。
- 无匹配、规则冲突或不确定输出进入待复核流程，不强行填入业务标签。
- 实验固定数据划分，避免同一投诉或近重复文本跨训练集与测试集；仅用训练集拟合特征处理。
- 记录代码、规则、数据划分、提示词和模型版本，保证结果可追溯。敏感记录仅保存在本地。
- 采用最少依赖和小步实现；测试使用人工构造且不含真实客户信息的样例。
- 变更公开模板时记录版本；实际业务标准版本独立管理，不用模板版本冒充业务版本。

## 数据与安全提醒

- **真实投诉数据不入库**，包括原始文本、脱敏后的真实记录、标注结果、模型输入输出、附件、录音、截图及客户信息。脱敏不自动等于允许公开。
- 不提交真实密钥、`.env`、内部业务原始文件、标签映射、提示词中的真实案例、日志、缓存或模型产物；不要在 Issues、PR、截图中粘贴这些内容。
- `.gitignore` 已覆盖常见数据格式及本地目录，但无法识别所有敏感内容，也不会取消追踪已提交文件。每次提交前查看 `git status` 和 `git diff --cached`，不要用 `git add -f` 绕过排除规则。
- 调用任何外部 LLM 前，须单独确认数据使用和传输授权、服务环境及必要的脱敏要求；拥有 API 密钥不等于已获得传输数据的授权。
- 业务原始文件和映射建议保存在受控的仓库外目录；即使放在被忽略目录中，也需自行做好访问控制和备份。
- 若误提交敏感信息，应立即停止继续公开传播、撤销受影响的密钥并处理暴露和历史记录；仅删除最新文件不能消除 Git 历史中的内容。

更多数据放置约定见 [data/README.md](data/README.md)。
