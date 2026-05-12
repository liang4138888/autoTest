---
name: tc-scripting
description: 自动化脚本生成；用于完整模式或明确自动化沉淀场景，将已确认测试用例转成 pytest 项目、测试数据和 fixtures。
---

# tc-scripting

## Purpose

`tc-scripting` 负责将已评审确认的测试用例转成可执行或可继续调试的 pytest 自动化测试脚本。

## When to use

仅在以下场景使用：

```text
完整模式
用户明确要求自动化测试沉淀
```

轻量模式和标准模式默认不启用。

## Preconditions

使用前必须确认：

1. 已有已评审确认的 `test-design/cases/testcases.md`。
2. 已提供接口文档。
3. 已提供测试环境说明。
4. 已明确测试数据要求。

缺少接口文档或环境说明时，不要生成可执行脚本，只能输出自动化设计缺口。

## Inputs

```text
test-design/cases/testcases.md
接口文档
测试环境说明
测试数据要求
```

## Outputs

```text
test-design/scripts/pytest/
test-design/data/testdata.yaml
fixtures
执行说明
```

## Required content

生成内容应包括：

1. pytest 项目结构。
2. 接口请求代码。
3. 断言。
4. 参数化数据。
5. fixtures。
6. 前置造数逻辑。
7. 数据清理逻辑。
8. 执行说明。

## Execution rules

1. 只能基于已确认用例生成脚本。
2. 不要扩大测试范围，范围变更应回流到 `tc-generating` 或 `tc-brainstorm`。
3. 测试数据应从用例的 `data_requirements` 转换而来。
4. 敏感数据不得使用真实手机号、身份证、用户隐私数据。
5. 需要明确数据生命周期：准备方式、隔离策略、清理策略、可重复性。
6. 接口断言应覆盖状态码、响应字段、错误码、Schema、鉴权和业务语义。
7. 不要在缺少环境信息时强行写死域名、账号或凭据。

## Review gate

```text
Gate：脚本评审
```

确认内容：

1. 脚本是否覆盖已确认用例。
2. 数据准备和清理是否安全。
3. 断言是否足够。
4. 脚本是否可重复执行。
5. 是否存在环境或权限依赖。

## Next step

进入 `tc-running`。
