# autoTest

`autoTest` 是一个 Claude Code 专属的测试设计工作流项目，用于把 PRD、TRD、接口文档或需求描述转化为可评审、可追溯、可按需自动化的测试设计产物。

## 项目定位

本项目不是通用业务代码仓库，而是面向 Claude Code 的测试设计编排项目。

核心原则：

```text
默认轻量，按需升级。
```

也就是说：

- 日常小需求默认只做测试分析和测试用例。
- 中等复杂需求增加测试计划。
- 核心链路、高风险域或自动化沉淀场景才进入完整流程。

## 文档入口

| 文件 | 用途 |
|---|---|
| `docs/local-test-design-pipeline.md` | AI 执行主流程，Claude 应优先读取 |
| `docs/local-test-design-reference.md` | 详细规则、职责、评审、追溯和准入准出标准 |
| `docs/local-test-design-examples.md` | Workflow 和用户注册重构示例 |

## Claude Code 结构

```text
.claude/
  settings.json
  commands/
    test-design.md
  agents/
    test-design-orchestrator.md
  skills/
    tc-brainstorm/SKILL.md
    tc-writing-plan/SKILL.md
    tc-generating/SKILL.md
    tc-scripting/SKILL.md
    tc-running/SKILL.md
```

- `.claude/settings.json`：项目级 Claude Code 行为约束。
- `.claude/commands/test-design.md`：项目级测试设计命令入口。
- `.claude/agents/test-design-orchestrator.md`：测试设计流程编排角色定义。
- `.claude/skills/tc-*/SKILL.md`：五个本地测试 Skill 的具体执行契约。

## 支持流程

### 轻量模式

```text
tc-brainstorm → tc-generating
```

产物：

```text
test-design/strategy/test_brainstorm.md
test-design/cases/testcases.md
```

### 标准模式

```text
tc-brainstorm → tc-writing-plan → tc-generating
```

产物：

```text
test-design/strategy/test_brainstorm.md
test-design/plan/test_plan.md
test-design/cases/testcases.md
```

### 完整模式

```text
tc-brainstorm → tc-writing-plan → tc-generating → tc-scripting → tc-running
```

产物：

```text
test-design/strategy/test_brainstorm.md
test-design/plan/test_plan.md
test-design/cases/testcases.md
test-design/data/testdata.yaml
test-design/scripts/pytest/
test-design/reports/test_report.md
test-design/traceability.md
```

## 产物目录

```text
test-design/
  requirement/     # PRD / TRD / 接口文档等输入材料
  strategy/        # test_brainstorm.md
  plan/            # test_plan.md
  cases/           # testcases.md
  data/            # testdata.yaml
  scripts/pytest/  # 完整模式下的 pytest 自动化脚本
  reports/         # test_report.md
```

## 使用方式

在 Claude Code 中提供需求材料后，可以这样提出请求：

```text
基于 test-design/requirement/prd.md 生成测试用例
```

```text
基于这份 PRD 生成测试计划和测试用例
```

```text
这是核心下单链路需求，按完整模式生成测试设计并沉淀自动化脚本
```

Claude 应先根据 `docs/local-test-design-pipeline.md` 判断模式，再调用对应 Skill：

- `tc-brainstorm`
- `tc-writing-plan`
- `tc-generating`
- `tc-scripting`
- `tc-running`

## 执行约束

1. 不默认执行完整模式。
2. 缺失关键业务规则时，先输出待确认问题，不编造用例。
3. 没有已确认用例、接口文档和环境说明时，不生成可执行自动化脚本。
4. `tc-scripting` 和 `tc-running` 只在完整模式或用户明确要求时启用。
5. 所有 AI 产物都是草稿，需要人工评审后再作为最终测试资产。
