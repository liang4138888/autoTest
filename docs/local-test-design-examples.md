# 设计阶段测试 Skill 示例

本文档保存测试设计流程示例。AI 执行入口请优先查看 `local-test-design-pipeline.md`，详细规则见 `local-test-design-reference.md`。

## 1. Workflow 配置示例

### 1.1 轻量模式

```yaml
name: local-test-design-lite
description: 轻量测试设计流程——需求分析 → 用例生成

workflow:
  - skill: tc-brainstorm
    input:
      - PRD 文件路径或需求描述
    output:
      - test_brainstorm.md

  - skill: tc-generating
    input:
      - test_brainstorm.md
      - PRD 文件路径或需求描述
    output:
      - testcases.md

  - gate: testcase-review
    description: 人工确认核心场景、异常场景、边界场景和数据需求
```

### 1.2 标准模式

```yaml
name: local-test-design-standard
description: 标准测试设计流程——需求分析 → 测试计划 → 用例生成

workflow:
  - skill: tc-brainstorm
    input:
      - PRD 文件路径
      - TRD 文件路径，可选
      - 历史 Bug 信息，可选
      - 接口文档，可选
    output:
      - test_brainstorm.md

  - skill: tc-writing-plan
    input:
      - test_brainstorm.md
    output:
      - test_plan.md

  - gate: plan-review
    description: 人工确认测试范围、模块拆分、优先级、数据依赖和工作量初估

  - skill: tc-generating
    input:
      - test_plan.md
      - test_brainstorm.md
      - PRD 文件路径
      - 接口文档，可选
    output:
      - testcases.md

  - gate: testcase-review
    description: 人工确认功能、异常、边界用例覆盖情况
```

### 1.3 完整模式

```yaml
name: local-test-design-full
description: 完整测试设计流程——需求分析 → 计划 → 用例 → 脚本 → 执行报告

workflow:
  - skill: tc-brainstorm
    input:
      - PRD 文件路径
      - TRD 文件路径，可选
      - 历史 Bug 信息，可选
      - 接口文档，可选
    output:
      - test_brainstorm.md

  - gate: strategy-review
    description: 人工评审测试范围、风险、不测范围和待确认问题

  - skill: tc-writing-plan
    input:
      - test_brainstorm.md
    output:
      - test_plan.md

  - gate: plan-review
    description: 人工评审模块拆分、优先级、执行顺序、数据依赖和工作量初估

  - skill: tc-generating
    input:
      - test_plan.md
      - test_brainstorm.md
      - PRD 文件路径
      - 接口文档，可选
    output:
      - testcases.md
      - traceability.md

  - gate: testcase-review
    description: 人工评审功能、异常、边界用例覆盖情况

  - skill: tc-scripting
    input:
      - testcases.md
      - 接口文档
      - 测试环境说明
    output:
      - pytest 项目
      - 测试数据
      - fixtures

  - skill: tc-running
    input:
      - pytest 项目路径
    output:
      - test_report.md

  - gate: result-review
    description: 人工评审执行结果、失败原因和准出条件
```

## 2. 用户注册重构示例

### 2.1 输入

```text
用户注册重构 PRD
后端 OpenAPI 文档
历史注册链路 Bug 列表
测试环境说明
```

### 2.2 模式判断

用户注册属于核心链路，且涉及手机号、邮箱、OAuth、实名认证、验证码、风控等多个模块。

建议选择：

```text
完整模式
```

### 2.3 `tc-brainstorm` 输出

识别出核心路径：

1. 手机号注册。
2. 邮箱注册。
3. 第三方 OAuth 注册。
4. 实名认证。
5. 验证码校验。
6. 注册风控。
7. 老用户兼容。

输出待确认问题：

```text
1. 验证码有效期是多少？
2. 同一手机号每日最多发送几次验证码？
3. OAuth 注册失败后是否允许重试？
4. 实名认证失败是否阻断注册？
5. 老用户再次进入注册流程时如何处理？
```

### 2.4 `tc-writing-plan` 输出

```text
P0：
- 手机号注册主流程
- 邮箱注册主流程
- OAuth 注册主流程
- 已注册用户拦截
- 验证码错误 / 过期

P1：
- 频繁发送验证码限制
- 实名认证失败
- OAuth 取消授权

P2：
- 文案展示
- 输入框格式提示
```

### 2.5 `tc-generating` 输出

生成三类用例：

```text
功能用例：32 条
异常用例：41 条
边界用例：28 条
合计：101 条
```

示例用例：

```yaml
case_id: TC-001
module_id: MOD-001
priority: P0
type: 功能测试
title: 未注册手机号输入正确验证码后注册成功
precondition:
  - 手机号未注册
  - 验证码有效
steps:
  - 输入手机号
  - 获取验证码
  - 输入正确验证码
  - 点击注册
expected:
  - 注册成功
  - 返回 userId
  - 用户进入登录态
data_requirements:
  - 未注册手机号
  - 有效验证码
related_api:
  - POST /register/phone
```

### 2.6 接口契约问题补充

从接口文档中发现：

```text
1. phone 字段示例为 number，但 Schema 定义为 string。
2. userId 返回类型文档为 integer，但客户端约定为 string。
3. errorCode 没有枚举定义。
4. captchaCode 是否必填未明确。
```

这些问题进入用例和脚本断言补充范围。

### 2.7 `tc-scripting` 输出

生成：

```text
pytest 项目
注册接口测试脚本
验证码测试 fixtures
手机号参数化数据
接口 Schema 断言
执行说明
```

### 2.8 `tc-running` 输出

```text
测试结果：
- 总用例：101
- 通过：86
- 失败：10
- 跳过：5

主要失败原因：
1. 手机号注册接口返回字段 userId 类型不一致。
2. 验证码过期错误码与接口文档不一致。
3. OAuth 回调测试环境不可用。
```
