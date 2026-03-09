---
timezone: UTC+8
---

# ghostin1024

**GitHub ID:** ghostin1024

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->
### 1\. 根本區別：Reactive 與傳統 EVM

-   **傳統 EVM (被動式)：** 傳統的智能合約是被動的。它們只能在外部帳戶 (EOA，如用戶錢包或機器人) 發起交易並支付 Gas 費時才會執行。如果沒有外部觸發，它們就會一直處於靜止狀態。
    
-   **Reactive EVM (主動/響應式)：** 採用了**控制反轉 (Inversion of Control, IoC)** 的概念。智能合約不再被動等待觸發，而是主動「監聽」區塊鏈上的特定事件。一旦條件滿足，合約會自主執行預設邏輯。
    
-   **執行效能：** 傳統 Ethereum EVM 是單執行緒、按順序處理交易；而 Reactive Network 採用平行化 EVM (Parallelized EVM)，不同的合約可以在不同的執行緒上獨立並行運作，大幅提升吞吐量並降低運算成本。
    

### 2\. 核心架構：ReactVM + Reactive Network 雙狀態環境

每一個部署在 Reactive Network 上的合約，實際上都存在於一個**雙狀態環境 (Dual-State Environment)** 中：

-   **Reactive Network 狀態：** 這是主網路層，運作方式類似典型的 EVM 區塊鏈。它負責與「系統合約」互動，主要用來處理跨鏈事件的「訂閱 (Subscribe)」和手動操作指令。
    
-   **ReactVM 狀態：** 這是一個專屬且隔離的虛擬機環境（每個部署者地址都有一個專屬的 ReactVM）。當網路監聽到你訂閱的事件發生時，會將資料傳入 ReactVM。這裡是合約執行 `react()` 核心邏輯、處理數據、並發起跨鏈回調 (Callbacks) 請求的地方。
    

這種架構將「事件監聽」與「邏輯運算」完美結合，同時保證了執行的安全與隔離性。

### 3\. 理解「事件驅動智能合約」(Reactive Smart Contracts, RSCs)

**睿应式智能合約 (RSCs)** 是一種為 Web3 打造的「IFTTT (If-This-Then-That)」自動化基礎設施。

-   **工作原理：** 開發者使用標準的 Solidity 編寫 RSC，並在合約中聲明想要監聽的目標鏈（如 Ethereum, BNB, Polygon 等）上的特定事件（Topic 0，例如某個代幣的 Transfer 或 DEX 的 Swap 事件）。
    
-   **自動化執行：** 當目標事件在源鏈上發生時，Reactive Network 會捕獲該事件，喚醒 ReactVM 處理邏輯，最後自主發送一筆交易到目標鏈執行後續動作。
    
-   **去中心化信任：** 過去要實現這種自動化，開發者必須在鏈下架設伺服器 (Cron jobs) 或依賴中心化的 Bot 甚至預言機來監聽並發送交易；RSC 將這一切完全搬到鏈上，實現無須信任的去中心化自動化。
    

### 4\. Reactive 適合解決什麼問題？

Reactive 非常適合處理需要**跨鏈互動**與**無須人為干預的自動化場景**，官方生態系統與開發案例中常見的應用包含：

-   **DeFi 自動化：** 例如在 Uniswap 上建立去中心化的止損/停利訂單 (Stop-loss/Take-profit)、資金池自動再平衡、防清算保護等。
    
-   **跨鏈應用：** 無須跨鏈橋的跨鏈資產轉移、跨鏈借貸（在 A 鏈抵押，在 B 鏈借款）、甚至是跨鏈的無信任第三方託管 (Escrow) 與仲裁。
    
-   **多重預言機數據聚合：** 自動從多個鏈上預言機收集數據，在 ReactVM 內計算平均值後再執行，消除對單一預言機的依賴。
    
-   **動態 NFT 與遊戲：** 根據玩家在低成本鏈上的即時行為 (如達成連勝紀錄)，自動在主鏈上觸發動態 NFT 的屬性變化或獎勵發放。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
