# UNIT V – Ethics, Evaluation, and Multimodal NLP

**Syllabus:** Hallucinations and factual consistency in LLMs · Bias and fairness in NLP systems · Explainability and interpretability of models · Multimodal models: CLIP, Flamingo, GPT-4o · Future directions: reasoning, alignment, multilingual models

> **Recommended for detailed preparation (Top-4 unit).** Reasons: entirely theory-based (no mathematics), very scoring if you write structured points, and Mid-2 asked two full questions (evaluation of LLMs; Multimodal NLP + CLIP/Flamingo/GPT-4o comparison) plus a short question on alignment.

---

## 0. Question Paper Analysis for Unit 5

### 0.1 Actual questions seen in the supplied papers (Mid-2)
| Mid-2 Q.No | Question (original wording) | Marks | Topic |
|---|---|---|---|
| 1(e) | What is alignment in Large Language Models? | 2 | Future directions |
| 4(a) | Discuss approaches used to evaluate the reliability and performance of Large Language Models, with reference to factual consistency, bias, fairness and explainability. | 5 | Evaluation / Ethics |
| 4(b) | Explain Multimodal NLP. Compare CLIP, Flamingo and GPT-4o based on their capabilities and applications. | 5 | Multimodal |

### 0.2 Question Bank (supplied – *expected*, not confirmed past papers)
Short: hallucination, factual consistency, causes of hallucination, bias, fairness, explainability, explainability vs interpretability, transparency, multimodal NLP, CLIP, Flamingo, GPT-4o, unimodal vs multimodal, alignment, multilingual models.
Long: hallucinations (causes, effects, mitigation); factual consistency evaluation; bias & fairness; explainability/interpretability; ethical issues; evaluation approaches; multimodal NLP; CLIP; Flamingo; GPT-4o; comparison of the three; future directions (reasoning, alignment); multilingual models and low-resource languages.

### 0.3 Priority classification
| Priority | Topics |
|---|---|
| **P1** | Evaluating reliability/performance of LLMs · Multimodal NLP + CLIP vs Flamingo vs GPT-4o · Hallucinations · Alignment |
| **P2** | Bias & fairness · Explainability vs interpretability · Factual consistency evaluation |
| **P3** | Multilingual models · Reasoning future directions · Ethical issues (privacy, misuse) |

---

## 1. Hallucinations in LLMs  ⭐ P1

### A. Definition
A **hallucination** in an LLM is the generation of text that is **fluent and confident but factually incorrect, fabricated, or not supported by the input or any real source**.

### B. Simple Explanation
The model is a *very good guesser of the next word*, not a database of facts. When it doesn't know, it still produces something plausible – like a student who writes a confident answer without knowing the facts.

### C. Detailed Explanation
**Types**
- **Intrinsic hallucination:** output contradicts the given source/input (e.g., a summary says "profits fell" when the article says "rose").
- **Extrinsic hallucination:** output adds information that cannot be verified from the source (made-up citations, fake references).
- Also: factual vs faithfulness hallucination.

**Causes (any two for a short answer)**
1. Noisy, biased or incorrect training data.
2. Model is trained to predict the next token (fluency), not truth.
3. Knowledge cut-off / missing knowledge about rare facts.
4. Ambiguous or misleading prompts.
5. Decoding randomness (high temperature), exposure bias, over-confidence.
6. Limited context / reasoning errors.

**Effects:** misinformation, legal/medical/financial risk, loss of trust, fake citations.

**Mitigation techniques**
| Technique | How it helps |
|---|---|
| **RAG / grounding** | Supplies verified documents to base answers on |
| Better data curation | Cleaner training data |
| **RLHF / alignment** | Rewards truthful, calibrated answers |
| Prompt design (CoT, "say I don't know", ask for citations) | Reduces guesswork |
| Lower temperature / constrained decoding | Less random |
| Tool use / function calling | Facts from calculators, search, APIs |
| Post-hoc fact-checking & human review | Detect and correct errors |

### H. Keywords
`hallucination`, `intrinsic`, `extrinsic`, `faithfulness`, `grounding`, `RAG`, `temperature`, `RLHF`, `fabricated citation`, `calibration`.

---

## 2. Factual Consistency  ⭐ P2

### A. Definition
**Factual consistency** is the degree to which generated text is **factually supported by (and does not contradict) the source document or a trusted knowledge base**.

### B. Simple Explanation
Does the summary/answer say only things that are true according to the original document or real facts?

### C. Approaches to Evaluate Factual Consistency
| Approach | Idea |
|---|---|
| **NLI / entailment-based** | Check whether source (premise) *entails* each generated sentence (hypothesis) using an NLI model (Unit III textual entailment) |
| **QA-based** | Generate questions from output, answer them from the source and from the output; compare answers (e.g., QAGS) |
| **Fact-verification against knowledge** | Break output into atomic claims, retrieve evidence, verify each (e.g., FActScore) |
| **Benchmarks** | TruthfulQA, fact-checking datasets (FEVER) |
| **Human evaluation** | Annotators judge correctness, faithfulness |
| **LLM-as-a-judge** | Another strong LLM scores consistency |
| Overlap metrics (ROUGE/BLEU) | Weak – measure word overlap, not truth |

### H. Keywords
`factual consistency`, `faithfulness`, `entailment`, `claim verification`, `TruthfulQA`, `FEVER`, `QAGS`, `LLM-as-judge`.

---

## 3. Bias and Fairness in NLP Systems  ⭐ P2

### A. Definition
- **Bias:** systematic and unfair prejudice in model outputs favouring or disadvantaging certain groups (gender, race, religion, region, language), usually inherited from data and design.
- **Fairness:** the property that an NLP system treats individuals and groups equitably, without discriminatory outcomes.

### B. Simple Explanation
If most training text associates "nurse" with "she" and "engineer" with "he", the model learns and repeats that stereotype. Fairness means correcting such unfair behaviour.

### C. Detailed Explanation
**Sources of bias:** (1) training data (historical/societal prejudice, under-representation), (2) annotation bias, (3) model/algorithm (embeddings encode stereotypes: man:doctor :: woman:nurse), (4) deployment/feedback loops, (5) evaluation bias.

**Examples:** biased resume screening, higher toxicity scores for dialects (e.g., African-American English), gender bias in translation ("He is a doctor, she is a nurse").

**Identifying/measuring bias:** bias benchmarks (StereoSet, CrowS-Pairs, BBQ), WEAT tests on embeddings, counterfactual testing (swap "he"→"she"), group metrics – **demographic parity**, **equalized odds**, audits and red-teaming.

**Mitigation:**
| Stage | Techniques |
|---|---|
| Data (pre-processing) | Balance/augment data, counterfactual data augmentation, filter toxic data |
| Model (in-processing) | Debiased embeddings, fairness constraints/regularisers, adversarial debiasing |
| Output (post-processing) | Filters, re-ranking, safety classifiers |
| Alignment | RLHF/constitutional methods, diverse human feedback |
| Governance | Documentation (model cards), transparency, diverse teams, continuous audits |

### H. Keywords
`bias`, `fairness`, `stereotype`, `demographic parity`, `equalized odds`, `counterfactual augmentation`, `debiasing`, `red-teaming`, `model cards`, `WEAT`.

---

## 4. Explainability and Interpretability  ⭐ P2

### A. Definition
- **Interpretability:** the degree to which a human can *understand the internal mechanism* of a model (how inputs → outputs) – e.g., decision trees, linear models.
- **Explainability:** the ability to provide *human-understandable reasons for a specific prediction*, often post-hoc for black-box models.

### B. Simple Explanation
Interpretability = "I can look inside the machine and understand how it works." Explainability = "The machine can tell me *why* it gave this answer."

### E. Comparison Table  (Explainability vs Interpretability)
| Basis | Interpretability | Explainability |
|---|---|---|
| Meaning | Understanding **how the model works internally** | Explaining **why a prediction was made** |
| Applies to | Inherently simple/transparent models | Complex black-box models (post-hoc) |
| Focus | Model structure and parameters | Individual decisions |
| Example | Reading coefficients of logistic regression | LIME/SHAP showing which words caused "spam" |

### C. Techniques for NLP models
Attention visualisation, **LIME** (local surrogate), **SHAP** (Shapley contributions), saliency/gradient-based attribution, probing classifiers, counterfactual explanations, chain-of-thought rationales.

**Importance:** trust, **transparency**, debugging, accountability, regulatory compliance (e.g., healthcare, finance, law), detecting bias.
**Challenges for LLMs:** billions of parameters, non-linear interactions, attention ≠ explanation, explanations can be unfaithful, trade-off between accuracy and interpretability.

### H. Keywords
`black box`, `LIME`, `SHAP`, `attention visualisation`, `saliency`, `probing`, `transparency`, `post-hoc`, `accountability`, `counterfactual`.

---

## 5. Evaluating Reliability and Performance of LLMs  ⭐ P1

> Direct answer to Mid-2 Q4(a): *"Discuss approaches used to evaluate the reliability and performance of LLMs, with reference to factual consistency, bias, fairness and explainability."*

### C. Detailed Explanation
| Dimension | Approaches / Metrics |
|---|---|
| **Language quality / performance** | Perplexity, BLEU, ROUGE, BERTScore (semantic similarity), task accuracy / F1 |
| **Benchmarks (capability)** | MMLU (knowledge), GLUE/SuperGLUE (understanding), HellaSwag (commonsense), GSM8K (maths), HumanEval (code) |
| **Factual consistency** | NLI-based and QA-based checks, claim verification, TruthfulQA, RAG-grounding, citation checking |
| **Bias & fairness** | StereoSet, CrowS-Pairs, BBQ, counterfactual tests, demographic parity/equalized odds, toxicity (RealToxicityPrompts) |
| **Explainability** | LIME, SHAP, attention/saliency maps, rationale (CoT) faithfulness checks |
| **Robustness & safety** | Adversarial prompts, red-teaming, prompt-injection tests, jailbreak resistance |
| **Calibration / reliability** | Does the model's confidence match accuracy? Consistency over repeated runs |
| **Human evaluation** | Ratings for fluency, relevance, correctness, helpfulness, harmlessness; pairwise comparison |
| **LLM-as-a-judge** | Strong LLM scores outputs (scalable, but can be biased) |

```
 Evaluation of LLMs
 ├── Automatic  : perplexity, BLEU/ROUGE, BERTScore, benchmarks, NLI checks
 ├── Human      : fluency, correctness, helpfulness, harmlessness
 ├── Ethical    : bias, fairness, toxicity audits
 ├── Trust      : factual consistency, explainability, calibration
 └── Safety     : red-teaming, robustness
```
**Challenges:** benchmark contamination, metric limitations, subjective human ratings, evolving risks → use a **combination** of automatic + human evaluation.

### G. Exam Answer (outline)
Intro (why evaluation is needed) → classification (automatic vs human) → performance metrics → factual consistency methods → bias & fairness methods → explainability methods → robustness/safety → challenges → conclusion (multi-dimensional evaluation).

### H. Keywords
`benchmarks`, `perplexity`, `BERTScore`, `MMLU`, `TruthfulQA`, `human evaluation`, `LLM-as-judge`, `red-teaming`, `calibration`, `robustness`.

---

## 6. Ethical Issues of LLMs  ⭐ P3
Bias & discrimination · Misinformation/hallucination · Privacy & data leakage (training on personal data) · Copyright/intellectual property · Misuse (phishing, deepfakes, malware) · Toxic content · Environmental cost · Job displacement · Lack of transparency/accountability · Over-reliance. **Mitigation:** responsible AI practices, alignment, regulation, audits, privacy-preserving training, watermarking, human oversight.

---

## 7. Multimodal NLP  ⭐ P1

### A. Definition
**Multimodal NLP** is the field that develops models able to process, relate and generate information from **multiple modalities** – such as **text, images, audio and video** – together with language.

### B. Simple Explanation
Humans understand the world using eyes, ears and language together. Multimodal models do the same: for example, look at a photo and answer questions about it in text.

### C. Detailed Explanation
**Unimodal vs Multimodal**
| Basis | Unimodal | Multimodal |
|---|---|---|
| Input | One modality (text only) | Two or more (text + image + audio) |
| Example | BERT, GPT-2 | CLIP, Flamingo, GPT-4o |
| Capability | Limited to one data type | Cross-modal understanding/generation |

**Integration / fusion strategies:** (1) *Early fusion* – combine raw features; (2) *Late fusion* – combine separate model outputs; (3) *Joint/shared embedding space* – map modalities into the same vector space (CLIP); (4) *Cross-attention* – one modality attends to another (Flamingo).

```
 Image ─► Image Encoder ─┐
                         ├─► Fusion (shared space / cross-attention) ─► Language model/decoder ─► Text/Image/Audio output
 Text  ─► Text  Encoder ─┘
```
**Applications:** image captioning, visual question answering (VQA), text-to-image search, document/chart understanding, voice assistants, accessibility (image descriptions), robotics, medical imaging reports.
**Challenges:** aligning modalities, large paired data needs, compute, hallucination about images, bias.

---

## 7.1 CLIP  ⭐ P1

### A. Definition
**CLIP (Contrastive Language–Image Pre-training)** (OpenAI, 2021) learns a **shared embedding space for images and text** by training an image encoder and a text encoder with a **contrastive loss** on ~400 million image–text pairs from the internet.

### C. Architecture and Working
```
 Images ─► Image Encoder (ViT / ResNet) ─► image embeddings I1..In ─┐
                                                                    ├─► similarity matrix (n×n)
 Captions ─► Text Encoder (Transformer) ─► text embeddings T1..Tn ──┘   maximise diagonal (matching pairs)
                                                                        minimise off-diagonal (non-matching)
```
1. Batch of N image–caption pairs; encode both into vectors.
2. Compute cosine similarity for all N×N image–text combinations.
3. **Contrastive loss** pulls matching pairs together and pushes mismatched pairs apart.
4. **Zero-shot classification:** convert class names into prompts ("a photo of a dog"), encode them, and choose the label whose text embedding is most similar to the image embedding.

**Capabilities:** zero-shot image classification, image–text retrieval, guiding image-generation models. **Limitations:** does not generate text, weak on counting/fine-grained tasks, inherits web bias.

---

## 7.2 Flamingo  ⭐ P1

### A. Definition
**Flamingo** (DeepMind, 2022) is a **visual language model** that can perform **few-shot** vision–language tasks by connecting a **frozen pretrained vision encoder** with a **frozen pretrained large language model** using new trainable bridging layers.

### C. Architecture and Working
```
 Image / Video ─► Vision Encoder (frozen) ─► Perceiver Resampler ─► fixed set of visual tokens ─┐
                                                                                               ▼
 Interleaved text ─► Frozen LLM layers  ◄── Gated Cross-Attention layers (new, trainable) ─► Generated text
```
- **Perceiver Resampler:** converts variable-size visual features into a fixed number of visual tokens.
- **Gated cross-attention layers** inserted between frozen LLM layers let text tokens attend to visual tokens (gate starts at zero, so the LLM's language ability is preserved).
- Handles **interleaved image/video and text** input; works with few examples in the prompt (**few-shot in-context learning**).
**Capabilities:** visual question answering, image/video captioning, visual dialogue, few-shot adaptation. **Limitations:** text-only output, compute-heavy, can hallucinate, limited public availability.

---

## 7.3 GPT-4o  ⭐ P1

### A. Definition
**GPT-4o ("omni")** (OpenAI, 2024) is a **natively multimodal** model trained end-to-end across **text, vision and audio**, able to accept combinations of these inputs and produce text (and audio/image) outputs in a single model.

### C. Detailed Explanation
- A *single neural network* handles all modalities (older voice pipelines chained separate speech-to-text → LLM → text-to-speech models and lost tone/emotion).
- **Features:** real-time voice conversation with low latency, understands images/screenshots/charts, can translate speech live, strong multilingual performance, expressive audio output.
- **Applications:** voice assistants, tutoring, real-time translation, accessibility tools for visually impaired users, document/chart analysis, customer support.
- **Limitations:** hallucination, bias, privacy/safety concerns with voice/image data, closed model, cost.

### E. Comparison – CLIP vs Flamingo vs GPT-4o  ⭐ P1 (Mid-2 Q4b)
| Basis | **CLIP** | **Flamingo** | **GPT-4o** |
|---|---|---|---|
| Developer / year | OpenAI, 2021 | DeepMind, 2022 | OpenAI, 2024 |
| Modalities | Image + text | Image/video + text (text output) | Text + image + audio (+ video frames) in, text/audio out |
| Core idea | Contrastive learning of a shared image–text embedding space | Frozen vision encoder + frozen LLM bridged by Perceiver Resampler & gated cross-attention | Single end-to-end multimodal network |
| Output | Similarity scores/embeddings (no text generation) | Generated text | Generated text/audio (conversational) |
| Learning style | Zero-shot via prompts | **Few-shot** in-context | Prompting, conversational, instruction-following |
| Key capability | Zero-shot image classification, retrieval | VQA, captioning, few-shot visual tasks | Real-time voice+vision assistant, multilingual |
| Applications | Image search, content moderation, text-to-image guidance | Visual QA, video description, visual dialogue | Voice assistants, tutoring, live translation, accessibility |
| Availability | Open-source weights | Research model (not widely released) | Commercial API/product |

### G. Exam Answer (12-mark outline)
Definition of multimodal NLP → unimodal vs multimodal → fusion strategies/diagram → CLIP (architecture, contrastive learning) → Flamingo (architecture) → GPT-4o (features) → comparison table → applications & challenges → conclusion.

### H. Keywords
`modality`, `contrastive learning`, `shared embedding space`, `zero-shot`, `ViT`, `Perceiver Resampler`, `gated cross-attention`, `few-shot`, `VQA`, `omni model`, `end-to-end`, `fusion`.

---

## 8. Future Directions: Reasoning, Alignment, Multilingual Models

### 8.1 Alignment  ⭐ P1

**A. Definition:** **Alignment** is the process of making an LLM's behaviour consistent with **human values, intentions and safety goals** – so it is **helpful, honest and harmless (HHH)**.

**B. Simple Explanation:** A model trained only to predict text may produce harmful or unhelpful output. Alignment trains it to do what humans actually *want*.

**C. Techniques:**
- **Supervised fine-tuning / instruction tuning** on high-quality human demonstrations.
- **RLHF (Reinforcement Learning from Human Feedback):** train a reward model from human preference rankings, then optimise the LLM with RL (e.g., PPO).
- **Direct Preference Optimization (DPO)** – simpler preference training without separate RL.
- **Constitutional AI / RLAIF** – model critiques itself against written principles.
- Red-teaming, safety filters, guardrails.

```
 Pre-trained LLM ─► Supervised fine-tuning ─► Reward model (human preferences) ─► RL optimisation ─► Aligned LLM
```
**Challenges:** conflicting human values, reward hacking, scalability of oversight, cultural differences.

**2-mark answer:** "Alignment is making LLMs behave in accordance with human intent and values (helpful, honest, harmless), typically using instruction tuning, RLHF and safety training."

### 8.2 Reasoning
**Current trend:** improving multi-step logical, mathematical and commonsense reasoning. **Approaches:** chain-of-thought, self-consistency (sample many reasoning paths, majority vote), tree-of-thought, tool-augmented reasoning (calculator/code), reasoning-focused models trained with reinforcement learning to "think" longer at inference time, neuro-symbolic methods. **Challenges:** reasoning errors, faithfulness of explanations, cost.

### 8.3 Multilingual Models  ⭐ P3
**Definition:** language models trained on many languages in a shared representation (e.g., **mBERT**, **XLM-R**, multilingual GPT/mT5) so they can understand and generate multiple languages and transfer knowledge across them.
**Importance:** serves the global population, **cross-lingual transfer** (train in English, apply in Hindi/Telugu), translation, inclusive access.
**Challenges:** **low-resource languages** with scarce data, data imbalance (English dominance), script/tokenisation differences, cultural bias, evaluation benchmarks lacking.
**Future scope:** data augmentation, transfer learning, community datasets, language-specific adapters, better tokenisers, speech + text multilingual models.

---

## 9. Quick 2-Mark Sheet (Unit V)

| Question | Crisp answer |
|---|---|
| Hallucination | Fluent but factually wrong/unsupported text generated by an LLM. |
| Factual consistency | Generated text agrees with (is supported by) the source or real-world facts. |
| Two causes of hallucination | Noisy/incomplete training data; next-token prediction objective favouring fluency over truth. |
| Bias | Systematic unfair prejudice in model output toward certain groups, inherited from data/design. |
| Fairness | Equitable treatment of all groups by an NLP system. |
| Explainability | Providing human-understandable reasons for a model's prediction. |
| Explainability vs interpretability | Interpretability = understand internal workings; explainability = justify specific outputs (post-hoc). |
| Why transparency | Builds trust, enables accountability, helps detect errors and bias. |
| Multimodal NLP | NLP with multiple modalities (text, image, audio, video). |
| CLIP | OpenAI model that aligns images and text in a shared space via contrastive learning; enables zero-shot classification. |
| Flamingo | DeepMind visual-language model combining frozen vision encoder and LLM via cross-attention for few-shot tasks. |
| GPT-4o | OpenAI's natively multimodal "omni" model (text, vision, audio) with real-time voice interaction. |
| Unimodal vs multimodal | One data type vs several data types processed together. |
| Alignment | Making LLMs follow human values/intent – helpful, honest, harmless (RLHF). |
| Multilingual models | Models trained on many languages (mBERT, XLM-R) enabling cross-lingual transfer. |
