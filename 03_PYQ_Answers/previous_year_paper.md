# Previous Year Question Papers — Complete Answers

This file contains answers to **three previous year (last year) mid-examination papers** supplied by the student:
- **Paper A — Last Year Mid-1 (Version 1)** — corresponds to the uploaded screenshot with Q1–Q8 covering Unit I topics.
- **Paper B — Last Year Mid-1 (Version 2)** — corresponds to the uploaded screenshot with Q1–Q8 covering Units I, II, III (a different question set/section, also labeled Mid-1).
- **Paper C — Last Year Mid-2** — corresponds to the uploaded screenshot with Q1–Q8 covering Units III and IV.

> **Note on classification:** These are the student-confirmed mappings of the uploaded images. All questions below are reproduced as closely as possible to the original wording from the screenshots. Some handwriting/print in the source images was slightly unclear in minor spots — in such cases the clearest reasonable reading is used, with no invented content beyond that.

---

# PAPER A — Last Year Mid-1 (Version 1)

**Unit covered:** Primarily Unit I (Text Generation & Language Models)

## PART A — Answer All Questions (5 × 2 = 10 Marks)

### Q1. Define GEN AI. Differentiate AI, ML, DL and GEN AI. (2 Marks) — *Unit I (foundational/introductory)*

**Answer:**
**Generative AI (GenAI)** is a subset of artificial intelligence focused on creating new, original content (text, images, audio, code) that resembles human-created data, by learning the underlying patterns and distributions of training data.

| Term | Definition | Example |
|---|---|---|
| **AI (Artificial Intelligence)** | Broad field of building systems that mimic human intelligence (reasoning, learning, perception) | Chess-playing programs, expert systems |
| **ML (Machine Learning)** | Subset of AI where systems learn patterns from data rather than being explicitly programmed | Spam email classifiers |
| **DL (Deep Learning)** | Subset of ML using multi-layered neural networks to learn hierarchical representations | Image recognition using CNNs |
| **Gen AI** | Subset of DL/AI focused specifically on **generating new content** rather than just classifying/predicting | ChatGPT, DALL·E |

**Keywords:** AI, ML, DL, Generative AI, hierarchy of subsets, content generation.

---

### Q2. What is attention in Text GEN AI and explain how do we calculate the attention of a given token in a text sequence? (2 Marks) — *Unit I, Topic: Attention Mechanism*

**Answer:** See **Unit I, Section 2** for the full explanation. In brief: Attention allows a model to focus on relevant parts of the input when processing each token. It is calculated by: (1) projecting each token into Query (Q), Key (K), Value (V) vectors; (2) computing `score = Q·K^T / √d_k`; (3) applying softmax to get attention weights; (4) computing a weighted sum of Value vectors using these weights.

**Keywords:** Query, Key, Value, scaled dot-product, softmax.

---

### Q3. What is LLM and explain briefly the components of LLM. (2 Marks) — *Unit I, Topic: LLM Components*

**Answer:** See **Unit I, Section 3**. Briefly: An LLM is a large Transformer-based model trained on massive text data to predict/generate language. Key components: Tokenizer, Embedding layer, Positional Encoding, stacked Transformer blocks (self-attention + feed-forward), and an output softmax layer over the vocabulary.

**Keywords:** Tokenizer, embeddings, positional encoding, Transformer blocks, output layer.

---

### Q4. What is the significance of Pre-Training and fine-tuning in Transformed based Language Models? (2 Marks) — *Unit I, Topic: Pre-training & Fine-tuning*

**Answer:** See **Unit I, Section 4**. Briefly: Pre-training on massive unlabeled data builds general language understanding (transfer learning foundation); fine-tuning then adapts this general model to specific tasks/domains using smaller labeled datasets, making LLMs both broadly capable and task-adaptable — this two-stage approach avoids training from scratch for every application.

**Keywords:** Self-supervised learning, transfer learning, task adaptation.

---

### Q5. Briefly explain the LangChain LLM Agent and how it operates. (2 Marks) — *Unit I, Topic: LLM Agents*

**Answer:** See **Unit I, Section 7**. Briefly: LangChain is a framework providing chains, tools, memory, and agents for building LLM-powered applications. A LangChain agent operates in a loop: receive query → LLM reasons about needed action → calls a tool/API → receives observation → LLM decides to act again or give a final answer.

**Keywords:** Chains, tools, memory, agent loop, observation.

---

## PART B — Answer Any Two Questions (2 × 10 = 20 Marks)

### Q6(a). What is RNN? Explain with its operational architecture. What is its relevance in GEN AI? (10 Marks) — *Unit I, Topic: RNN*

**Full Answer:** See **Unit I, Section 1, Part G** (University Examination Answer) for the complete structured 10-mark answer, covering: definition, operational architecture (unfolded diagram, hidden state recurrence formula), limitations (vanishing gradient), and relevance to Generative AI as the conceptual foundation for sequence modeling.

---

### Q6(b). What is LSTM Neural Network? Differentiate this with RNN with an example. (10 Marks) — *Unit I, Topic: LSTM*

**Answer:**
1. **Definition:** LSTM is a special RNN variant with gating mechanisms (forget, input, output gates) and a cell state that solves the vanishing gradient problem, enabling learning of long-range dependencies. (See Unit I, Section 1 for full architecture and formulas.)
2. **Architecture:** Explain the three gates and cell state update equations (see Unit I diagram and formulas).
3. **Differentiation with RNN:**

| Feature | RNN | LSTM |
|---|---|---|
| Memory | Single hidden state only | Cell state (long-term) + hidden state (short-term) |
| Gating | None | Forget, Input, Output gates |
| Long-range dependency handling | Poor | Good |
| Example | Predicting the next word in "The sky is ___" (short context) | Predicting the next word in a paragraph where the subject was introduced several sentences earlier (requires long-term memory) |

4. **Example:** In the sentence "I grew up in France... [many words later] ...so I speak fluent **French**," an RNN may forget "France" by the time it needs to predict "French," while an LSTM's cell state can retain this information across the long gap due to its forget/input gate mechanism.
5. **Conclusion:** LSTM's gating mechanism makes it significantly more effective than plain RNN for tasks involving longer text sequences, which is why it was widely used before Transformers became dominant.

**Keywords:** Cell state, forget/input/output gate, long-range dependency, vanishing gradient.

---

### Q7(a). What is Transformer in GEN AI? Explain its architecture with neat diagram and different components of the architecture. (10 Marks) — *Unit I, Topic: Transformer Architecture*

**Full Answer:** See **Unit I, Section 1** for full details: definition, self-attention formula, multi-head attention, positional encoding, feed-forward layers, Add & Norm (residual + layer norm), and the encoder-decoder structure, with the Mermaid diagram provided.

---

### Q7(b). What are the decoding Strategies in Transformer based Language models. Discuss about any two strategies. How do we decide when to use which decoding strategy? (10 Marks) — *Unit I, Topic: Decoding Strategies*

**Full Answer:** See **Unit I, Section 5, Part G** for the complete structured answer covering all strategies, detailed discussion of two (recommend Beam Search + Temperature/Top-p Sampling), the comparison table, and task-based decision criteria.

---

### Q8(a). What are LLM Agents? List out important Agents being used in Text GEN AI. (10 Marks) — *Unit I, Topic: LLM Agents*

**Full Answer:** See **Unit I, Section 7, Part G**. Covers definition of LLM agents, LangChain's agent operation loop, and brief descriptions of AutoGen (multi-agent collaboration) and the ReAct framework (Thought-Action-Observation loop) as important agent approaches, plus real-world application examples.

---

### Q8(b). What is Foundation model in text Gen? Briefly explain the GPT model. (10 Marks) — *Unit I, Topic: Foundation Models*

**Full Answer:** See **Unit I, Section 6, Part G**. Covers the definition of foundation models, GPT's decoder-only architecture, causal language modeling objective, autoregressive generation process, and applications.

---

# PAPER B — Last Year Mid-1 (Version 2)

**Units covered:** Unit I (Q1, Q2, Q6a), Unit II (Q3, Q7), Unit III (Q4, Q5, Q8)

## PART A — Answer All Questions (5 × 2 = 10 M)

### Q1. What is the difference between pre-training and fine-tuning of a Large Language Model (LLM)? (2 Marks) — *Unit I, Topic: Pre-training vs Fine-tuning*

**Answer:** See **Unit I, Section 4** comparison table. Briefly: Pre-training uses massive unlabeled data with self-supervised objectives to build general language understanding (expensive, done once); fine-tuning uses smaller labeled, task-specific data to adapt the pre-trained model to a particular application (cheaper, done repeatedly for different tasks).

---

### Q2. What is the purpose of temperature in text generation from a language model? (2 Marks) — *Unit I, Topic: Decoding Strategies (Temperature Sampling)*

**Answer:** See **Unit I, Section 5, Point 4**. Briefly: Temperature (`T`) controls the randomness/creativity of generated text by reshaping the output probability distribution before sampling: `P_i = exp(logit_i/T) / Σexp(logit_j/T)`. Low `T` (<1) makes output more deterministic/confident (sharper distribution); high `T` (>1) makes output more random/creative (flatter distribution); `T=1` leaves the distribution unchanged.

**Keywords:** Temperature, probability distribution, sharpening, flattening.

---

### Q3. Differentiate between GANs and Diffusion Models. (2 Marks) — *Unit II, Topic: GANs vs Diffusion*

**Answer:** See **Unit II, Section 3, Part E** comparison table. Briefly:

| Aspect | GANs | Diffusion Models |
|---|---|---|
| Mechanism | Adversarial (Generator vs Discriminator) | Iterative denoising of noise |
| Training stability | Often unstable | More stable |
| Sampling speed | Fast | Slower (multi-step), improving with distillation (SD Turbo) |

---

### Q4. What is in-context learning? Give one example. (2 Marks) — *Unit III, Topic: In-Context Learning*

**Answer:** See **Unit III, Section 1**. In-context learning is the ability of an LLM to perform a new task by conditioning on examples provided within the prompt itself, without updating model weights. **Example:** Providing a few translation examples ("sea otter → loutre de mer, cheese → fromage") in the prompt, after which the model correctly translates a new word it wasn't explicitly trained to translate, purely from the pattern shown in-context.

---

### Q5. What is Direct Preference Optimization (DPO), and how does it differ from traditional Reinforcement Learning from Human Feedback (RLHF)? (2 Marks) — *Unit III, Topic: DPO vs RLHF*

**Answer:** See **Unit III, Section 4**. DPO directly optimizes a language model on preference data using a simple classification-style loss, without needing a separate reward model or reinforcement learning loop — unlike RLHF, which requires three stages (SFT, reward model training, and PPO-based RL fine-tuning). DPO is simpler, more stable, and computationally cheaper than RLHF while achieving comparable alignment quality.

---

## PART B — Answer Any Two Questions (2 × 10 = 20 M)

### Q6(a). Explain the architecture and working principles of Transformer-based language models. Discuss the role of self-attention and positional encoding. (10 Marks) — *Unit I, Topic: Transformer Architecture*

**Full Answer:** See **Unit I, Section 1**. Cover: overall architecture diagram, self-attention mechanism (Q, K, V, scaled dot-product formula), why positional encoding is needed (Transformers have no inherent sense of order since they process all tokens in parallel), multi-head attention, feed-forward layers, and Add & Norm.

---

### Q6(b). Explain different decoding strategies used in LLMs, including greedy decoding, beam search, temperature sampling, and top-k/top-p sampling. Compare their effects on generated text. (10 Marks) — *Unit I, Topic: Decoding Strategies*

**Full Answer:** See **Unit I, Section 5** — covers all four/six strategies in full detail (greedy, beam search, temperature, top-k, top-p) with formulas, diagram, and the comparison table showing determinism vs diversity trade-offs.

---

### Q7(a). Explain the working principles of Convolutional Neural Networks (CNNs), Vision Transformers (ViTs), and Generative Adversarial Networks (GANs). Highlight their major applications. (10 Marks) — *Unit II, Topic: CNN, ViT, GAN*

**Answer:**
1. **CNN:** See Unit II, Section 1 — convolution, pooling, hierarchical feature extraction. **Applications:** image classification, facial recognition, medical imaging.
2. **ViT:** See Unit II, Section 1 — patch embedding, positional encoding, Transformer encoder, CLS token. **Applications:** image classification at scale, backbone for multimodal models like CLIP.
3. **GAN:** See Unit II, Section 2 — Generator vs Discriminator adversarial training, minimax objective. **Applications:** realistic face generation, deepfakes, image super-resolution, data augmentation.
4. **Diagram:** Include CNN diagram, ViT diagram, and GAN diagram (all provided in Unit II notes).
5. **Conclusion:** CNNs excel at local feature extraction with strong inductive bias, ViTs leverage global self-attention for large-scale learning, and GANs provide a powerful adversarial framework for realistic generative image synthesis — each suited to different generative/discriminative tasks in computer vision.

**Keywords:** Convolution, pooling, patch embedding, self-attention, Generator, Discriminator, adversarial training.

---

### Q7(b). Explain the Diffusion Model framework for image generation. Discuss the forward and reverse diffusion processes and compare diffusion models with GANs. (10 Marks) — *Unit II, Topic: Diffusion Models*

**Full Answer:** See **Unit II, Section 3, Part G** — complete structured answer covering forward/reverse diffusion processes, DDPM framework, denoising score matching, applications (DALL·E, SD XL, SD Turbo), and the GAN vs Diffusion comparison table.

---

### Q8(a). Explain prompt engineering, in-context learning, and prompt-tuning. Discuss how these techniques can improve the performance of generative AI applications. (10 Marks) — *Unit III, Topic: Prompt Engineering*

**Full Answer:** See **Unit III, Section 2, Part G**. Cover: prompt engineering definition and techniques (zero-shot, few-shot, Chain-of-Thought, role prompting), in-context learning (Unit III, Section 1), and prompt-tuning (learned soft-prompt embeddings vs manually written prompts), concluding with how each improves task alignment and reduces fine-tuning costs.

---

### Q8(b). Explain Function Calling and AI Agents. Describe how LLMs can use external tools and APIs to perform tasks such as customer support, document automation, and information retrieval. (10 Marks) — *Unit III, Topic: Function Calling*

**Full Answer:** See **Unit III, Section 5, Part G** — complete structured answer covering the function calling workflow (definitions provided → model decides → structured call generated → execution → result fed back → final response), with the diagram, and specific application examples (customer support via order-status APIs, document automation, information retrieval).

---

# PAPER C — Last Year Mid-2

**Units covered:** Unit III (Q1, Q2, Q6) and Unit IV (Q3, Q4, Q5, Q7, Q8)

## PART A (10 marks) — Answer ALL Questions

### Q1. Compare LoRA and full fine-tuning using a small example. (2 Marks) — *Unit III, Topic: LoRA*

**Full Answer:** See **Unit III, Section 3, Part G** — includes the full numerical example (1000×1000 weight matrix: full fine-tuning = 1,000,000 parameters vs LoRA rank-8 = 16,000 parameters, ~1.6% of full fine-tuning), plus the comparison table.

---

### Q2. Briefly explain two types of prompt engineering techniques. (2 Marks) — *Unit III, Topic: Prompt Engineering*

**Answer:** See **Unit III, Section 2**. Two commonly explained techniques:
1. **Chain-of-Thought (CoT) Prompting:** Instructs the model to reason step-by-step before giving a final answer (e.g., "Let's think step by step"), significantly improving performance on multi-step reasoning/math problems.
2. **Few-shot Prompting:** Provides a handful of input-output examples within the prompt to demonstrate the desired pattern/format, helping the model generalize to a new, similar input.

---

### Q3. Illustrate how function calling enables LLMs to use external tools or APIs. (2 Marks) — *Unit III, Topic: Function Calling*

**Answer:** See **Unit III, Section 5**. Briefly: The LLM is given a schema of available functions; based on the user's query, it decides which function to call and generates structured arguments (e.g., JSON); the application executes the actual API call outside the model; the result is returned to the LLM, which uses it to generate the final natural-language response. This allows LLMs to access real-time data and perform real-world actions beyond their own static training knowledge.

---

### Q4. Recall scaling power laws for Large Models learning. (2 Marks) — *Unit IV, Topic: Scaling Laws*

**Answer:** See **Unit IV, Section 1**. Briefly: Scaling laws describe power-law relationships between model loss and model size (N), dataset size (D), and compute (C) — loss decreases predictably but with diminishing returns as these factors increase (`L ∝ N^-α`). The Chinchilla scaling law showed model size and data size should be scaled together proportionally for compute-optimal training.

---

### Q5. Demonstrate how quantization reduces memory usage with a simple parameter-count example. (2 Marks) — *Unit IV, Topic: Quantization*

**Answer:** See **Unit IV, Section 5**. Example: A model with 1 billion parameters in FP32 (4 bytes/parameter) requires 4 GB of memory. Quantizing to INT8 (1 byte/parameter) reduces this to 1 GB — a 4x reduction in memory usage, while typically retaining most of the model's accuracy.

---

## PART B (2 × 10 = 20 marks) — ANSWER ANY TWO

### Q1(a). Compare in-context learning with parameter-efficient fine-tuning in terms of cost, flexibility, and performance. (10 Marks) — *Unit III, Topic: ICL vs PEFT*

**Full Answer:** See **Unit III, Section 1, Part G** — complete comparison covering cost (ICL near-zero vs PEFT requiring training), flexibility (ICL allows instant task switching vs PEFT needs retraining per task), and performance (PEFT generally more reliable for complex/niche/persistent tasks), with the comparison table.

---

### Q1(b). Analyze the differences between RLHF, DPO, and ORPO for aligning models with human preference. (10 Marks) — *Unit III, Topic: RLHF/DPO/ORPO*

**Full Answer:** See **Unit III, Section 4, Part G** — complete structured answer with definitions of all three, the RLHF 3-stage pipeline diagram, DPO's direct optimization approach, ORPO's single-stage no-reference-model approach, and the full comparison table (stages, reward model, RL requirement, stability, cost).

---

### Q2(a). Briefly explain various Distributed training techniques used for large models. (10 Marks) — *Unit IV, Topic: Distributed Training*

**Full Answer:** See **Unit IV, Section 4, Part G** — covers data parallelism, model parallelism, pipeline parallelism, tensor parallelism, and ZeRO/FSDP, each with explanation of what is split and when it's used, plus diagrams and comparison table.

---

### Q2(b). Analyze the trade-offs between dense transformers and MoE transformers in terms of compute, training stability, cost and inference. (10 Marks) — *Unit IV, Topic: Mixture-of-Experts*

**Full Answer:** See **Unit IV, Section 2, Part G** — complete structured answer covering MoE architecture (gating/router, sparse activation), advantages (larger capacity without proportional compute), challenges (load balancing, training instability), and the dense vs MoE comparison table.

---

### Q3(a). Describe the architecture of FlashAttention and how it reduces memory overhead. (10 Marks) — *Unit IV, Topic: FlashAttention*

**Full Answer:** See **Unit IV, Section 3, Part G** — covers the HBM/SRAM memory bottleneck problem, FlashAttention's tiling and recomputation mechanism, running softmax, and why it produces the exact same output as standard attention while being significantly faster/more memory-efficient.

---

### Q3(b). Briefly explain various techniques used to improve inference efficiency. (10 Marks) — *Unit IV, Topic: Inference Efficiency*

**Full Answer:** See **Unit IV, Section 5, Part G** — covers Speculative Decoding (draft model + parallel verification), Token Merging (merging redundant tokens), and Quantization (precision reduction with the numerical example), including the comparison table.

---

## Summary Note
Several questions above repeat verbatim or near-verbatim across Paper A, Paper B, and Paper C (e.g., decoding strategies, LoRA, RLHF/DPO/ORPO, function calling) — these repetitions are flagged in the **Question Paper Analysis** notes and strongly indicate **high-priority topics** for the semester examination. See the unit-wise notes for the single authoritative, most detailed version of each topic's explanation.
