# 3.2 Strategic Inference: Learning Behavior Generators for Hidden Strategic States

## Section thesis

> AI helps strategic inference not by replacing behavioral game theory with black-box prediction, but by turning behavioral theories, game structures, and agent simulations into trainable behavior generators — generators that can be _constructed_, _inverted_, and _expanded_.

> AI 对 strategic inference 的贡献,不是用黑箱预测替代行为博弈论,而是把行为理论、博弈结构和 agent simulation 转化为可训练的行为生成器——这些生成器可被**构造**、**反演**、**扩展**。

统一问题分两面: [ \underbrace{a_t \sim G_\phi(h_t, z_i)}_{\text{forward generator}}, \qquad \underbrace{p(z_i \mid h_t, a_t)}_{\text{inversion}} ] 其中 (h_t) 是 observable history,(z_i) 是 latent strategic state。

> **三个动词框架(贯穿全节,用以划清小节边界):**
> 
> - **Construct**(3.2.2、3.2.4):决定 (G_\phi) 的形式与来源——它从哪来、长什么样。
> - **Invert**(3.2.3):在 (G_\phi) 已给定的前提下,从观测 (a) 反推 (z)。
> - 3.2.2 与 3.2.4 是"构造"轴的两端(手写理论 vs. 机器发现);3.2.3 居中,是"反演"。3.2.4 由 3.2.3 暴露的局限驱动:真实生成器可能不在手写库里。

---

### 3.2.1 Fit Is Cheap, Identification Is Hard

**Leading question:**

> When observed strategic behavior can be rationalized by many latent games, what exactly has been identified?

**功能:** 立问题——行为拟合容易,机制识别困难。并区分**两类**识别失败,分别铺垫后两节。

**核心论点:**

- 行为拟合 ≠ 机制识别。
- flexible behavioral model 可能 rationalize 数据,但不一定识别 payoff、belief、rationality type。
- 深度模型若只提高拟合能力,可能扩大欠识别问题。
- 所有后续方法必须回答:提供了什么识别约束?允许什么 counterfactual inference?

**两类欠识别(本节新增的层次,务必挑明):**

1. **Falsifiability / 经验内容型**(对应后文"约束生成器形式"):模型本身是否对行为施加任何可检验限制。
2. **Recovery / 参数可恢复型**(对应后文"反演恢复 (z)"):即使模型有结构,观测是否足以唯一 pin down 其参数。

**代表文献:**

- **Haile, Hortaçsu & Kosenok** — _falsifiability 型_。注意实际结论比"经验内容取决于额外限制"更强:**即使对不可观测量施加很强的先验限制,QRE 也不施加任何可证伪限制,能 rationalize 任意 normal-form game 中的任意行为分布**。即 QRE 本身经验内容为零,可证伪性完全来自额外维持假设。
- **Freihaut & Ramponi**(_On Feasible Rewards in Multi-Agent IRL_)— _recovery 型_。刻画 Markov games 中的 **feasible reward set**(所有能 rationalize 给定均衡的 reward),指出**单个 Nash 均衡可对应多种 reward 结构**(reward 非唯一);用 entropy-regularized Markov games 得到唯一均衡再做 sample-complexity 分析。
    
    > 术语提醒:避免用"multi-reward 欠识别",准确说法是"feasible reward set / 均衡观测下的 reward 非唯一"。
    

**收束句:**

> Strategic inference starts from two distinct identification failures: a model may fail to _restrict_ behavior at all (falsifiability), or may restrict behavior yet fail to _pin down_ its parameters from observed equilibria (recovery). The purpose of AI is not merely to fit behavior, but to structure the space of admissible explanations along both axes.

---

### 3.2.2 Neuralizing Behavioral Priors: From Theory as Explanation to Theory as Generator (Construct, I)

**Leading question:**

> How can behavioral game-theoretic priors become trainable constraints that _construct_ a generator (G_\phi(h,z)), rather than hand-written final explanations?

**功能:** AI 将行为理论操作化为生成器的**形式约束**(construct,而非 invert)。本节回答"生成器从哪来"的第一种答案:**手写理论**。

**核心论点:** 理论不再是最终解释,而是变成:

1. pretraining distribution
2. architecture
3. teacher signal / feature
4. regularizer / loss
5. candidate model family

**代表文献:**

- **Bourgin et al.**(_Cognitive Model Priors_)— 用认知模型生成的合成数据预训练神经网络,再在小规模真实人类数据上微调,取得 SOTA 提升。**域的说明(必须交代):** 该工作的对象是**个体风险决策(prospect theory 框架)**,不是策略/博弈行为;在此作为 "theory-as-pretraining-prior" 的**范式模板从 individual choice 迁移而来**,而非直接的博弈证据。
- **Hartford, Wright & Leyton-Brown / GameNet** — 从手写 cognitive 特征到可训练的神经结构。**Framing 张力(必须调和):** 论文**自我定位**是"在不依赖专家知识的前提下自动完成 cognitive modeling",强调摆脱手写特征;本节将其读为"theory as architecture"(其 matrix-unit 架构软性编码了 iterated-response 结构)。两者可调和,但要点明这一张力。
    
    > **预埋对照线:** GameNet 代表"**黑箱但可训练**"的一端——它把建模负担从特征工程转移到了模型解释。这条线在 3.2.4 与 AutoBM(可解释、符号化)形成"**black-box → interpretable**"对照,可主动利用。
    

**边界说明:** QRE 作为 behavioral prior 可在此提及(theory → form),但 **Ling 应进入 3.2.3**:Ling 的重点不是"构造一个 QRE 形式的生成器",而是把它当作 **differentiable inverse layer** 用于反演。构造 vs. 反演的动词区分在此落地。

**收束句:**

> Neuralizing behavioral priors does not solve identification by itself; it _constructs_ the generator — turning behavioral theory into a trainable constraint on the form of (G_\phi(h,z)). Whether that generator can be inverted is the question of the next section.

---

### 3.2.3 Inverting Behavior Generators: Differentiable Equilibrium Layers and Hidden Strategic States (Invert)

**Leading question:**

> Once behavior is represented by a structured generator (G_\phi(h,z)), how can observed actions be _inverted_ into latent values, beliefs, relations, or game parameters?

**功能:** 3.2 的核心小节——行为生成器**反演**(invert,而非 construct)。这里 (G_\phi) 的形式已给定,问题是恢复 (z)。

#### 3.2.3.a Differentiable equilibrium layers for inverse game learning

**核心文献：** Ling, Fang & Kolter, _What Game Are We Playing? End-to-End Learning in Normal and Extensive Form Games_

- 处理 "inverse" 设定:博弈参数未知、须从观测中学习。
- 把 regularized equilibrium solver(等价于一种 QRE)嵌入端到端学习,作为 **differentiable behavior map**;用 primal-dual Newton 法求均衡,并通过对均衡解反向传播解析地计算所有博弈参数的梯度。
- 从 observed play 学 payoff、chance 或 game parameters。
- 形式：(G_\theta(h) = \operatorname{QRE}_\tau(\mathcal{G}_\theta, h))
- Caveat：识别的参数是相对于 **assumed equilibrium-response map**(此处即 QRE)的,不是唯一的"真实博弈"。

#### 3.2.3.b Weak-rationality constrained private-information inference

**核心文献：** Cui & Yu, _Inferring Private Valuations from Behavioral Data in Bilateral Sequential Bargaining_(框架 BLUE)

- 利用"多数 bargainer 不选严格劣势策略"这一**弱理性**事实,从行为数据导出 valuation 的 **feasible intervals**,再用 RNN 学习复杂历史依赖(behavior function 映射 valuation + bargaining history → 决策),并以基于 feasible interval 的损失函数学习。
- 论证功能：弱理性约束提供中间路线,**不强制 full equilibrium rationality**(论文明确要避开 strong equilibrium assumptions)。

#### 3.2.3.c Social-relation and Theory-of-Mind inversion

**核心文献：** Shum et al., _Theory of Minds: Understanding Behavior in Groups Through Inverse Planning_

- 提出 Composable Team Hierarchies (CTH),grounded 在 stochastic games / MARL,作为可执行行为生成模型;从稀疏群体行为反推 hidden relations(team, alliance, group structure)。
- **机制说明(微调):** 反演方式是 **Bayesian inverse planning**,而非 gradient-based 神经反演——放在"generator inversion"主题下概念上成立,但别让读者误以为是可训练的梯度反演。
- latent strategic states 不限于 private values / rewards,还包括 social relations 和 higher-order structures。

**收束句:**

> Once behavior is represented as (G_\phi(h,z)), strategic inference becomes generator inversion: observed actions are used to recover latent values, beliefs, risk attitudes, social relations, or game parameters — always _relative to_ the assumed response map. This raises the limitation that motivates the next section: what if the true generator is not in the hand-written library at all?

---

### 3.2.4 Expanding the Behavior-Generator Space: MARL and LLMs as Model-Discovery Engines (Construct, II)

**Leading question:**

> What if the existing behavioral-theory library is too small to contain the true generator?

**功能:** 回答"生成器从哪来"的第二种答案:**机器发现**。MARL 和 LLM **扩展候选生成器空间**(construct,扩展前向生成器族 (G_\phi) 本身),从而为后续反演提供更大的可容许解释空间——它们本身**不是**在做反演,不应让 (p(z\mid h,a)) 过度承诺到这里。

**两条互补路径:**

- **MARL —— 可执行的 strategic dynamics**(如 Song et al. / MAGAIL)
    - MAGAIL：为 general Markov games 提供 multi-agent imitation learning 框架,建立在 generalized inverse RL 之上,给出实用的 multi-agent actor-critic 算法,可模仿高维多智能体环境中的复杂行为。
    - **归类说明:** MAGAIL 本身带 inverse/imitation 性质,与 3.2.3 有重叠;放在此处是因为它**产出可执行的多智能体策略动态**,扩充了"装得下真实交互"的生成器族,而非主打反演单一 latent (z)。
- **LLM —— 可解释的符号化 behavioral hypotheses**(Xie, Yu, Shou & Huang / **AutoBM**, AAAI-26)
    - AutoBM：用 LLM 直接从**真实人类行为数据**中系统地**生成、评估、精化可解释行为模型**;模型表示为结构化自然语言规范(显式定义符号化 term、可调参数、解释与设计理由)。
    - 流程:LLM 把规范翻译成可执行 PyTorch 代码 → 梯度优化可调参数 → 验证集 NLL 评估;搜索为**演化 + LLM 自我精化**的混合(先让 LLM 批判性评估现有模型并给理由,再做有原则的重组与扰动;演化种群避免幻觉累积与局部最优)。
    - 在最后通牒博弈、重复 RPS、连续双向拍卖上,**一致优于人工构造模型(QRE / IA / ERC 等)**及 LLM 基线(BoxLM、FunSearch),同时保持可解释性。
    - **承接对照线:** 与 3.2.2 的 GameNet(黑箱可训练)形成 "**black-box → interpretable**" 闭合——值得一提,AutoBM 论文本身就把 GameNet 一类方法归为"black-box models lacking interpretability"。

**Caveat —— 非对称,务必拆开(原"共同 caveat"会误述 AutoBM):**

- **MARL 一侧:** 合成 rollout 是 simulated trajectories,**不能直接当作真实行为证据**,需经真实数据 grounding。
- **LLM 一侧:** LLM 提出的假设不会自动为真,需要经验筛选——而 **AutoBM 恰恰把这一筛选操作化了**(模型按真实数据拟合 + 验证集 NLL 选择)。换言之,AutoBM 的经验纪律是内建的,其输出的认识论地位强于"合成 ≠ 证据"的笼统说法。

**收束句:**

> MARL and LLMs expand the behavior-generator space in complementary ways: MARL supplies executable strategic dynamics that must be grounded against real data, while LLMs supply interpretable symbolic hypotheses that can be implemented, fitted, and revised — with AutoBM showing that the empirical selection of such hypotheses can itself be automated. Both _enlarge_ the space of forward generators (G_\phi), which subsequent inversion can then exploit.

---

## 3.2 最终 story chain

[ \text{(two) underidentification} ;\rightarrow; \underbrace{\text{neuralized priors}}_{\text{construct I}} ;\rightarrow; \underbrace{\text{generator inversion}}_{\text{invert}} ;\rightarrow; \underbrace{\text{generator-space expansion}}_{\text{construct II}} ]

> 两类欠识别(可证伪 / 可恢复) → 理论先验神经化(构造生成器形式) → 行为生成器反演(恢复 (z)) → MARL/LLM 扩展生成器空间(机器发现,黑箱→可解释)

**贯穿主线:** 全节由 **construct ↔ invert** 两个动词组织;3.2.2 与 3.2.4 是构造轴的手写端与发现端,3.2.3 是反演;两类欠识别(3.2.1)分别落到"约束形式"与"恢复参数";GameNet→AutoBM 一条 black-box→interpretable 对照线串起 3.2.2 与 3.2.4。