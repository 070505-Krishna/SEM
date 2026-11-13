# Mid-2 Paper — Questions and Answers

**Paper pattern (as supplied):** Part A – answer all, 5 × 2 = 10 marks · Part B – answer any two, 2 × 10 = 20 marks.
**Units covered:** Unit 4 and Unit 5 only (Unit 3 was not examined in either mid).
**Note:** Two copies were supplied (clean and hand-struck). The strike-mark meaning was not stated, so every question is answered. In the struck copy Q1(e) reads "SafeRL"; the clean copy reads "Safe RL" — same question. (In the semester paper descriptive questions carry 12 marks: add one extra example/point to the 10-mark answers.)

---

# PART A (2 marks each)

### Q1(a) — What is Data Efficient RL? How is it unique?
**Unit 4 · Topic: Data-efficient RL · 2 marks**

**Data-efficient RL** learns good policies from very few real-world interactions, typically by learning a **probabilistic model** of the dynamics (GP / ensembles) and optimising the policy using that model (e.g. PILCO).
**Unique because:** it models **uncertainty** explicitly and reuses all data, needing tens–hundreds of trials rather than millions, so it suits real robots and costly experiments.

---

### Q1(b) — What is Real to Sim Inference? Explain Differentiable Simulation
**Unit 4 · Topic: Sim-to-real · 2 marks**

- **Real-to-sim inference:** using real-world data to infer a simulator's parameters (mass, friction, delays) and their uncertainty so the simulator matches reality (system identification, e.g. SimOpt/BayesSim).
- **Differentiable simulation:** a physics simulator written so gradients of outcomes w.r.t. actions/parameters are computed by automatic differentiation (Brax, MuJoCo MJX, Warp). It enables gradient-based policy optimisation and parameter fitting with far fewer samples; contacts are difficult.

---

### Q1(c) — Explain how RL can be used in Healthcare? Give one example
**Unit 4 · Topic: Healthcare · 2 marks**

RL learns **sequential treatment policies (dynamic treatment regimes)**: state = patient vitals/labs, action = drug/dose/setting, reward = improved health outcome minus side-effects; usually trained on historical data (offline RL) for safety.
**Example:** recommending fluid and vasopressor doses for **sepsis** patients in the ICU (also insulin dosing in an artificial pancreas).

---

### Q1(d) — Explain Fine tuning of LLM with Human Feedback
**Unit 5 · Topic: RLHF · 2 marks**

A pretrained LLM is aligned with human preferences in three steps: (1) **SFT** on human-written demonstrations; (2) train a **reward model** from human rankings of outputs; (3) optimise the LLM with RL (**PPO**) to maximise the reward minus a **KL penalty** to the reference model. Result: more helpful, honest and harmless outputs.

---

### Q1(e) — What is Safe RL? What is the importance of it in real world
**Unit 5 · Topic: Safe RL · 2 marks**

**Safe RL** learns policies that maximise reward while satisfying safety constraints (formulated as a **Constrained MDP**: max E[ΣγR] s.t. E[ΣγC] ≤ d), during both learning and deployment.
**Importance:** real-world mistakes are costly or dangerous (cars, drones, medical devices, power/HVAC systems), so unrestricted trial-and-error is unacceptable; safe RL gives reliable, trustworthy, deployable systems.

---

# PART B (10 marks each)

## Q2

### Q2(a) — What are Key concepts in Data-efficient learning with probabilistic methods from real data
**Unit 4 · Topic: Data-efficient learning · 10 marks**

**1. Introduction.** In robotics and other physical systems, real data is slow, expensive and risky. Data-efficient RL uses **probabilistic models learned from real data** to extract the most information from every trial.

**2. Key concepts**
1. **Probabilistic dynamics model** – predicts a distribution p(s′|s,a) (Gaussian Process, Bayesian NN, probabilistic ensemble) rather than a point estimate.
2. **Uncertainty modelling** – *aleatoric* (inherent noise) and *epistemic* (lack of data; decreases with more data).
3. **Model-based policy search** – improve policy parameters by simulating trajectories through the learned model and following the gradient of expected return (e.g. **PILCO**).
4. **Uncertainty propagation** – long-term predictions propagate full distributions (moment matching/sampling) so the planner doesn't over-trust the model → reduces **model bias**.
5. **Uncertainty-driven exploration** – visit regions where the model is uncertain.
6. **Data reuse** – all collected data refines the model; no data thrown away.
7. **Prior knowledge** – physics priors, simulators, demonstrations reduce needed data.

**3. Pipeline**
```
 Run policy on real system -> collect data -> learn probabilistic model
        ^                                           |
        +-- improve policy via model-based search <-+
```

**4. Comparison with model-free deep RL**

| | Model-free deep RL | Probabilistic data-efficient RL |
|---|---|---|
| Trials needed | Very high | Very low |
| Uncertainty | Not modelled | Central |
| Computation | Lighter per step | Heavy model fitting/planning |
| Weakness | Sample-hungry | Model error, GP scaling |

**5. Example.** Learning to swing up and balance a cart-pole or to control a robot arm in only a few trials using PILCO.

**6. Conclusion.** Combining probabilistic modelling with policy search enables RL on real hardware.

---

### Q2(b) — Explain the methods: Gaussian Processes (GPs), Probabilistic Graphical Models (PGMs) and Variational Inference
**Unit 4 · Topic: Probabilistic methods · 10 marks**

**1. Gaussian Processes (GPs)**
- *Definition:* a distribution over functions specified by mean m(x) and kernel k(x,x′); any finite set of outputs is jointly Gaussian.
- *Prediction:* μ(x*) = k*ᵀ(K + σ²I)⁻¹y; σ²(x*) = k(x*,x*) − k*ᵀ(K + σ²I)⁻¹k*.
- *Kernel example:* RBF k = σ_f² exp(−‖x−x′‖²/2ℓ²).
- *In RL:* dynamics model (PILCO), value-function model, surrogate in Bayesian optimisation.
- *Pros:* uncertainty estimates, small data. *Cons:* O(n³), high-dimension difficulties.

**2. Probabilistic Graphical Models (PGMs)**
- *Definition:* graph-based representation of a joint distribution; nodes = random variables, edges = dependencies.
- *Types:* Bayesian networks (directed): P(x₁..x_n) = Π P(x_i | parents); Markov random fields (undirected); HMM / dynamic Bayesian networks for time series.
```
 s_t -> s_{t+1} -> s_{t+2}     hidden states
  |       |           |
 o_t    o_{t+1}     o_{t+2}    observations
```
- *In RL:* structured dynamics, belief tracking (POMDP), causal reasoning, injecting prior knowledge.

**3. Variational Inference (VI)**
- *Definition:* approximates an intractable posterior p(z|x) with a tractable q_φ(z) by optimisation.
- *Objective:* minimise KL(q‖p) ⇔ maximise **ELBO** = E_q[ln p(x|z)] − KL(q(z)‖p(z)).
- *Mean-field:* q(z) = Π q_i(z_i). VAEs use neural networks for q and p.
- *In RL:* Bayesian neural-network dynamics, latent world models, scalable uncertainty.

**4. Comparison**

| | GP | PGM | VI |
|---|---|---|---|
| Nature | Function prior/model | Dependency structure | Approximate inference method |
| Output | Mean + variance | Joint/conditional probabilities | Approx. posterior |
| Scalability | Poor for large n | Depends on graph | Good |

**5. Conclusion.** GPs model functions with uncertainty, PGMs encode structure, and VI makes inference tractable; together they power data-efficient model-based RL.

---

## Q3

### Q3(a) — What is RLHF? Discuss its importance and benefits.
**Unit 5 · Topic: RLHF · 10 marks**

**1. Definition.** **Reinforcement Learning from Human Feedback (RLHF)** fine-tunes a pretrained model, especially an LLM, with a reward model learned from human preference comparisons, optimised via RL (PPO), so outputs match human intent and values.

**2. Why needed.** LLM pre-training only predicts the next token; it does not ensure outputs are helpful, truthful, or safe. "Good response" cannot be written as a simple hand-coded reward, but humans can easily say which of two answers is better.

**3. Overview pipeline**
```
 Pretrained LLM -> SFT -> sample responses -> human ranking -> Reward model -> PPO (reward − β·KL) -> Aligned LLM
```

**4. Importance**
- Aligns models with human values (helpful, honest, harmless).
- Encodes subjective qualities (tone, safety, style).
- Reduces toxic/biased/hallucinated outputs.
- Turns raw language models into usable assistants (InstructGPT, ChatGPT-style).

**5. Benefits**
- Better instruction following and user satisfaction.
- Smaller RLHF-tuned model can be preferred over a much larger raw model.
- Needs far less human data than training from scratch (preferences, not full writing).
- Controllable behaviour via the reward signal; KL term preserves language ability.
- Tools: TRLX, RL4LMs, TRL; lightweight variants (LoRA, DPO, RLAIF) cut cost.

**6. Limitations.** Costly/biased human labels, reward hacking, training instability, alignment tax.

**7. Conclusion.** RLHF is the standard technique to align LLMs with human preferences.

---

### Q3(b) — Explain the three important steps in RLHF: i) Supervised Fine-Tuning (SFT) ii) Reward Model Training iii) Reinforcement Learning (RL) Optimization
**Unit 5 · Topic: RLHF steps · 10 marks**

**Overview diagram**
```
 [1] SFT            [2] Reward Model            [3] RL Optimisation
 demos -> LLM       rank outputs -> r_φ         π_θ + PPO using r_φ − β·KL
```

**Step i — Supervised Fine-Tuning (SFT)**
- Collect a dataset of prompts with high-quality human-written responses.
- Fine-tune the pretrained LLM by supervised learning (cross-entropy on next token).
- Output: π_SFT, which follows instructions reasonably; serves as starting policy and **reference model**.

**Step ii — Reward Model (RM) Training**
- For each prompt, sample several responses from π_SFT; human labelers **rank** them.
- Convert rankings to pairs (preferred y_w, rejected y_l).
- Train a model (often the LLM with a scalar head) with the Bradley–Terry loss:
 **L(φ) = −E[ log σ( r_φ(x,y_w) − r_φ(x,y_l) ) ]**
- Output: r_φ(x,y), a scalar score of human preference – a proxy for human judgement.

**Step iii — RL Optimisation**
- Policy π_θ = LLM (initialised from π_SFT); state = prompt + generated tokens; action = next token.
- Generate response → score with r_φ → update using **PPO** to maximise
 **R(x,y) = r_φ(x,y) − β·KL(π_θ ‖ π_ref)**
- KL penalty keeps the model near π_SFT, preventing reward hacking and degenerate text.
- Can repeat: collect more feedback, retrain the RM.

**Summary table**

| Step | Data | Method | Output |
|---|---|---|---|
| SFT | Prompt–response demos | Supervised learning | π_SFT |
| Reward model | Human rankings | Pairwise preference loss | r_φ |
| RL | Prompts + RM scores | PPO + KL penalty | Aligned LLM |

---

## Q4

### Q4(a) — What is RL for Validation? Give two examples
**Unit 5 · Topic: RL for validation · 10 marks**

**1. Definition.** **RL for validation** uses RL to **automatically discover failures** of a system (software, controller, vehicle, aircraft logic) by training an agent that controls the environment's disturbances to cause failure. Known approach: **Adaptive Stress Testing (AST)**.

**2. Why needed.** Safety-critical systems fail in rare situations that hand-written tests and random sampling seldom reach; failures must be found before deployment.

**3. How it works**
- The system under test runs inside a simulator.
- The RL agent's actions = disturbances (other vehicles' moves, pedestrian behaviour, sensor noise, wind).
- **Reward:** large when a failure (collision, constraint violation) occurs, plus log-likelihood of the scenario so that failures found are realistic.
- Output: failure trajectories used to debug and improve the system.
```
 RL tester -> disturbances -> [Simulator + system under test] -> failure? -> reward -> RL tester
```

**4. Examples**
1. **Autonomous driving:** finding scenarios (sudden cut-in, pedestrian crossing, sensor dropout) in which the self-driving planner crashes.
2. **Aircraft collision avoidance (ACAS X):** finding encounter geometries in which the avoidance logic fails to prevent a near mid-air collision.
(Other: testing drone controllers, validating healthcare devices.)

**5. Advantages.** Efficient search of rare events, automatic, produces interpretable failure cases. **Limitations:** quality depends on simulator fidelity; cannot prove absence of failures.

---

### Q4(b) — Explain the role of RL in i) autonomous driving ii) autonomous flying and iii) aircraft collision avoidance
**Unit 5 · Topic: Safety-critical applications · 10 marks**

**i) Autonomous driving**
- *Role:* decision-making and planning – lane change, merging, overtaking, intersection handling, adaptive speed control; learns from simulators with rewards for safety, progress, comfort.
- *State:* sensor/perception output (positions, speeds); *action:* steer, accelerate, brake; *reward:* collision penalty, progress, smoothness.
- *Challenges:* safety, rare events, sim-to-real gap, partial observability (POMDP).
- *Validation:* RL (adaptive stress testing) finds failure scenarios for the driving stack.

**ii) Autonomous flying**
- *Role:* low-level control and navigation of drones/UAVs – stabilisation, agile manoeuvres, racing, obstacle avoidance, wind adaptation; also stratospheric balloon navigation.
- Policies trained in simulation, transferred with sim-to-real techniques (domain randomisation).
- *Benefits:* handles nonlinear dynamics and uncertainty without hand-tuned controllers. *Challenges:* crashes are costly → safe RL, robust control.

**iii) Aircraft collision avoidance**
- *Role:* choose advisories (climb, descend, maintain) to avoid mid-air collisions while minimising unnecessary alerts. **ACAS X** formulates this as an **MDP/POMDP** (uncertain intruder state, pilot response) and optimises a policy by dynamic programming/RL, producing a lookup table; neural networks can **compress** the table for onboard use (ACAS Xu for drones).
- RL/stress testing then validates the logic across millions of encounters.

**Comparison**

| Application | Decision type | Key challenge |
|---|---|---|
| Autonomous driving | Planning and control in traffic | Rare, safety-critical events |
| Autonomous flying | Continuous control | Dynamics, wind, crash cost |
| Collision avoidance | Discrete advisories under uncertainty | Very low failure tolerance |

**Conclusion.** RL provides a way to optimise and validate decision-making for safety-critical systems, with safe RL and simulation-based validation being key to deployment.
