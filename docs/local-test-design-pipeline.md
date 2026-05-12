# 设计阶段测试 Skill 执行主流程

本文档是给 AI 执行落地使用的主流程。详细职责、评审标准、追溯规则见 `local-test-design-reference.md`，示例见 `local-test-design-examples.md`。

## 1. 适用场景

当用户提供 PRD、TRD、接口文档或需求描述，并希望生成测试分析、测试计划、测试用例、自动化脚本或测试报告时，使用本流程。

核心原则：

```text
默认轻量，按需升级。
```

不要默认跑完整链路。先判断需求复杂度，再选择执行模式。

## 2. 可用 Skill

| Skill | 用途 |
|---|---|
| `tc-brainstorm` | 需求澄清、测试范围、风险识别、复杂度判断 |
| `tc-writing-plan` | 模块拆分、优先级、执行顺序、数据依赖、工作量初估 |
| `tc-generating` | 生成测试用例，覆盖功能、异常、边界场景 |
| `tc-scripting` | 根据已确认用例生成 pytest 自动化脚本 |
| `tc-running` | 执行 pytest 脚本并生成测试报告 |

## 3. 输入识别

收到用户材料后，先判断输入类型：

1. 只有需求描述或 PRD：可进入 `tc-brainstorm`。
2. 有 PRD + TRD：优先进入 `tc-brainstorm`，并将 TRD 作为补充输入。
3. 有接口文档：在 `tc-brainstorm` 中识别接口风险，在 `tc-generating` 中补充接口用例。
4. 有已确认测试策略或测试计划：可直接进入 `tc-generating`。
5. 有已确认测试用例并要求自动化：可进入 `tc-scripting`。
6. 有 pytest 项目并要求执行：可进入 `tc-running`。

如果输入不足，不要强行生成完整产物，先输出待确认问题。

## 4. 模式判断规则

### 4.1 默认轻量模式

未命中升级条件时，使用轻量模式。

适用：

1. 文案修改。
2. 简单字段展示。
3. 简单按钮逻辑。
4. 单接口小改动。
5. 单页面交互调整。
6. 影响范围明确的小需求。

流程：

```text
tc-brainstorm → tc-generating → 用例确认
```

产物：

```text
test_brainstorm.md
testcases.md
```

### 4.2 升级到标准模式

满足任一条件，使用标准模式：

1. 涉及多个业务模块。
2. 涉及多个接口联调。
3. 需要明确测试优先级。
4. 需要准备特定测试数据。
5. 有较多待确认问题。
6. 存在明显业务风险。
7. 项目需要测试排期或工作量评估。

流程：

```text
tc-brainstorm → tc-writing-plan → tc-generating → 用例确认
```

产物：

```text
test_brainstorm.md
test_plan.md
testcases.md
```

### 4.3 升级到完整模式

满足任一条件，使用完整模式：

1. 属于 P0 / P1 核心链路。
2. 明确要求自动化测试沉淀。
3. 涉及多系统联动。
4. 涉及资金、库存、订单、权益、履约等高风险域。
5. 需要持续回归。
6. 接口契约变更较大。
7. 需要输出执行报告。

流程：

```text
tc-brainstorm → tc-writing-plan → tc-generating → tc-scripting → tc-running → 结果评审
```

产物：

```text
test_brainstorm.md
test_plan.md
testcases.md
traceability.md
pytest 项目
test_report.md
```

## 5. 执行路由

### 5.1 用户要求“生成测试用例”

1. 先执行 `tc-brainstorm`。
2. 根据复杂度判断轻量 / 标准 / 完整模式。
3. 轻量模式：继续执行 `tc-generating`。
4. 标准或完整模式：先执行 `tc-writing-plan`，再执行 `tc-generating`。
5. 如果材料缺失，先列待确认问题。

### 5.2 用户要求“生成测试计划”

1. 先确认是否已有 `test_brainstorm.md` 或等价测试分析。
2. 没有则先执行 `tc-brainstorm`。
3. 再执行 `tc-writing-plan`。

### 5.3 用户要求“生成自动化脚本”

1. 确认是否已有已评审的 `testcases.md`。
2. 确认是否有接口文档、测试环境说明、测试数据要求。
3. 信息不足时先列缺口。
4. 信息足够时执行 `tc-scripting`。

### 5.4 用户要求“执行测试并出报告”

1. 确认 pytest 项目路径。
2. 确认测试环境配置。
3. 执行 `tc-running`。
4. 输出 `test_report.md`。

### 5.5 用户要求“完整测试设计流程”

1. 先执行 `tc-brainstorm`。
2. 判断是否真的需要完整模式。
3. 如果不满足完整模式条件，说明建议降级为轻量或标准模式。
4. 如果用户坚持完整模式，按完整模式执行。

## 6. 缺失信息处理

### 6.1 PRD / TRD 最低要求

至少需要：

1. 需求背景。
2. 业务目标。
3. 业务流程。
4. 核心规则。
5. 验收标准。
6. 影响范围。
7. 不做范围或边界说明。

缺失关键内容时，优先输出待确认问题，不要编造业务规则。

### 6.2 接口测试最低要求

涉及接口测试或自动化时，至少需要：

1. 接口路径。
2. 请求方法。
3. 请求字段。
4. 响应字段。
5. 必填字段。
6. 错误码。
7. 鉴权方式。
8. 样例请求 / 响应。

缺失时，只能生成接口测试设计建议，不能直接生成可执行脚本。

## 7. 输出规则

1. 先给模式判断结论，再给执行流程。
2. 所有产物必须标明来源材料。
3. 测试用例必须覆盖三层：功能、异常、边界。
4. 每条用例应包含数据需求。
5. 标准 / 完整模式下，用例应保留需求 ID、模块 ID、用例 ID。
6. 完整模式下，应维护需求到执行结果的追溯关系。
7. Skill 输出是草稿，必须提示需要人工评审。

## 8. 禁止行为

1. 不要默认执行完整模式。
2. 不要在缺少关键规则时编造用例。
3. 不要把 `tc-scripting` 和 `tc-running` 作为设计阶段默认步骤。
4. 不要为轻量需求生成过多文档。
5. 不要在没有接口文档和环境说明时生成可执行自动化脚本。
6. 不要把 AI 输出直接视为最终测试结论。

## 9. 推荐目录

轻量模式：

```text
test-design/
  strategy/test_brainstorm.md
  cases/testcases.md
```

标准模式：

```text
test-design/
  strategy/test_brainstorm.md
  plan/test_plan.md
  cases/testcases.md
```

完整模式：

```text
test-design/
  strategy/test_brainstorm.md
  plan/test_plan.md
  cases/testcases.md
  data/testdata.yaml
  scripts/pytest/
  reports/test_report.md
  traceability.md
```
