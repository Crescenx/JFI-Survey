# Section 3.1 构造性均衡：放松、近似与转化 Nash 证书

_(Constructive Equilibrium: Relaxing, Approximating, and Transforming the Nash Certificate)_

**主线**：均衡的难点不在搜索空间大，而在它要求一份全局的 no-profitable-deviation 证书。本节把现代 AI 方法读成一条围绕该证书的迂回谱系——保持、残差化、时间松弛、概念替换、空间近似、状态转化+在线、最后重新证明。每一节顶上标出它走的是哪种迂回（转化/松弛/近似），**以及 AI 载体所在层（参数化对象 / oracle / 引擎，或标明为 pre-AI 前置与基线）**，并只挂 1–2 篇已核实的文献。判别底线：**入选方法的目标函数或终止条件本身就是把 Nash gap / exploitability / regret 压向零，或直接产出并证明均衡**；均衡仅作副产物涌现的方法不入正文。

---

### 3.1.1 The Constructive Gap（提出问题）

**角色：pre-AI 前置（问题设定 / 复杂性分区）。** 本节不含 AI，也不应含——它为整条迂回谱系设定那份"要被绕过的证书"。

存在性是免费的（Nash 1951 由不动点定理保证），难的是构造：一般和博弈中找精确 Nash 是 PPAD-complete，双人零和则经 LP / no-regret 对偶变得可解。这一分区制支配下游一切。

> _Key sentence:_ Existence is free; what is hard—and regime-dependent—is constructing the certificate. The rest of this section asks, for each method: which detour around that certificate does it take?

参考：Daskalakis, Goldberg & Papadimitriou, _The Complexity of Computing a Nash Equilibrium_, 2009。

### 3.1.2 转化（不松弛）：经典数值求解器

**角色：对照基线（pre-AI，零神经网络）。** 大规模计算 / 梯度下降 / 参数化三轴全不沾，正是后文一切 AI 迂回的参照零点。

最保守一步：不削弱证书，把它改写成代数 / 互补 / LP 系统（support enumeration、Lemke–Howson、homotopy）。它不"绕过"难题而是付全价，因此只在显式、有限、可控规模的博弈上可行——是后文所有迂回的对照基线。

> _Key sentence:_ Classical solvers transform the certificate without relaxing it; their limit is not weakness but the demand for an explicit, finite, fully specified game.

参考：Lemke & Howson, 1964；（工具）Gambit。

### 3.1.3 转化（残差化）：可微 / 正则化方法

**AI 载体：参数化策略（policy gradient + 神经网络），大规模 / 梯度 / 参数化三轴齐备。** 本节最纯粹的当代 AI 样板。

把证书改写成正则化最佳响应映射的不动点，偏离激励作为可微残差被梯度驱动到零。NashPG 固定正则化强度于大值、靠迭代精炼参考策略，在双人零和中单调改进并收敛到精确 Nash 且不需唯一性假设，并可扩展到 No-Limit Texas Hold'em、Battleship 等大规模域；QFR（论文题 _A Policy-Gradient Approach to Solving Imperfect-Information Games with Best-Iterate Convergence_）是首个在双人零和不完美信息扩展形式博弈中可证 best-iterate 收敛到正则化 Nash 的 principled policy gradient，并规避重要性采样。

> _Key sentence:_ These methods do not enumerate deviations; they encode the certificate as the vanishing residual of a regularized response map and let gradient dynamics certify it implicitly.

参考：NashPG（arXiv 2510.18183）；QFR（arXiv 2408.00751，ICLR 2025）。

### 3.1.4 松弛（时间化）：Regret 最小化

**AI 载体：神经化 regret 最小化（Deep CFR——以网络逼近 regret/strategy、梯度训练）。** 重心落在参数化的 Deep CFR；原版 CFR 是表格式 no-regret，仅作概念根。

把"一次性全称偏离检验"松弛为"长期无后悔"：exploitability 就是被直接最小化的量，均衡是学习轨迹的极限对象。CFR 用信息集级 counterfactual regret 给出这一思路的表格式内核；Deep CFR 进一步用神经网络逼近 regret/strategy、靠梯度训练摆脱表格抽象，把同一无后悔松弛带到函数逼近与大规模域。

> _Key sentence:_ Equilibrium becomes the limiting object of a no-regret trajectory rather than the solution of a static system; the deep variant carries that trajectory into parameterized function approximation.

_护栏_：仅限 _regret-minimizing_ self-play；纯 RL 自博弈（AlphaZero/AlphaStar 类）优化回报、均衡只是涌现的副产物，不属本节。Nash 保证主要限于双人零和（接 3.1.5）。本节 AI 性住在 Deep CFR 的网络逼近层，no-regret 收敛保证本身来自学习理论。

参考：Zinkevich et al., 2007；Brown et al., _Deep CFR_, 2019。

### 3.1.5 松弛（概念替换）：从 Nash 退到 (C)CE

**AI 载体：被元梯度训练的参数化 regret minimizer（把 CCE 学习者拽回 Nash）；JPSRO 的 deep-RL oracle。** "退到 (C)CE"是 AI 学习器在一般和里继承的**约束**，不是本节卖点；本节的方法是对该约束的回应。

一般和里 no-regret 动态只保证收敛到 CCE、no-swap-regret 收敛到 CE——多项式可验证，但比 Nash 更弱的证书，这构成学习器在一般和里的天然约束。本节的方法正是对它的回应：元学习 CFR 用基于**策略相关性**的 meta-loss 训练一个参数化的 regret minimizer，在保持收敛到 CCE 的同时把解主动推向 Nash，并给出到 Nash 的距离上界；JPSRO 则在 deep-RL best-response oracle 之上、以 max-Gini (C)CE 为（经典）选择规则解决均衡选择。

> _Key sentence:_ The CCE/CE substitution is the constraint, not the contribution: parameterized meta-learning is deployed to steer a CCE-converging learner back toward Nash, with the meta-loss bounding the residual gap.

_护栏_：JPSRO 的终极对象是 CCE/CE 而非 Nash，写作时须把"换证书"说破，避免读者误以为它在解 Nash。本节 AI 性住在元学习 / oracle 层；CCE/CE 的可验证性本身来自 no-regret 理论，非深度学习。

参考：Marris et al., _JPSRO_, 2021；Sychrovský et al., _Approximating Nash Equilibria in General-Sum Games via Meta-Learning_（arXiv 2504.18868）。

### 3.1.6 近似（经验化）：EGTA 与 PSRO

**AI 载体：PSRO 的 deep-RL best-response oracle（参数化、梯度训练、可大规模）。EGTA 作经验化前置设定。**

当 full game 写不出来时，EGTA 用 simulator 采样把它降格为可被采样的经验博弈——这是本节的设定 / 前置。PSRO 在此之上把 AI 推到前台：它在不断扩展的 policy population 上求解受限经验博弈，其 best-response oracle 是参数化的 deep RL 智能体（梯度训练、可大规模），终止条件是该 oracle 找不到可获利偏离（受限博弈上 exploitability→0）。由此形成三重近似：策略空间 / payoff / best response。

> _Key sentence:_ Empirical methods approximate both sides of the certificate—a simulated payoff model in place of the full game, and a growing tested population in place of all deviations—with a parameterized RL oracle doing the deviation search.

_护栏_：MARL 在本节作为 PSRO 的 best-response oracle / 策略生成器出现——这正是本节 AI 载体所在；但**不单列通用 MARL 为均衡求解方向**：通用 MARL 优化回报、一般和无收敛保证，均衡是副产物。

参考：Wellman, Tuyls & Greenwald, _EGTA: A Survey_, 2024（arXiv 2403.04018）；McAleer et al., _Pipeline PSRO_, 2020。

### 3.1.7 转化（状态）+ 在线再证明：公共信念状态与安全子博弈重求解

**AI 载体：深度神经价值函数（ReBeL 的 self-play 深度 RL 价值/策略网络；DeepStack 的深度反事实价值网络）。** Libratus 若引仅作非神经的历史先声。

把不完美信息博弈提升为以 PBS 为状态的连续态完美信息 MDP，再用实时嵌套子博弈重求解 + safe-resolving，使融合后整体可利用度不超过蓝图——证书被改造成在线、局部、带误差界的保证，并安全应对树外动作。支撑大规模的正是深度神经价值函数：ReBeL 用 self-play 深度 RL 学到的价值/策略网络，在双人零和可证收敛到 Nash；DeepStack 用深度反事实价值网络作同类先声。

> _Key sentence:_ The certificate is neither precomputed nor learned globally, but re-established locally and online, with a bounded-exploitability guarantee acting as the real-time certificate—scaled by a deep value network.

_护栏_：anchor 在不完美信息、带 exploitability 界的变体。ReBeL / DeepStack 为深度学习锚点；Libratus 无神经网络（CFR+ 蓝图 + 实时子博弈 + LP 安全求解），如引仅作历史对照。通用 RL+search（完美信息 AlphaZero）均衡为副产物，不纳入。

参考：Brown et al., _ReBeL_, 2020（可选 DeepStack 作深度价值网络先声；Libratus 作非神经历史对照）。

### 3.1.8 重新证明：LLM 符号求解与算法合成

**角色：AI 作引擎，符号作求解机制（AI 引擎 ≠ AI 求解）。** LLM 本身满足大规模 / 梯度 / 参数化，但对"均衡"这个对象走的是符号路径——这正是谱系另一端的"返回"。

前面各路都在降低证书的计算负担，LLM 这一支反向把证书带回来，且分两层。其一，**实例求解 + 证明**：PrimeNash 用 LLM agent 自主推导闭式 Nash 并产出机器可检验证明对象，是最纯粹的"解+证"。其二，**算法合成**：LegoNE 把类 Python 语言写的算法自动编译为约束优化问题，求解即推导并证明该算法的近似界——它解的是"如何解均衡"。

> _Key sentence:_ LLM-based methods do not relax the certificate; they reconstruct it—either proving a specific equilibrium (PrimeNash) or synthesizing solvers whose approximation bounds are themselves provable (LegoNE)—with the AI as engine and the certificate rebuilt symbolically.

_护栏_：排除均衡仅作工具的 LLM 工作（Consensus Game 式解码、LLM-as-players 评测、用 NE 协调多智能体）——其终极目的不是解均衡。

参考：Liu et al., _PrimeNash_, 2025（Nexus）；Li et al., _LegoNE_, 2025（arXiv 2508.11874）。

### 3.1.9 Synthesis：从精确证书到可计算代理，再返回

收束为一条迂回谱系：经典**转化**为代数系统 → 可微方法**转化**为残差不动点 → regret **时间松弛**为长期无后悔 → 概念**松弛**替换为 (C)CE → EGTA/PSRO **空间近似**为经验群体无偏离 → PBS 把证书**在线化** → LLM 试图**重新证明**。贯穿其中的一条副线是：AI（大规模 / 梯度 / 参数化）在每一站买到的是**逼近与规模**，而 Nash / 收敛保证总是从某一层经典机制（LP / QP / no-regret / 安全重求解 / 符号）**重新进入**——深度学习放大尺度，经典层守住严谨。

> _Key sentence:_ AI does not abolish the rigor of Nash equilibrium; it reorganizes the pipeline through which the certificate is searched, approximated, substituted, and—at the far end—re-verified. The field travels away from the exact certificate and, with symbolic methods, begins the journey back.