# Unit 5 — RLHF, Safe RL, RL for Validation and Scientific Discovery

**Priority: HIGH (detailed prep recommended).** Mid-2 asked 6 questions from this unit (LLM fine-tuning with human feedback, Safe RL, RLHF, three steps of RLHF, RL for validation, driving/flying/collision avoidance).

Syllabus topics: Fine-tuning LLMs with human feedback (TRLX, RL4LMs, light-weight RLHF) · Safe RL (e.g. optimising commercial HVAC with constraints) · RL for validation (autonomous driving, autonomous flying, aircraft collision avoidance) · RL for scientific discovery (molecular design and drug discovery, active learning for new materials, nuclear fusion) · student presentation topics.

---

## 1. RLHF — Reinforcement Learning from Human Feedback  ⭐ Priority 1 (Mid-2 Q1d, Q3a, Q3b)

### A. Definition
**RLHF** is a technique that fine-tunes a pretrained language model using a **reward model learned from human preference judgements**, optimised with reinforcement learning, so that outputs become more helpful, honest and harmless.

### B. Simple Explanation
A pretrained LLM predicts text but doesn't know what people *prefer*. Humans rank its answers; a reward model learns those preferences; RL then pushes the LLM towards answers the reward model scores highly.

### C. The three steps

**Step I — Supervised Fine-Tuning (SFT)**
Fine-tune the pretrained LLM on a dataset of high-quality (prompt, human-written response) pairs. Gives a reasonable starting policy π_SFT (instruction-following).

**Step II — Reward Model (RM) Training**
For each prompt sample several responses, humans **rank** them. Train a model r_φ(x,y) on pairwise preferences with the Bradley–Terry loss:
L(φ) = − E[ log σ( r_φ(x, y_w) − r_φ(x, y_l) ) ]   (y_w = preferred, y_l = rejected)

**Step III — RL Optimisation**
Treat the LLM as policy π_θ (state = prompt + tokens so far, action = next token). Optimise with **PPO** to maximise
**R = r_φ(x,y) − β · KL( π_θ ‖ π_ref )**
The KL penalty keeps the model close to the SFT model, prevents reward hacking and gibberish.

### D. Diagram
```
 Pretrained LLM --(1) SFT on demos--> π_SFT
                                        |
 prompts -> sample responses -> humans rank -> (2) Reward Model r_φ
                                        |
 π_SFT --(3) PPO with reward r_φ − β·KL--> Aligned LLM (π_θ)
```

### Importance and Benefits
- **Alignment** with human values and intent; fewer toxic/harmful outputs.
- More **helpful and truthful** answers; better instruction following.
- Captures **subjective, hard-to-specify goals** (tone, style, safety) that a hand-written reward can't.
- Smaller RLHF-tuned models can outperform larger raw models (InstructGPT result).
- Basis of modern chat assistants.

**Limitations:** costly human labels, annotator bias, reward hacking, training instability.

### Tools and lightweight RLHF
- **TRLX** (CarperAI) and **RL4LMs** (AllenAI) – open-source libraries for RL fine-tuning of LMs (also Hugging Face **TRL**).
- **Light-weight RLHF** – reduce cost by parameter-efficient tuning (**LoRA/PEFT**), smaller reward models, and alternatives that avoid full RL such as **DPO** (Direct Preference Optimisation) or AI feedback (**RLAIF**).

### G. Exam answer structure
Definition → motivation → 3-step pipeline (with diagram + equations) → importance/benefits → tools (TRLX, RL4LMs) → limitations → conclusion.

### H. Keywords
Reward model, human preference, Bradley–Terry, SFT, PPO, KL penalty, reference policy, reward hacking, alignment, TRLX, RL4LMs, LoRA, DPO.

---

## 2. Safe Reinforcement Learning  ⭐ Priority 1 (Mid-2 Q1e)

### A. Definition
**Safe RL** is RL that learns a good policy while **satisfying safety constraints** (or limiting risk) both during learning and deployment.

### B. Simple Explanation
Maximise reward but never do dangerous things: a drone should learn to fly fast without crashing; a cooling controller should save energy without overheating the room.

### C. Detailed Explanation
**Formulation – Constrained MDP (CMDP):**
maximise E[ Σ γ^t r_t ] subject to E[ Σ γ^t c_t ] ≤ d   (c = cost/safety signal, d = limit)

**Approaches**
| Approach | Idea |
|---|---|
| Lagrangian / CPO | Convert constraint to penalty, adjust multiplier; Constrained Policy Optimisation |
| Shielding / safety filters | A monitor blocks unsafe actions |
| Safe exploration | Explore only within known-safe region (uncertainty-aware) |
| Risk-sensitive RL | Optimise CVaR/variance instead of mean |
| Safe policy improvement | Deploy new policy only if provably no worse than baseline |

**Example – commercial HVAC:** optimise energy use with comfort (temperature/humidity) and equipment limits as constraints; **policy improvement with constraints** guarantees the new controller is not worse than the existing one.

**Importance in real world:** failures cost lives/money (cars, medical, robots, power grids); can't use trial-and-error freely; regulatory acceptance; builds trust.

### H. Keywords
CMDP, constraint, cost function, Lagrangian, CPO, shielding, safe exploration, risk-sensitive, safe policy improvement.

---

## 3. RL for Validation  ⭐ Priority 1 (Mid-2 Q4a, Q4b)

### A. Definition
**RL for validation** uses RL to **search for failures** of a system under test: the RL agent controls disturbances/adversarial scenarios to find the most likely or most severe failure cases. (Method: **Adaptive Stress Testing**.)

### B. Simple Explanation
Instead of a developer writing test cases, an RL "tester" is rewarded for making the system fail (e.g. a pedestrian stepping out at the worst time), revealing weaknesses before deployment.

### C. Detailed Explanation
- System under test is a simulator; the RL agent chooses environment actions (other vehicles' behaviour, noise, wind).
- **Reward:** high when failure (collision, constraint breach) occurs, plus the log-likelihood of the scenario so that found failures are realistic.
- Output: failure trajectories to fix the system.

**Two examples (2 marks):** (1) stress-testing an autonomous car's planner (cut-in, sudden braking, pedestrian crossing); (2) finding collision scenarios for aircraft collision-avoidance logic.

### Role of RL in specific systems  (Mid-2 Q4b)
**(i) Autonomous driving:** decision-making and planning (lane change, merging, intersections, speed control); policies trained in simulators; RL also used to generate rare dangerous scenarios for validation.
**(ii) Autonomous flying:** control and navigation of UAVs/drones (stabilisation, agile racing, obstacle avoidance), stratospheric balloon navigation; sim-to-real transfer used; validation of flight controllers.
**(iii) Aircraft collision avoidance:** systems like **ACAS X** model the problem as an MDP/POMDP (uncertain sensor tracks) and optimise advisories (climb/descend) by dynamic programming to balance safety and nuisance alerts; neural networks can compress the large lookup tables; RL/stress testing validates that no collision scenarios are missed.

```
 Scenario simulator <--- disturbances (RL tester) ---- reward: failure & likelihood
        |
 System under test (car / aircraft logic) --> failure found? --> report & fix
```

### H. Keywords
Adaptive stress testing, failure search, simulator, ACAS X, safety-critical, rare events, POMDP.

---

## 4. RL for Scientific Discovery

### Molecular design and drug discovery
Generate molecules as sequences (SMILES) or graphs; the policy adds atoms/fragments; **reward = predicted properties** (binding affinity, solubility, synthesizability, novelty). RL finds molecules optimising these properties.

### Active learning for synthesising new materials
Choose which experiment/material to synthesise next to maximise information (exploration vs exploitation, similar to bandits/Bayesian optimisation), cutting the number of costly lab experiments.

### Nuclear fusion experiments
Deep RL controls the **magnetic coils of a tokamak** to shape and stabilise plasma (DeepMind with EPFL's TCV tokamak) — control from simulation transferred to the real reactor.

---

## 5. Student Presentations — Other Topics (brief notes for 2-mark questions)
| Topic | One-line description |
|---|---|
| **Multi-agent RL** | Several agents learn together; cooperative, competitive or mixed (e.g. traffic, games) |
| **Hierarchical RL** | High-level policy picks sub-goals/options, low-level policy executes; handles long horizons |
| **RL for hyper-parameter optimisation & NN architecture search** | RL controller proposes architectures/hyper-parameters; reward = validation accuracy |
| **Multi-task transfer** | One policy/representation learned across tasks and reused on new tasks |
| **Lifelong (continual) RL** | Keep learning over a stream of tasks without forgetting earlier ones |
| **RL in biological systems** | Models of animal learning (dopamine/TD), RL for controlling biological processes |

---

## Unit 5 — Quick Revision
- **RLHF:** SFT → reward model (preferences) → PPO with KL penalty.
- **Safe RL:** CMDP, maximise reward s.t. cost ≤ d; HVAC example.
- **RL for validation:** RL adversary finds failures (adaptive stress testing).
- **Driving / flying / ACAS X:** decision control, UAV control, MDP/POMDP collision logic.
- **Science:** molecule design, materials active learning, fusion plasma control.
