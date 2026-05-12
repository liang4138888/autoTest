---
name: tc-writing-plan
description: 测试计划拆分；用于标准或完整模式下，将测试分析拆解为模块级测试计划、优先级、依赖、执行顺序和工作量初估。
---

# tc-writing-plan

## Purpose

`tc-writing-plan` 负责把 `tc-brainstorm` 的测试分析结果拆成可执行的模块测试计划。

## When to use

仅在以下模式使用：

```text
标准模式
完整模式
```

轻量模式默认不启用。

## Inputs

```text
test-design/strategy/test_brainstorm.md
PRD / TRD，可选
接口文档，可选
```

## Outputs

```text
test-design/plan/test_plan.md
```

## Required sections

`test_plan.md` 应包含：

1. 模块清单。
2. 每个模块的测试目标。
3. 模块优先级。
4. 测试类型。
5. 执行顺序。
6. 数据依赖。
7. 接口依赖。
8. 环境依赖。
9. 自动化建议。
10. 风险说明。
11. 工作量初估。

## Execution rules

1. 必须基于 `test_brainstorm.md` 进行拆分。
2. 不要重新发散需求范围，范围变更应回流到 `tc-brainstorm`。
3. 优先级应结合核心链路、风险、数据成本和联调成本判断。
4. 工作量评估不能只看用例数量，还要考虑环境、数据、联调、回归、历史 Bug、自动化成本。
5. 如果缺少接口、环境或数据准备信息，应在计划中标为依赖或待确认项。
6. 轻量模式下不要默认生成 `test_plan.md`。

## Review gate

```text
Gate：计划确认
```

确认内容：

1. 模块拆分是否合理。
2. 优先级是否正确。
3. 执行顺序是否合理。
4. 数据依赖是否完整。
5. 接口和环境依赖是否明确。
6. 工作量初估是否有明显遗漏。

## Next step

进入 `tc-generating`。
