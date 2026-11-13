# Unit 3 — Policy Gradient, Actor-Critic and Model-Based RL (Continuous Actions)

**Priority: MEDIUM (basic prep for 2-mark questions + one 12-mark answer if time permits).** No question from this unit appeared in Mid-1 or Mid-2, so its weight in the semester paper cannot be judged from the supplied papers. It is mathematically heavier, so it is the unit recommended to skip for detailed prep.

Syllabus topics: Importance sampling · policy gradient theorem · actor-critic (SPG, DDPG) · Proximal Policy Optimisation · example applications · model-based RL and MPC (learning dynamics models: graph networks, stochastic processes, diffusion models, physics-based models, ensembles) · planning with learned models · Model Predictive Control · real-time control applications.

---

## 1. Why continuous actions need new methods
Q-learning needs max over actions, which is easy for a few discrete actions but hard for continuous actions (e.g. torque, steering angle). Solution: **learn the policy directly** (policy-based methods).

| | Value-based (DQN) | Policy-based |
|---|---|---|
| Learns | Q(s,a), policy implicit | Policy π_θ(a|s) directly |
| Action space | Discrete | Discrete or continuous |
| Policy type | Deterministic (greedy) | Can be stochastic |
| Convergence | Can oscillate | Smoother, local optimum |
| Variance | Lower | Higher |

---

## 2. Importance Sampling

### Definition
**Importance sampling** estimates an expectation under one distribution p using samples drawn from another distribution q, by re-weighting: **E_{x~p}[f(x)] = E_{x~q}[ (p(x)/q(x)) f(x) ]**.

### Use in RL
Off-policy learning: data from behaviour policy b is used to evaluate/improve target policy π with the ratio ρ_t = π(a_t|s_t)/b(a_t|s_t). Used in off-policy policy gradients and PPO's probability ratio. Weakness: high variance when ratios are large (mitigated by clipping/truncation or weighted IS).

---

## 3. Policy Gradient Theorem

### Definition
The policy gradient theorem gives the gradient of expected return J(θ) with respect to policy parameters without differentiating the state distribution:

**∇_θ J(θ) = E_{π_θ}[ ∇_θ ln π_θ(a|s) · Q^{π}(s,a) ]**

### Simple explanation
Increase the probability of actions that led to high returns, decrease those that led to low returns.

### REINFORCE algorithm
1. Run an episode with π_θ.
2. For each step compute return G_t.
3. θ ← θ + α · ∇_θ ln π_θ(a_t|s_t) · G_t

**Baseline** b(s) (usually V(s)) reduces variance: replace G_t with (G_t − b(s)) = advantage estimate.

**Continuous actions:** use Gaussian policy a ~ N(μ_θ(s), σ²); ∇ ln π is easy to compute.

| Pros | Cons |
|---|---|
| Works for continuous actions | High variance |
| Can learn stochastic policies | Sample-inefficient, on-policy |
| Better convergence properties | Converges only to local optimum |

---

## 4. Actor-Critic Methods

### Definition
**Actor-critic** combines a policy (**actor**) with a learned value function (**critic**). The critic evaluates the actor's actions, giving lower-variance gradients than pure REINFORCE.

```
 state --> [ACTOR π_θ] --action--> env --> reward, s'
   |                                         |
   +----> [CRITIC V_w or Q_w] <--------------+
              | TD error δ = r + γV(s') − V(s)
              +--> updates actor (policy gradient) and critic (regression)
```

- Critic update: w ← w + α_w δ ∇_w V_w(s)
- Actor update: θ ← θ + α_θ δ ∇_θ ln π_θ(a|s)  (δ acts as advantage)

### SPG — Stochastic Policy Gradient
Actor outputs a probability distribution; gradient as in the theorem above; exploration is built into the stochastic policy.

### DPG / DDPG — (Deep) Deterministic Policy Gradient
- Actor outputs a **deterministic** action a = μ_θ(s).
- **Deterministic policy gradient:** ∇_θ J = E[ ∇_θ μ_θ(s) · ∇_a Q_w(s,a) |_{a=μ_θ(s)} ]
- **DDPG** = DPG + DQN tricks: **replay buffer**, **target networks** with soft update θ′ ← τθ + (1−τ)θ′, plus **exploration noise** (Ornstein–Uhlenbeck or Gaussian) added to actions; off-policy; continuous action spaces.

| | SPG | DDPG |
|---|---|---|
| Policy | Stochastic | Deterministic |
| On/Off policy | On-policy | Off-policy |
| Exploration | From policy randomness | Added noise |
| Sample efficiency | Lower | Higher |

---

## 5. Proximal Policy Optimisation (PPO)

### Definition
PPO is a policy-gradient algorithm that limits how much the policy can change per update, using a **clipped surrogate objective**, for stable and simple training.

**Probability ratio:** r_t(θ) = π_θ(a_t|s_t) / π_θold(a_t|s_t)

**Objective:** L^{CLIP}(θ) = E[ min( r_t(θ) Â_t , clip(r_t(θ), 1−ε, 1+ε) Â_t ) ]  (typical ε = 0.2)

Full loss = L^{CLIP} − c₁ (value loss) + c₂ (entropy bonus).

**Why:** a too-large step can destroy a good policy; clipping removes the incentive to move far from the old policy (a simpler alternative to TRPO's KL-constrained optimisation). Uses Generalised Advantage Estimation (GAE), several epochs per batch.

**Applications:** robotics, game agents (OpenAI Five), and RLHF for LLMs (see Unit 5).

---

## 6. Model-Based RL

### Definition
**Model-based RL** learns (or is given) a **model of the environment dynamics** s′ = f(s,a) (and reward) and uses it for planning or to generate simulated experience.

### Learning dynamics models
| Model type | Idea | Notes |
|---|---|---|
| Neural networks / **Ensembles** | Several models; disagreement = uncertainty | e.g. PETS; reduces model bias |
| **Stochastic processes (Gaussian processes)** | Probabilistic predictions with uncertainty | Data-efficient, small data |
| **Graph networks** | Objects/joints as nodes, interactions as edges | Good for multi-body/robot structure |
| **Diffusion models** | Generative model of trajectories/next states | Captures multi-modal, long-horizon dynamics |
| **Physics-based models** | Known equations with learned parameters | Interpretable, generalise well |

### Planning with learned models
- **Dyna:** use the model to generate imagined transitions to train the policy.
- **Trajectory optimisation / random shooting / CEM:** sample action sequences, roll out in the model, choose best.
- **MCTS** with learned model (MuZero).

### Model-free vs Model-based

| | Model-free | Model-based |
|---|---|---|
| Sample efficiency | Low | High |
| Asymptotic performance | Often higher | Limited by model error |
| Computation | Cheaper at decision time | Planning cost |
| Needs model | No | Yes (learned or known) |

---

## 7. Model Predictive Control (MPC)

### Definition
**MPC** is a control strategy that, at each time step, **optimises a sequence of actions over a finite horizon H** using a model, **applies only the first action**, then re-plans at the next step (**receding horizon**).

```
 observe s_t --> optimise a_t..a_{t+H} using model --> apply a_t only --> observe s_{t+1} --> repeat
```

Advantages: handles constraints, feedback corrects model errors. Disadvantages: needs a good model, heavy online computation.

**Real-time control examples:** drone/quad-rotor control, autonomous-vehicle trajectory tracking, robot manipulation, HVAC and process control, legged locomotion.

---

## Unit 3 — Exam Answer Skeleton (12 marks, if attempted)
Policy gradient → Intro (policy-based RL) → theorem + equation → REINFORCE + baseline → Actor-critic diagram → DDPG/PPO objective → advantages/disadvantages → applications.

## Unit 3 — Quick Revision (2-mark ready)
- **Importance sampling:** E_p[f] = E_q[(p/q) f].
- **Policy gradient:** ∇J = E[∇ln π · Q].
- **Actor-critic:** actor = policy, critic = value, TD error as advantage.
- **DDPG:** deterministic actor-critic with replay + target networks + noise.
- **PPO:** clipped ratio objective, ε≈0.2.
- **MPC:** plan H steps with model, execute first action, repeat.
