<!-- solve-problem:b443e093-2af0-468f-af71-911b03020b3b -->
# Optimal MEV-auction closing rules

A [Tangle Solve problem](https://solve.tangle.tools/problems/b443e093-2af0-468f-af71-911b03020b3b).

## What is the problem?

MEV-Boost-style auctions let Ethereum block proposers sell the right to build their block to the highest-bidding relayer/builder. The census question asks for an **optimal closing rule** for this auction: how should the protocol set (i) the moment bidding closes relative to the proposer's slot deadline, (ii) whether/when bidders may update bids as private information (e.g., newly observed mempool transactions, "order flow") arrives, and (iii) how to penalize or mitigate strategic withholding of blocks or bids?

A reasonable formalization: a finite-horizon auction with deadline *T*; bidders *i* receive private signals θᵢ(t) following a stochastic process (e.g., Poisson arrivals of MEV opportunities); each submits/updates a bid bᵢ(t) ≤ v(θᵢ(t)); the winner pays a function of the bid profile; the proposer receives expected revenue *R*; latency advantages give some bidders earlier signal access (asymmetric information arrival). The design goal is a closing rule (T, update policy) optimizing a welfare objective combining proposer revenue *R*, a fairness/concentration measure over winners (e.g., long-run Herfindahl index of winner shares), and proposer/utility losses from strategic withholding (winner declining to reveal a block when realized value falls). Ambiguity: the census does not specify the objective's precise weighting or the information structure — the solver must fix these explicitly.

## Why does it matters?

Closing rules directly determine how much advantage accrues to builders with the lowest latency — the "privileged timing" that concentrates block building among a few firms (e.g., two dominant builders on Ethereum today). A principled closing rule would let protocol designers trade proposer revenue against decentralization quantitatively rather than ad hoc. It also informs current debates on commitment order auctions (Su, Miller 2022), order-flow auctions, and Ethereum protocol changes like ePBS (enshrined proposer-builder separation).

## What's already known?

- Empirical game-theoretic studies (2023–2024) of MEV bidding: dynamic bidding wars, last-moment bid sniping, builder concentration — Wu et al. (2024) models strategic bidding in on-chain auctions (verify exact content of arXiv:2312.14510).
- Flash Boys 2.0 (Daian et al. 2019) established the latency-driven front-running problem; Roughgarden (2020/2021) formalized transaction fee mechanism design axioms (UIC, MIC, OCA-proofness).
- Order-flow auctions: private order flow gives exclusive builders informational advantage (Roth et al. on centralization pressures; Sокаl/Heilman-type latency analyses).
- Classic auction theory: dynamic auctions with arriving information (Pandora's problem, sequential auctions), and the literature on "sniping" in hard-close vs. soft-close auctions (eBay studies; Roth & Ockenfels 2002).
- Not yet done: a normative characterization of the optimal *deadline/update* rule under asymmetric information arrival with a concentration-vs-revenue objective. No mechanism is known to be optimal for this tripartite tradeoff.

## What would count as solving it?

1. **Positive result:** a closing rule (deadline T*, update/withdrawal policy) with a proof that it maximizes, or obtains a constant-factor approximation to, a stated objective R − λ·(concentration) − μ·(withholding loss) in a specified stochastic signal-arrival model.
2. **Impossibility/lower bound:** a proof that no closing rule achieves positive revenue while bounding winner concentration below a threshold when information arrival is asymmetric — establishing a fundamental tradeoff.
3. **Empirical/theory-informed result:** a calibrated simulation or game-theoretic equilibrium computation (empirical game theory over realistic builder latency distributions) demonstrating a rule that Pareto-dominates the status quo MEV-Boost close-at-slot-deadline rule.

## How to start

1. **Read Roth & Ockenfels (2002) on hard vs. soft closes** — sniping under hard deadlines is the closest mechanism-design analogue; understand why soft (amazonic) closes reduce timing advantage, and why MEV-Boost currently uses a hard close.
2. **Model the simplest case first:** two bidders, one low-latency with Poisson(λ₁) signal arrivals and one with λ₂ < λ₁; first-price sealed-bid with deadline T. Compute equilibrium and winner-share HHI as functions of T; this already yields the core tradeoff curve.
3. **Add withholding:** let the winner observe a final signal after bidding and allow block refusal (as in MEV-Boost today — proposers bear slashing/missed-slot risk). Characterize when longer bid windows reduce withholding.
4. **Main obstacle:** the objective is a mixture of revenue (auction theory standard) and concentration (a distributional property across slots) — no off-the-shelf optimal mechanism theorem covers it; you may need to define a new objective and re-derive incentive compatibility.

## Key references

- Daian, P., Goldfeder, S., Kell, T., Li, Y., Zhao, X., Bentov, I., Breidenbach, L., Juels, A. (2019). "Flash Boys 2.0: Frontrunning, Transaction Reordering, and Consensus Instability in Decentralized Exchanges." arXiv:1904.05234. — Empirically demonstrates bot latency races in DEX arbitrage; foundational MEV paper. https://arxiv.org/abs/1904.05234
- Roughgarden, T. (2020). "Transaction Fee Mechanism Design." arXiv:2106.01340. — Axiomatizes fee mechanism design (UIC, MIC, OCA-proofness); the framework to imitate for closing-rule axioms. https://arxiv.org/abs/2106.01340
- Roth, A. E., Ockenfels, A. (2002). "Last-Minute Bidding and the Rules for Second Price Auctions." AER 92(4). — Shows hard vs. soft closing rules change sniping equilibria; direct precedent for closing-rule design. https://www.aeaweb.org/articles?id=10.1257/00028280260344632
- Wu et al. (2024). "Strategic Bidding Wars in On-chain Auctions." arXiv:2312.14510. — Per the census, game-theoretic/empirical study of strategic bidding in on-chain auctions; primary cited key work — **verify existence and content**, as this identifier could not be independently confirmed.
- Su, L., Miller, T. (2022). "Preference-Driven Order Flow Auctions." arXiv:2211.09399. — Proposes order-flow auctions letting users express ordering preferences; relevant downstream application. https://arxiv.org/abs/2211.09399

## Warnings

- The Wu et al. (2024) reference comes solely from the census record and may not resolve; do not rely on it existing before verifying.
- The problem statement is underspecified: no canonical objective exists. Explicitly state your objective and information-arrival model early; referees will judge the modeling choice.
- Don't conflate builder concentration with proposer centralization — the closing rule affects builder competition, a different market layer.
- A common dead end: pure revenue maximization ignores latency asymmetry entirely (standard results apply) — the problem is only interesting with asymmetric signal arrival.
- Soft-close (extending deadline on late bids) interacts with consensus timing: block proposals have hard slot deadlines, so any rule must fit within one slot (~12s); a mechanism ignoring this is not implementable.

---
*`BCGT-000299` · flagged for review*

## Acceptance guidance

1. **Positive result:** a closing rule (deadline T*, update/withdrawal policy) with a proof that it maximizes, or obtains a constant-factor approximation to, a stated objective R − λ·(concentration) − μ·(withholding loss) in a specified stochastic signal-arrival model.
2. **Impossibility/lower bound:** a proof that no closing rule achieves positive revenue while bounding winner concentration below a threshold when information arrival is asymmetric — establishing a fundamental tradeoff.
3. **Empirical/theory-informed result:** a calibrated simulation or game-theoretic equilibrium computation (empirical game theory over realistic builder latency distributions) demonstrating a rule that Pareto-dominates the status quo MEV-Boost close-at-slot-deadline rule.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/b443e093-2af0-468f-af71-911b03020b3b?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000299.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000299)
