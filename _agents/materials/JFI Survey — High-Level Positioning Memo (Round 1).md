# JFI Survey — High-Level Positioning Memo (Round 1)

**AI for Game Theory and Game Theory for AI**

Jianwei Huang
June 1, 2026

---

*To the co-authors*

Xiaohan has initialized our invited survey for the **Journal of the Franklin Institute Bicentennial Special Issue** and asked for feedback at the skeleton level — narrative structure and choice of topics — rather than sentence-level polish. This memo gives enough context to engage even if you have not yet read the draft, then sets out where I believe the paper must aim. Your comments are warmly invited (see the final section).

**Repository:** github.com/Crescenx/JFI-Survey · Overleaf link in Xiaohan's email of May 31.

---

## What the paper is

A **position paper, not a catalog**, organized around one idea: artificial intelligence and game theory have each matured to the point where each now needs the other, and that need can be read in two reciprocal directions.

- **Part I — AI for Game Theory.** AI makes strategic specifications executable. Three subdirections: constructive equilibrium, strategic inference, and mechanism design.
- **Part II — Game Theory for AI.** Game theory supplies the relational properties that must be evaluated, optimized, and governed once AI systems interact. Three subdirections: strategic evaluation, training and alignment, and deployment governance.

The unifying claim is a single shared problem underneath both directions — a **specification ↔ construction gap**: game theory can specify strategic conditions but cannot construct them at scale; AI can construct at scale but cannot say what its behavior should satisfy. The current draft (intro + recap + the two parts + a rough conclusion) compiles and reads coherently. The framing is already strong. What follows is how to make it last.

---

## The vision: what this paper should become

An invited bicentennial paper should not summarize a field — it should **define** one. The draft has assembled the right material and a genuinely good bidirectional frame. Converting it from a competent survey into a paper still cited in fifty years requires not more content but a few sharper claims. Five positioning moves, none of which expand scope:

### 1. Name the real fault line: on-path vs. off-path

The draft's frame is "counterfactual robustness." Correct, but too soft to carry the paper. The precise and durable tension underneath it is this: **every equilibrium concept is a statement about off-path behavior, while every learning system sees only on-path data.** Equilibrium quantifies over the deviation not taken, the type not reported, the opponent not faced; data is only whatever was actually played. This one divide generates all six subdirections — counterfactual regret manufactures values for unvisited information sets; ex-post regret in learned auctions is an off-path object; exploitability is a maximal off-path deviation; debate concerns arguments a dishonest prover could make. Make **on-path/off-path** the recurring technical refrain. It is more precise than "robustness" and it is the genuine engine of the paper.

### 2. The co-evolution loop has three nodes, not two — and the third is the paper's real discovery

The current co-evolution claim is a two-node loop, specification ↔ construction, which reads as a slogan. The draft's strongest evidence points to a missing third node: **verification**. The frontier work in every subdirection shares one feature — it produces a checkable object: machine-checkable equilibrium proofs, exactly strategy-proof mechanisms with post-hoc certification, debate protocols with a guaranteed efficient honest strategy, calibrated-regret audits that are testable from behavior alone. The field is converging on a new kind of object — the **checkable strategic certificate**, neither a closed-form proof nor a black-box output. Name it. The loop becomes:

> specification → construction → verification → richer specification

This upgrade turns a slogan into a research program and is, in my view, the paper's strongest bid for lasting impact.

### 3. The LLM agent is where both directions collapse — the longevity hedge

Large language models currently appear in four scattered places. The deep observation, which almost no existing survey states cleanly, is that **the LLM agent is the first entity that is simultaneously a constructor of strategic specifications and a strategic subject of them**: it can solve a game symbolically, serve as a behavior generator, write a mechanism, be evaluated as a player, be aligned by debate, and collude in a market. It is the one object appearing in all six cells, and it dissolves the very boundary the paper is built on. Stating this once, with force — likely in the conclusion — is what will make the paper read as prescient a decade from now rather than dated. No new section is required; it is a synthesis of material already present.

### 4. Name the fourth turn of game theory's binding question — boldly

The recap traces three historical turns: existence (Nash) → design (mechanism design) → computation (algorithmic game theory). The paper promises to speak to "the future of game theory itself," then closes softly. We should commit to the claim: **the fourth turn moves the binding question from "can the equilibrium be computed?" to "can the strategic guarantee be certified inside a deployed, learning system?"** Game theory thereby acquires an engineering-and-verification face alongside its analytic one. That is the one sentence the paper should be remembered for; it belongs in the abstract, the introduction, and the final paragraph.

### 5. Defend the flank: do not let "robustness" flatten equilibrium

A positioning risk with a sophisticated JFI readership. "Counterfactual robustness" invites the misreading that game theory reduces to worst-case or robust optimization. But the field's distinctive content is the fixed point — the mutual consistency of beliefs and best responses — which is strictly more than one-sided minimax robustness. A single sharp sentence early (*equilibrium is robustness that must hold reciprocally and simultaneously across agents, not adversarially against nature*) inoculates the paper against the strongest objection a serious game theorist will raise.

---

## On the structure and topic choices (skeleton-level)

Answering Xiaohan's specific questions:

- **Is the narrative skeleton appropriate for a survey?** Yes. The five-section spine and the bidirectional organization are right. Organizing by problem rather than by community is the correct choice and should be defended explicitly in the introduction.

- **Are the three subdirections under each side well chosen?** Yes, with two notes. (a) The deliberate exclusion of generic return-maximizing multi-agent RL is defensible but must be stated in the text — readers will otherwise wonder at its near-absence. (b) Deployment governance (4.3) is currently the thinnest subdirection; as the culmination of Part II it should either be broadened or have its calibrated-regret core developed more deeply.

- **One cross-cut to add:** mechanism design (Part I) and deployment governance (Part II) share a single problem — designing rules so that self-interested AI agents behave well. Draw this connection explicitly even if the sections stay where they are.

---

## Invitation to co-authors

I would welcome any reactions you have to the positioning above — whether on the central thesis, the framing of game theory's next turn, or the coverage in your own areas. Please feel free to comment directly in this document, in Overleaf, or via GitHub, whichever is most convenient, and of course only as your time allows. I am happy to convene a short call once everyone has had a chance to look.



— *Jianwei*