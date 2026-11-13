# Mid-2 Examination – Questions with Answers (Units III, IV & V)

**Paper pattern (as printed):** Part-A: Answer all questions (5 × 2 = 10 M) · Part-B: Answer any two questions (10 × 2 = 20 M); each Part-B question has parts (a) and (b) of 5 marks each.
**Units covered:** Unit III (Transformers and Pretrained Models), Unit IV (Prompting, LLMs and NLP Agents), Unit V (Ethics, Evaluation, Multimodal NLP).

> **Reading note:** The paper image is fully readable. Handwritten tick marks on the paper are ignored.
> **Depth note:** Each Part-B part carries 5 marks here. In the semester paper each question is 12 marks, so these answers are written at *full descriptive depth*; for a 5-mark answer, write the key bold points, one diagram and one table.
> **Unit mapping:** Q1(a),(b), Q2(a),(b) → Unit III · Q1(c),(d), Q3(a),(b) → Unit IV · Q1(e), Q4(a),(b) → Unit V. See `unit_3.md`, `unit_4.md`, `unit_5.md` for complete theory.

---

# PART – A (2 Marks each)

### Q1(a) Define Textual Entailment with a suitable example.
**Unit III – Applications of pretrained models | 2 Marks**

**Textual entailment** (Natural Language Inference) is the NLP task of deciding whether a **hypothesis** statement can be logically **inferred from a given premise**. The possible labels are **entailment, contradiction, neutral**.

**Example:**
- **Premise:** "A man is playing a guitar on the stage."
- **Hypothesis:** "A man is playing a musical instrument." → **Entailment** (true given the premise).
- Hypothesis "The man is sleeping." → *Contradiction*; "The man is a famous singer." → *Neutral*.

Typically solved by fine-tuning BERT/RoBERTa on `[CLS] premise [SEP] hypothesis [SEP]` (datasets: SNLI, MNLI).

**Keywords:** `premise`, `hypothesis`, `entailment`, `contradiction`, `neutral`, `NLI`.

---

### Q1(b) What are sentence embeddings? Mention any two applications.
**Unit III – Applications of pretrained models | 2 Marks**

**Sentence embeddings** are fixed-length, dense numerical vectors that capture the **meaning of a whole sentence**, so that sentences with similar meanings lie close to each other in vector space (measured by cosine similarity). They are obtained, for example, by pooling BERT token vectors or by **Sentence-BERT (SBERT)**.

**Applications (any two):**
1. **Semantic search / information retrieval** – find documents matching the meaning of a query.
2. **Paraphrase and duplicate-question detection**; also text clustering, recommendation, and retrieval in RAG.

**Keywords:** `dense vector`, `semantic similarity`, `cosine similarity`, `SBERT`, `pooling`.

---

### Q1(c) Differentiate between zero-shot and few-shot prompting with suitable examples.
**Unit IV – Prompt engineering | 2 Marks**

| Basis | Zero-shot prompting | Few-shot prompting |
|---|---|---|
| Examples in prompt | **None** – only instruction | **A few (2–5)** input–output examples |
| Relies on | Knowledge from pre-training | Pattern learned from examples (in-context learning) |
| Accuracy on complex/format-specific tasks | Lower | Higher |
| Prompt length | Short | Longer |
| **Example** | *"Classify the sentiment: 'The movie was fantastic!'"* → Positive | *"Loved it → Positive; Worst service → Negative; Absolutely great food → ?"* → Positive |

**Keywords:** `zero-shot`, `few-shot`, `demonstrations`, `in-context learning`.

---

### Q1(d) What is function calling in Large Language Models?
**Unit IV – Function calling | 2 Marks**

**Function calling** (tool calling) is a feature where an LLM, instead of replying in plain text, outputs a **structured request (JSON) naming an external function/API and its arguments**. The application executes the function and returns the result to the LLM, which then writes the final answer.

**Flow:** User query → LLM chooses function (e.g., `get_weather(city="Hyderabad")`) → application runs API → result returned to LLM → LLM gives final answer.
It lets LLMs access real-time data, databases and actions, and reduces hallucination.

**Keywords:** `tool use`, `JSON schema`, `API`, `arguments`, `structured output`.

---

### Q1(e) What is alignment in Large Language Models?
**Unit V – Future directions | 2 Marks**

**Alignment** is the process of making an LLM's behaviour consistent with **human intentions, values and safety requirements** – so that it is **helpful, honest and harmless**. It is achieved through **instruction tuning, Reinforcement Learning from Human Feedback (RLHF)** (reward model from human preference rankings + RL optimisation), preference optimisation (DPO), Constitutional AI and red-teaming.

**Keywords:** `RLHF`, `human feedback`, `reward model`, `helpful-honest-harmless`, `safety`.

---

# PART – B (5 + 5 Marks per question; answer any TWO questions)

## Q2(a) Explain the Transformer architecture with a neat diagram. Describe its major components and their functions.
**Unit III – Transformer | 5 Marks (P1)**

### 1. Introduction
The **Transformer** was introduced by **Vaswani et al. (Google, 2017)** in the paper *"Attention Is All You Need"*. It replaces recurrence with **self-attention**, so the entire sequence is processed **in parallel** and long-range dependencies are captured directly. It forms the basis of BERT, GPT, T5 and modern LLMs.

### 2. Architecture Diagram
```
        INPUT SENTENCE                          OUTPUT (shifted right)
              │                                          │
   [Input Embedding]                            [Output Embedding]
              │                                          │
        (+) Positional Encoding                   (+) Positional Encoding
              │                                          │
  ┌───────────▼───────────┐                  ┌───────────▼───────────┐
  │ Multi-Head Self-Attn  │                  │ Masked Multi-Head Attn│
  │ Add & Norm            │                  │ Add & Norm            │
  │ Feed-Forward          │ ── K, V ───────► │ Encoder–Decoder Attn  │
  │ Add & Norm            │                  │ Add & Norm            │
  └───────────────────────┘                  │ Feed-Forward          │
     ENCODER STACK (N = 6)                   │ Add & Norm            │
                                             └───────────▼───────────┘
                                                DECODER STACK (N = 6)
                                                    [Linear]
                                                    [Softmax]
                                                Output probabilities
```

### 3. Major components and functions
| Component | Function |
|---|---|
| Input / output embeddings | Convert tokens into dense vectors (d_model = 512) |
| **Positional encoding** | Adds sine/cosine position information because no recurrence exists |
| **Multi-head self-attention** | Every token attends to all tokens; Attention(Q,K,V) = softmax(QKᵀ/√d_k)V; multiple heads capture different relations |
| **Masked self-attention (decoder)** | Blocks attention to future tokens so generation is auto-regressive |
| **Encoder–decoder (cross) attention** | Decoder queries attend to encoder output → connects source and target |
| **Position-wise feed-forward network** | Two linear layers with ReLU; adds non-linearity per position |
| **Residual connections + Layer normalisation** | Improve gradient flow and stabilise training |
| **Linear + Softmax** | Convert decoder output to vocabulary probabilities |

### 4. Working (translation example)
1. Source sentence → embeddings + positional encoding → passes through N encoder layers → contextual representations.
2. Decoder receives previous output tokens, applies masked self-attention, then cross-attention over encoder output, then feed-forward.
3. Softmax gives probability of next word; process repeats until `<EOS>`.

### 5. Advantages / Disadvantages
Advantages: parallel training, strong long-range dependencies, scalable. Disadvantages: O(n²) attention cost, needs large data and compute.

**Keywords:** `self-attention`, `multi-head`, `positional encoding`, `encoder–decoder`, `masked attention`, `residual`, `layer norm`, `Vaswani 2017`.

---

## Q2(b) Compare BERT, RoBERTa and GPT based on their architectures, training approaches and applications.
**Unit III – Pretrained models | 5 Marks (P1)**

### 1. Introduction
BERT, RoBERTa and GPT are pretrained Transformer language models. BERT/RoBERTa use the Transformer **encoder** (best for *understanding*); GPT uses the Transformer **decoder** (best for *generation*).

### 2. Comparison Table
| Basis | **BERT** | **RoBERTa** | **GPT** |
|---|---|---|---|
| Developer / year | Google, 2018 | Facebook AI, 2019 | OpenAI, 2018 onwards |
| **Architecture** | Encoder-only Transformer; Base = 12 layers, 768 hidden, 12 heads (110M); Large = 24 layers (340M) | Same encoder architecture as BERT | Decoder-only Transformer (GPT-1 ≈ 117M … GPT-3 ≈ 175B) |
| Direction of context | **Bidirectional** | Bidirectional | **Unidirectional** (left-to-right, causal mask) |
| **Pre-training task** | Masked Language Modelling (15% tokens) + Next Sentence Prediction | MLM only; **NSP removed**; **dynamic masking** | Causal language modelling (predict next token) |
| Training data/settings | BooksCorpus + Wikipedia (≈16 GB), static masking | ≈160 GB, larger batches, longer training | Large web-scale text; scaled with size |
| Tokenizer | WordPiece | Byte-level BPE | BPE |
| Adaptation | Fine-tuning with task head | Fine-tuning | Fine-tuning / zero-/few-shot prompting, instruction tuning |
| **Applications** | Classification, sentiment, QA (SQuAD), NER, entailment, search ranking | Same tasks with better accuracy | Text generation, chatbots, summarization, translation, code generation, QA |
| Weakness | Cannot generate text naturally; 512-token limit | Higher training cost | Sees only left context; hallucination |

### 3. Diagram of difference
```
 BERT / RoBERTa : [CLS] I love [MASK] language [SEP]  → sees left AND right → predicts masked word
 GPT            : I   love   natural → ?              → sees only left context → predicts next word
```

### 4. Conclusion
BERT introduced deep bidirectional pre-training; RoBERTa optimised its training recipe for better performance; GPT's autoregressive decoder makes it ideal for generative tasks.

**Keywords:** `encoder-only`, `decoder-only`, `MLM`, `NSP`, `dynamic masking`, `causal LM`, `bidirectional`, `autoregressive`.

---

## Q3(a) Explain Retrieval-Augmented Generation (RAG) with a neat architecture diagram. Describe the retrieval and generation processes.
**Unit IV – RAG | 5 Marks (P1)**

### 1. Definition
**RAG** (Lewis et al., 2020) combines an **information-retrieval component** with a **generative LLM**: relevant documents are retrieved from an external knowledge source and supplied in the prompt so that the generated answer is **grounded** in them. *Analogy:* an open-book examination.

### 2. Need
LLMs have a knowledge cut-off, hallucinate, and cannot access private data. RAG supplies up-to-date, verifiable knowledge without retraining.

### 3. Architecture Diagram
```
 OFFLINE INDEXING
 Documents ─► Chunking ─► Embedding model ─► [ Vector Database ]
                                                    ▲
 ONLINE QUERY                                       │ similarity search (top-k chunks)
 User Query ─► Embedding model ─► Query vector ─────┘
                                                    │ retrieved context
                    Prompt = Query + Retrieved Context
                                  ▼
                         [ LLM / Generator ]
                                  ▼
                   Grounded answer (with citations) ─► User
```

### 4. Retrieval process
1. **Indexing:** documents are split into chunks, converted to vectors by an embedding model, and stored in a vector database (FAISS, Pinecone, Chroma).
2. **Query encoding:** user query is converted to an embedding with the same model.
3. **Similarity search:** nearest-neighbour search (cosine similarity) returns the **top-k** most relevant chunks; optional re-ranking improves precision.

### 5. Generation process
1. Retrieved chunks and the user question are combined into an **augmented prompt** ("Answer using only the context below…").
2. The LLM generates a fluent answer conditioned on this context, optionally citing the sources.
3. The output is returned to the user; if no relevant context is found the model can say it does not know.

### 6. Advantages and applications
| Advantage | Explanation |
|---|---|
| Up-to-date knowledge | Update documents, not the model |
| Fewer hallucinations | Answers grounded in evidence |
| Source citation | Verifiability |
| Private data use | Enterprise documents |
| Cost-effective | No retraining |
**Applications:** enterprise chatbots, customer support, legal/medical QA, "chat with PDF", research assistants.
**Limitations:** quality depends on retrieval, added latency, context-length limits.

**Keywords:** `retriever`, `generator`, `embedding`, `vector database`, `chunking`, `top-k`, `grounding`, `augmented prompt`.

---

## Q3(b) Explain LLM-based agents. Discuss the roles of LangChain, ReAct and AutoGen in developing intelligent agents.
**Unit IV – Agents | 5 Marks (P1)**

### 1. Definition
An **LLM-based agent** is an autonomous system that uses an LLM as its reasoning "brain" to **plan, use tools, remember and act** over multiple steps to achieve a goal. A plain LLM only answers; an agent **acts**.

### 2. Architecture
| Component | Role |
|---|---|
| LLM (controller) | Understands goal, reasons, decides next action |
| Planning | Breaks goals into sub-tasks, reflects and corrects |
| Memory | Short-term (context) and long-term (vector store) |
| Tools | Search, calculator, code interpreter, APIs, databases (via function calling) |
| Environment | User input and tool observations |
```
 Goal ─► [ LLM Agent ] ⇄ Memory
             │ action     ▲ observation
             ▼            │
         [ Tools: Search | API | Code | DB ] ─► Final answer
 Loop: Perceive → Think/Plan → Act → Observe → Repeat
```

### 3. Roles of LangChain, ReAct and AutoGen
| | Role in developing agents |
|---|---|
| **LangChain** | Open-source **framework** providing building blocks: model interfaces, prompt templates, chains, memory, retrievers/vector stores, tools and an agent executor. It makes it easy to assemble LLM applications (e.g., RAG chatbots, tool-using agents). |
| **ReAct (Reason + Act)** | A **reasoning paradigm** (Yao et al., 2022): the agent interleaves **Thought → Action → Observation** steps. Reasoning decides which tool to use, and tool results update reasoning, reducing hallucination and making decisions interpretable. |
| **AutoGen** | Microsoft **multi-agent framework**: several agents (e.g., Planner, Coder, Executor, Critic, UserProxy) **converse and collaborate** to solve complex tasks, with code execution and human-in-the-loop. |

**ReAct example:**
```
Thought: I need the population of Hyderabad.
Action:  Search["Hyderabad population"]
Observation: ≈ 10 million
Thought: I have the answer.
Final Answer: About 10 million.
```

### 4. Applications and challenges
Applications: research assistants, coding assistants, customer support, workflow automation. Challenges: compounding errors, cost/latency, loops, safety of actions.

**Keywords:** `agent`, `planning`, `memory`, `tools`, `Thought–Action–Observation`, `chains`, `agent executor`, `multi-agent`, `human-in-the-loop`.

---

## Q4(a) Discuss approaches used to evaluate the reliability and performance of Large Language Models, with reference to factual consistency, bias, fairness and explainability.
**Unit V – Evaluation and ethics | 5 Marks (P1)**

### 1. Introduction
LLMs can be fluent yet unreliable (hallucination, bias, opaque behaviour). Evaluation therefore must be **multi-dimensional**, combining automatic metrics, benchmarks and human judgement.

### 2. Approaches
```
 Evaluation of LLMs
 ├── Performance  : perplexity, BLEU/ROUGE, BERTScore, benchmarks (MMLU, GLUE, GSM8K, HumanEval)
 ├── Factual consistency : NLI / QA-based checks, claim verification, TruthfulQA
 ├── Bias & fairness     : StereoSet, CrowS-Pairs, BBQ, counterfactual tests, parity metrics
 ├── Explainability      : LIME, SHAP, attention/saliency, CoT rationales
 └── Reliability/safety  : robustness, calibration, red-teaming, human evaluation, LLM-as-judge
```

| Dimension | Methods |
|---|---|
| **Performance** | Perplexity for language modelling; BLEU (translation), ROUGE (summarization); BERTScore for semantic similarity; task benchmarks such as MMLU, GLUE/SuperGLUE, GSM8K, HumanEval |
| **Factual consistency** (does the output agree with source/facts?) | **NLI-based** (source entails output sentence), **QA-based** (answers from source vs output compared), **claim verification** against retrieved evidence (FActScore, FEVER), **TruthfulQA**, human fact-checking, RAG with citation checking |
| **Bias** | Bias benchmarks (StereoSet, CrowS-Pairs, BBQ), WEAT on embeddings, **counterfactual testing** (swap gender/race terms and compare outputs), toxicity tests |
| **Fairness** | Group metrics – **demographic parity**, **equalized odds**; subgroup performance comparison; audits; mitigation through data balancing, debiasing, RLHF |
| **Explainability** | **LIME/SHAP** word-importance, attention visualisation, saliency maps, probing, checking that chain-of-thought rationales are faithful |
| **Reliability** | Robustness to paraphrase/adversarial prompts, consistency across runs, **calibration** (confidence vs accuracy), red-teaming, jailbreak and prompt-injection tests |
| **Human & LLM judges** | Human ratings (fluency, correctness, helpfulness, harmlessness), pairwise preference; **LLM-as-a-judge** for scalable scoring |

### 3. Challenges
Overlap metrics (BLEU/ROUGE) do not measure truth; benchmark contamination; subjective human ratings; LLM-judge bias; evolving risks → use a **combination** of methods.

### 4. Conclusion
Reliable LLM deployment needs continuous evaluation across accuracy, factuality, fairness, transparency and safety.

**Keywords:** `perplexity`, `benchmarks`, `TruthfulQA`, `entailment`, `demographic parity`, `equalized odds`, `LIME`, `SHAP`, `calibration`, `red-teaming`, `LLM-as-judge`.

---

## Q4(b) Explain Multimodal NLP. Compare CLIP, Flamingo and GPT-4o based on their capabilities and applications.
**Unit V – Multimodal NLP | 5 Marks (P1)**

### 1. Multimodal NLP
**Definition:** the branch of NLP that builds models able to process, relate and generate information from **multiple modalities** – text, images, audio, video – together.
**Why:** humans use several senses with language; text-only models cannot answer "what is in this picture?".
**Fusion approaches:** early fusion, late fusion, **shared embedding space** (CLIP), **cross-attention** (Flamingo), single end-to-end network (GPT-4o).
```
 Image ─► Image Encoder ─┐
                         ├─► Fusion (shared space / cross-attention) ─► Language decoder ─► Output
 Text  ─► Text  Encoder ─┘
```
**Applications:** image captioning, visual question answering, image–text search, document/chart understanding, voice assistants, accessibility.
**Challenges:** modality alignment, large paired datasets, compute, hallucination, bias.

### 2. Brief description of the three models
- **CLIP (OpenAI, 2021):** image encoder (ViT/ResNet) + text encoder trained with a **contrastive loss** on ~400M image–text pairs so matching pairs have high cosine similarity; enables **zero-shot image classification** and image–text retrieval; does not generate text.
- **Flamingo (DeepMind, 2022):** **frozen vision encoder** + **frozen LLM**, bridged by a **Perceiver Resampler** and **gated cross-attention** layers; accepts interleaved images/video and text; strong **few-shot** visual question answering and captioning; text output.
- **GPT-4o (OpenAI, 2024):** a single **natively multimodal ("omni")** model trained end-to-end on text, vision and audio; supports **real-time voice conversation**, image understanding and multilingual interaction.

### 3. Comparison Table
| Basis | CLIP | Flamingo | GPT-4o |
|---|---|---|---|
| Modalities | Image + text | Image/video + text | Text + image + audio (and video frames) |
| Core idea | Contrastive learning, shared embedding space | Frozen vision + frozen LLM linked by gated cross-attention | Single end-to-end multimodal network |
| **Capabilities** | Zero-shot classification, image–text retrieval/matching | Few-shot VQA, captioning, visual dialogue | Real-time voice + vision chat, translation, reasoning over images, text generation |
| Output | Embeddings / similarity scores | Text | Text and audio |
| Learning mode | Zero-shot via text prompts | Few-shot in-context | Prompting / conversational |
| **Applications** | Image search, content moderation, guiding text-to-image models | Visual question answering, video description, assistive tools | Voice assistants, tutoring, live translation, accessibility, customer support |
| Limitations | No generation; weak at counting/fine detail | Compute heavy; limited availability | Closed model; hallucination and privacy concerns |

### 4. Conclusion
CLIP aligns images and text; Flamingo adds few-shot visual-language generation; GPT-4o unifies text, vision and audio in one real-time model – showing the evolution from alignment to full multimodal assistants.

**Keywords:** `modality`, `contrastive learning`, `shared embedding space`, `zero-shot`, `Perceiver Resampler`, `gated cross-attention`, `few-shot`, `VQA`, `omni model`, `fusion`.
