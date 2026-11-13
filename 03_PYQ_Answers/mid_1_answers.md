# Mid-1 Paper — Questions and Answers

**Paper pattern (as supplied):** Part A – answer all, 5 × 2 = 10 marks · Part B – answer any two, 2 × 10 = 20 marks.
**Units covered:** Unit 1 and Unit 2 only.
**Note:** Two copies of this paper were supplied: a clean copy and a copy with some questions struck through by hand. The meaning of the strike-marks was not stated, so all questions are answered here. (In the semester paper each descriptive question carries 12 marks, so add one extra point/example to the 10-mark answers.)

---

# PART A (2 marks each)

### Q1(a) — What is Markov Decision Process? Explain briefly all the elements in MDP.
**Unit 1 · Topic: MDP · 2 marks**

An **MDP** is a mathematical framework for sequential decision-making where the next state depends only on the current state and action (**Markov property**). It is the tuple **(S, A, P, R, γ)**.

| Element | Meaning |
|---|---|
| **S** | Set of states |
| **A** | Set of actions |
| **P(s′\|s,a)** | Transition probability |
| **R(s,a)** | Reward function |
| **γ** | Discount factor (0 ≤ γ ≤ 1) |

Goal: find policy π* maximising expected return G_t = Σ γ^k r_{t+k+1}.

---

### Q1(b) — Discuss Policy and Value Iterations, with equations.
**Unit 1 · Topic: Dynamic programming · 2 marks**

- **Policy iteration:** alternate
 - *Evaluation:* V(s) ← Σ_a π(a|s) Σ_{s′} P(s′|s,a)[R + γV(s′)]
 - *Improvement:* π′(s) = argmax_a Σ_{s′} P(s′|s,a)[R + γV(s′)]
 until the policy is stable.
- **Value iteration:** V_{k+1}(s) = max_a Σ_{s′} P(s′|s,a)[R + γV_k(s′)]; repeat until change < θ, then extract the greedy policy.

Policy iteration: few but costly iterations. Value iteration: many cheap iterations. Both need a known model and converge to π*.

---

### Q1(c) — Explain temporal difference learning. Give two methods in TDL.
**Unit 1 · Topic: TD learning · 2 marks**

**TD learning** is model-free learning that updates value estimates after every step by **bootstrapping**:
V(s_t) ← V(s_t) + α[ r_{t+1} + γV(s_{t+1}) − V(s_t) ]; the bracket is the **TD error δ**.

Two methods:
1. **SARSA** (on-policy): Q(s,a) ← Q(s,a) + α[r + γQ(s′,a′) − Q(s,a)]
2. **Q-learning** (off-policy): Q(s,a) ← Q(s,a) + α[r + γ max_{a′}Q(s′,a′) − Q(s,a)]

(Also TD(λ) and n-step TD.)

---

### Q1(d) — Explain Q-Learning algorithm? What is a Q-Table?
**Unit 2 · Topic: Q-learning · 2 marks**

**Q-learning** is a model-free, off-policy algorithm that learns Q*(s,a) with
Q(s,a) ← Q(s,a) + α[r + γ max_{a′}Q(s′,a′) − Q(s,a)].
Steps: initialise Q=0 → pick action by ε-greedy → observe r, s′ → update Q → s←s′ → repeat.

**Q-table:** a table with rows = states and columns = actions; each cell stores Q(s,a). The best action in a state is the column with the highest value.

| State | Up | Down | Left | Right |
|---|---|---|---|---|
| S1 | 0.2 | 0.5 | 0.1 | **0.9** |

---

### Q1(e) — What is regret analysis? Explain in detail.
**Unit 2 · Topic: Bandits / regret · 2 marks (answer in detail as asked)**

**Regret** measures how much reward an algorithm loses compared with always playing the optimal arm:
**ρ(T) = T·μ* − Σ_{t=1}^{T} E[r_t]**, where μ* is the best arm's mean reward. Equivalent: Σ_a Δ_a E[N_T(a)] with gap Δ_a = μ* − μ_a.

- Regret analysis studies how fast ρ(T) grows with T to compare algorithms.
- Greedy and fixed-ε-greedy: **linear** regret. UCB and Thompson sampling: **logarithmic** O(log T) regret.
- Lower bound (Lai–Robbins): Ω(log T). Sub-linear regret ⇒ the algorithm is learning (average regret → 0).
- Used to design exploration strategies with guarantees.

---

# PART B (10 marks each)

## Q2

### Q2(a) — What is 'State Value Function' and 'Action Value Function'. Explain them in detail.
**Unit 1 · Topic: Value functions · 10 marks**

**1. Introduction.** A value function estimates how good it is, in terms of expected future (discounted) reward, for an agent to be in a state or to take an action, when following a policy π.

**2. State-value function v_π(s)** — expected return starting from state s and following π thereafter:
v_π(s) = E_π[ G_t | S_t = s ] = E_π[ Σ_{k=0}^∞ γ^k r_{t+k+1} | S_t = s ]

**3. Action-value function q_π(s,a)** — expected return starting from s, taking action a, then following π:
q_π(s,a) = E_π[ G_t | S_t = s, A_t = a ]

**4. Relationship**
- v_π(s) = Σ_a π(a|s) q_π(s,a)
- q_π(s,a) = Σ_{s′} P(s′|s,a)[R(s,a) + γ v_π(s′)]

**5. Bellman forms**
- v_π(s) = Σ_a π(a|s) Σ_{s′} P(s′|s,a)[R + γ v_π(s′)]
- q_π(s,a) = Σ_{s′} P(s′|s,a)[R + γ Σ_{a′} π(a′|s′) q_π(s′,a′)]

**6. Optimal value functions**
v*(s) = max_π v_π(s), q*(s,a) = max_π q_π(s,a), and π*(s) = argmax_a q*(s,a).

**7. Backup diagram**
```
   (s)  --- π(a|s) ---> (a) --- P(s'|s,a), r ---> (s')
    v_π(s)               q_π(s,a)                 v_π(s')
```

**8. Comparison**

| | v_π(s) | q_π(s,a) |
|---|---|---|
| Input | state | state + action |
| Tells | how good the state is | how good an action is in that state |
| Control | needs model to pick action | argmax gives action directly |
| Used in | TD(0), policy evaluation | Q-learning, SARSA |

**9. Example.** Grid-world: v(s) of a cell next to the goal is high; q(s, "move right") is higher than q(s, "move left") in that cell.

**10. Conclusion.** Value functions underpin all RL methods: they quantify long-term desirability and let the agent improve its policy greedily.

---

### Q2(b) — Explain the equation V^π(s) = Σ_{a∈A} π(a|s) Σ_{s′∈S} P(s′|s,a)[R(s,a) + γV^π(s′)]
**Unit 1 · Topic: Bellman expectation equation · 10 marks**

**1. Name.** This is the **Bellman expectation equation** for the state-value function.

**2. Symbols**

| Symbol | Meaning |
|---|---|
| V^π(s) | Value of state s under policy π |
| π(a\|s) | Probability that the policy chooses action a in s |
| P(s′\|s,a) | Probability of moving to s′ after a in s |
| R(s,a) | Immediate reward |
| γ | Discount factor |
| V^π(s′) | Value of next state |

**3. Step-by-step reading**
1. **Inner bracket** R(s,a) + γV^π(s′) = reward now + discounted value of what follows.
2. **Σ_{s′} P(s′|s,a)[…]** averages over possible next states (environment randomness) → this equals q^π(s,a).
3. **Σ_a π(a|s)[…]** averages over actions (the agent's policy randomness).

**4. Meaning.** The value of a state equals the expected immediate reward plus the discounted expected value of the successor state. It is a **recursive** (self-consistent) definition.

**5. Backup diagram**
```
        s
     / / \ \        <- choose a with π(a|s)
    a1 a2 a3
    /\
  s'1 s'2           <- move with P(s'|s,a), get R
```

**6. How it is solved**
- Iterative policy evaluation: V_{k+1}(s) = RHS using V_k (converges to V^π).
- Or as linear system V = R^π + γP^πV → V = (I − γP^π)^{-1} R^π.

**7. Bellman optimality version.** Replace the policy average by a max: V*(s) = max_a Σ_{s′} P(s′|s,a)[R + γV*(s′)].

**8. Small numerical illustration.** One action with probability 1, R = 2, γ = 0.9, V(s′) = 10, deterministic transition → V(s) = 2 + 0.9×10 = 11.

**9. Importance.** Basis for dynamic programming, TD learning (sample-based version) and Q-learning.

---

## Q3

### Q3(a) — What is Function Approximation? Give an example.
**Unit 2 · Topic: Function approximation · 10 marks**

**1. Definition.** Function approximation represents value function or policy with a parameterised function v̂(s;w) ≈ v_π(s) (or q̂(s,a;w)), where w is a weight vector much smaller than the number of states, instead of a lookup table.

**2. Need.** Tables fail when states are huge or continuous (curse of dimensionality) and give no generalisation. With approximation, learning about one state improves estimates for similar states.

**3. Types**
- **Linear:** v̂(s;w) = wᵀx(s), x(s) = features (tile coding, RBF, polynomial, Fourier).
- **Non-linear:** neural networks (deep RL).
- Others: decision trees, nearest neighbour, kernel methods.

**4. Objective and update.** Minimise mean squared value error:
J(w) = E[(v_π(s) − v̂(s;w))²].
Semi-gradient TD(0): **w ← w + α[r + γv̂(s′;w) − v̂(s;w)] ∇_w v̂(s;w)**
For linear case ∇_w v̂ = x(s).

**5. Diagram**
```
 state s --> [feature extractor x(s)] --> [function with weights w] --> v̂(s;w)
                      ^                                                  |
                      +------ update w using TD error <------------------+
```

**6. Example.** **Mountain-car / cart-pole:** the state (position, velocity) is continuous. Use tile coding to form features and a linear approximator for q̂(s,a;w). **Atari:** a CNN approximates Q(s,a) from raw pixels (deep Q-learning).

**7. Advantages / disadvantages**

| Advantages | Disadvantages |
|---|---|
| Handles large/continuous spaces | Can diverge (deadly triad: bootstrapping + off-policy + approximation) |
| Generalises to unseen states | Approximation error (bias) |
| Memory efficient | Needs feature design or lots of data |

**8. Conclusion.** Function approximation lets RL scale to real-world problems and is the foundation of deep RL.

---

### Q3(b) — Discuss core concept of deep Q-learning. Elaborate its components.
**Unit 2 · Topic: DQN · 10 marks**

**1. Core concept.** Deep Q-learning replaces the Q-table with a **deep neural network** Q(s,a;θ) trained by Q-learning targets. Input: state (e.g. game frames). Output: one Q-value per action. The agent picks the action with the highest Q (ε-greedy during training).

**2. Loss.** L(θ) = E[ ( y − Q(s,a;θ) )² ], with target **y = r + γ max_{a′} Q(s′,a′;θ⁻)**.

**3. Architecture diagram**
```
 4 stacked frames -> [Conv] -> [Conv] -> [FC] -> Q(s,a1) ... Q(s,an)
```

**4. Components**
1. **Q-network** – CNN (or MLP) approximating Q.
2. **Experience replay buffer** – stores (s,a,r,s′,done); random minibatches break temporal correlation and reuse data.
3. **Target network** (θ⁻) – a delayed copy updated every C steps; stabilises targets.
4. **ε-greedy exploration** with decaying ε.
5. **Loss function and optimiser** – squared TD error, Adam/RMSProp.
6. **Preprocessing** – grayscale, resize, frame stacking, reward clipping.

**5. Algorithm**
1. Initialise Q-network θ, target θ⁻=θ, replay buffer D.
2. For each step: choose a (ε-greedy) → execute → store (s,a,r,s′) in D.
3. Sample minibatch from D → compute targets with θ⁻ → gradient step on θ.
4. Every C steps copy θ → θ⁻.

**6. Why the tricks matter.** Plain Q-learning with a NN is unstable because of correlated data and moving targets (**nonstationarity**). Replay and target network address these.

**7. Extensions.** Double DQN (overestimation), Dueling DQN, Prioritised replay, Rainbow.

**8. Application.** Atari games from pixels (DeepMind 2015).

**9. Limitations.** Only discrete actions, sample-inefficient, overestimation bias.

---

## Q4

### Q4(a) — Discuss how Bayesian Optimization enhances RL.
**Unit 2 · Topic: Bayesian optimisation · 10 marks**

**1. Definition.** **Bayesian optimisation (BO)** optimises expensive black-box functions by fitting a probabilistic **surrogate model** (usually a Gaussian Process) and selecting the next evaluation using an **acquisition function**.

**2. Loop**
```
 data -> fit GP surrogate (μ(x), σ(x)) -> maximise acquisition (EI/UCB/PI) -> evaluate f(x*) -> add to data -> repeat
```

**3. Acquisition functions**
- **UCB:** μ(x) + κσ(x)
- **Expected Improvement (EI)**
- **Probability of Improvement (PI)**
They balance **exploitation** (high μ) and **exploration** (high σ).

**4. How BO enhances RL**
1. **Hyper-parameter tuning** – learning rate, γ, network size, ε schedule found with few training runs (each RL run is costly).
2. **Policy search** – treat policy parameters θ as input and episodic return as the black-box function; useful in robotics with expensive real trials.
3. **Sample efficiency** – surrogate predicts promising settings, so fewer real interactions.
4. **Principled exploration** – uncertainty guides where to try next (links to bandits).
5. **Robust to noise** – GP models noisy returns.
6. **Safe exploration** – constrained BO (e.g. SafeOpt) keeps trials within safe region.
7. **Reward-shaping / architecture tuning** – optimise reward weights or network design.

**5. Example.** Tuning a robot's walking controller: each trial takes minutes on hardware; BO finds good gait parameters in ~20–50 trials instead of thousands.

**6. Limitations.** GP cost O(n³), struggles in high dimension (>~20 params), sensitive to kernel choice.

**7. Conclusion.** BO adds data-efficient, uncertainty-aware search to RL.

---

### Q4(b) — Explain: i) 'Exploration' ii) 'Exploitation'. Give one scenario where each of these is adopted by an RL agent.
**Unit 2 (with Unit 1 background) · Topic: Exploration–exploitation · 10 marks**

**1. Dilemma.** An agent must balance gathering new information with using what it already knows. Too much exploitation → stuck in sub-optimal behaviour; too much exploration → wasted reward.

**2. (i) Exploration:** choosing actions that are not currently believed to be best in order to discover new information about the environment and possibly better rewards.
**Scenario:** A robot vacuum with an incomplete map takes an unvisited corridor to learn the layout. (Another: a recommender shows a new movie to learn user taste.)

**3. (ii) Exploitation:** choosing the action with the highest estimated value to maximise immediate reward using current knowledge.
**Scenario:** After mapping, the same robot follows the known shortest route to its charger. (Another: a bandit agent pulls the arm with the highest average payoff.)

**4. Comparison**

| | Exploration | Exploitation |
|---|---|---|
| Goal | Information gain | Reward now |
| Action choice | Random / uncertain actions | Greedy action |
| Risk | Short-term loss | Missing better options |
| Needed when | Early learning, uncertain estimates | Estimates are reliable |

**5. Strategies**
- **ε-greedy:** explore with probability ε, otherwise exploit; ε decays.
- **Softmax (Boltzmann):** probability ∝ exp(Q/τ).
- **UCB:** A = argmax[Q + c√(ln t / N)].
- **Optimistic initialisation** and **Thompson sampling**.
- In deep RL: entropy bonuses, curiosity-driven bonuses.

**6. Conclusion.** Good RL agents explore heavily early and shift gradually towards exploitation; regret analysis measures how well this balance is achieved.
