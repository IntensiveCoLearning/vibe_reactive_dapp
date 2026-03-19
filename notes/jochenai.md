---
timezone: UTC+8
---

# jochenai

**GitHub ID:** jochenai

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-19
<!-- DAILY_CHECKIN_2026-03-19_START -->
The Approval Magic Demo illustrates how reactive smart contracts can automate token approval workflows, eliminating the need for manual follow-up transactions. Instead of users approving tokens and then separately initiating transfers or swaps, the system listens for approval events and executes predefined actions automatically.

At the core is the ApprovalService, a subscription-based contract that manages users and funds the gas required for executing callbacks. The ApprovalListener monitors blockchain events such as token approvals and subscription updates, triggering the appropriate contract logic when conditions are met.

Two main flows demonstrate the system’s capabilities. In the Exchange flow, approved tokens are transferred from the user and immediately exchanged for ETH, which is sent back to them. In the Swap flow, approved tokens are routed through a decentralized exchange, swapped for another token, and returned to the user—all in a single automated sequence.

Supporting contracts like ApprovalEthExch and ApprovalMagicSwap implement these behaviors, while ApprovalDemoToken provides a testing environment.

Overall, the demo highlights how reactive infrastructure can streamline DeFi interactions, reduce friction, and improve user experience, while raising important considerations around security, gas efficiency, scalability, and cross-network compatibility.
<!-- DAILY_CHECKIN_2026-03-19_END -->

# 2026-03-18
<!-- DAILY_CHECKIN_2026-03-18_START -->

Uniswap V2 stop order system consists of three main components. First, an origin chain contract (UniswapDemoToken) is a simple ERC-20 token used for testing swaps. Second, a reactive contract (UniswapDemoStopOrderReactive) listens to Uniswap V2 pool Sync events, which update reserve balances. When the exchange rate crosses a predefined threshold, the contract triggers a stop order by emitting a callback request. Third, a destination chain contract (UniswapDemoStopOrderCallback) receives this callback, verifies conditions such as caller authenticity, token balances, and price, then executes the swap via a Uniswap router and sends the resulting tokens to the user. After completion, it emits an event signaling the reactive contract to finalize the process.

The demo highlights how reactive contracts can automate trading decisions based on real-time on-chain data, enabling decentralized stop orders without manual intervention. It builds on a broader reactive network concept, where contracts respond to blockchain events across chains.

The article also suggests improvements, including dynamic subscriptions for multiple orders, better error handling and retry logic, support for various DEXs and routers, and more reliable data flow. Deployment instructions target Ethereum Sepolia and Reactive Lasna test environments.
<!-- DAILY_CHECKIN_2026-03-18_END -->

# 2026-03-17
<!-- DAILY_CHECKIN_2026-03-17_START -->


Lesson explains how to deploy a Reactive Network Demo using Remix, consisting of three smart contracts across multiple blockchains.

**Deployment Process:**

1.  **Origin Contract (BasicDemoL1Contract.sol):** Compile and deploy to any supported origin chain using Remix with MetaMask as the injected provider. Save the resulting contract address.
    
2.  **Destination Contract (BasicDemoL1Callback.sol):** Compile and deploy to a supported destination chain, sending 0.01 Ether and including a unique callback proxy address (\_callback\_sender). Copy the deployed contract address.
    
3.  **Reactive Contract (BasicDemoReactiveContract.sol):** Deploy to Reactive Lasna with specific parameters including the system contract address, origin and destination chain IDs, origin contract address, event topic, and destination contract address.
    

Each contract deployment requires MetaMask confirmation, and users should verify transaction details on relevant block explorers. The process links contracts across chains, enabling cross-chain reactive functionality.
<!-- DAILY_CHECKIN_2026-03-17_END -->

# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->



The Reactive Network Demo illustrates a cross-chain event monitoring and response system through three interconnected smart contracts deployed across multiple blockchain networks.

## Architecture Overview

The demo consists of three contracts working in concert:

1.  **Origin Contract** (BasicDemoL1Contract): Accepts Ether transfers and emits a `Received` event containing transaction details.
    
2.  **Reactive Contract** (BasicDemoReactiveContract): Deployed on Reactive Lasna, this contract subscribes to logs from the origin chain. It monitors events in real-time and executes predefined logic when conditions are met. Specifically, it checks if `topic_3` (the transaction amount) exceeds 0.01 Ether, then triggers a callback to the destination chain.
    
3.  **Destination Contract** (BasicDemoL1Callback): Receives and processes callbacks from the Reactive Network, logging transaction metadata while restricting access to authorized senders only.
    

## Key Features

The reactive contract uses a `LogRecord` struct to receive structured event data, including chain ID, contract address, topics, block information, and transaction details. Its `react()` function processes incoming logs and emits a `Callback` event when conditions are satisfied, enabling cross-chain communication.

## Deployment Process

Users deploy contracts sequentially: first the origin contract on a supported chain, then the destination contract with a 0.01 Ether transaction value, and finally the reactive contract on Reactive Lasna with proper configuration parameters.
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->




Lesson 7 introduces Reactive Contracts (RCs) designed for Uniswap V2, demonstrating how they function similarly to Ethereum smart contracts while automating stop orders based on predefined conditions.

## Key Concepts

**Contract Purpose**: The UniswapDemoStopOrderReactive contract monitors Uniswap V2 liquidity pools by tracking Sync events to detect when stop-order conditions are met, then executes callback transactions on the Ethereum blockchain.

**Core Components**:

-   **Event Declarations**: Events like Subscribed, AboveThreshold, CallbackSent, and Done log contract operations for transparency
    
-   **Contract Variables**: Constants define Uniswap's Sync event topic and stop-order contract topic, while state variables store configuration (pair address, client, token selection, coefficient, threshold)
    
-   **Constructor**: Initializes the contract with Uniswap pair details, stop-order contract reference, and client information, then subscribes to relevant events
    

## Execution Logic

The **react() function** handles two event types:

1.  **Stop-Order Events**: Verifies execution and marks operations as complete
    
2.  **Sync Events**: Decodes reserve data and checks if conditions trigger the stop order via the below\_threshold() function
    

The **below\_threshold() function** calculates whether the reserve ratio falls below the defined threshold by comparing reserve ratios (reserve1/reserve0 or reserve0/reserve1, depending on token selection) multiplied by a coefficient.

## Workflow

The contract follows a four-step lifecycle: subscribing to events, monitoring pool reserves, triggering stop orders when price thresholds are hit, and capturing completion events. This demonstrates how RCs provide an accessible way to automate trading strategies on Uniswap V2 using familiar smart contract concepts.
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->





Lesson 6  
Uniswap V2 is a decentralized exchange protocol on Ethereum that uses **liquidity pools** to enable automated token swaps without traditional order books or market makers. Each pool holds reserves of two tokens (a trading pair), managed by smart contracts following the **Constant Product Market Maker (CPMM)** model.

The core pricing mechanism is the **constant product formula**: **x × y = k**, where x and y are the reserves of the two tokens, and k remains constant (adjusted for fees). This invariant ensures that as one token is bought (reducing its reserve), its price rises relative to the other, balancing supply and demand dynamically.

Swaps occur via the pool's swap function, which:

-   Specifies desired output amounts (amount0Out/amount1Out).
    
-   Verifies sufficient liquidity and positive inputs.
    
-   Calculates input amounts from actual token balances received.
    
-   Applies a **0.3% fee** by checking an adjusted product (multiplying balances by 1000 and subtracting 3×input) to ensure **k** is preserved or increased.
    
-   Updates reserves via \_update.
    
-   Transfers output tokens.
    
-   Emits a **Swap** event and supports flash swaps via callbacks.
    

Key events for monitoring:

-   **Swap**: Logs sender, input amounts (amount0In/amount1In), output amounts (amount0Out/amount1Out), and recipient (to). Essential for tracking trades.
    
-   **Sync**: Emits updated reserves (reserve0/reserve1) after swaps, liquidity changes, or direct transfers, keeping pool state transparent.
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->






Lesson 5:  
Oracles act as essential bridges between blockchains and the real world, enabling smart contracts to access off-chain data—such as price feeds, weather reports, or event outcomes—while maintaining decentralization and trustlessness. This solves the **oracle problem**: reliably importing external information onto the blockchain without creating single points of failure or excessive trust requirements.

Decentralized oracle networks like **Chainlink** and **Band Protocol** fetch data from multiple sources, aggregate it, and often use multisig protocols to ensure consensus before submitting data on-chain, minimizing manipulation risks. Popular applications include DeFi (price-based lending/liquidations), parametric insurance (automatic payouts on verified events), and trustless online betting.

The article provides a Chainlink code example for fetching ETH/USD prices, but highlights a key limitation: traditional smart contracts are passive—they only query oracles when explicitly called by an Externally Owned Account (EOA), preventing true real-time reactivity.

**Reactive Contracts** address this by actively monitoring on-chain events (including oracle emissions) and automatically executing actions, potentially across chains. Integrating oracles with Reactive Contracts enables dynamic, real-time responses to off-chain events once they are brought on-chain, greatly expanding blockchain's utility.

**Key notes:**

-   Oracles are vital for connecting smart contracts to real-world data.
    
-   Decentralized networks (e.g., Chainlink) ensure secure, reliable data feeds.
    
-   Traditional contracts lack proactive behavior; Reactive Contracts enable event-driven automation.
    
-   Oracle + Reactive Contract synergy unlocks powerful, real-time decentralized applications.
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->







# Reactive Contracts: Dual-State Architecture and Subscriptions

**Dual-State Environment:** Each RC exists in two instances—one on the Reactive Network (a blockchain with system contracts) and one in a ReactVM (an isolated virtual machine). The Reactive Network handles user-initiated transactions and event subscriptions, while the ReactVM executes business logic when triggered by events. Detection of the execution context uses a `detectVm()` function checking for system contract code at a specific address. Modifiers (`rnOnly` and `vmOnly`) enforce which functions execute in each environment.

**Subscriptions (Lesson 4):** Subscriptions allow RCs to automatically respond to events from other contracts. The `ISubscriptionService` interface enables contracts to subscribe/unsubscribe from events using parameters like chain ID, contract address, and event topics. Subscriptions use wildcards (address(0), REACTIVE\_IGNORE) for flexible filtering, but at least one criterion must be specific. The `IReactive` interface provides the `react()` function, which processes incoming `LogRecord` data and emits callbacks to destination chains.

## Key notes

1.  **Architectural Parallelism:** The dual-state design enables high-performance, parallel processing by separating administrative functions (Reactive Network) from event-driven logic (ReactVM).
    
2.  **Context Awareness:** Developers must explicitly manage which code executes where using environment detection and modifiers, preventing conflicts between states.
    
3.  **Event-Driven Automation:** Subscriptions enable truly reactive systems where contracts automatically respond to external events without user intervention.
    
4.  **Callback-Based Cross-Chain Communication:** RCs communicate across chains through callbacks emitted from the ReactVM, enabling complex multi-chain workflows.
    
5.  **Flexibility with Constraints:** While subscriptions offer powerful filtering options, certain patterns (ranges, disjunctions, all chains/contracts) are prohibited to maintain efficiency and prevent combinatorial explosion.
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->








# Summary of Reactive Contracts Lessons 1-2

**Reactive Contracts (RCs)** represent a paradigm shift in blockchain automation. Unlike traditional smart contracts that passively wait for external transactions, RCs actively monitor blockchains for specified events and autonomously execute predefined actions in response.

## Core Concepts

The fundamental difference lies in **Inversion of Control (IoC)**. Traditional contracts require external actors—users or bots—to initiate execution. RCs flip this model: they independently decide when to execute based on detected events. This eliminates the need for centralized intermediaries holding private keys and signing transactions, enabling fully decentralized automation.

RCs operate by continuously monitoring specified blockchain addresses for events of interest—transfers, swaps, oracle updates, or any smart contract activity. When a matching event occurs, the RC's `react()` method is automatically triggered. RCs are stateful, allowing them to accumulate historical data and respond when conditions are met.

## Technical Implementation

RCs implement the `IReactive` interface, which receives event data through `LogRecord` structures containing chain ID, contract address, indexed topics, and transaction details. The RC processes this data and can emit `Callback` events to initiate transactions on destination chains, enabling cross-chain functionality.

## Practical Applications

RCs enable diverse use cases:

-   **Oracle Data Aggregation**: Combining data from multiple oracles across chains for precise, decentralized information
    
-   **UniSwap Stop Orders**: Monitoring pool prices to execute trades trustlessly
    
-   **DEX Arbitrage**: Detecting price discrepancies across pools and executing arbitrage automatically
    
-   **Liquidity Pool Rebalancing**: Automatically managing assets across multiple exchanges
    

## Key Advantage

By shifting from centralized bots to decentralized automation, RCs provide faster, more reliable, and trustless execution while maintaining full transparency—both inputs and outputs remain on-chain, creating more dynamic and responsive blockchain applications.
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->









# 1\. What Reactive Contracts Are

**Reactive Contracts (RCs)** are a new type of smart contract designed to **automatically respond to blockchain events** without requiring a user transaction to trigger them.

Traditional Ethereum smart contracts are **passive**:

-   They execute **only when a transaction calls them**.
    

Reactive Contracts are **event-driven**:

-   They **monitor blockchain events** (logs, transfers, contract calls).
    
-   When a specified event happens, they **automatically execute logic and trigger new transactions**.
    

Conceptually:

```
Traditional Ethereum Contract
User → send tx → contract executes

Reactive Contract
Event occurs → contract auto executes → performs action
```

This creates an **“If This → Then That” automation model** for blockchain systems.

* * *

# 2\. Why Reactive Contracts Exist

The main problem they address in Ethereum:

### Limitation of current smart contracts

Smart contracts **cannot trigger themselves**.

They require:

-   users
    
-   bots
    
-   keepers
    
-   off-chain automation services
    

Example:

-   Stop-loss on a DEX
    
-   Liquidation triggers
    
-   Cross-chain asset syncing
    

Currently these require **off-chain bots holding private keys**, which introduces centralization and operational risk.

Reactive Contracts aim to remove this dependency.

* * *

# 3\. The Role of Reactive Network in the Ethereum Ecosystem

Reactive Network acts as a **cross-chain automation layer for EVM ecosystems**.

Its role:

### 1️⃣ Event Monitoring

Reactive nodes subscribe to blockchain events from **Ethereum and other EVM chains**.

Examples of events:

-   token transfers
    
-   swaps
    
-   contract state changes
    
-   NFT transfers
    

* * *

### 2️⃣ Autonomous Logic Execution

When events occur:

1.  Event detected
    
2.  Contract logic executed in **ReactVM**
    
3.  New transactions triggered on destination chains
    

This allows contracts to:

-   react to Ethereum events
    
-   execute logic automatically
    
-   interact across multiple chains
    

* * *

### 3️⃣ Cross-Chain Automation Layer

Reactive Contracts can:

-   monitor events on **one chain**
    
-   execute logic on **another chain**
    

Example architecture:

```
Ethereum → emits event
        ↓
Reactive Network detects event
        ↓
Reactive Contract executes logic
        ↓
Transaction sent to another chain
```

This enables **cross-chain workflows** without custom infrastructure.

* * *

# 4\. Core Architecture

## Reactive Network

A dedicated blockchain infrastructure designed for **event-driven contract execution**.

Characteristics:

-   EVM compatible
    
-   Proof-of-Stake
    
-   specialized event monitoring nodes
    
-   designed for cross-chain automation
    

* * *

## ReactVM

The execution environment where reactive logic runs.

Important properties:

-   high-throughput parallel processing
    
-   sandboxed execution
    
-   optimized for event processing
    

* * *

## Dual Contract Deployment

Reactive Contracts deploy **two copies**:

### 1️⃣ Network Contract

-   runs on Reactive Network
    
-   handles subscriptions and interactions
    

### 2️⃣ ReactVM Contract

-   processes events
    
-   executes automated logic
    

The two environments **do not share state**, even though they use the same bytecode.

* * *

# 5\. Key Capabilities

### Autonomous execution

Contracts trigger themselves when conditions occur.

### Cross-chain interoperability

Reactive Contracts can watch **multiple EVM chains** and trigger transactions across them.

### Event subscriptions

Developers specify:

-   which chain
    
-   which contract
    
-   which event
    

to monitor.

### Trustless automation

No centralized bots required.
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
