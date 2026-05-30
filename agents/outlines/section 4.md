## 4. Game Theory for Strategic AI: From Scalar Scores to Relational Robustness

### Section thesis

> Static ML asks whether a model scores well. Strategic AI asks whether a policy remains robust when other agents adapt.

**中心问题：**

> When AI systems act strategically with other adaptive agents, what should be evaluated, optimized, and governed?

**主结构：** evaluation → equilibrium-seeking training → deployment governance

**贯穿三节的统一动作（the spine）：** 每一节都把一个**标量**替换成一个**博弈论对象**——benchmark score → exploitability / equilibrium；fixed scalar reward → equilibrium selection；source-code inspection → equilibrium-level audit。

---

### 4.1 Strategic Evaluation: Competence Is Opponent-Relative

**Leading question:**

> If strategic competence depends on opponents, protocols, and strategy spaces, can it still be evaluated as a scalar model score?

**功能：** 将评价单位从 isolated model score 转为 policy-under-interaction。本节的主线是 **opponent-relative competence**；但在能对 competence 做博弈论评价之前，先要确认 agent 有一个稳定的 utility 可供"出招"——这一前置条件由 utility-theory 文献负责审计。

**主要叙事：**

- **Step 1 — Static benchmark fails under adaptive opponents.** 传统 accuracy / win-rate 在对手自适应时失效。最权威的 RL 侧诊断来自 **Balduzzi et al. (2018, NeurIPS)** "Re-evaluating Evaluation"：benchmark suite 与 baseline 的增殖稀释了评测、cherry-picking 风险上升 → 需要 population-level 的评价单位。
    
- **Step 2 — opponent-relative competence：RL / EGTA 传统提供"词汇库"。** 这条线**早于、且奠基了**今天的 LLM 评测词汇，按概念顺序：
    
    - **Wellman (2006, AAAI)** EGTA — 用 simulation/data 估计 empirical game 再以 solution concept 归纳评测，是 population-level evaluation 的命名时刻；
    - **Lanctot et al. (2017, NeurIPS)** PSRO + joint-policy correlation — 形式化"policy 对训练对手 overfit、execution 不泛化"，即 competence 的 profile-依赖性；
    - **Balduzzi et al. (2018)** Nash averaging — 以 meta-game 的 max-entropy Nash 加权，避免被 redundant tasks / weak baselines 误导；
    - **Omidshafiei et al. (2019, _Scientific Reports_)** α-Rank — 显式追踪 non-transitivity / sink 的 population ranking（_caveat：Yang et al. 2020 ICLR 指出 scalability 限制，提到时一并标注_）；
    - **Balduzzi et al. (2019, ICML)** — 区分 transitive vs. non-transitive 结构，在 cycle 中"提升强度但针对谁不明确"，几乎是本节论点的原话；
    - **Czarnecki et al. (2020, NeurIPS)** Spinning Tops — 把 non-transitivity 从玩具例子提升为 real-world games 的普遍几何。
    - **工程证据**：AlphaStar (Vinyals et al. 2019, **_Nature_**) 以 least-exploitable agents 的 Nash 分布为输出；DeepStack (Moravčík et al. 2017, **_Science_**) / Libratus / Pluribus 以 **exploitability (LBR)** 而非 win-rate 为核心指标 → opponent-relative robustness 已是已部署 RL 系统的主流范式。
- **Step 3 — 同一词汇在 LLM 上的最新一章。** 把上述词汇迁移到 LLM strategic behavior，可见其 profile-依赖：**GTBench**（战略推理随 game structure 变化）；**Akata et al. (2025, _Nature Human Behaviour_ 9(7):1380–1390)** repeated games（cooperation/coordination 受 prompt、对手、交互调制：GPT-4 在 PD 中 unforgiving，在 Battle of the Sexes 中无法与轮换式对手协调）。→ LLM benchmark 不是孤岛，而是 EGTA / α-Rank 传统的下游应用。
    
- **Step 4 — 前置条件：preference coherence 是博弈论评价的地基。** 上面三步默认 agent 有一个 well-defined utility 可供出招；但博弈论的全部装置（equilibrium、best response、payoff）都预设了这一点。若 agent 的偏好都不 coherent、还随 framing 漂移，则 exploitability / ranking 全建在沙子上。utility-theory 文献正是审计这一前置条件：
    
    - **Ross, Kim & Lo (2024, COLM)** "LLM economicus?" — 以 utility theory 为范式测 inequity aversion / risk-loss aversion / time discounting；发现当前 LLM 既非完全 human-like 也非完全 economicus，且**难以跨设置维持一致经济行为**（论文内含一个 _competence test_——只分析通过该 test 的模型，本身就承认"先验偏好一致性、后验策略能力"的分离）；
    - **Mazeika et al. (2025, _NeurIPS_ Spotlight)** "Utility Engineering" — Thurstonian random-utility 模型显示现代 LLM 的 preference 满足 transitivity/completeness，且 coherence **随模型规模上升**；
    - _（脚注：Mei et al. 2024 _PNAS_ 行为博弈 Turing test；Horton 2023 NBER；Aher et al. 2023 ICML。）_
    
    > Before competence can be ranked game-theoretically, the agent must even _have_ a stable utility to play from. Step 4 audits that precondition; Steps 2–3 audit play given it.
    

**收束句:**

> The first role of game theory is diagnostic: it asks what it means to evaluate competence when performance is opponent-relative — and, prior to that, whether the agent has a coherent utility for any such evaluation to be well-posed.

---

### 4.2 Training and Tuning: Alignment as Equilibrium Selection

**Leading question:**

> When the alignment signal cannot be reduced to a fixed scalar objective — because it is defined only _against an adversary_, only as a _non-transitive preference_, or only as _what a bounded verifier can be brought to accept_ — what should training optimize instead?

_(三个从句对应 Strand 2 / 1 / 3，顺序即下文展开顺序。)_

**内部叙事（开篇框架）:** 统一答案——把"最大化一个固定标量目标"替换成"**选择一个博弈的均衡**"。两点先交代清楚：

- **self-play 是三条 strand 共享的计算机制，不是分类轴。** 其"自对弈→Nash"的理论根基是 fictitious play 与其深度化版本。_(Brown 1951；Robinson 1951 证二人零和收敛；Heinrich, Lanctot, Silver 2015 FSP；Heinrich & Silver 2016 Neural FSP。)_
- **三条 strand 由"均衡认证什么"区分**：鲁棒性(2) / 偏好(1) / 真值(3)。
- 展开顺序 **2 → 1 → 3**：GAN 是"训练即均衡求解"的历史母版，先讲它既确立模板、又暴露困难，Strand 1 与 3 都继承之。

**Strand 2 · Adversarial games（母版 + 动力学诊断）**

- **模板**：GAN 作为 zero-sum minimax，Nash = 判别器无法区分真假数据 → Goodfellow et al. 2014。
- **alignment 实例**：鲁棒训练 = saddle-point (Madry et al. 2018)；red-teaming = attacker–defender game (Perez et al. 2022)。
- **困难（经验事实）**：一阶方法常不收敛，GAN 博弈甚至可能没有 local Nash equilibrium (Farnia & Ozdaglar 2020)。
- **【Boxed supplement】差分博弈力学**：将 game Jacobian 分解为对称(potential)与反对称(Hamiltonian)分量，据此提出 SGA 寻找 stable fixed points → Balduzzi et al. 2018 (ICML) / 2019 (JMLR, _Differentiable Game Mechanics_)。**定位：不是 Nash solver、不是核心 anchor，而是"为何 equilibrium-seeking 训练旋转/不收敛"的动力学透镜；区分 stable fixed point 与 local Nash。** 相邻谱系：Mescheder et al. 2017；Daskalakis et al. 2017。

**Strand 1 · Preference games（模板用于"偏好不可标量"）**

- **不够用**：Bradley–Terry 强制 transitivity；真实/聚合偏好可 intransitive、cyclic → Bradley & Terry 1952；May 1954；Tversky 1969；前身 Dudík et al. 2015。
- **概念锚点 NLHF**：把 pairwise preference model 当作对称双人博弈，解为 regularized Nash equilibrium，用 Nash-MD 末次迭代收敛 → Munos et al. 2024 (ICML)。一句话：RLHF → equilibrium selection。
- **可扩展实现**（主讲 INPO）：INPO（no-regret self-play 逼近 Nash，单一损失，Zhang et al. ICLR 2025）；DNO（iterative-DPO，Rosset et al. 2024）；SPPO（constant-sum, Hedge，Wu et al. 2024）；**SPO / Minimaximalist**（理论母版，基于 social choice 的 Minimax Winner，Swamy et al. ICML 2024）。
- **接回 box（收敛动力学）**：Mirror Descent 仅保证 average-iterate 收敛，last-iterate 绕 Nash 振荡 (Mertikopoulos et al.)；last-iterate 方案如 MPO、EGPO (extragradient)。
- **PSRO 桥接**（一句话）：robust policy = 对策略 population 的稳健性搜索 → Lanctot et al. 2017。
- _(脚注：distribution-matching self-play — SPIN, Chen et al. 2024。)_

**Strand 3 · Interactive-proof games（模板用于"裁判弱于模型"）**

- **复杂度根基**：零和 debate game 上 self-play，多项式时间裁判下最优对弈可达 PSPACE，与 IP=PSPACE 同构 → Irving, Christiano, Amodei 2018。
- **两个 canonical 协议**：Debate（对称，两 prover + judge）；**Prover-Verifier Games**（非对称，贴 weak-to-strong；只有部分变体可证地具理想均衡，sequential 形式会失效，Anil et al. 2021；规模化：OpenAI legibility, Kirchner et al. 2024）。
- **障碍与精化**：obfuscated arguments（不诚实 prover 可造需指数时间驳倒的论证，Barnes & Christiano 2020）；doubly-efficient debate（诚实 prover 总有多项式时间获胜策略，Brown-Cohen, Irving, Piliouras 2023）。
- **实证落地**：更强 expert 辩论、较弱 non-expert 裁判设定下，debate 一致提升准确率（76%/88% vs 基线 48%/60%），对抗式协议优于非对抗基线（Khan et al., ICML 2024 Best Paper）。
- **收束定位**：监督信号不再可靠时，用 interactive-proof 式博弈把均衡设计成"认证真值"的机制。

---

### 4.3 Deployment Governance: Local Alignment Is Not System Alignment

**Leading question:**

> When individually aligned AI agents interact through markets, institutions, and delegation chains, what system-level equilibria do they induce?

**功能：** 从 training 转到 deployment；核心不是 "AI 是否 aligned"，而是局部 aligned 的 agent 在互动中诱导的 system-level equilibria 及其可审计性。切口收窄为一条 game-theoretic 主线：**no-regret learning 的经济学 + calibrated-regret auditing**。

**主要叙事（保留 Delegation → Calvano → Hartline 三步骨架，重心后移到 Step 3）：**

- **Step 1 — Delegation games：为 "local ≠ system" 提供概念分解。** **Sourbut, Hammond & Wood (2024, IJCAI)** 把 multi-principal multi-agent 形式化为 delegation games，将 failure modes 正交分解为 (control × cooperation) × (alignment × capabilities)，证明任一单 measure 不充分、四者一起充分，并给 principal welfare loss 上界。→ 确立"为何 per-agent property 不可组合"，定位为**概念 decomposition / framework**，非本节主理论支撑。
    
- **Step 2 — Algorithmic pricing 作为 adaptive market failure（现象引入 + 关键限定）。** **Calvano et al. (2020, _AER_ 110(10):3267–3297)**：Bertrand 寡头中 Q-learning agents 无通讯下学到 supra-competitive prices，由 punishment-then-return 策略支撑——但这是 _simulation_ 结果（论文自承 Q-learning 在 repeated games 无一般收敛保证）。 **限定**：Asker–Fershtman–Pakes (2022 AEA P&P / 2024 JEMS) 表明"collusion"很大程度是 _asynchronous learning protocol_ 的产物，synchronous / 最小经济结构下基本消失；den Boer–Meylahn–Schinkel (2022) 论证其脆弱、可被 optimizing 对手剥削；Cartea et al. (2025) 给出 Folk-Theorem 式结果，collusion 只是可达 payoff 族中的一种而非必然。 → 治理对象不应是"算法是不是 Q-learning"，而应是 _观察到的 strategic behavior 是否满足某个 equilibrium-level 性质_。这把治理从 source-code inspection 推向 behavioral audit。
    
- **Step 3 — Calibrated-regret audit 作为治理回应（本节理论 climax）。** **关键的"for AI"桥**：经典 AGT 早于 AI agents，但这些 AI learning agents _正是_ 那些定理所量化的 no-regret learners——这正是"game theory **for AI**"在本节落地的方式。
    
    - _理论基石（使"观察到的行为"成为可审计对象）_：no-internal-regret learning 收敛到 correlated equilibrium（**Foster–Vohra 1997；Hart–Mas-Colell 2000, _Econometrica_**, regret-matching；**Blum–Mansour 2007, _JMLR_**, external→swap 归约）；smooth games 中 no-regret learners 的 social welfare 由 price of anarchy 自动保证（**Roughgarden 2015, _JACM_** / STOC 2009, Kalai Prize；**Syrgkanis–Tardos 2013, STOC**, composable mechanisms）；现代算法以接近最优速率收敛（**Syrgkanis–Agarwal–Luo–Schapire 2015, _NeurIPS_ Best Paper**；Anagnostides et al. 2022, STOC，swap regret polylog(T)；Dagan et al. 2024, STOC）。
    - _审计机制_：**Hartline, Long & Zhang (2024, CSLAW)** 与 **Hartline, Wang & Zhang (2025, CSLAW)** 不查源码、而测 seller 是否满足 vanishing (pessimistic) calibrated regret，证明 (i) 任何 no-regret 算法可 retrofit 通过审计；(ii) 满足者不能被操纵进入 supra-competitive 价；(iii) 给 sample complexity bound。**Hartline (2026 综述)** 把 no-swap-regret 提为"算法智能体的理性公理"，明确架起 classical AGT → AI agents 的桥。_（措辞：作为 building on classical no-regret theory 的 **recent program**，而非 established field；主引 CSLAW 两篇 primary papers，2026 综述作 programmatic 引用。）_
    - _（若空间充裕，可延伸 learning in mechanisms：Braverman et al. 2018 "Selling to a no-regret buyer," EC；Banchio–Skrzypacz 2022, EC, first-price bid suppression。）_

**收束句:**

> The third role of game theory is institutional: it asks what system-level equilibria are induced when individually aligned agents interact, and how those equilibria can be audited, reshaped, or governed.

---

