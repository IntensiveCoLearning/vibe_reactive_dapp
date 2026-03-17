---
timezone: UTC+8
---

# SArreic

**GitHub ID:** SArreic

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-17
<!-- DAILY_CHECKIN_2026-03-17_START -->
The February 2026 EIP roundup reveals a dual focus: a surge of new proposals at the core and contract layers to enhance scalability, alongside incremental refinements to blob mechanics and user experience, reinforcing Ethereum's commitment to a rollup-centric roadmap while improving L1 efficiency.

Discussions intensified around the upcoming Hegota upgrade. **FOCIL (EIP-7805)** has been locked as the Consensus Layer headliner, enshrining censorship resistance by mandating validator-specified transaction lists. For the Execution Layer, **Frame Transactions (EIP-8141)** emerges as the leading candidate to advance account abstraction. Complementing this, Justin Drake's **Strawmap** provides a concrete execution path through seven forks until 2029, targeting Fast L1, Gigagas L1, Teragas L2, post-quantum security, and L1 privacy.

Key new Draft EIPs address foundational gaps. **EIP-8105 (Encrypted Mempool)** proposes protocol-level transaction encryption to eliminate malicious MEV. **EIP-8130** enables flexible account authentication via on-chain configuration, moving beyond static ECDSA. **EIP-8142 (Block-in-Blobs)** restructures blocks by moving execution payloads into blobs, aligning with danksharding and simplifying zkEVM proving. **EIP-8148** introduces configurable validator sweep thresholds, transforming staking rewards into programmable, routable liquidity. **EIP-8151** enhances `ecrecover` to respect key deactivation (EIP-7851), bridging a critical gap between legacy signatures and modern account security. On the networking front, **EIP-8159** standardizes Block Access List exchange (eth/71), enabling scalable execution optimizations.

Notably, **ERC-8153 (Facet-Based Diamonds)** standardizes module discovery for Diamond contracts, making complex modular architectures natively legible to the entire tooling ecosystem.

Finally, a provocative question emerges: Can AI accelerate Ethereum's roadmap? While experiments show AI rapidly prototyping future designs, the true bottleneck remains **community governance**. Protocol evolution is constrained by security validation and multi-stakeholder consensus, not code velocity. AI may accelerate application-layer experimentation, but core-layer progression will remain deliberately paced. The critical factor is whether the ecosystem can attract deeply knowledgeable participants who understand Ethereum's vision and technical interdependencies—ensuring that as AI speeds up implementation, governance retains the wisdom to navigate complexity.
<!-- DAILY_CHECKIN_2026-03-17_END -->

# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->

Today I learnt the study case of Fiet.

Fiet is a novel protocol enabling market makers to bridge actively managed off-chain liquidity (from banks, exchanges, etc.) into on-chain AMMs via synthetic assets. Traders interact with these pools normally, but settlement occurs asynchronously—when liquidity is temporarily unavailable, trades enter a queue requiring users to manually claim funds later. This creates a paradox: in volatile moments demanding speed, the experience degrades due to manual intervention.

Reactive Network resolves this by introducing **event-driven automation**. Through Reactive Smart Contracts, the system autonomously monitors the settlement queue and, upon detecting that liquidity has been restored (signaled by on-chain events), **instantly executes the final settlement transaction** without any user action or centralized keepers.

This integration transforms asynchronous settlement from a UX compromise into a competitive advantage. It demonstrates a sophisticated use case beyond simple event mirroring—involving **queue management, state aggregation, and conditional execution**. As DeFi evolves to incorporate institutional-grade, off-chain capital, automation ceases to be a "nice-to-have" and becomes core infrastructure. Reactive Network provides the native, trust-minimized layer that makes off-chain liquidity feel on-chain native.
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->


Today I read the article - Exit driven universal evolution theory - redundancy, governance, and innovation

This article introduces a unifying theoretical framework for understanding long-term evolution across scales—from biology to technology to institutions—termed **Exit-driven Evolution**. It challenges the traditional focus on selection, competition, and fitness by proposing that evolution is not driven by rewarding success, but by the **continuous elimination of structures that fail to persist**. In an uncertain world characterized by dispersed information, bounded rationality, and perpetual perturbations, errors are not anomalies but statistical certainties. Thus, a system's primary objective is not to optimize, but to **maximize survival time** under the condition of inevitable failure.

The framework redefines core concepts through the lens of **exit**—the irreversible state where a participant can no longer continue the game (e.g., death, bankruptcy, fork, migration). All evolutionary selection is ultimately realized through exit. A system's long-term viability is determined by the dynamic relationship among three fundamental variables:

-   **Redundancy (R)**: The historical buffer capacity that determines how much failure a system can withstand. It is the price paid for the right to make mistakes.
    
-   **Governance (G)**: The structural intervention that shapes or raises the cost of exit, reducing uncertainty by freezing successful paths.
    
-   **Innovation (I)**: The mechanism for generating new paths, lowering or reconstructing exit costs, and creating uncertainty.
    

These variables are bound by a conceptual survival constraint: **G × I ≤ R**. This inequality implies that any attempt to change the structure (G or I) consumes the buffer (R) needed to absorb failures. Pursuing short-term efficiency is essentially trading survival probability for immediate performance—an **efficiency trap**. Redundancy, once depleted, cannot be instantly restored by governance or innovation alone.

Key corollaries emerge from this view. System vitality is not a function of internal order, but of the **availability of genuine, low-cost exit paths**. Governance tends toward capture, suppressing innovation unless externally checked. Voice (complaint, voting) can be absorbed; **exit is the only non-absorbable negative feedback**. When the **Exit Index** approaches zero—meaning real departure is no longer feasible—a system enters a **"walking dead" state**: superficially stable but internally incapable of substantive evolution. Examples range from biological populations (where high R allows massive individual exit but lineage survival) to open protocols like Bitcoin (where low G and high R buy long-term resilience) to rigid institutions where blocked exit makes collapse a dynamical inevitability. Ultimately, the framework suggests that long-lived systems are characterized by localized failure, peaceful exit, and cheap trial-and-error. Evolution is not a journey toward optimality, but a process of avoiding extinction.
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->



This week's Ethereum ecosystem update highlights four pivotal developments spanning infrastructure, governance, institutional adoption, and wallet UX.

ENS introduced **on.eth**, a canonical on-chain registry assigning each blockchain a resolvable subdomain (e.g., `base.on.eth`) within the ENS namespace. Leveraging ERC-7828 and the Interop SDK, it returns verifiable metadata including chain IDs and interoperable addresses, replacing fragmented off-chain mappings. This transforms ENS into a multi-chain naming layer, enabling human-readable cross-chain identifiers in the `domain.eth@chain` format.

Across Protocol proposed a controversial governance shift from a DAO to a US C-corporation, AcrossCo. The proposal allows ACX holders to swap tokens for equity or sell at $0.04375 per token in USDC, aiming to remove structural barriers in institutional dealings. The market responded with an 81% token surge, highlighting the tension between decentralized purity and commercial pragmatism.

Mastercard launched a Crypto Partner Program with 85+ members including Binance, Circle, and PayPal, focused on integrating stablecoin payments into its global merchant network. The initiative targets cross-border B2B settlements using regulated stablecoins like USDC and PYUSD, bridging crypto-native issuers with traditional fiat distribution rails.

MetaMask integrated Uniswap as its primary swap provider, routing trades through Uniswap v2/v3/v4 and UniswapX across 16+ chains. This embeds Uniswap's liquidity directly into the wallet interface, eliminating the need for external aggregators and positioning Uniswap's routing layer as core infrastructure for leading self-custody platforms.
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->




Today I learnt the ERC-8183.

ERC-8183 is not merely a payment standard; it is a foundational primitive for **escrowed conditional settlement** in the emerging agent economy. It formalizes the binding of tasks, deliverables, and release conditions through a simple yet pivotal state machine involving three roles: Client (funds the escrow), Provider (submits results), and Evaluator (adjudicates completion). The workflow—Open → Funded → Submitted → Completed/Rejected/Expired—ensures that funds are locked first, and only a designated Evaluator can trigger the final settlement, determining whether payment is released to the Provider or refunded to the Client.

Its significance lies in shifting the focus from mere token transfer to the harder problem of **settlement**. While discussions often center on whether agents can think or pay gas, ERC-8183 addresses the core of commercial closure: What constitutes "done"? Who judges it? What happens if it fails? It fills the void where real-world economic friction lives.

Crucially, ERC-8183 does not operate in isolation. It forms a collaborative stack with ERC-8004 (trust and verification) and x402 (streamlined payment flows)—the former providing attestations to constrain malicious evaluators, the latter enabling off-chain payment intents. However, the standard is not without limitations. It currently models **one-off jobs rather than complex contracts**, lacking native support for multi-phase deliveries, stateful workflows, or dispute arbitration. The Evaluator's power remains a central point of trust, often requiring supplemental reputation or staking layers for high-value tasks.

Despite these debates, ERC-8183's true value is its **grounding in market reality**. It emerged not from theoretical ambition but from production use cases, with active teams in the Ethereum Magicians community seeking to feed their operational experience back into the standard. It exposes the hard questions—Who decides fairness? How do we handle rejection?—that any viable agent commerce layer must eventually answer. ERC-8183 may still be a primitive in its early shaping, but it is a primitive aimed at the right problem: turning blockchain-based tasks from isolated transactions into enforceable, conditional economic relationships.
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->





Today I learnt the relationships and connections between polymarkets and reactive network.

Prediction markets, long predating blockchain, have proven their core thesis: markets aggregate dispersed knowledge more effectively than polls or pundits. However, their traditional form as corporate-run platforms introduced a structural dependency—participants must trust the operator to fairly adjudicate outcomes and process payouts. Blockchain fundamentally transforms this model by encoding markets into self-executing protocols. Trust shifts from a counterparty to transparent, immutable code, enabling permissionless participation where anyone can create markets or provide liquidity without gatekeepers. Crucially, on-chain markets transcend mere decentralization through **composability**; they become infrastructure components whose price signals can be integrated into DeFi protocols, DAO treasuries, or insurance mechanisms. The technical stack—market contracts issuing result tokens (e.g., YES/NO), AMMs for continuous liquidity, oracles for deterministic settlement—forms a pipeline where beliefs flow in and settlements flow out.

Yet, even this evolved model operates as a passive observer. Markets measure sentiment and generate signals but stop there; they cannot autonomously act upon the probabilities they discover. This is where **Reactive Network** introduces a paradigm shift. As an EVM-compatible execution layer designed for **Reactive Smart Contracts**, it inverts the traditional control flow. These contracts are not user-called but triggered by cross-chain event logs. They listen for specific conditions across multiple chains, autonomously execute Solidity logic, and can dispatch transactions to destination chains. This transforms prediction markets from informational endpoints into active, conditional actuators. A market signal—like YES token probability exceeding a threshold—can now automatically trigger a hedge on a lending protocol or rebalance a cross-chain portfolio. Reactive Network thus bridges the gap between blockchain's power to observe and its potential to act, elevating prediction markets from a layer of applications to a responsive layer of the internet's financial infrastructure.
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->






Ivan Ivanitskiy, Head of Developer Relations at Reactive Network, provided key insights into the technology's capabilities and limitations during a workshop. He clarified that while Reactive Smart Contracts are inherently public—meaning any arbitrage strategy coded within them can be reverse-engineered from the bytecode—the network is best suited for non-high-frequency strategies due to an inherent ~10-second latency in cross-chain execution. A major advantage highlighted is enhanced security, particularly when integrated with AI agents; instead of entrusting an agent with a private key, the AI only triggers a predefined workflow, with the actual fund movement logic secured immutably on-chain. Regarding cross-chain operations, Ivan acknowledged the impossibility of true atomicity across different chains but demonstrated how the network handles failures through application-level retry mechanisms, as implemented in their bridge. He also confirmed that while the core focus remains on EVM chains, Solana support is on the roadmap, initially targeting specific applications via community-developed connectors. Finally, he noted that while trading bots can be built on Reactive, the technology's true value lies in embedding secure, automated features like stop-loss directly into DApps, with developer grants flexibly awarded based on a project's specific needs and alignment.
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->







Today I have learnt basic information about Reactive Network.

Reactive Network, developed by PARSIQ, is an innovative blockchain infrastructure that introduces Reactive Smart Contracts (RSCs) to enable event-driven, cross-chain automation on Ethereum and other networks. Unlike traditional passive contracts that wait for direct calls, RSCs are deployed on the Reactive Network and programmed to autonomously listen for specific event logs emitted by contracts on source chains like Ethereum. Upon detecting a matching event, the contract’s logic is executed within a parallelized execution environment called ReactVM, which can then trigger subsequent actions—such as sending transactions or cross-chain messages—to interact with protocols on destination chains. This architecture effectively creates a decentralized "if this, then that" (IFTTT) layer for Web3, eliminating the need for centralized bots or off-chain infrastructure. By shifting complex, event-driven computations off expensive Layer 1 networks and enhancing interoperability, Reactive Network offers developers a cost-efficient, EVM-compatible platform for building truly autonomous and composable decentralized applications.
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
