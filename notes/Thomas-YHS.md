---
timezone: UTC+8
---

# Thomas

**GitHub ID:** Thomas-YHS

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-23
<!-- DAILY_CHECKIN_2026-03-23_START -->
\# Sprint Plan v1 - Verified Auto-Execution Middleware

周期：48 小时（黑客松冲刺）

范围：主方案 V1（P0 必须交付，P1 只做一个加分项）

输入：PRD + User Stories

\## 1) Sprint Goal

在 48 小时内交付一个可演示、可审计、可复现的 `event -> react -> callback` 闭环，并满足最小信任校验与失败恢复能力。

\## 2) Team Capacity

假设团队编制（5 人）：

1\. 智能合约工程师（SC）

2\. 集成工程师（INT）

3\. 前端工程师（FE）

4\. QA/自动化（QA）

5\. 产品/演示（PO）

可用性假设：

1\. 每人 2 天，每天有效开发 12 小时。

2\. 每人预留 2 小时用于沟通、同步、提交材料。

3\. 每人净开发时间约 20 小时。

容量估算：

1\. 总净开发容量`5 * 20 = 100 小时`

2\. 按 `1 SP ~= 3 小时` 换算：约 `33 SP`

3\. 预留 15% 缓冲`5 SP`

4\. 可承诺容量`28 SP`

\## 3) Story 评估与承诺

\### 3.1 Story Point 估算

1\. US-01 规则创建与保存：3 SP

2\. US-02 事件监听与触发判定：5 SP

3\. US-03 执行前信任校验：5 SP

4\. US-04 callback 自动续费执行：5 SP

5\. US-05 失败重试与降级：3 SP

6\. US-06 审计日志与执行报告：3 SP

7\. US-07 规则配置页面：5 SP

8\. US-08 执行监控与报告页面：5 SP

9\. US-09 Gate ON/OFF 对比：2 SP

10\. US-10 提交证据打包：2 SP

\### 3.2 DoR（Definition of Ready）检查

1\. AC 已存在：全部故事满足。

2\. 依赖清晰：US-04、US-08 依赖上游完成。

3\. 外部阻塞：ERC-8004 real 接口存在不确定性，需 mock 兜底。

\### 3.3 Sprint 承诺

**Committed（28 SP）**

1\. US-01（3）

2\. US-02（5）

3\. US-03（5）

4\. US-04（5）

5\. US-05（3）

6\. US-06（3）

7\. US-07（5）

合计`29 SP`（超出 1 SP，可通过缩减 US-07 UI 细节控制）

**Stretch（不承诺）**

1\. US-08（5）

2\. US-09（2）

3\. US-10（2）

\## 4) 依赖与关键路径

依赖关系：

1\. US-01 -> US-02

2\. US-02 + US-03 -> US-04

3\. US-04 -> US-05 -> US-06

4\. US-01 -> US-07

5\. US-06 -> US-08

6\. US-04 + US-06 -> US-10

关键路径：

1\. US-01 -> US-02 -> US-03 -> US-04 -> US-05 -> US-06

外部依赖：

1\. Trust 数据源（real 模式）

2\. 目标测试链 RPC 稳定性

\## 5) 执行排期（按小时）

\### 0-8h

1\. SC：US-01（核心字段与规则存储）

2\. SC：US-02（事件监听与触发判定）

3\. INT：US-03（mock 通路先打通）

4\. FE：US-07（规则配置表单骨架）

5\. QA：准备 E2E 测试脚手架

验收里程碑：

1\. 能创建规则

2\. 能监听并命中一次触发

\### 8-16h

1\. SC + INT：US-04（callback 成功路径）

2\. INT：US-03（real 通路对接）

3\. FE：US-07（编辑/启停、表单校验）

4\. QA：回归触发链路

验收里程碑：

1\. 至少 1 次完整成功回调

2\. trust gate ON 下可拦截不合规样本

\### 16-24h

1\. INT：US-05（重试与降级）

2\. INT + QA：幂等验证

3\. FE：日志基础展示（服务 US-06）

4\. PO：准备中期演示脚本

验收里程碑：

1\. 失败后自动重试可观测

2\. 无重复执行

\### 24-36h

1\. INT + QA：US-06（审计日志字段完整）

2\. FE：US-07 打磨与错误提示

3\. SC：性能与 gas 快速优化

验收里程碑：

1\. 报告包含触发原因、执行者、tx、结果

2\. 用户可查看规则状态

\### 36-48h

1\. 全员：稳定性回归 + 演示彩排

2\. 有余量时做 US-09（Gate ON/OFF）

3\. 最后做提交证据整理（US-10 简版）

验收里程碑：

1\. 达成 PRD 核心 KR（成功率/拦截率/幂等）

2\. 形成可提交证据包

\## 6) 风险与缓解

1\. 风险：ERC-8004 real 接口不稳定缓解：US-03 采用 mock/real 双通路；real 失败时不阻塞主链路。

2\. 风险：callback 跨链失败率高缓解：优先做 US-05；先保成功样例，再扩展场景。

3\. 风险：前端范围膨胀导致延期缓解：US-07 只保核心操作；US-08 放入 Stretch。

4\. 风险：关键知识集中在单人缓解：SC/INT 双人结对关键模块，每 6 小时交叉 review。

5\. 风险：临近答辩才集成导致不稳

缓解：每 8 小时一次端到端集成验收，不等“最后一天”。

\## 7) Done 定义（Sprint DoD）

1\. 至少 1 次完整闭环成功（有 tx 证据）。

2\. Trust Gate 可阻断不合规执行。

3\. 失败重试可运行，且无重复执行。

4\. 可读执行报告可导出。

5\. 路演脚本可在 5 分钟内跑通。
<!-- DAILY_CHECKIN_2026-03-23_END -->

# 2026-03-19
<!-- DAILY_CHECKIN_2026-03-19_START -->

开了Workshop 正在完成Demo2，同时陷入了一点迷茫，究竟可以用Reactive 做什么呢，为什么要这么做，明天还需要再思考一下，于此同时自动监听续费的MVP已经完成
<!-- DAILY_CHECKIN_2026-03-19_END -->

# 2026-03-18
<!-- DAILY_CHECKIN_2026-03-18_START -->


\# QA Execution Report - Sprint Plan v1

执行日期：2026-03-18

执行角色：QA

执行基线`docs/06-sprint-plan/Sprint-Plan-v1.md`

\## 1. 执行说明

本报告基于\*\*当前工作区最新快照\*\*重跑结果。执行过程中工作区文件有变更（含测试与前端文件更新），因此已重新执行核心验证命令并以最终一次通过结果为准。

\## 2. 执行环境与命令

\- Solidity: `forge 1.2.3`

\- Python: `python3`

执行命令：

1\. `forge build`

2\. `forge test -vv`

3\. `python3 -m unittest tests/test_int_exec.py -v`

4\. `python3 scripts/run_int_demo.py`

5\. 前端静态依赖检查`index.html` 引用文件存在性）

\## 3. 执行结果总览

| 项目 | 结果 |

| --------------- | ---------------------------------- |

| 合约编译 | 通过 |

| Solidity 测试 | 10/10 通过 |

| Python INT 测试 | 6/6 通过 |

| INT 演示脚本 | 通过，生成证据文件 |

| 前端资源完整性 | `styles.css` / `app.js` 均存在 |

命令摘录：

\- `forge test -vv10 passed, 0 failed`

\- `python3 -m unittest ...Ran 6 tests ... OK`

\- `python3 scripts/run_int_demo.py`：输出 `artifacts/int_audit_log.jsonartifacts/int_summary.md`

\## 4. Story 验收状态（US-01 ~ US-07）

| Story | 状态 | 依据 |

| --------------------------- | ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------- |

| US-01 规则创建与保存 | 通过 | `testCreateRuleStoresRequiredFieldsAndMetadatatestCreateRuleRevertsOnMissingRequiredFields` 通过。 |

| US-02 事件监听与触发判定 | 通过 | `testTriggerNotMatchedWritesNoExecutionRecordAndMarksEventProcessedtestTriggerPathIsIdempotentForDuplicateEventId`、时间/规则过滤测试通过。 |

| US-03 执行前信任校验 | 通过 | `testTrustGateRejectsLowScoreExecutortestTrustGateSupportsMockAndRealSwitchtests/test_int_exec.py` 信任相关用例通过。 |

| US-04 callback 自动续费执行 | 通过 | callback 成功/失败路径测试通过，且执行报告包含核心字段。 |

| US-05 失败重试与降级 | 通过 | `testCallbackFailureEntersRetryThenFailsAtMaxRetriestestRetryRecoversAndPreventsDoubleCharge` 通过。 |

| US-06 审计日志与执行报告 | 通过 | 合约导出报告测试 + Python `AuditLogger` 过滤/导出测试通过。 |

| US-07 规则配置页面 | 部分通过（待人工） | 页面结构与脚本文件完整；尚未在真实浏览器完成人工交互回归（创建/编辑/启停/移动端）。 |

\## 5. Sprint DoD 判定

| DoD | 判定 | 说明 |

| ----------------------------------- | -------------------------- | ---------------------------------------------------------- |

| 至少 1 次完整闭环成功（有 tx 证据） | 达成 | 合约与 INT 测试均覆盖成功闭环，含 callback 结果字段。 |

| Trust Gate 可阻断不合规执行 | 达成 | 低分/模式切换场景均通过。 |

| 失败重试可运行，且无重复执行 | 达成 | 重试成功/重试失败/幂等相关场景通过。 |

| 可读执行报告可导出 | 达成 | 合约报告字段验证通过；INT 导出产物已生成。 |

| 路演脚本可在 5 分钟内跑通 | 基本达成（待 FE 人工确认） | INT 侧脚本可快速跑通；前端完整手工路演尚未在浏览器端实测。 |

\## 6. 风险与待办

1\. 工作区在执行期出现过内容变更，建议冻结代码快照后再做最终评审签收。

2\. 当前环境缺少 `pytest`，Python 测试采用 `unittest` 已可执行；如 CI 预期 `pytest`，需补齐依赖。

3\. 建议补一次前端人工回归（桌面 + 移动视口）作为 US-07 最终验收证据。

\## 7. QA 结论

在当前快照下，Sprint P0（US-01~US-06）可判定为通过；US-07 代码已具备基础能力，需补充浏览器人工回归证据后可闭环签收。
<!-- DAILY_CHECKIN_2026-03-18_END -->

# 2026-03-17
<!-- DAILY_CHECKIN_2026-03-17_START -->



\# PRD: 跨链订阅支付守护 x ERC-8004（Hackathon MVP）

\## 1. Summary

本 PRD 定义一个黑客松可交付的 MVP：当订阅余额不足或即将到期时，Reactive Contract 自动触发跨链续费回调，并在执行前进行 ERC-8004 身份与信誉校验。

目标是在测试网完成可演示的端到端闭环`event -> react -> callback`，并沉淀可验证证据（交易哈希、执行日志、身份校验结果）。

\## 2. Contacts

| Name | Role | Comment |

| ---------- | ----------------------- | -------------------------- |

| Yuehongshu | Product Owner / Builder | 需求定义、优先级与最终验收 |

| Yuehongshu | Smart Contract Engineer | RC、业务合约、回调流程实现 |

| Yuehongshu | Demo Owner | 演示脚本、提交材料与讲解 |

\## 3. Background

\### Context

当前订阅型链上服务面临三类问题：

1\. 用户资金分散在不同链，目标链余额不足时容易断供。

2\. 自动执行通常缺少“执行者可信度”证明。

3\. 出问题后很难快速解释“为何触发、谁执行、结果如何”。

\### Why Now

Reactive Hackathon 强调链上自动化执行能力，本题目同时覆盖自动化、跨链与可信执行，业务故事清晰，适合在短周期里做出高辨识度 Demo。

\### Newly Possible

通过 Reactive Contract 的事件监听与回调机制，可快速打通自动化执行路径；结合 ERC-8004 作为信任门槛，可把“自动执行”升级为“可验证自动执行”。

\## 4. Objective

\### Objective

构建一个可在测试网稳定演示的跨链订阅守护 MVP，证明系统可以在订阅中断前自动续费，并对执行者进行身份与信誉校验。

\### Why It Matters

\- 对用户：减少订阅中断风险，降低手动续费负担。

\- 对团队：展示“Reactive 自动化 + 可验证信任”的差异化方案。

\- 对黑客松评审：有完整闭环、明确价值和可验证证据。

\### Key Results (SMART)

1\. 在黑客松截止前，完成至少 1 次真实触发的 E2E 流程`event -> react -> callback`），并在演示中复现。

2\. 所有自动续费执行都必须经过 ERC-8004 门槛（白名单 + 最低信誉分），未通过时必须阻断执行。

3\. 每次触发产出完整审计记录：触发条件、执行者身份、信誉分、目标链交易哈希、最终状态。

4\. Demo 当天执行成功率达到 100%（基于预置测试场景至少 1 次成功回调）。

\## 5. Market Segment(s)

\### Primary Segment（MVP）

\- 需要跨链持有资金、并依赖持续订阅服务的 Web3 项目方或高级用户。

\- 核心 Job：确保服务不中断，不需要人工盯盘续费。

\- 核心约束：演示时间短、测试网资源有限、外部协议接口不稳定。

\### Secondary Segment（Post-MVP）

\- DAO Treasury 管理者、自动化运维团队。

\- 核心 Job：在可审计、可解释前提下执行链上自动化策略。

\## 6. Value Proposition(s)

1\. 自动守护：在阈值事件发生时自动触发续费，减少中断。

2\. 可信执行：执行前做 ERC-8004 身份与信誉校验，不是“谁都能执行”。

3\. 可解释审计：每次动作可回放、可核验，便于评审和后续运营。

4\. 快速扩展：MVP 从“订阅续费”起步，后续可扩展到 DAO 执行、收益归集、风控处置。

\## 7. Solution

\### 7.1 UX / Demo Flow

1\. 页面显示订阅状态（余额、到期时间、守护阈值）。

2\. 人为制造“余额不足/即将到期”场景。

3\. 触发链上事件，被 Reactive Contract 捕获。

4\. 执行前展示 ERC-8004 校验结果（身份 + 信誉分 + 是否通过）。

5\. 回调执行续费，页面回显目标链交易哈希与状态恢复。

6\. 展示执行报告（触发原因、执行者、结果）。

\### 7.2 Key Features

\#### P0（必须完成）

1\. 阈值触发自动续费（余额阈值或到期窗口至少支持 1 种）。

2\. 至少 1 次跨链回调成功案例（测试网可验证）。

3\. ERC-8004 白名单 + 最低信誉分校验，未通过必须拒绝执行。

4\. 执行日志与证据落地（交易哈希、状态、触发原因）。

\#### P1（加分）

1\. 动态阈值（考虑费用变化或订阅波动）。

2\. 失败自愈（重试 + 超时降级策略）。

3\. 可解释报告模板（用于评审展示和复盘）。

\#### P2（展示增强）

1\. 多策略模板（保守/平衡/激进）。

2\. 简单可视化监控面板（状态、触发次数、成功率）。

\### 7.3 Technology (MVP Scope)

1\. `SubscriptionGuard`：订阅阈值与触发规则。

2\. Reactive Contract：监听事件并执行回调逻辑。

3\. `AgentTrustAdapter`：读取 ERC-8004 身份/信誉并完成门槛判断。

4\. 目标续费合约：可先接 mock，再切真实接口。

5\. 日志聚合脚本：汇总触发记录与演示证据。

\### 7.4 Assumptions

1\. ERC-8004 测试网接口可用，且能读取到可验证身份/信誉数据。

2\. 目标链和回调链在演示窗口内稳定。

3\. 可预置测试资金与测试账号，避免演示时余额/权限不足。

4\. 黑客松评审认可“测试网可验证证据”作为完成度证明。

\### 7.5 Acceptance Criteria（必须可 Demo）

1\. 给定“余额不足/即将到期”条件，系统在无需人工干预下触发自动续费流程。

2\. 当执行 Agent 不在白名单或信誉分低于阈值时，流程被阻断并记录拒绝原因。

3\. 当执行 Agent 通过校验时，续费回调成功，页面可展示有效 tx hash。

4\. Demo 结束时可提交 4 份证据：部署地址、事件日志、回调交易哈希、执行报告截图。

5\. 5 分钟讲解可完整走通一次成功流程，并能解释一次失败/阻断分支。

\## 8. Release

\### 8.1 48 小时里程碑

1\. Day 1 AM（0-6h）

\- 搭建基础工程与合约骨架。

\- 跑通最小事件触发与回调链路。

2\. Day 1 PM（6-12h）

\- 接入 ERC-8004 适配器。

\- 完成白名单 + 最低信誉分门槛校验。

3\. Day 2 AM（12-24h）

\- 完成日志与证据输出。

\- 增加失败重试或降级路径（至少 1 条）。

\- 补齐 Demo 页面关键状态显示。

4\. Day 2 PM（24-48h）

\- 全流程联调与压测演练。

\- 录制 Demo 视频，整理提交材料。

\- 准备现场讲稿与问题答辩要点。

\### 8.2 Version Scope

\- V0（Hackathon MVP）：仅覆盖单场景“订阅守护”+ 可信执行。

\- V1（赛后增强）：动态阈值、多策略模板、可视化监控。

\- V2（产品化方向）：扩展到 DAO 条件执行、收益归集、风控自动处置。

\### 8.3 Risks & Mitigation

1\. ERC-8004 标准迭代风险：通过适配器隔离接口变化。

2\. 跨链延迟或失败：预置重试机制与演示备用路径。

3\. Demo 不稳定：准备固定可复现脚本和测试资金。
<!-- DAILY_CHECKIN_2026-03-17_END -->

# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->




## 1\. Reactive Network 是什么

Reactive Network 的核心是“事件驱动的链上自动化”。

Reactive Contract（RC）持续监听 EVM 链事件日志，当条件满足时自动执行 Solidity 逻辑，并向目标链发起 callback。

这解决了传统模式下“必须由用户/机器人手动发交易触发”的问题。

## 2\. 执行模型（工作流）

1.  Origin 链合约产生事件（event）。
    
2.  Reactive Contract 订阅并监听该事件。
    
3.  满足条件后，RC 构造 payload 并发出 callback 意图。
    
4.  目标链 Callback Proxy 转发调用到 Destination 合约。
    
5.  Destination 合约做发送方与身份校验后执行业务逻辑。
    

一句话：`监听事件 -> 条件判断 -> 跨链回调 -> 目标链验证执行`。

## 3\. Reactive 的核心特点

-   持续监听：不依赖人工触发，事件出现即可响应。
    
-   条件驱动：可在合约中定义阈值、状态、地址等条件。
    
-   跨链自动化：从 Origin 触发，到 Destination 执行闭环。
    
-   Solidity 原生开发：业务逻辑直接写在合约里，便于审计与复用。
    
-   安全边界清晰：通过 Callback Proxy、发送方校验、rvm id 校验限制调用来源。
    

## 4\. 使用方法（可直接落地）

### 4.1 设计三类合约

-   Origin 合约：负责发出业务事件。
    
-   Reactive 合约：负责订阅+过滤+构造 callback。
    
-   Destination 合约：负责接收并验证 callback。
    

### 4.2 关键实现模板

Origin（触发事件）

```solidity
event Received(address indexed origin, address indexed sender, uint256 indexed value);

receive() external payable {
    emit Received(tx.origin, msg.sender, msg.value);
}
```

Reactive（阈值过滤 + callback）

```solidity
function react(LogRecord calldata log) external vmOnly {
    if (log.topic_3 < minTriggerWei) return;

    bytes memory payload = abi.encodeWithSignature(
        "callback(address,address,address,uint256,uint256)",
        address(this),
        address(uint160(log.topic_1)),
        address(uint160(log.topic_2)),
        log.topic_3,
        log.tx_hash
    );

    emit Callback(destinationChainId, destinationCallback, GAS_LIMIT, payload);
}
```

Destination（验证后执行）

```solidity
function callback(
    address reactiveSender,
    address originEoa,
    address originSender,
    uint256 amount,
    uint256 sourceTxHash
) external authorizedSenderOnly rvmIdOnly(reactiveSender) {
    emit CallbackReceived(tx.origin, msg.sender, reactiveSender, originEoa, originSender, amount, sourceTxHash);
}
```

## 5\. 典型应用场景

-   自动止盈/止损
    
-   抵押率监控与清算保护
    
-   跨链资产策略自动再平衡
    
-   链上告警触发自动应急动作
    

## 6\. 常见问题与排查

-   不触发 callback：检查订阅 topic、阈值条件、触发金额。
    
-   callback 被拒绝：检查 callback proxy 地址、`rvmIdOnly` 配置。
    
-   部署失败：检查部署 value、RPC 可用性、账户余额和 gas。
    
-   事件不匹配：确认 Origin 事件签名与 `topic0` 一致。
    

## 7\. 总结

Reactive Network 的价值不只是“跨链”，而是把链上事件驱动自动化做成可编程、可验证、可审计的闭环。

## Reactive Network Use Case

![image.png](attachment:ecdc3063-affd-4d23-9862-06be659ceda5:image.png)
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->





先打个卡，今天可能时间不是特别多
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->






**A. Origin 合约（事件源）**  
代码位置：OriginContract.sol

1.  第 1 行 // SPDX-License-Identifier: GPL-2.0-or-later  
    语法：源码许可证声明，不影响运行逻辑，但编译器和开源平台会读取它。
    
2.  第 2 行 pragma solidity ^0.8.20;  
    语法：编译器版本约束，^0.8.20 表示允许 0.8.20 <= version < 0.9.0。
    
3.  第 6 行 contract OriginContract {  
    语法：定义合约类型，部署后会有一个链上地址（ORIGIN\_ADDR）。
    
4.  第 7 行 event Received(address indexed origin, address indexed sender, uint256 indexed value);  
    语法：定义事件。  
    细节：3 个 indexed 参数都会进入 topic1~topic3，可被高效过滤检索。  
    执行意义：Reactive 侧订阅的就是这个事件的 topic0 + 地址 + 链 ID。
    
5.  第 9 行 receive() external payable {  
    语法：receive 是特殊函数。  
    触发条件：合约收到原生币且 calldata 为空时自动执行。  
    external 仅外部调用路径进入；payable 表示允许接收币。
    
6.  第 10 行 emit Received(tx.origin, msg.sender, msg.value);  
    语法：emit 发日志。  
    语义：
    
    -   tx.origin 是最初发起交易的 EOA。
        
    -   msg.sender 是当前直接调用者。
        
    -   msg.value 是这次调用附带的金额。  
        执行效果：产生一条日志供 Reactive 网络捕获。
        
7.  第 12 行 (bool ok,) = payable(tx.origin).call{value: msg.value}("");  
    语法细节：
    
    -   payable(tx.origin)：把地址转成可收款类型。
        
    -   低级调用 call{value: ...}("")：转出原生币。
        
    -   返回 (success, returndata)，这里只接收 success。  
        执行效果：把收到的金额原样退回交易发起者。
        
8.  第 13 行 require(ok, "Refund failed");  
    语法：条件检查，不满足则回滚整笔交易。  
    效果：若退款失败，事件和状态都不会最终生效（事务原子性）。
    
9.  第 15 行 }  
    receive 与合约体结束。
    

* * *

**B. Reactive 合约（监听并发回调意图）**  
代码位置：ReactiveContract.sol

1.  第 4-6 行 import
    
    -   IReactive：定义 LogRecord 和 Callback 事件（接口）  
        参考：IReactive.sol
        
    -   ISystemContract：系统订阅服务接口
        
    -   AbstractReactive：提供 vm 检测、vmOnly、REACTIVE\_IGNORE、service 等能力  
        参考：AbstractReactive.sol
        
2.  第 10 行 contract ReactiveContract is IReactive, AbstractReactive {  
    语法：多继承。  
    效果：本合约必须实现 react(...)；同时拥有父类字段和修饰器。
    
3.  第 11 行 uint64 internal constant GAS\_LIMIT = 1\_000\_000;  
    语法：常量，编译期写死。  
    用途：回调交易给目标链时使用的 gas limit。
    
4.  第 13-16 行 immutable 变量  
    语法：构造函数赋值后不可改，读取成本低于普通 storage。  
    用途：固定源链、目标链、阈值、目标回调合约地址。
    
5.  第 18-26 行构造函数参数  
    包含系统地址、链 ID、源合约、源事件 topic0、目标回调地址、最小触发金额。
    
6.  第 27 行 service = ISystemContract(payable(\_service));  
    语法：类型转换到接口实例。  
    效果：后续可调用 service.subscribe(...)。
    
7.  第 29-32 行赋值 immutable 字段。  
    执行后这些关键参数固定。
    
8.  第 34 行 if (!vm) {  
    vm 来自父类 AbstractReactive，在父构造中通过 detectVm() 判定。  
    判定逻辑见：AbstractReactive.sol (line 35)  
    意义：只在顶层 Reactive Network 副本上做订阅，避免 VM 副本重复订阅。
    
9.  第 35-37 行 service.subscribe(...)  
    接口定义见：ISubscriptionService.sol (line 17)  
    参数解释：
    
    -   originChainId：源链
        
    -   \_originContract：监听的源合约地址
        
    -   \_originTopic0：事件签名哈希（Received(...)）
        
    -   后 3 个 REACTIVE\_IGNORE：忽略 topic1~3 的过滤
        

10.  第 42 行 function react(LogRecord calldata log) external vmOnly {  
     语法：
     
     -   external 外部入口
         
     -   calldata 只读参数（省 gas）
         
     -   vmOnly 修饰器来自父类，要求 vm == true，否则 revert。  
         LogRecord 结构体字段定义见 IReactive.sol (line 10)。
         
11.  第 43-45 行阈值判断  
     log.topic\_3 对应你事件里的 value（第 3 个 indexed 参数）。  
     小于阈值直接 return，不触发目标链回调。
     
12.  第 47-48 行地址还原  
     topic 在结构里是 uint256，地址是低 160 位，所以用：  
     address(uint160(log.topic\_1)) / address(uint160(log.topic\_2))  
     分别还原 originEOA 与 originSender。
     
13.  第 50-57 行 abi.encodeWithSignature(...)  
     语法：按函数签名编码 calldata。  
     你编码的是：  
     callback(address,address,address,uint256,uint256)  
     顺序必须与 Destination 合约函数签名一致。  
     参数分别是：
     
     -   reactiveSender（本合约地址）
         
     -   originEoa
         
     -   originSender
         
     -   amount（log.topic\_3）
         
     -   sourceTxHash（log.tx\_hash）
         
14.  第 59 行 emit Callback(...)  
     事件定义在 IReactive：见 IReactive.sol (line 25)。  
     这是关键跨链意图信号，Reactive 网络据此在目标链执行回调。
     

* * *

**C. Destination 合约（目标链回调落点）**  
代码位置：DestinationContract.sol

1.  第 4 行继承 AbstractCallback  
    父类位置：AbstractCallback.sol
    
2.  第 9-17 行定义 CallbackReceived 事件  
    前三个字段 indexed，便于浏览器/监听服务按来源、调用者、reactiveSender 检索。
    
3.  第 19 行构造函数  
    constructor(address _callbackProxy, address_ expectedReactiveSender) payable AbstractCallback(\_callbackProxy)  
    语法点：
    
    -   payable 允许部署时附带资金
        
    -   AbstractCallback(\_callbackProxy) 是父构造调用
        
4.  父构造实际做了什么（关键）  
    见 AbstractCallback.sol (line 12)：
    
    -   rvm\_id = msg.sender
        
    -   vendor = _callback_sender
        
    -   addAuthorizedSender(\_callback\_sender)  
        最后一条把 callback proxy 写入授权白名单。
        
5.  第 21 行 rvm\_id = \_expectedReactiveSender;  
    你在子构造中覆盖了父构造默认值。  
    语义：
    
    -   若传 0x0，配合 rvmIdOnly 变成“放行任意 reactiveSender”
        
    -   若传具体地址，只允许那一个 Reactive sender
        
6.  第 24-30 行 callback 函数签名  
    参数顺序必须与 Reactive 侧 abi.encodeWithSignature(...) 完全一致，否则解码失败。
    
7.  第 30 行修饰器链  
    authorizedSenderOnly rvmIdOnly(reactiveSender)  
    来源：
    
    -   authorizedSenderOnly 在 AbstractPayer.sol (line 22)，要求 senders\[msg.sender\] == true。
        
    -   rvmIdOnly 在 AbstractCallback.sol (line 18)，要求 rvm\_id == 0 || rvm\_id == reactiveSender。  
        两层都过才会继续执行。
        
8.  第 31 行 emit CallbackReceived(...)  
    记录成功回调证据，供提交作业时链上证明。
    

* * *

**D. 依赖基类的“执行影响”要点**

1.  AbstractReactive 构造函数  
    见 AbstractReactive.sol (line 19)  
    会设置：
    
    -   vendor = service = SERVICE\_ADDR
        
    -   将 SERVICE\_ADDR 加入授权发送者
        
    -   detectVm() 判断当前是否 VM 副本
        
2.  detectVm() 判定逻辑  
    看固定地址 0x...fffFfF 有没有代码。
    
    -   有代码：vm=false（顶层 RN）
        
    -   无代码：vm=true（RVM）  
        这直接决定 vmOnly / rnOnly 的行为。
        
3.  AbstractPayer 授权与支付
    
    -   授权映射：mapping(address => bool) senders
        
    -   authorizedSenderOnly 统一做 ACL 校验
        
    -   \_pay 用低级 call 转账并检查余额/成功状态
        

* * *

**E. 端到端执行流程（你这次实测）**

1.  用户调用 [trigger-origin.sh](http://trigger-origin.sh) 给 Origin 转账。
    
2.  Origin receive() 发 Received 日志并退款。
    
3.  Reactive 订阅系统捕获该日志，调用 react(log)。
    
4.  react 校验阈值，通过后发 Callback 意图事件。
    
5.  目标链 callback proxy 调用 Destination.callback(...)。
    
6.  Destination 经过 authorizedSenderOnly + rvmIdOnly 校验后发 CallbackReceived。
    
7.  你在浏览器看到 Origin 事件 + Destination 事件，形成完整证明链路。
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->







今天主要关注了React VM相关的内容，并且阅读了Reactive的源码，下面是总结的笔记：  
文档目标

-   仅解释 ReactVM 在 Reactive 架构中的角色。
    
-   重点说明为什么代码里会出现 `if (!vm)`、`vmOnly`，以及“副本/双状态”的真实含义。
    

## 1\. ReactVM 是什么

ReactVM 是 Reactive Network 中用于执行 Reactive Contract 自动化逻辑的私有执行环境。

当源链事件匹配订阅条件后，事件处理并不是由普通 EOA 直接执行，而是由 ReactVM 执行 `react(...)`。

## 2\. 为什么会说有“两个副本”

同一份 Reactive 合约代码在系统中对应两个执行语境：

-   Reactive Network 侧实例：负责控制面动作，例如 `subscribe()` / `unsubscribe()`。
    
-   ReactVM 侧执行实例：负责执行面动作，例如响应日志并运行 `react(...)`。
    

这就是“副本”的含义：同一逻辑、不同环境、职责拆分。

## 3\. 双状态（Dual-State）含义

Reactive 文档中的双状态可以理解为：

-   Reactive Network State：由你直接发交易调用合约函数时更新。
    
-   ReactVM State：由订阅事件触发、在 ReactVM 执行 `react(...)` 时更新。
    

两者不是自动强一致同步关系，因此要明确区分“谁在更新哪一侧状态”。

## 4\. 为什么 `if (!vm)` 必须存在

在你的 `ReactiveContract` 里：

```solidity
if (!vm) {
    service.subscribe(originChainId, _originContract, _originTopic0, REACTIVE_IGNORE, REACTIVE_IGNORE, REACTIVE_IGNORE);
}
```

这里的目的：只在 Reactive Network 侧注册订阅，避免在 ReactVM 执行副本里重复做控制面操作。

文档语义上，`subscribe()` / `unsubscribe()` 在 VM 内调用无效或不应依赖，因此应在非 VM 分支执行。

## 5\. 为什么 `vmOnly` 必须存在

在 `react(LogRecord calldata log)` 上使用 `vmOnly`，表示该入口只允许 ReactVM 执行。

这样可以保证：

-   普通外部调用者不能伪造事件直接驱动回调逻辑。
    
-   执行路径与 Reactive Runtime 的事件触发模型一致。
    

## 6\. 与你项目的映射

-   Origin 合约发 `Received` 事件。
    
-   Reactive Network 实例已通过 `subscribe` 注册监听。
    
-   事件命中后由 ReactVM 调用 `react(...)`。
    
-   `react(...)` 满足阈值后发 `Callback` 事件。
    
-   目标链 callback proxy 调用 Destination 合约并触发 `CallbackReceived`。
    

## 7\. 常见误区

1.  误区：一个合约只有一份状态。
    

结论：Reactive 架构下存在执行语境拆分与双状态概念。

1.  误区：在 `react()` 里动态 `subscribe` 就能生效。
    

结论：订阅属于控制面，应在非 VM 路径管理。

1.  误区：`vmOnly` 只是普通权限控制。
    

结论：它核心是执行环境约束，不只是地址 ACL。

## 8\. 一句话总结

Reactive 的关键是“控制面（Network 订阅）与执行面（ReactVM 响应）分离”。理解这点，就能理解为什么代码里同时出现 `if (!vm)` 和 `vmOnly`。
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->








# 文档目的

沉淀 `ReactiveDemo1` 的最小可复现工程流程：从编码到部署、触发、验收。

## 项目范围

-   Origin 合约：接收转账并发出 `Received` 事件。
    
-   Reactive 合约：订阅 Origin 事件，满足阈值后发出跨链 `Callback` 意图。
    
-   Destination 合约：接收 callback proxy 调用并发出 `CallbackReceived` 事件。
    

## 环境与前置

-   工具：Foundry（`forge`、`cast`）。
    
-   配置：复制 `.env.example` 到 `.env` 并填写私钥/RPC/链参数。
    
-   关键变量：
    
    -   Origin：`ORIGIN_RPC` `ORIGIN_PRIVATE_KEY` `ORIGIN_CHAIN_ID`
        
    -   Destination：`DESTINATION_RPC` `DESTINATION_PRIVATE_KEY` `DESTINATION_CHAIN_ID`
        
    -   Reactive：`REACTIVE_RPC` `REACTIVE_PRIVATE_KEY` `SYSTEM_CONTRACT_ADDR`
        
    -   回调参数：`DESTINATION_CALLBACK_PROXY_ADDR` `EXPECTED_REACTIVE_SENDER`
        
    -   地址回填：`ORIGIN_ADDR` `CALLBACK_ADDR`
        
    -   阈值/触发值：`MIN_TRIGGER_WEI` `TRIGGER_VALUE_WEI`
        

## 编码流程（实现顺序）

1.  定义事件源合约 `src/OriginContract.sol`
    
2.  定义回调接收合约 `src/DestinationContract.sol`
    
3.  定义反应式合约 `src/ReactiveContract.sol`
    
4.  编写单测 `test/ReactiveContract.t.sol`
    

## 关键代码片段

### 1) Origin 事件触发

```solidity
event Received(address indexed origin, address indexed sender, uint256 indexed value);

receive() external payable {
    emit Received(tx.origin, msg.sender, msg.value);

    (bool ok,) = payable(tx.origin).call{value: msg.value}("");
    require(ok, "Refund failed");
}
```

### 2) Reactive 订阅 + 阈值过滤 + 回调意图

```solidity
constructor(
    address _service,
    uint256 _originChainId,
    uint256 _destinationChainId,
    address _originContract,
    uint256 _originTopic0,
    address _destinationCallback,
    uint256 _minTriggerWei
) payable {
    service = ISystemContract(payable(_service));

    originChainId = _originChainId;
    destinationChainId = _destinationChainId;
    destinationCallback = _destinationCallback;
    minTriggerWei = _minTriggerWei;

    if (!vm) {
        service.subscribe(
            originChainId, _originContract, _originTopic0, REACTIVE_IGNORE, REACTIVE_IGNORE, REACTIVE_IGNORE
        );
    }
}

function react(LogRecord calldata log) external vmOnly {
    if (log.topic_3 < minTriggerWei) {
        return;
    }

    address originEoa = address(uint160(log.topic_1));
    address originSender = address(uint160(log.topic_2));

    bytes memory payload = abi.encodeWithSignature(
        "callback(address,address,address,uint256,uint256)",
        address(this),
        originEoa,
        originSender,
        log.topic_3,
        log.tx_hash
    );

    emit Callback(destinationChainId, destinationCallback, GAS_LIMIT, payload);
}
```

### 3) Destination 回调接收

```solidity
constructor(address _callbackProxy, address _expectedReactiveSender) payable AbstractCallback(_callbackProxy) {
    // If _expectedReactiveSender is zero, rvm check is disabled (wildcard).
    rvm_id = _expectedReactiveSender;
}

function callback(
    address reactiveSender,
    address originEoa,
    address originSender,
    uint256 amount,
    uint256 sourceTxHash
) external authorizedSenderOnly rvmIdOnly(reactiveSender) {
    emit CallbackReceived(tx.origin, msg.sender, reactiveSender, originEoa, originSender, amount, sourceTxHash);
}
```

### 4) 单测核心断言

```solidity
vm.expectEmit(true, true, true, true);
emit Callback(84532, destination, 1_000_000, expectedPayload);

reactive.react(log);
```

## 本地构建与测试

```bash
forge build
forge test
```

验收标准：`forge test` 全部通过（当前记录为 `2 passed, 0 failed`）。

## 部署与运行流程（生产步骤）

按顺序执行，且每一步完成后回填 `.env`。

### Step 1 部署 Origin

```bash
./script/deploy-origin.sh
```

等价核心命令：

```bash
forge create --broadcast --rpc-url "$ORIGIN_RPC" --private-key "$ORIGIN_PRIVATE_KEY" src/OriginContract.sol:OriginContract
```

### Step 2 部署 Destination

```bash
./script/deploy-destination.sh
```

等价核心命令：

```bash
forge create --broadcast --rpc-url "$DESTINATION_RPC" --private-key "$DESTINATION_PRIVATE_KEY" src/DestinationContract.sol:DestinationContract --value "${DESTINATION_DEPLOY_VALUE_WEI}" --constructor-args "$DESTINATION_CALLBACK_PROXY_ADDR" "$EXPECTED_REACTIVE_SENDER"
```

### Step 3 部署 Reactive

```bash
./script/deploy-reactive.sh
```

等价核心命令：

```bash
TOPIC0=$(cast keccak "Received(address,address,uint256)")
forge create --broadcast --rpc-url "$REACTIVE_RPC" --private-key "$REACTIVE_PRIVATE_KEY" src/ReactiveContract.sol:ReactiveContract --value "${REACTIVE_DEPLOY_VALUE_WEI}" --constructor-args "$SYSTEM_CONTRACT_ADDR" "$ORIGIN_CHAIN_ID" "$DESTINATION_CHAIN_ID" "$ORIGIN_ADDR" "$TOPIC0" "$CALLBACK_ADDR" "$MIN_TRIGGER_WEI"
```

### Step 4 触发 Origin 事件

```bash
./script/trigger-origin.sh
```

等价核心命令：

```bash
cast send "$ORIGIN_ADDR" --rpc-url "$ORIGIN_RPC" --private-key "$ORIGIN_PRIVATE_KEY" --value "$TRIGGER_VALUE_WEI"
```

## 已执行记录（2026-03-10 Asia/Shanghai）

-   Origin Contract: `0x9A76EB25854B42B60B984C933b08eD12eeAeFCd8`
    
-   Destination Contract: `0x2F4dDA454F470537445e70F479EF9D7F24Ce40Ae`
    
-   Reactive Contract: `0x4DA4f5ebe4de9Da95Ab7DEdFa92a76755531918b`
    
-   Deploy Origin Tx: `0xe218241014d541437b7953d0e6c6d83a0b4de15b269020decdbdbd93277d4a87`
    
-   Deploy Destination Tx: `0xf0f7767ecd347404985e4f006461b1f0e709ec848ad9412413c4d994970b91f0`
    
-   Deploy Reactive Tx: `0xeaa98e9f8539e79b849fd273eb3de1e5ece215d13eb0b60530944ac1c8a2cfb1`
    
-   Trigger Origin Tx: `0xcccddb46d1cda1d193b8c353d687cc2a9949b6dd4028a16489c136371140783f`
    
-   Callback Tx: `0x58077A0F1FEF7B98CAA6214A003D0ACB049298E9632D2FD2B70DAA4E6037FB35`
    

## 验收与证明

-   Origin 事件：
    
    -   `Received` in Sepolia tx event log
        
-   Destination 事件：
    
    -   `CallbackReceived` in Base Sepolia tx event log
        
-   归档材料：
    
    -   `DEPLOYMENT_RECORD.md`
        
    -   `SUBMISSION_TEMPLATE.md`
        

## 常见失败点

-   `.env` 未回填最新地址（`ORIGIN_ADDR` / `CALLBACK_ADDR`）。
    
-   `MIN_TRIGGER_WEI` 大于 `TRIGGER_VALUE_WEI` 导致不触发回调。
    
-   `EXPECTED_REACTIVE_SENDER` 设置错误导致 `rvmIdOnly` 校验失败。
    
-   部署资金不足（`DESTINATION_DEPLOY_VALUE_WEI` / `REACTIVE_DEPLOY_VALUE_WEI`）。
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->









今天完成了Demo的部署并运行，具体的实现如下文所示：  
\# 跨链事件与回调作业提交

\## 1) 部署信息

\- Origin Contract 地址: `0x9A76EB25854B42B60B984C933b08eD12eeAeFdC8`

\- Destination Contract 地址: `0x2F4dDA454F470537445e70F479EF9D7F24Ce40Ae`

\- Reactive Contract 地址: `0x4DA4f5ebe4de9Da95Ab7DEdFa92a76755531918b`

\## 2) 交易哈希

\- Deploy Origin Tx: `0xe218241014d541437b7953d0e6c6d83a0b4de15b269020decdbdbd93277d4a87`

\- Deploy Destination Tx: `0xf0f7767ecd347404985e4f006461b1f0e709ec848ad9412413c4d994970b91f0`

\- Deploy Reactive Tx: `0xeaa98e9f8539e79b849fd273eb3de1e5ece215d13eb0b60530944ac1c8a2cfb1`

\- Trigger Origin Tx: `0xcccddb46d1cda1d193b8c353d687cc2a9949b6dd4028a16489c136371140783f`

\- Callback Tx（目标链）: `0x58077A0F1FEF7B98CAA6214A003D0ACB049298E9632D2FD2B70DAA4E6037FB35`

\## 3) 触发成功证明

!\[1773144120335\](image/SUBMISSION\_TEMPLATE/1773144120335.png)

\- Origin `Received` 事件浏览器链接: [https://sepolia.etherscan.io/tx/0xcccddb46d1cda1d193b8c353d687cc2a9949b6dd4028a16489c136371140783f#eventlog](https://sepolia.etherscan.io/tx/0xcccddb46d1cda1d193b8c353d687cc2a9949b6dd4028a16489c136371140783f#eventlog)

!\[1773144297441\](image/SUBMISSION\_TEMPLATE/1773144297441.png)

\- Destination `CallbackReceived` 事件浏览器链接: [https://sepolia.basescan.org/tx/0x58077A0F1FEF7B98CAA6214A003D0ACB049298E9632D2FD2B70DAA4E6037FB35#eventlog](https://sepolia.basescan.org/tx/0x58077A0F1FEF7B98CAA6214A003D0ACB049298E9632D2FD2B70DAA4E6037FB35#eventlog)

\- Reactscan Lasna 成功截图 [https://lasna.reactscan.net/address/0x8dc56a4e682b2e12f5e4056d8e82b30c2dee94bc/2](https://lasna.reactscan.net/address/0x8dc56a4e682b2e12f5e4056d8e82b30c2dee94bc/2)

!\[1773144433736\](image/SUBMISSION\_TEMPLATE/1773144433736.png)

\## 4) 简短说明

\- 本次测试中，向 Origin 合约发送 `1000000000000000` wei 后，Reactive 合约监听到 `Received` 事件并触发跨链回调，在 Base Sepolia 上成功执行 callback 交易 `0x58077A0F1FEF7B98CAA6214A003D0ACB049298E9632D2FD2B70DAA4E6037FB35`，完成 `CallbackReceived` 事件证明。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->










终于又开始了新的残酷共学，这次一定要坚持下来，这次想看看能不能做一个简单的链上套利机器人。  
今天看了看文档，把demo的代码拉下来，基础的配置调试了一下，明天部署到测试链上，下面是今天的笔记：

##   
Reactive Network

Reactive 的核心可以引用文档中的一句话来描述：

> Reactive Contracts monitor event logs across EVM chains and execute Solidity logic automatically when conditions are met. Instead of waiting for users or bots to trigger transactions, RCs run continuously and decide when to send cross-chain callback transactions — essentially providing **if-this-then-that automation for smart contracts**.

Reactive实现的最重要的一点是可以在链上持续监控并且发送callback到指定链，传统区块链必须有人发起交易，而Reactive Contracts **持续运行并监听事件**，当条件满足时会自动执行操作。

* * *

Reactive Network 的执行模型本质上是：

**监听 Origin 上的事件 → 在 Destination 上发送 callback → 通过 Callback Proxy 保证安全与可验证性**

Event on Origin → Reactive listens → Callback sent to Destination → Destination verifies through Callback Proxy

### Reactive 可以实现的场景

1.  自动止盈/止损
    
2.  清算保护
    
3.  自动切换投资组合
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
