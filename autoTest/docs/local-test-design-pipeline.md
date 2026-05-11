# 设计阶段测试 Skill 本地化方案

## 1. 背景

设计阶段的目标，是在拿到需求文档后，将测试工作从“理解需求”推进到“具备执行条件”。

但设计阶段流程不能对所有需求一刀切。小需求如果也跑完整链路，会带来过多文档、评审和自动化成本。

因此，本方案采用：

> 默认轻量，按需升级。

即：

- 日常需求走轻量流程。
- 中等复杂需求走标准流程。
- 核心链路、复杂需求、自动化沉淀场景走完整流程。

本方案不新增一套全新的 Skill，而是基于当前已有测试 Skill 进行本地化整合，形成一套可串联、可评审、可追溯、可迭代的设计阶段测试工作流。

## 2. 目标

本地化方案要解决以下问题：

1. 拿到 PRD / TRD 后，快速判断需求是否可测。
2. 根据需求复杂度选择合适流程，而不是默认跑完整链路。
3. 自动拆解测试范围、风险点和测试优先级。
4. 按需生成测试计划、测试用例、测试数据和自动化脚本。
5. 保留必要人工评审节点，避免直接使用 AI 草稿。
6. 支持需求、用例、接口、数据、脚本、结果之间的追溯关系。
7. 支持需求变更后的回流更新。

## 3. 本地 Skill 映射

当前可复用的本地测试 Skill 如下：

| 本地 Skill | 定位 | 对应能力 |
|---|---|---|
| `tc-brainstorm` | 测试需求分析与策略设计 | 需求澄清、测试范围、风险识别、复杂度判断 |
| `tc-writing-plan` | 测试计划拆分 | 模块拆分、优先级、执行计划、工作量初估 |
| `tc-generating` | 测试用例生成 | 功能、异常、边界用例生成 |
| `tc-scripting` | 自动化脚本生成 | 测试数据落地、断言生成、pytest 脚本生成 |
| `tc-running` | 测试脚本执行与报告 | 执行结果收集、测试报告生成 |

不建议一开始新增以下独立 Skill：

```text
requirement-clarify
test-strategy
test-case-design
contract-test
test-data-generator
test-estimation
```

原因是当前已有 Skill 已经覆盖大部分能力，优先通过职责扩展和流程串联完成本地化。

## 4. 核心原则：默认轻量，按需升级

完整能力可以保留，但不能默认全部启用。

推荐原则：

```text
默认只跑：
tc-brainstorm → tc-generating

满足复杂度条件后升级到：
tc-brainstorm → tc-writing-plan → tc-generating

只有核心链路或自动化场景才跑：
tc-brainstorm → tc-writing-plan → tc-generating → tc-scripting → tc-running
```

也就是说：

> 流程能力要完整，但日常使用要轻。

## 5. 分层执行模式

### 5.1 轻量模式：默认流程

适合大多数日常需求。

```text
PRD / 需求描述
  ↓
tc-brainstorm
需求澄清、范围识别、风险判断
  ↓
tc-generating
生成核心用例、异常用例、边界用例
  ↓
人工确认
```

#### 适用场景

1. 文案修改。
2. 简单字段展示。
3. 简单按钮逻辑。
4. 单接口小改动。
5. 单页面交互调整。
6. 影响范围明确的小需求。

#### 产物

```text
test_brainstorm.md
testcases.md
```

#### 不默认生成

```text
test_plan.md
pytest 项目
test_report.md
单独 traceability.md
完整工时估算
```

#### 评审 Gate

轻量模式只保留一个评审点：

```text
Gate：用例确认
```

确认内容：

1. 核心场景是否覆盖。
2. 异常 / 边界是否遗漏。
3. 是否存在待确认问题。
4. 数据需求是否清楚。

---

### 5.2 标准模式：中等复杂需求

适合普通业务迭代、有多个模块或明显风险点的需求。

```text
PRD / TRD
  ↓
tc-brainstorm
需求澄清、测试策略、风险识别
  ↓
tc-writing-plan
模块拆分、优先级、执行顺序、数据依赖、工作量初估
  ↓
tc-generating
功能、异常、边界用例生成
  ↓
人工确认
```

#### 适用场景

1. 普通功能需求。
2. 单模块较大调整。
3. 多模块小范围联动。
4. 涉及接口联调。
5. 有数据准备成本。
6. 需要给项目排期。

#### 产物

```text
test_brainstorm.md
test_plan.md
testcases.md
```

其中 `test_plan.md` 内应包含：

1. 模块级测试计划。
2. 执行优先级。
3. 数据准备项。
4. 接口依赖。
5. 风险说明。
6. 工作量初估。

#### 评审 Gate

标准模式保留两个评审点：

```text
Gate 1：策略 / 计划确认
Gate 2：用例确认
```

---

### 5.3 完整模式：核心链路 / 自动化场景

适合核心链路、复杂需求、多系统联动、需要自动化沉淀的场景。

```text
PRD / TRD / 接口文档
  ↓
tc-brainstorm
需求澄清、测试策略、风险识别
  ↓
tc-writing-plan
模块拆分、优先级、执行顺序、数据依赖、工作量初估
  ↓
tc-generating
功能、异常、边界用例生成
  ↓
tc-scripting
pytest 脚本、参数化数据、fixtures、断言生成
  ↓
tc-running
执行测试脚本，生成测试报告
  ↓
结果评审
```

#### 适用场景

1. 注册 / 登录。
2. 下单 / 支付。
3. 优惠券 / 促销。
4. 库存 / 履约。
5. 会员 / 权益。
6. 多系统联动。
7. 核心链路重构。
8. 明确要求自动化沉淀。

#### 产物

```text
test_brainstorm.md
test_plan.md
testcases.md
traceability.md
pytest 项目
test_report.md
```

#### 评审 Gate

完整模式保留四个评审点：

```text
Gate 1：策略评审
Gate 2：计划评审
Gate 3：用例评审
Gate 4：执行结果评审
```

说明：`tc-running` 属于执行闭环能力，不是设计阶段必选步骤；只有在完整模式或自动化落地场景下启用。

## 6. 流程升级判断规则

### 6.1 从轻量模式升级到标准模式

满足任一条件，建议升级到标准模式：

1. 涉及多个业务模块。
2. 涉及多个接口联调。
3. 需要明确测试优先级。
4. 需要准备特定测试数据。
5. 有较多待确认问题。
6. 存在明显业务风险。
7. 项目需要测试排期或工作量评估。

### 6.2 从标准模式升级到完整模式

满足任一条件，建议升级到完整模式：

1. 属于 P0 / P1 核心链路。
2. 需要自动化测试沉淀。
3. 涉及多系统联动。
4. 涉及资金、库存、订单、权益、履约等高风险域。
5. 需要持续回归。
6. 接口契约变更较大。
7. 需要输出执行报告。

## 7. 输入质量要求

为避免 AI 根据不完整信息过度补全，输入材料需要满足最低要求。

### 7.1 PRD / TRD 最低要求

至少包含：

1. 需求背景。
2. 业务目标。
3. 业务流程。
4. 核心规则。
5. 验收标准。
6. 影响范围。
7. 不做范围或边界说明。

如果缺失关键内容，Skill 应优先输出待确认问题，而不是强行生成完整用例。

### 7.2 接口文档最低要求

如果涉及接口测试或自动化，接口文档至少包含：

1. 接口路径。
2. 请求方法。
3. 请求字段。
4. 响应字段。
5. 必填字段。
6. 错误码。
7. 鉴权方式。
8. 样例请求 / 响应。

### 7.3 可选增强输入

以下内容不是必需，但能显著提升输出质量：

1. 历史 Bug。
2. 线上故障记录。
3. 埋点方案。
4. 灰度方案。
5. 测试环境说明。
6. Mock 方案。
7. 数据准备说明。

## 8. 各 Skill 职责定义

### 8.1 `tc-brainstorm`

#### 定位

`tc-brainstorm` 是设计阶段入口，负责需求澄清、测试范围识别、风险识别和流程模式建议。

#### 输入

```text
PRD 文件
TRD 文件，可选
接口文档，可选
历史 Bug 信息，可选
业务背景说明，可选
```

#### 输出

轻量模式：

```text
test_brainstorm.md
```

标准 / 完整模式：

```text
test_brainstorm.md
复杂度判断
推荐流程模式
风险清单
待确认问题
```

#### 输出内容

应包括：

1. 需求背景。
2. 测试目标。
3. 业务流程拆解。
4. 测试范围。
5. 不测范围。
6. 暂不可测范围。
7. 核心业务规则。
8. 风险点识别。
9. 测试类型建议。
10. 待确认问题。
11. 推荐测试模式：轻量 / 标准 / 完整。

---

### 8.2 `tc-writing-plan`

#### 定位

`tc-writing-plan` 负责将测试策略拆成可执行的模块测试计划，并给出工作量初估。

#### 适用模式

```text
标准模式
完整模式
```

轻量模式默认不启用。

#### 输入

```text
test_brainstorm.md
PRD / TRD，可选
接口文档，可选
```

#### 输出

```text
test_plan.md
```

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

---

### 8.3 `tc-generating`

#### 定位

`tc-generating` 负责根据需求分析或测试计划生成测试用例。

#### 输入

轻量模式：

```text
test_brainstorm.md
PRD / 需求描述
```

标准 / 完整模式：

```text
test_plan.md
test_brainstorm.md
PRD / TRD
接口文档，可选
```

#### 输出

```text
testcases.md
```

完整模式可额外输出：

```text
traceability.md
```

#### 用例覆盖层次

用例应按三层递进生成：

```text
功能层
异常层
边界层
```

#### 用例结构

建议统一为：

```yaml
case_id: TC-001
module_id: MOD-001
requirement_id: REQ-001
priority: P0
type: 功能测试
title: 未注册手机号输入正确验证码后注册成功
precondition:
  - 手机号未注册
  - 验证码有效
steps:
  - 输入手机号
  - 点击获取验证码
  - 输入正确验证码
  - 点击注册
expected:
  - 注册成功
  - 返回用户信息
  - 用户进入登录态
data_requirements:
  - 未注册手机号
  - 有效验证码
related_api:
  - POST /register/phone
```

---

### 8.4 `tc-scripting`

#### 定位

`tc-scripting` 负责将确认后的测试用例转成自动化测试脚本。

#### 适用模式

```text
完整模式
```

轻量模式和标准模式默认不启用。

#### 输入

```text
testcases.md
接口文档
测试环境说明
测试数据要求
```

#### 输出

```text
pytest 项目
测试脚本
测试数据文件
fixtures
执行说明
```

#### 职责

1. 生成 pytest 项目结构。
2. 生成接口请求代码。
3. 生成断言。
4. 生成参数化数据。
5. 生成 fixtures。
6. 生成前置造数逻辑。
7. 生成数据清理逻辑。
8. 支持后续执行。

---

### 8.5 `tc-running`

#### 定位

`tc-running` 负责执行自动化测试脚本并生成测试报告。

#### 适用模式

```text
完整模式
```

`tc-running` 是执行闭环能力，不是设计阶段默认能力。

#### 输入

```text
pytest 项目路径
测试环境配置
执行参数，可选
```

#### 输出

```text
test_report.md
```

#### 输出内容

应包括：

1. 执行环境。
2. 执行时间。
3. 总用例数。
4. 通过数。
5. 失败数。
6. 跳过数。
7. 失败用例明细。
8. 失败原因分析。
9. 阻塞问题。
10. 后续建议。

## 9. 接口契约校验本地化方案

当前不单独新增 `contract-test` Skill。

接口契约校验先作为 `tc-generating` 和 `tc-scripting` 的增强能力。

### 9.1 在 `tc-generating` 阶段补充接口用例

读取 OpenAPI / Swagger / 接口文档后，补充：

1. 必填字段缺失用例。
2. 字段类型错误用例。
3. 枚举值非法用例。
4. 返回字段缺失用例。
5. 状态码异常用例。
6. 错误码覆盖用例。
7. 鉴权缺失或鉴权失败用例。
8. 幂等性验证用例。
9. 超时和重试约定用例。
10. 空值 / null 处理用例。
11. 分页、排序、筛选规则用例。
12. 兼容性变更校验用例。

### 9.2 在 `tc-scripting` 阶段生成断言

自动化脚本中补充：

1. 请求字段校验。
2. 响应字段校验。
3. 状态码校验。
4. Schema 校验。
5. 错误码校验。
6. 鉴权校验。
7. 业务语义校验。

### 9.3 后续演进

如果接口契约测试需求变多，再单独拆出：

```text
contract-test
```

作为独立 Skill。

## 10. 测试数据生成本地化方案

当前不单独新增 `test-data-generator` Skill。

测试数据能力拆到两个阶段：

```text
tc-generating：输出数据需求
tc-scripting：落地测试数据
```

### 10.1 用例阶段输出数据需求

每条用例必须带：

```yaml
data_requirements:
  - 未注册手机号
  - 已注册手机号
  - 错误验证码
  - 过期验证码
```

### 10.2 脚本阶段生成测试数据

在 `tc-scripting` 中，将数据需求转成：

1. pytest fixtures。
2. mock 数据。
3. 参数化数据。
4. API 前置造数步骤。
5. 数据清理逻辑。

### 10.3 数据类型

测试数据需要覆盖：

| 数据类型 | 示例 |
|---|---|
| 字段边界数据 | 空手机号、非法手机号、超长邮箱 |
| 业务状态数据 | 未注册用户、已注册用户、黑名单用户 |
| 关联关系数据 | 已绑定微信、未绑定微信、手机号已被占用 |
| 环境依赖数据 | 测试门店、测试商品、测试库存、测试账号 |
| 异常数据 | 过期验证码、错误验证码、被风控手机号 |
| 清理策略 | 测试后是否删除账号、是否可重复执行 |

### 10.4 数据生命周期

完整模式下需要明确测试数据生命周期：

1. 数据准备方式：手工准备 / API 造数 / SQL 初始化 / Mock。
2. 数据隔离策略：避免污染共享环境。
3. 数据清理策略：测试后删除、回滚或标记废弃。
4. 数据可重复性：同一用例能否多次执行。
5. 敏感数据要求：不能使用真实手机号、身份证、用户隐私数据。

## 11. 工作量与风险评估本地化方案

当前不单独新增 `test-estimation` Skill。

工作量与风险评估先放在两个阶段：

| 阶段 | 评估内容 |
|---|---|
| `tc-brainstorm` | 粗略评估复杂度、风险、推荐流程模式 |
| `tc-writing-plan` | 按模块评估执行顺序、依赖、数据准备、自动化成本、工作量初估 |

### 11.1 评估因素

不能只看用例数量，还要考虑：

1. 环境是否稳定。
2. 是否涉及多端联调。
3. 是否依赖第三方系统。
4. 是否需要造历史数据。
5. 是否涉及压测 / 安全测试。
6. 是否需要兼容旧版本。
7. 是否需要线上灰度验证。
8. 是否已有自动化脚本。
9. 历史 Bug 密度。
10. 开发接口是否按时完成。

### 11.2 输出示例

```text
模块：用户注册重构

基础功能测试：3 人日
接口测试：1.5 人日
测试数据准备：1 人日
回归测试：1 人日
联调与问题定位：1 人日
风险缓冲：20%

建议排期：约 9 - 10 人日
```

## 12. 人工评审 Gate

Skill 输出是草稿，不是终稿。

不同模式使用不同评审强度。

### 12.1 轻量模式

```text
Gate：用例确认
```

评审内容：

1. 核心场景是否覆盖。
2. 异常 / 边界是否遗漏。
3. 是否存在待确认问题。
4. 数据需求是否清楚。

### 12.2 标准模式

```text
Gate 1：策略 / 计划确认
Gate 2：用例确认
```

评审内容：

1. 测试范围是否合理。
2. 不测范围是否明确。
3. 模块拆分是否合理。
4. 优先级是否正确。
5. 执行顺序是否合理。
6. 用例是否覆盖核心链路。
7. 数据依赖是否完整。

### 12.3 完整模式

```text
Gate 1：策略评审
Gate 2：计划评审
Gate 3：用例评审
Gate 4：执行结果评审
```

评审内容：

1. 测试范围、风险、不测范围是否合理。
2. 模块拆分、优先级、执行顺序是否合理。
3. 核心链路、异常场景、边界条件是否覆盖。
4. 自动化脚本是否可执行、可维护。
5. 失败用例是否为真实缺陷。
6. 是否满足准出条件。

## 13. 准入 / 准出标准

### 13.1 测试设计准入

进入测试设计前，建议满足：

1. PRD / TRD 已提供。
2. 核心业务流程已明确。
3. 关键业务规则已明确。
4. 需求影响范围已明确。
5. 核心待确认问题有负责人。

标准模式 / 完整模式还应满足：

1. 核心接口文档已提供。
2. 测试环境可用，或有明确 Mock 方案。
3. 关键测试数据准备方式已明确。

### 13.2 测试设计准出

测试设计完成后，建议满足：

1. P0 / P1 核心场景已有用例覆盖。
2. 主要异常和边界场景已有用例覆盖。
3. 高风险点已有对应测试策略或用例。
4. 关键数据需求已明确。
5. 待确认问题已记录并同步。

完整模式还应满足：

1. 自动化脚本可执行。
2. 核心链路执行结果已产出。
3. 阻塞问题已记录。
4. 已知风险已同步。

## 14. 追溯关系

轻量模式下，追溯关系可以内嵌在 `testcases.md` 中。

标准模式下，建议在 `testcases.md` 中保留：

```text
需求 ID
模块 ID
用例 ID
接口 ID
数据需求
```

完整模式下，建议独立输出：

```text
traceability.md
```

追溯链路：

```text
需求
  ↓
模块
  ↓
用例
  ↓
接口
  ↓
数据
  ↓
脚本
  ↓
执行结果
```

示例：

```text
REQ-001 手机号注册
  MOD-001 手机号注册模块
  TC-001 未注册手机号注册成功
  API-001 POST /register/phone
  DATA-001 未注册手机号
  SCRIPT test_register.py::test_phone_register_success
  RESULT passed
```

追溯关系用于解决：

1. 需求是否都有用例覆盖。
2. 高风险模块是否有重点用例。
3. 每条用例需要哪些数据。
4. 每个接口是否被业务场景覆盖。
5. 需求变更时能否定位影响范围。
6. 缺陷能否反查到需求和用例。

## 15. 变更回流机制

需求、接口、业务规则发生变化后，不能只改用例，需要沿流程回流。

轻量模式：

```text
PRD 变更
  ↓
更新 tc-brainstorm 输出
  ↓
更新 tc-generating 用例
```

标准模式：

```text
PRD 变更
  ↓
更新 tc-brainstorm 输出
  ↓
更新 tc-writing-plan
  ↓
更新 tc-generating 用例
```

完整模式：

```text
PRD / 接口变更
  ↓
更新 tc-brainstorm 输出
  ↓
更新 tc-writing-plan
  ↓
更新 tc-generating 用例
  ↓
更新 tc-scripting 脚本
  ↓
重新 tc-running
```

每个产物建议保留来源信息：

```yaml
source_prd_version: v1.3
source_api_version: 2026-05-11
generated_at: 2026-05-11
depends_on:
  - test_brainstorm.md
  - test_plan.md
  - testcases.md
```

## 16. 产物目录建议

推荐目录结构：

```text
test-design/
  requirement/
    prd.md
    trd.md
  strategy/
    test_brainstorm.md
  plan/
    test_plan.md
  cases/
    testcases.md
  data/
    testdata.yaml
  scripts/
    pytest/
  reports/
    test_report.md
  traceability.md
```

轻量模式可以只保留：

```text
test-design/
  strategy/
    test_brainstorm.md
  cases/
    testcases.md
```

标准模式可以保留：

```text
test-design/
  strategy/
    test_brainstorm.md
  plan/
    test_plan.md
  cases/
    testcases.md
```

完整模式再补齐：

```text
test-design/
  data/
  scripts/
  reports/
  traceability.md
```

## 17. Workflow 配置示例

### 17.1 轻量模式

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

### 17.2 标准模式

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

### 17.3 完整模式

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

## 18. 用户注册重构示例

### 18.1 输入

```text
用户注册重构 PRD
后端 OpenAPI 文档
历史注册链路 Bug 列表
测试环境说明
```

### 18.2 模式判断

用户注册属于核心链路，且涉及手机号、邮箱、OAuth、实名认证、验证码、风控等多个模块。

建议选择：

```text
完整模式
```

### 18.3 `tc-brainstorm` 输出

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

### 18.4 `tc-writing-plan` 输出

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

### 18.5 `tc-generating` 输出

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

### 18.6 接口契约问题补充

从接口文档中发现：

```text
1. phone 字段示例为 number，但 Schema 定义为 string。
2. userId 返回类型文档为 integer，但客户端约定为 string。
3. errorCode 没有枚举定义。
4. captchaCode 是否必填未明确。
```

这些问题进入用例和脚本断言补充范围。

### 18.7 `tc-scripting` 输出

生成：

```text
pytest 项目
注册接口测试脚本
验证码测试 fixtures
手机号参数化数据
接口 Schema 断言
执行说明
```

### 18.8 `tc-running` 输出

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

## 19. 最终结论

本地化后的方案不是新增一批 Skill，而是基于当前已有测试 Skill 形成分层测试设计流程：

```text
轻量模式：
tc-brainstorm → tc-generating

标准模式：
tc-brainstorm → tc-writing-plan → tc-generating

完整模式：
tc-brainstorm → tc-writing-plan → tc-generating → tc-scripting → tc-running
```

核心原则是：

1. 默认轻量，按需升级。
2. `tc-brainstorm` 负责需求澄清、测试策略和模式判断。
3. `tc-writing-plan` 只在标准 / 完整模式启用，负责计划拆分和工作量初估。
4. `tc-generating` 负责功能、异常、边界用例。
5. `tc-scripting` 和 `tc-running` 只在完整模式启用。
6. 契约测试先作为增强能力嵌入用例和脚本阶段。
7. 测试数据先作为用例数据需求和脚本 fixtures 落地。
8. 工时估算先作为策略和计划阶段的风险评估能力。
9. 不同模式匹配不同评审强度。
10. 完整模式保留需求到执行结果的追溯关系。

最终目标是：

> 把设计阶段测试工作从“所有需求都跑重流程”，调整为“日常需求轻量处理，复杂需求逐级加码，核心链路完整闭环”。
