# Current Academic Year — Mid-2 Question Paper — Complete Answers

**Units covered:** Unit IV (Q1, Q2, Q3, Q6) and Unit V (Q4, Q5, Q7, Q8)

> This is the current academic year's Mid-2 paper, distinct from the previous year's Mid-2 paper (see `previous_year_paper.md`, Paper C). This paper draws heavily and almost entirely from **Units IV and V**, both marked Priority 1 in this preparation plan.

---

## PART A (5×2=10M) — Answer ALL Questions

### Q1. Explain the concept of Scaling Laws in Deep Learning. (2 Marks) — *Unit IV, Topic: Scaling Laws*

**Answer:** See **Unit IV, Section 1**. Scaling laws describe predictable, empirical power-law relationships between a model's performance (loss) and factors like model size (N), dataset size (D), and compute (C): `L ∝ N^(-α)`. As these factors increase, loss decreases but with diminishing returns. The Chinchilla scaling law demonstrated that model size and training data size should be scaled together proportionally for compute-optimal performance, rather than scaling model size alone.

**Keywords:** Power-law, model size, dataset size, compute, diminishing returns, Chinchilla.

---

### Q2. What is Mixture-of-Experts (MoE) Architecture? (2 Marks) — *Unit IV, Topic: MoE*

**Answer:** See **Unit IV, Section 2**. MoE is an architecture where multiple specialized sub-networks ("experts") exist, and a gating/router network dynamically selects only a small subset (top-k) of experts to process each input token, rather than using the entire network for every input. This allows the model to have a very large total parameter count (high capacity) while keeping the actual compute cost per token low, since only the selected experts are activated (sparse activation).

**Keywords:** Experts, gating network, router, top-k routing, sparse activation.

---

### Q3. Explain Flash Attention and Speculative Decoding. (2 Marks) — *Unit IV, Topics: FlashAttention, Speculative Decoding*

**Answer:**
- **FlashAttention** (see Unit IV, Section 3): An IO-aware, exact attention algorithm that reduces memory overhead by using tiling (computing attention in small blocks that fit in fast GPU SRAM) and recomputation, avoiding the need to store the full N×N attention matrix in slower HBM memory — producing the same output as standard attention but much faster and more memory-efficient, especially for long sequences.
- **Speculative Decoding** (see Unit IV, Section 5): A technique to speed up text generation where a small, fast "draft" model proposes several candidate tokens ahead, which a larger "target" model then verifies in a single parallel forward pass — accepted tokens are kept, and any mismatch triggers regeneration from that point, speeding up generation without changing output quality.

**Keywords:** Tiling, HBM/SRAM, recomputation, draft model, target model, parallel verification.

---

### Q4. What are multimodal generative models? (2 Marks) — *Unit V, Topic: Multimodal Models*

**Answer:** See **Unit V, Section 1**. Multimodal generative models are AI models designed to process and/or generate information across multiple data modalities (text, images, audio, video) simultaneously, by learning joint representations connecting these modalities. Examples include CLIP (image-text alignment via contrastive learning) and unified models like GPT-4o, Gemini, and Claude 3, which can jointly reason over vision, language, and sometimes audio within a single architecture.

**Keywords:** Multimodal, joint representation, CLIP, cross-modal reasoning.

---

### Q5. Explain AI alignment and hallucinations in Generative AI. (2 Marks) — *Unit V, Topic: Alignment & Hallucinations*

**Answer:** See **Unit V, Section 4**.
- **Hallucination:** When an LLM generates fluent, confident-sounding output that is factually incorrect or fabricated, because the model is optimized to predict statistically plausible text, not to verify truth.
- **AI Alignment:** The broader effort to ensure an AI system's behavior and outputs match human values, intentions, and safety requirements, typically achieved through techniques like RLHF, DPO, ORPO, and red-teaming.

**Keywords:** Hallucination, alignment, RLHF, red-teaming, factual consistency.

---

## PART B (2×10=20M) — Answer any TWO questions

### Q6(a). Explain the role of Quantization and Token Merging in reducing computational cost and improving the inference speed of Large Language Models. (10 Marks) — *Unit IV, Topic: Inference Efficiency*

**Answer:**
1. **Introduction:** As LLMs grow larger, deploying them efficiently requires techniques to reduce computational and memory costs without significantly sacrificing output quality. Quantization and Token Merging are two key such techniques.
2. **Quantization:**
   - Definition: Reducing the numerical precision used to represent model weights/activations (e.g., FP32 → FP16 → INT8 → INT4).
   - Mechanism: Each parameter requires fewer bits to store and compute, directly reducing memory footprint and often speeding up computation (since lower-precision arithmetic can be faster on supporting hardware).
   - **Example:** A 1-billion-parameter model in FP32 (4 bytes/param) requires 4 GB of memory. Quantized to INT8 (1 byte/param), it requires only 1 GB — a 4x reduction, enabling deployment on smaller/cheaper hardware with minimal accuracy loss.
3. **Token Merging:**
   - Definition: Identifying and merging similar or redundant tokens during the forward pass of a model (particularly effective in Vision Transformers, also applicable in language models), reducing the effective sequence length processed by subsequent layers.
   - Mechanism: Tokens with highly similar representations are combined into a single representative token, reducing the total number of tokens the model must process in deeper layers, thereby reducing computation (since attention cost scales with sequence length).
4. **Combined Benefit:** Both techniques reduce the computational and memory burden of running large models, enabling faster inference, lower latency, and deployment on resource-constrained hardware (including edge devices), which is critical for real-world, cost-effective industry deployment of generative AI.
5. **Diagram:**
```
Quantization: FP32 weight (4 bytes) --> INT8 weight (1 byte)  [4x memory reduction]
Token Merging: [tok1, tok2(similar to tok1), tok3, tok4] --> [merged(tok1,tok2), tok3, tok4]  [shorter sequence]
```
6. **Conclusion:** Quantization and Token Merging are complementary efficiency techniques — quantization reduces the cost of representing and computing with each parameter, while token merging reduces the number of tokens that need to be processed — together enabling practical, cost-effective deployment of large generative AI models.

**Keywords:** Quantization, FP32/FP16/INT8, token merging, memory footprint, inference latency, edge deployment.

---

### Q6(b). Describe the Mamba and RWKV architectures. How do they provide efficient alternatives to traditional Transformer architectures? (10 Marks) — *Unit IV, Topic: Attention-Free Architectures*

**Full Answer:** See **Unit IV, Section 6, Part G** — complete structured answer covering: motivation (Transformer's O(N²) attention cost and growing KV cache), Mamba's selective State Space Model mechanism (input-dependent parameters, linear-time complexity), RWKV's hybrid parallelizable-training/RNN-like-inference design, the comparison table (Transformer vs Mamba vs RWKV), and real-world long-sequence application examples.

---

### Q7(a). Explain the architecture and applications of multimodal AI models such as GPT-4o, Gemini and Claude 3. How do these models integrate vision, language and tools? (10 Marks) — *Unit V, Topic: Multimodal Agent Systems*

**Full Answer:** See **Unit V, Section 1, Part G** — complete structured answer covering the definition of multimodal models, CLIP's contrastive learning approach, and the unified architecture of GPT-4o/Gemini/Claude 3 that jointly processes vision, language, and (in some cases) audio, combined with function calling/tool use for agentic multimodal reasoning. Includes applications such as visual question answering, document/chart understanding, and multimodal customer support, plus the relevant diagrams.

---

### Q7(b). Discuss the role of AI agents such as AutoGPT in autonomous task execution. Explain the importance of planning, reflection and tool usage in agentic systems. (10 Marks) — *Unit V, Topic: AI Agents*

**Full Answer:** See **Unit V, Section 3, Part G** — complete structured answer covering AutoGPT's definition, the agentic loop (planning → tool use → memory → reflection) with diagram, the importance of each component, a real-world example, and limitations of autonomous agent systems.

---

### Q8(a). Explain Synthetic Data Generation and its importance in training Generative AI models. Discuss its advantages and limitations. (10 Marks) — *Unit V, Topic: Synthetic Data*

**Full Answer:** See **Unit V, Section 5, Part G** — complete structured answer covering the definition of synthetic data generation, its process/diagram, advantages (cost reduction, privacy preservation, class balancing, scalability), and limitations (distribution mismatch, model collapse risk, bias amplification).

---

### Q8(b). Discuss the ethical, legal and policy challenges associated with Generative AI, with reference to the EU AI Act, India's Digital India guidelines on AI, AI Bill of Rights and open-source models such as Mistral and LLaMA 3. (10 Marks) — *Unit V, Topic: Legal & Policy Frameworks*

**Full Answer:** See **Unit V, Section 6, Part G** — complete structured answer covering: introduction to Generative AI's ethical/legal challenges, the EU AI Act's risk-tiered regulatory approach, the US AI Bill of Rights' five non-binding principles, India's innovation-focused Digital India AI guidelines, the policy tension around open-weight models (Mistral, LLaMA 3), the comparison table across frameworks, and a concluding synthesis of global regulatory philosophies.

---

## Analysis Note

This paper tests **Units IV and V almost exclusively**, with no questions from Units I, II, or III. This is a notable shift compared to the previous year's papers (which emphasized Units I and III more heavily) and reinforces that **Unit IV and Unit V are current high-priority units** for this academic year's semester examination — see the main recommendation summary for full reasoning.
