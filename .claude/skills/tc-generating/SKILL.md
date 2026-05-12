---
name: tc-generating
description: 测试用例生成；用于根据测试分析或测试计划生成覆盖功能、异常、边界场景的 Markdown 测试用例。
---

# tc-generating

## Purpose

`tc-generating` 负责根据 `test_brainstorm.md` 或 `test_plan.md` 生成测试用例。

## When to use

- 轻量模式：`tc-brainstorm` 后直接使用。
- 标准模式：`tc-writing-plan` 后使用。
- 完整模式：`tc-writing-plan` 后使用，并可维护追溯关系。

## Inputs

轻量模式：

```text
test-design/strategy/test_brainstorm.md
PRD / 需求描述
```

标准 / 完整模式：

```text
test-design/plan/test_plan.md
test-design/strategy/test_brainstorm.md
PRD / TRD
接口文档，可选
```

## Outputs

主输出：

```text
test-design/cases/testcases.md
```

完整模式可额外输出：

```text
test-design/traceability.md
```

## Required coverage

用例必须按三层递进覆盖：

```text
功能层
异常层
边界层
```

## Case structure

建议每条用例使用以下结构：

```yaml
case_id: TC-001
module_id: MOD-001
requirement_id: REQ-001
priority: P0
type: 功能测试
title: 用例标题
precondition:
  - 前置条件
steps:
  - 操作步骤
expected:
  - 预期结果
data_requirements:
  - 测试数据需求
related_api:
  - 关联接口，可选
```

## Execution rules

1. 必须覆盖核心功能、异常路径和边界条件。
2. 每条用例必须包含数据需求。
3. 标准 / 完整模式必须保留需求 ID、模块 ID、用例 ID。
4. 接口文档存在时，应补充接口契约类用例：必填缺失、类型错误、枚举非法、错误码、鉴权、幂等、分页排序等。
5. 接口契约校验作为本 Skill 的增强能力，不单独拆出 `contract-test`。
6. 不要在业务规则缺失时编造用例，应输出待确认问题。
7. 完整模式下应维护 `test-design/traceability.md`。

## Review gate

```text
Gate：用例确认
```

确认内容：

1. 核心场景是否覆盖。
2. 异常 / 边界是否遗漏。
3. 数据需求是否清楚。
4. 接口用例是否覆盖关键契约。
5. 是否存在待确认问题。

## Next step

- 轻量模式：结束，等待人工确认。
- 标准模式：结束，等待人工确认。
- 完整模式：用例评审通过后进入 `tc-scripting`。
