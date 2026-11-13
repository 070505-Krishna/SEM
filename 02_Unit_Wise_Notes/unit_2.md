# Unit 2 — RL in Discrete Action Spaces; Exploration, Uncertainty, Partial Observability

**Priority: HIGH (detailed prep recommended).** Mid-1 asked 6 questions from this unit (Q-learning/Q-table, regret analysis, function approximation, deep Q-learning, Bayesian optimisation, exploration vs exploitation).

Syllabus topics: Q-learning · function approximation and deep Q-learning · nonstationarity in RL · Atari example · Monte Carlo Tree Search · AlphaGo · multi-armed bandits · Bayesian optimisation · regret analysis · POMDP · belief, memory and sequence modelling (probabilistic methods, RNNs, transformers).

---

## 1. Q-Learning  ⭐ Priority 1 (Mid-1 Q1d)

### A. Definition
**Q-learning** is a model-free, **off-policy** TD control algorithm that learns the optimal action-value function Q*(s,a) directly, independent of the policy being followed.

### B. Simple Explanation
Keep a score card Q(s,a) for "how good is action a in state s". After each step, nudge the score towards (reward + best score available next).

### C. Detailed Explanation
**Update rule**
**Q(s,a) ← Q(s,a) + α [ r + γ max_{a′} Q(s′,a′) − Q(s,a) ]**

**Algorithm**
1. Initialise Q(s,a) = 0 for all s,a.
2. For each episode: start at s.
3. Choose a from s using **ε-greedy** (random with prob. ε, else argmax Q).
4. Execute a; observe r, s′.
5. Update Q(s,a) with the rule above.
6. s ← s′; repeat until terminal. Decay ε over time.

**What is a Q-Table?** A lookup table with **one row per state and one column per action**; each cell stores the current estimate of Q(s,a). The best action in a state is the column with the largest value.

| State \ Action | Up | Down | Left | Right |
|---|---|---|---|---|
| S1 | 0.2 | 0.5 | 0.1 | **0.9** |
| S2 | 0.4 | 0.3 | 0.0 | **0.7** |
| … | | | | |

Limitation: table explodes for large/continuous state spaces → function approximation (next section).

**Properties:** off-policy (target uses max, behaviour uses ε-greedy); converges to Q* if all (s,a) visited infinitely often and α decays properly.

### D. Diagram
```
 state s --> [ Q-table lookup ] --> ε-greedy action a --> env
   ^                                                       |
   +---- Q(s,a) update <---- (r, s') <---------------------+
```

### E. Q-learning vs SARSA

| Feature | Q-learning | SARSA |
|---|---|---|
| Type | Off-policy | On-policy |
| Target uses | max_{a′} Q(s′,a′) | Q(s′,a′) of action actually taken |
| Behaviour | Learns optimal (riskier) path | Learns safer path (cliff-walking) |

### G. University Examination Answer
Definition → Q function meaning → update rule with symbols → algorithm steps → Q-table (example table) → ε-greedy → off-policy note → advantages (simple, model-free) / limitations (table size, overestimation) → applications (games, routing).

### H. Keywords
Q-table, action-value, off-policy, ε-greedy, max operator, learning rate α, discount γ, TD target, convergence.

---

## 2. Function Approximation  ⭐ Priority 1 (Mid-1 Q3a)

### A. Definition
**Function approximation** represents the value function (or policy) by a parameterised function, e.g. v̂(s; w) ≈ v_π(s), instead of a table, so that learning **generalises** to unseen states.

### B. Simple Explanation
Rather than memorising a score for every possible situation, learn a formula (with adjustable weights) that predicts the score from features of the situation.

### C. Detailed Explanation
- **Linear:** v̂(s;w) = wᵀx(s), x(s) = feature vector (tile coding, radial basis functions, polynomials).
- **Non-linear:** neural networks (deep RL).
- **Objective:** minimise mean-squared value error  J(w) = E[(v_π(s) − v̂(s;w))²]
- **Semi-gradient TD(0) update:** w ← w + α [ r + γ v̂(s′;w) − v̂(s;w) ] ∇_w v̂(s;w)
- **Why needed:** large or continuous state spaces; need generalisation; memory saving.
- **Issue — deadly triad:** function approximation + bootstrapping + off-policy training can diverge.

**Example:** Mountain-car with state (position, velocity) – continuous, so a table is impossible; use tile coding with a linear approximator. Another: Atari frames → CNN → Q-values.

### E. Table vs Function approximation

| | Tabular | Function approximation |
|---|---|---|
| Storage | One entry per state | Fixed number of weights |
| Generalisation | None | Yes |
| State space | Small, discrete | Large/continuous |
| Convergence | Strong guarantees | Weaker; can diverge |

### G. Exam answer structure
Definition → need (curse of dimensionality) → types (linear, NN) → gradient update equation → example → advantages/disadvantages.

### H. Keywords
Generalisation, weights w, feature vector, tile coding, semi-gradient, MSVE, deadly triad.

---

## 3. Deep Q-Learning (DQN)  ⭐ Priority 1 (Mid-1 Q3b)

### A. Definition
**Deep Q-Learning** uses a deep neural network Q(s,a;θ) to approximate the action-value function, trained with Q-learning targets, stabilised by **experience replay** and a **target network**.

### B. Simple Explanation
Q-learning where the Q-table is replaced by a neural network that takes the state (e.g. game pixels) and outputs a Q-value for every action.

### C. Components
1. **Q-network** (CNN for images): input state → output Q for each action.
2. **Experience replay buffer:** store (s,a,r,s′,done); train on random mini-batches → breaks correlation between consecutive samples, reuses data.
3. **Target network** Q(s,a;θ⁻): a periodically copied (frozen) network used to compute targets → stabilises learning.
4. **Loss:** L(θ) = E[ ( r + γ max_{a′} Q(s′,a′;θ⁻) − Q(s,a;θ) )² ]
5. **ε-greedy exploration** with annealed ε.
6. **Preprocessing:** frame stacking, grayscale, reward clipping.
7. **Optimiser:** RMSProp/Adam with gradient descent on θ.

**Improvements:** Double DQN (reduces overestimation), Dueling DQN, Prioritised replay, Rainbow.

### D. Diagram
```
 env --(s,a,r,s')--> [Replay Buffer] --minibatch--> [Q-network θ] --loss--> update θ
                                                         ^
                                       every C steps copy θ -> θ⁻ [Target network]
```

### Nonstationarity in RL
The data distribution changes as the policy improves, and bootstrapped targets keep moving because the network is learning from its own changing predictions. This violates the i.i.d. assumption of supervised learning. **Remedies:** replay buffer, target network, small learning rates, careful exploration schedules.

### Example application — Atari
Input: 4 stacked 84×84 grayscale frames → conv layers → fully-connected → one Q-value per joystick action. Same architecture and hyper-parameters learned many Atari games from pixels and score only (DeepMind, 2015).

### G. Exam answer structure
Definition → why not Q-table → architecture diagram → components (replay, target network, loss) → algorithm steps → Atari example → advantages/limitations (discrete actions only, sample-inefficient, overestimation).

### H. Keywords
DQN, experience replay, target network, Bellman target, CNN, ε-greedy, Double DQN, overestimation, nonstationarity.

---

## 4. Monte Carlo Tree Search (MCTS) and AlphaGo

### A. Definition
**MCTS** is a best-first search that builds a search tree by repeated random **simulations (rollouts)** and uses the results to choose the best action.

### C. Four steps (per iteration)
1. **Selection** – from root, follow child with highest UCT:  UCT = w_i/n_i + c √( ln N / n_i )
2. **Expansion** – add a new child node.
3. **Simulation (rollout)** – play to the end with a default/random policy.
4. **Backpropagation** – update win/visit counts back to the root.
After many iterations pick the most visited root child.

```
 Selection -> Expansion -> Simulation -> Backpropagation  (repeat)
```

### AlphaGo (application)
- **Policy network** (supervised on expert games, then RL self-play) suggests moves.
- **Value network** estimates win probability, replacing long rollouts.
- **MCTS** combines both for lookahead. AlphaGo beat world champion Lee Sedol (2016).
- **AlphaGo Zero / AlphaZero:** learns purely by self-play, a single network gives policy + value, no human data.

### H. Keywords
Rollout, UCT, selection/expansion/simulation/backup, policy network, value network, self-play.

---

## 5. Multi-Armed Bandits and Exploration  ⭐ Priority 1 (Mid-1 Q4b)

### A. Definition
**Multi-armed bandit (MAB):** a one-state RL problem where an agent repeatedly picks one of k arms, each with unknown reward distribution, to maximise total reward.

### C. Exploration vs Exploitation
- **Exploitation:** choose the action currently believed best.
- **Exploration:** try other actions to gain information that may reveal a better one.

| | Exploration | Exploitation |
|---|---|---|
| Aim | Gain information | Gain reward now |
| Risk | Short-term loss | Stuck in sub-optimal choice |
| Scenario | Recommender shows a new, unrated movie to learn preferences | Recommender shows the user's top-rated genre |

**Scenarios with an RL agent**
- *Exploration:* a robot vacuum takes an unvisited corridor to learn the room map. A game agent tries an unusual move.
- *Exploitation:* the same robot follows the known shortest route to its charger. A trading agent keeps the best known strategy.

### Strategies
| Method | Idea |
|---|---|
| ε-greedy | Random action with prob. ε, else greedy |
| Optimistic initial values | Start Q high → forces trying everything |
| UCB | A_t = argmax_a [ Q_t(a) + c √(ln t / N_t(a)) ] |
| Thompson sampling | Sample from posterior of each arm, play the best sample |
| Gradient bandit | Learn action preferences with softmax |

---

## 6. Regret Analysis  ⭐ Priority 1 (Mid-1 Q1e)

### A. Definition
**Regret** is the difference between the total reward of always playing the optimal arm and the reward actually obtained by the algorithm.

### C. Detailed Explanation
**ρ(T) = T·μ* − Σ_{t=1}^{T} E[r_t]** (μ* = mean reward of best arm). Equivalent: Σ_a E[N_T(a)]·Δ_a with gap Δ_a = μ* − μ_a.

| Algorithm | Regret growth |
|---|---|
| Pure greedy | Linear O(T) (may lock onto wrong arm) |
| ε-greedy (fixed ε) | Linear O(T) |
| UCB1 | Logarithmic O(log T) |
| Thompson sampling | Logarithmic O(log T) |

Lower bound (Lai–Robbins): any good algorithm must suffer Ω(log T) regret. **Sub-linear regret ⇒ the algorithm learns** (average regret → 0).

```
 Regret
   |      greedy / fixed ε  (linear)
   |     /
   |    /      UCB (log)
   |   /   ___----
   |  /_---
   +------------------- T
```

### G. Exam answer structure
Definition → formula → meaning of Δ_a → why useful (compare algorithms without full knowledge) → regret rates table → graph → conclusion.

### H. Keywords
Cumulative regret, optimal arm, gap Δ, sub-linear regret, UCB, logarithmic bound.

---

## 7. Bayesian Optimisation  ⭐ Priority 1 (Mid-1 Q4a)

### A. Definition
**Bayesian optimisation (BO)** is a sample-efficient strategy to optimise an expensive black-box function by building a probabilistic **surrogate model** (usually a Gaussian Process) and choosing the next point via an **acquisition function**.

### C. Loop
1. Fit GP surrogate to observations so far → gives mean μ(x) and uncertainty σ(x).
2. Maximise **acquisition function** (Expected Improvement, UCB = μ + κσ, Probability of Improvement) → next x.
3. Evaluate the true function at x, add to data; repeat.

The acquisition function automatically balances **exploration (high σ)** and **exploitation (high μ)**.

### How BO enhances RL
- **Hyper-parameter tuning** (learning rate, γ, network size) with few expensive training runs.
- **Policy search:** optimise policy parameters directly when each rollout is costly (robotics).
- **Sample efficiency:** surrogate reduces real-world trials.
- **Principled exploration** via uncertainty.
- **Reward / reward-shaping parameter** tuning and safe exploration.
- Handles noisy, non-differentiable objectives.

### G. Exam answer structure
Definition → loop diagram → GP + acquisition function → exploration–exploitation balance → enhancements to RL (bullets above) → limitations (GP scales O(n³), poor in high dimensions).

### H. Keywords
Surrogate model, Gaussian process, acquisition function, expected improvement, black-box, sample efficiency.

---

## 8. Partially Observable MDP (POMDP), Belief, Memory, Sequence Modelling

### A. Definition
A **POMDP** is an MDP in which the agent cannot see the true state; it receives **observations** that give partial, noisy information.

### C. Detailed Explanation
**Tuple (S, A, T, R, Ω, O, γ)** — Ω = observation set, O(o|s′,a) = observation probability.

**Belief state b(s):** probability distribution over states; updated by Bayes rule:
b′(s′) = η · O(o|s′,a) · Σ_s T(s′|s,a) b(s)
A POMDP is an MDP over belief states (continuous, hard to solve exactly).

**Memory / sequence modelling** — use history instead of single observation:
- **Probabilistic methods:** Bayes filter, Kalman filter, particle filter to track belief.
- **Recurrent networks (RNN/LSTM/GRU):** hidden state acts as learned belief; e.g. **DRQN** (DQN with LSTM).
- **Transformers:** attention over the whole history; e.g. Decision Transformer treats RL as sequence modelling.

| Approach | Memory form | Pros | Cons |
|---|---|---|---|
| Frame stacking | Last k frames | Simple | Fixed short memory |
| RNN/LSTM | Hidden state | Learned, compact | Hard to train long dependencies |
| Transformer | Attention over history | Long-range | Costly for long sequences |
| Bayes/particle filter | Explicit belief | Principled | Needs model |

```
 true state s (hidden) --> observation o --> [Belief update / RNN] --> belief b --> policy π(a|b)
```

**Examples:** poker (hidden cards), robot with noisy sensors, doctor with test results.

### H. Keywords
POMDP, observation, belief state, Bayes update, DRQN, LSTM, transformer, Decision Transformer, particle filter.

---

## Unit 2 — Quick Revision
- Q-learning update: Q ← Q + α[r + γ max Q′ − Q].
- DQN = Q-network + replay buffer + target network.
- UCB: Q + c√(ln t / N). Regret ρ(T) = Tμ* − Σ rewards.
- BO = GP surrogate + acquisition function.
- POMDP = MDP + observations; track belief or use RNN/transformer.
