# /test-design

项目级测试设计入口命令。

## 用途

当用户提供 PRD、TRD、接口文档或需求描述，并希望生成测试分析、测试计划、测试用例、自动化脚本或测试报告时，使用本命令。

执行前优先读取：

1. `docs/local-test-design-pipeline.md`
2. 必要时参考 `docs/local-test-design-reference.md`
3. 需要示例时参考 `docs/local-test-design-examples.md`

具体 Skill 执行契约位于：

- `.claude/skills/tc-brainstorm/SKILL.md`
- `.claude/skills/tc-writing-plan/SKILL.md`
- `.claude/skills/tc-generating/SKILL.md`
- `.claude/skills/tc-scripting/SKILL.md`
- `.claude/skills/tc-running/SKILL.md`

## 输入

支持以下输入形式：

- PRD 文件路径
- TRD 文件路径
- 接口文档路径
- 需求描述文本
- 已生成的 `test_brainstorm.md`
- 已确认的 `test_plan.md`
- 已确认的 `testcases.md`
- pytest 项目路径

## 路由规则

### 生成测试用例

1. 先执行 `tc-brainstorm`。
2. 根据复杂度判断轻量 / 标准 / 完整模式。
3. 轻量模式：继续执行 `tc-generating`。
4. 标准或完整模式：先执行 `tc-writing-plan`，再执行 `tc-generating`。
5. 输出到：
   - `test-design/strategy/test_brainstorm.md`
   - `test-design/plan/test_plan.md`，如适用
   - `test-design/cases/testcases.md`

### 生成测试计划

1. 如果没有测试分析，先执行 `tc-brainstorm`。
2. 再执行 `tc-writing-plan`。
3. 输出到：
   - `test-design/strategy/test_brainstorm.md`
   - `test-design/plan/test_plan.md`

### 生成自动化脚本

1. 确认已有已评审的 `test-design/cases/testcases.md`。
2. 确认接口文档、测试环境说明和测试数据要求已提供。
3. 信息不足时先输出缺口。
4. 信息充分时执行 `tc-scripting`。
5. 输出到：
   - `test-design/scripts/pytest/`
   - `test-design/data/`，如生成测试数据

### 执行测试并生成报告

1. 确认 pytest 项目路径。
2. 确认测试环境配置。
3. 执行 `tc-running`。
4. 输出到：
   - `test-design/reports/test_report.md`

## 模式判断

默认轻量模式。

升级标准模式条件：

- 多业务模块
- 多接口联调
- 需要测试优先级
- 需要特定测试数据
- 待确认问题较多
- 存在明显业务风险
- 需要排期或工作量评估

升级完整模式条件：

- P0 / P1 核心链路
- 明确要求自动化沉淀
- 多系统联动
- 资金、库存、订单、权益、履约等高风险域
- 需要持续回归
- 接口契约变更较大
- 需要执行报告

## 输出要求

1. 先输出模式判断结论。
2. 再输出执行流程。
3. 产物写入 `test-design/` 对应目录。
4. 测试用例覆盖功能、异常、边界三层。
5. 每条用例包含数据需求。
6. 缺失关键业务规则时，输出待确认问题，不编造结论。
7. 标准 / 完整模式保留需求 ID、模块 ID、用例 ID。
8. 完整模式维护 `test-design/traceability.md`。
9. 明确提示 AI 输出需要人工评审。
