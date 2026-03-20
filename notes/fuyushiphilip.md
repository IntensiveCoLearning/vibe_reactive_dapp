---
timezone: UTC+8
---

# FYS

**GitHub ID:** fuyushiphilip

**Telegram:** 

## Self-introduction

Let's vibe Reactive dApp！

## Notes

<!-- Content_START -->
# 2026-03-20
<!-- DAILY_CHECKIN_2026-03-20_START -->
![Screenshot 2026-03-20 at 11.07.04 PM.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/fuyushiphilip/images/2026-03-20-1774019241369-Screenshot_2026-03-20_at_11.07.04_PM.png)
<!-- DAILY_CHECKIN_2026-03-20_END -->

# 2026-03-19
<!-- DAILY_CHECKIN_2026-03-19_START -->

\[Origin Chain / Target Chain (e.g. Ethereum, Sepolia)\]

│

│ Emit Events (Transfer, OwnershipTransferred, BridgeRequest, Swap...)

▼

\[Reactive Network\]

┌────────────┐

│ ReactVM │

│ │

│ SentinelRC.sol │ ← Reactive Contract

│ ├─ subscribe() │

│ ├─ react() callback │

│ └─ anomaly check │

│ │ │

│ ▼ │

│ Decision: Anomaly? │

│ Yes → Trigger callback │

└─────────────┘

│

▼ Callback tx (pause / emergencyWithdraw / freeze)

\[Target Contract\] ← pause() / emergencyWithdraw(to TimelockVault)

│

▼

\[TimelockVault.sol\] ← 資金鎖定 24h，攻擊者無法立即提領
<!-- DAILY_CHECKIN_2026-03-19_END -->

# 2026-03-18
<!-- DAILY_CHECKIN_2026-03-18_START -->


### DeFi 合約保護情境

1.  **異常大額轉移 / 流動性抽乾防護（Large Transfer / Liquidity Drain）**
    
    -   **情境**：偵測單筆 Transfer 或 Swap 金額超過 TVL 一定比例（例如 5-10%），或短時間內多次大額提領（可能為 rug pull 或 exploit 前兆）。
        
    -   **RC 實現**：訂閱目標合約的 Transfer、Swap、Sync、Withdraw 等事件 → decode 金額與時間 → 若異常，立即 callback 呼叫 pause()、emergencyWithdraw() 或凍結池子。
        
    -   **優點**：秒級響應，全 on-chain 無 bot 延遲。
        
    -   **例子**：Uniswap V2 池保護，或一般 liquidity pool 異常監控。
        
2.  **價格異常衝擊 / Flash Loan 操縱防護（Price Manipulation / Flash Loan Attack）**
    
    -   **情境**：偵測短時間內價格劇烈波動（>5-10%）或 flash loan 特徵（大額 borrow → swap → repay）。
        
    -   **RC 實現**：監聽 Sync、Swap 事件 + 結合 cron 檢查池子儲備 → 異常時自動暫停交易、調整 slippage 限制，或觸發 circuit breaker。
        
    -   **優點**：直接監聽事件 log，比 off-chain 輪詢更快；可跨鏈驗證價格。
        
    -   **例子**：Uniswap V2 Stop-Loss / Take-Profit Demo（可反向用於協議保護）；Flash Loan 模式檢測。
        
3.  **清算風險 / 健康因子保護（Liquidation Protection）**
    
    -   **情境**：協議層或用戶倉位健康因子接近危險值，防止大規模清算或 cascade effect。
        
    -   **RC 實現**：用 cron 事件定期檢查健康因子，或監聽 borrow/repay 事件 → 低於閾值時自動補 collateral、還債，或發警報給 guardian。
        
    -   **優點**：24/7 自動化，無需用戶手動或 bot 維護；支援多策略（單補抵押、單還債、組合）。
        
    -   **例子**：Aave Liquidation Protection Demo（官方）；Aave Unified Protection（單合約服務多用戶）。
        
4.  **未經授權所有權變更 / 治理攻擊防護（Ownership / Admin Takeover）**
    
    -   **情境**：偵測 OwnershipTransferred、RoleGranted 或可疑 upgrade 事件。
        
    -   **RC 實現**：訂閱相關事件 → 若新 owner 非預期地址，自動 veto、暫停或轉移資產到 timelock。
        
    -   **優點**：透明可驗證，防止開發者或 attacker 惡意接管。
        
    -   **例子**：治理合約保護，或一般 admin 角色監控。
        
5.  **惡意 Approval / 無限授權濫用防護（Infinite Approval Exploit）**
    
    -   **情境**：偵測大量或可疑 Approval 事件（尤其 approve max uint）。
        
    -   **RC 實現**：監聽 Approval 事件 → 檢查 spender 是否可信 → 若異常，自動 revoke 或暫停合約。
        
    -   **優點**：可實現 conditional / time-limited approval，減少 scam 攻擊面。
        
    -   **例子**：Approval Magic Demo（官方可延伸為防護）。
        
6.  **流動性池自動再平衡 / 風險管理（Pool Rebalancing）**
    
    -   **情境**：池子不平衡或風險累積。
        
    -   **RC 實現**：監聽 Sync 事件 + cron → 自動 rebalance 或調整參數。
        
    -   **例子**：官方提到的 pools rebalancing use case。
        

### Bridge 合約保護情境

1.  **未匹配跨鏈事件 / Double-Spend 防護（Unmatched Lock/Mint）**
    
    -   **情境**：來源鏈 lock/burn 後，目標鏈 mint 未匹配或金額異常。
        
    -   **RC 實現**：訂閱來源鏈 BridgeRequest、Lock、Burn 事件 → 在目標鏈驗證後才 mint；若異常，自動 freeze 或 pause bridge。
        
    -   **優點**：原生跨鏈，burn 先於 mint，減少中間人風險。
        
    -   **例子**：Reactive Bridge 官方設計（去中心化跨鏈轉移）。
        
2.  **異常大額跨鏈轉移防護（Large Cross-Chain Transfer）**
    
    -   **情境**：單筆跨鏈金額超過閾值或頻率異常（可能為 drain 攻擊）。
        
    -   **RC 實現**：監聽跨鏈事件 → 自動觸發暫停、限額或 multisig 審核。
        
    -   **優點**：即時跨鏈響應，無需多個 off-chain relayer。
        
3.  **Relayer / Validator 異常行為防護**
    
    -   **情境**：relayer 提交可疑訊息或延遲。
        
    -   **RC 實現**：監聽 bridge 事件 + 獨立驗證 → 異常時 halt 操作。
        
    -   **例子**：Reactive Bridge 使用 event-driven 取代傳統 validator，降低信任。
        
4.  **橋合約升級 / 配置變更防護（Bridge Upgrade Security）**
    
    -   **情境**：bridge 合約被 upgrade 或參數惡意修改。
        
    -   **RC 實現**：監聽 upgrade 事件 → 自動回滾或通知 guardian。
<!-- DAILY_CHECKIN_2026-03-18_END -->

# 2026-03-17
<!-- DAILY_CHECKIN_2026-03-17_START -->



RC contracts are essentially non-upgradable. If you want to modify functions or perform iterative updates, you must redeploy the contract. This is because RC contracts involve relationships between subscriptions, execution logic, and callbacks, and they do not natively support upgrades. After redeployment, you also need to manage subscription migration and state. Subscriptions are registered in the system's subscription mechanism, and the logic is executed on the RBM. These two environments are isolated from each other. Once the contract is deployed, the subscription rules, contract address, and callback logic are all fixed and cannot be directly upgraded. The only option is to redeploy a new version of the contract.
<!-- DAILY_CHECKIN_2026-03-17_END -->

# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->




┌──────────────────────────────────┐

│ Applications │

│ DeFi / NFT / Games / DAO │

│ Uniswap / Aave / NFT markets │

└──────────────────────────────────┘

│

┌──────────────────────────────────┐

│ Automation Layer │

│ Reactive / Chainlink / Gelato │

│ Event-driven execution │

└──────────────────────────────────┘

│

┌──────────────────────────────────┐

│ Data & Indexing Layer │

│ The Graph / Analytics / ETL │

│ Queryable blockchain data │

└──────────────────────────────────┘

│

┌──────────────────────────────────┐

│ Smart Contract Layer │

│ Solidity / Rust / Move contracts │

└──────────────────────────────────┘

│

┌──────────────────────────────────┐

│ Execution Layer │

│ EVM / SVM / Move VM │

└──────────────────────────────────┘

│

┌──────────────────────────────────┐

│ Consensus & Network Layer │

│ Ethereum / Solana / Cosmos │

└──────────────────────────────────┘  
  
Consensus / Network Layer

validator  
p2p network  
consensus protocol

Execution Layer

transaction execution  
state transition  
gas accounting

Smart Contract Layer

Uniswap pool  
NFT contract  
DAO governance  
  
  
Data / Indexing Layer

log indexing  
query API  
analytics

Automation Layer（Reactive 所在）

watch event  
execute logic  
send tx

Application Layer

DEX  
Lending  
NFT  
GameFi  
DA
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->





**Reactive Contract 基本架構（以 Reactive Network 為例）**

通常會看到三個主要角色合約：

1.  **Origin Contract**（源頭合約）

•  部署在來源鏈（Ethereum Sepolia、Arbitrum、BNB Chain…）

•  會 emit Event

2.  **Reactive Contract**（反應合約）**← 這才是主角**

•  部署在 **Reactive Network**（專門的 EVM 相容執行層）

•  繼承 Reactive 基礎合約

•  實作 react() 或類似 callback 函數

•  透過 subscribe 訂閱事件

3.  **Destination Contract**（目標**合約）**

•  接收 Reactive Contract 跨鏈發過來的 callback / 訊息

•  可在任何支援的 EVM 鏈上
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->






**Reactive Contracts 是什麼？**

傳統智能合約（Solidity）特性：

•  被動式（Passive）

•  必須由外部帳戶（EOA）或另一合約主動呼叫才會執行

•  無法自己「醒來」對鏈上事件做出反應

**Reactive Contract（反應式智能合約）**：

•  主動式（Active / Reactive）

•  可以**訂閱**其他鏈（或同一鏈）的事件 log

•  當特定事件 + 條件成立時 → **自動執行**邏輯

•  支援跨鏈自動觸發（callback / 發送交易到目標鏈）

•  本質上是「鏈上版的 If-This-Then-That (IFTTT)」

核心創新：**Inversion of Control (控制反轉)**

→ 不再是「使用者呼叫合約」，而是「合約在事件發生時被系統呼叫」。
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->







Reactive Network 完整执行生命周期

User

│

│ tx trigger()

▼

BasicDemoL1Contract

│

│ emit Trigger

▼

Ethereum Log

│

│ indexed by

▼

Reactive Network

│

│ match subscription

▼

ReactVM

│

│ call react()

▼

BasicDemoReactiveContract

│

│ send tx

▼

BasicDemoL1Callback

│

│ emit Reacted

▼

Done
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->








傳統 DeFi 架構（Bot 驅動）

flowchart LR

A\[Blockchain Event<br>Swap / Price / Transfer\]

A --> B\[Indexers<br>TheGraph / Custom Indexer\]

B --> C\[Off-chain Bot\]

C --> D{Strategy Logic}

D -->|trigger| E\[Send Transaction\]

E --> F\[Smart Contract\]  
  
  
Reactive Network 架構（Event-driven）  
flowchart LR

A\[Blockchain Event<br>Swap / Price / Transfer\]

A --> B\[Reactive Network<br>Event Engine\]

B --> C\[Reactive Contract\]

C --> D\[Execute Logic\]

D --> E\[Send Transaction<br>to target chain\]
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->









傳統Smart Contract 是被動的，意思是沒人調用contract, contract 就什麼都不會做。

現在的DeFi系統依賴bots/Keepers 監聽鏈上的交易。

Reactive Contract 監聽鏈上事件，然後自動執行 contract，核心概念Event-driven Smart Contract。

| Smart Contract | Reactive Contract |
| --- | --- |
| User trigger transaction | Blockchain event |
| Passive | Auto |
| Bot/Keeper | network |
| Call-based | Event-driven |
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->










![Screenshot 2026-03-09 at 7.51.52 PM.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/fuyushiphilip/images/2026-03-09-1773057195173-Screenshot_2026-03-09_at_7.51.52_PM.png)
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
