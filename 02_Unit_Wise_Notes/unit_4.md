# Unit 4 — Data-Efficient RL and Simulation-to-Reality Transfer

**Priority: HIGH (detailed prep recommended).** Mid-2 asked 5 questions from this unit (data-efficient RL ×2, real-to-sim and differentiable simulation, RL in healthcare, GPs/PGMs/Variational Inference).

Syllabus topics: Data-efficient learning with probabilistic methods from real data (e.g. policy search in robotics) · real-to-sim inference and differentiable simulation · RL for physical systems (locomotion, contact-rich manipulation, logistics, energy, transport) · RL for healthcare.

---

## 1. Data-Efficient RL  ⭐ Priority 1 (Mid-2 Q1a, Q2a)

### A. Definition
**Data-efficient RL** (sample-efficient RL) aims to learn good policies from **very few real-world interactions**, typically by learning a **probabilistic model** of the dynamics from data and using it to plan or optimise the policy.

### B. Simple Explanation
Real robots break, wear out and are slow. So instead of millions of trials, learn a model after a few trials, **know how unsure you are**, and practise inside that model.

### C. Key Concepts
1. **Probabilistic dynamics model:** predicts a *distribution* over next states p(s′|s,a), not a single value (Gaussian Processes, Bayesian neural nets, probabilistic ensembles).
2. **Uncertainty quantification:** **aleatoric** (noise in the system) vs **epistemic** (lack of data). Epistemic uncertainty drops as data increases.
3. **Model-based policy search:** policy parameters are improved by simulating trajectories through the learned model and following gradients of expected return (e.g. **PILCO** – GP model + analytic gradients).
4. **Uncertainty propagation:** predictions are propagated through time as distributions, which avoids over-trusting the model (reduces *model bias*).
5. **Exploration guided by uncertainty:** visit states where the model is unsure.
6. **Data reuse:** all real data is used to refine the model (not discarded as in on-policy methods).
7. **Prior knowledge:** physics priors, simulators, demonstrations reduce data needs.

### How it is unique (vs standard model-free deep RL)
| Feature | Standard deep RL | Data-efficient (probabilistic) RL |
|---|---|---|
| Interactions needed | Millions | Tens to hundreds |
| Model | Often none | Learned probabilistic model |
| Uncertainty | Ignored | Explicitly modelled |
| Best for | Simulators/games | Real robots, costly experiments |
| Weakness | Sample-hungry | Model errors, scaling of GPs |

### D. Diagram
```
 real robot --(few trials)--> data D --> [Probabilistic model (GP / ensemble)]
        ^                                        |
        |                          simulate with uncertainty
        +------ better policy π  <---- [Policy optimisation]
```

### G. Exam answer structure
Definition → motivation (real-world data is costly) → key concepts list → pipeline diagram → comparison table → example (robot arm/cart-pole learned in a few trials by PILCO) → advantages/limitations.

### H. Keywords
Sample efficiency, probabilistic model, uncertainty, epistemic/aleatoric, model bias, PILCO, policy search, Gaussian process, ensemble.

---

## 2. Probabilistic Methods: GPs, PGMs and Variational Inference  ⭐ Priority 1 (Mid-2 Q2b)

### 2.1 Gaussian Processes (GPs)
**Definition:** A GP is a distribution over functions, fully specified by a **mean function m(x)** and **covariance (kernel) function k(x,x′)**, such that any finite set of function values is jointly Gaussian.

**Simple idea:** instead of one fitted curve, you get a "cloud" of plausible curves; where data is dense the cloud is thin (confident), elsewhere wide (uncertain).

**Prediction** for noisy data (noise σ²):
- Mean: μ(x*) = k*ᵀ (K + σ²I)⁻¹ y
- Variance: σ²(x*) = k(x*,x*) − k*ᵀ (K + σ²I)⁻¹ k*

Common kernel: RBF  k(x,x′) = σ_f² exp(−‖x−x′‖² / 2ℓ²).

**Role in RL:** model dynamics s_{t+1} − s_t = f(s_t,a_t) (PILCO); also surrogate in Bayesian optimisation.
**Pros:** uncertainty, works with small data, non-parametric. **Cons:** O(n³) cost, kernel choice, poor in high dimensions.

### 2.2 Probabilistic Graphical Models (PGMs)
**Definition:** PGMs use a **graph** (nodes = random variables, edges = dependencies) to represent a joint probability distribution compactly.

- **Bayesian networks** (directed acyclic): P(x₁…x_n) = Π_i P(x_i | parents(x_i))
- **Markov random fields** (undirected): factorise over cliques.
- **Dynamic models:** Hidden Markov Model, Dynamic Bayesian Network, state-space model — used for POMDPs and dynamics.

```
 s_t -----> s_{t+1} -----> s_{t+2}      (hidden states)
  |           |              |
  v           v              v
 o_t        o_{t+1}        o_{t+2}      (observations)   <- HMM
```
**Role in RL:** structured dynamics models, belief tracking, causal structure, incorporating prior knowledge, reasoning under uncertainty.

### 2.3 Variational Inference (VI)
**Definition:** VI approximates an intractable posterior p(z|x) with a simpler distribution q_φ(z) by **optimisation** rather than sampling.

**Objective:** minimise KL( q_φ(z) ‖ p(z|x) ) ⇔ maximise the **ELBO**:
ELBO = E_q[ ln p(x|z) ] − KL( q_φ(z) ‖ p(z) )  (reconstruction term − regulariser)

**Mean-field VI:** q(z) = Π q_i(z_i). **VAE** uses neural networks for q and p.
**Role in RL:** Bayesian neural-network dynamics, latent state-space models (world models), approximate posterior over policies/parameters.

### E. Comparison

| | GP | PGM | VI |
|---|---|---|---|
| Nature | Non-parametric model of functions | Structured representation of joint distribution | Approximate-inference technique |
| Gives | Mean + variance | Dependency structure, inference | Tractable approximate posterior |
| Strength | Uncertainty, small data | Interpretable, uses prior structure | Scalable to large models |
| Weakness | O(n³) | Exact inference hard | Approximation bias |

### H. Keywords
Kernel, mean function, posterior, covariance matrix, Bayesian network, factorisation, HMM, ELBO, KL divergence, mean-field, intractable posterior.

---

## 3. Real-to-Sim Inference and Differentiable Simulation  ⭐ Priority 1 (Mid-2 Q1b)

### A. Definition
- **Real-to-sim inference:** using real-world data to **infer the parameters (and uncertainty) of a simulator** (masses, friction, delays, etc.) so that the simulator matches reality (system identification / Bayesian parameter inference, e.g. SimOpt, BayesSim).
- **Differentiable simulation:** a simulator written so that **gradients of outcomes w.r.t. actions or parameters can be computed** by automatic differentiation (e.g. Brax, MuJoCo MJX, NVIDIA Warp, DiffTaichi).

### B. Simple Explanation
First make the simulator behave like the real robot (real-to-sim), then train safely in it and transfer back (sim-to-real). If the simulator is differentiable, the policy can be trained by gradient descent through the physics rather than by slow trial and error.

### C. Detailed Explanation
**Sim-to-real loop**
```
 real data --> infer sim params (real-to-sim) --> train policy in sim
      ^                                                   |
      +-------- deploy on real system, collect data <-----+
```
**Differentiable simulation uses**
- Policy optimisation: ∇_θ J via backpropagation through dynamics → far fewer samples than model-free RL.
- System identification: fit simulator parameters by gradient descent to real trajectories.
- Design optimisation (robot shape, controllers).

**Challenges:** discontinuities at contacts give noisy/zero gradients; long horizons → exploding or vanishing gradients; local minima.
Related: **domain randomisation** (train over many randomised simulators), **sim-to-real gap**.

| | Non-differentiable sim + RL | Differentiable sim |
|---|---|---|
| Gradient info | Estimated by sampling | Exact via autodiff |
| Samples needed | Many | Fewer |
| Contacts | Handled by sampling | Difficult |

### G. Exam answer structure
Definition of each → sim-to-real loop diagram → how each helps data efficiency → challenges → conclusion.

### H. Keywords
Sim-to-real gap, system identification, domain randomisation, autodiff, Brax, MuJoCo, contact discontinuity.

---

## 4. RL for Physical Systems

| Domain | Use of RL | Status / Example |
|---|---|---|
| **Locomotion** | Legged robots learn walking, running, recovery from falls in simulation, transferred to hardware | Successful (quadrupeds, bipeds) |
| **Contact-rich manipulation** | Insertion, in-hand manipulation, assembly | **Open problem**: discontinuous contact dynamics, friction uncertainty, sparse reward |
| **Logistics** | Warehouse robot routing, bin packing, inventory & scheduling | Industrial adoption |
| **Energy** | Building HVAC, smart-grid and battery scheduling, data-centre cooling | Real deployments |
| **Transport** | Traffic-signal control, fleet dispatching, vehicle control | Pilots and research |

Why physical RL is hard: safety, wear and tear, real-time constraints, partial observability, limited data.

---

## 5. RL for Healthcare  ⭐ Priority 1 (Mid-2 Q1c)

### A. Definition
RL in healthcare learns **sequential treatment policies** (dynamic treatment regimes) that choose interventions over time to maximise long-term patient outcomes, using patient data.

### C. Formulation
- **State:** vitals, lab values, history. **Action:** drug dose, fluid, ventilator setting, test. **Reward:** survival, improvement in health score, minus side-effects.
- Mostly **offline RL** (learn from historical records), since experimenting on patients is unsafe.

### Applications / Examples
- **Sepsis treatment** (fluids and vasopressors in ICU – "AI Clinician").
- **Artificial pancreas / insulin dosing** for diabetes.
- Radiotherapy dose planning, ventilator weaning, personalised chronic-disease management, drug-dose optimisation.

### Challenges
Safety, offline data bias and confounding, interpretability, partial observability, ethics and regulation.

**One-line example (2 marks):** an RL agent recommends insulin dose each hour from glucose readings to keep glucose in the target range while avoiding hypoglycaemia.

### H. Keywords
Dynamic treatment regime, offline RL, sepsis, personalised medicine, safety, interpretability.

---

## Unit 4 — Quick Revision
- Data-efficient RL = learn from few real trials using probabilistic models + uncertainty.
- GP = distribution over functions (mean + kernel). PGM = graph of dependencies. VI = maximise ELBO.
- Real-to-sim = infer sim parameters from real data; differentiable sim = gradients through physics.
- Physical RL: locomotion works, contact-rich manipulation still open.
- Healthcare RL: treatment policies, usually offline, e.g. sepsis, insulin.
