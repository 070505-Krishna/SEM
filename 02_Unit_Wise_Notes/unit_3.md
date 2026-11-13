# UNIT III – Transformers and Pretrained Models

**Syllabus:** Transformer architecture (Vaswani et al., 2017) · Self-attention · Multi-head attention · Position encoding · BERT, RoBERTa, GPT architectures · Fine-tuning vs feature extraction · Applications: QA, text entailment, sentence embeddings

> **Recommended for detailed preparation (Top-4 unit).** Reasons: it is the core of modern NLP, answers are concept- and diagram-based (no heavy numericals), and Mid-2 tested it with both a diagram question and a comparison question.

---

## 0. Question Paper Analysis for Unit 3

### 0.1 Actual questions seen in the supplied papers (Mid-2)
| Mid-2 Q.No | Question (original wording) | Marks | Topic |
|---|---|---|---|
| 1(a) | Define Textual Entailment with a suitable example. | 2 | Applications |
| 1(b) | What are sentence embeddings? Mention any two applications. | 2 | Applications |
| 2(a) | Explain the Transformer architecture with a neat diagram. Describe its major components and their functions. | 5 | Transformer |
| 2(b) | Compare BERT, RoBERTa and GPT based on their architectures, training approaches and applications. | 5 | BERT/RoBERTa/GPT |

### 0.2 Question Bank (supplied – *expected*, not confirmed past papers)
Short: Transformer & inventor, self-attention, Q/K/V, multi-head attention purpose, positional encoding need, BERT architecture, BERT vs GPT, RoBERTa vs BERT, encoder/decoder roles, fine-tuning, fine-tuning vs feature extraction, sentence embeddings, QA, textual entailment, two applications.
Long: Transformer diagram; self-attention with Q/K/V; multi-head; positional encoding; BERT; GPT vs BERT; RoBERTa; comparison of three; fine-tuning vs feature extraction; applications in QA/entailment/embeddings.

### 0.3 Priority classification
| Priority | Topics |
|---|---|
| **P1** | Transformer architecture · BERT vs RoBERTa vs GPT · Self-attention (Q, K, V) · Textual entailment · Sentence embeddings |
| **P2** | Multi-head attention · Positional encoding · Fine-tuning vs feature extraction · BERT architecture & pre-training (MLM, NSP) |
| **P3** | QA details · Encoder/decoder roles · Layer normalisation, feed-forward, residual connections |

---

## 1. Transformer Architecture (Vaswani et al., 2017)  ⭐ P1

### A. Definition
The **Transformer** is a deep-learning architecture introduced by **Vaswani et al. (Google, 2017) in the paper "Attention Is All You Need"**. It processes entire sequences in parallel using **self-attention** instead of recurrence or convolution.

### B. Simple Explanation
An RNN reads word by word. A Transformer reads the *whole sentence at once* and lets every word "look at" every other word to decide which are important. This is faster (parallel) and handles long-distance relationships better.

### C. Detailed Explanation
**Overall structure:** Encoder stack (N = 6 identical layers) + Decoder stack (N = 6 identical layers) (original paper: d_model = 512, 8 heads, feed-forward size 2048).

**Encoder layer =** (1) Multi-Head Self-Attention → Add & Norm → (2) Position-wise Feed-Forward Network → Add & Norm.
**Decoder layer =** (1) **Masked** Multi-Head Self-Attention → Add & Norm → (2) **Encoder–Decoder (cross) attention** → Add & Norm → (3) Feed-Forward → Add & Norm.

### D. Diagram (easy to redraw)
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
  │ Feed-Forward          │ ── K, V ───────► │ Cross-Attention       │
  │ Add & Norm            │                  │ Add & Norm            │
  └───────────────────────┘                  │ Feed-Forward          │
      ENCODER  (× N)                         │ Add & Norm            │
                                             └───────────▼───────────┘
                                                  DECODER (× N)
                                                    [Linear]
                                                    [Softmax]
                                                  Output probabilities
```

### Major Components and Functions
| Component | Function |
|---|---|
| **Input/Output embedding** | Convert tokens to dense vectors (d_model = 512). |
| **Positional encoding** | Adds word-order information (sine/cosine) since there is no recurrence. |
| **Multi-head self-attention** | Each token attends to all tokens; captures different relationships in parallel heads. |
| **Masked self-attention (decoder)** | Prevents a position from seeing future tokens (auto-regressive generation). |
| **Encoder–decoder attention** | Decoder queries attend to encoder outputs (keys, values) – links source and target. |
| **Feed-forward network (FFN)** | Two linear layers with ReLU applied to each position independently: adds non-linearity. |
| **Residual connections** | Add input of a sub-layer to its output → eases gradient flow. |
| **Layer normalisation** | Stabilises and speeds up training. |
| **Linear + Softmax** | Maps decoder output to vocabulary probabilities. |

### E. Comparison – RNN/LSTM vs Transformer
| Basis | RNN/LSTM | Transformer |
|---|---|---|
| Processing | Sequential | **Parallel** |
| Long-range dependency | Weak | Strong (direct attention between any two tokens) |
| Training speed | Slow | Fast on GPUs |
| Word order | Inherent | Needs positional encoding |
| Complexity | O(n) steps | O(n²) attention memory |

**Advantages:** parallelisation, long-range context, scalable, basis of BERT/GPT. **Disadvantages:** quadratic cost with sequence length, needs large data/compute, no inherent order.

### G. University Exam Answer (12-mark outline)
Intro (limits of RNNs; Vaswani et al. 2017) → overall diagram → encoder (sub-layers) → decoder (sub-layers, masking, cross-attention) → table of components & functions → self-attention formula → positional encoding → working for a translation example → advantages/disadvantages → applications (BERT, GPT, T5, ViT) → conclusion.

### H. Keywords
`self-attention`, `multi-head`, `encoder–decoder`, `positional encoding`, `residual connection`, `layer normalisation`, `masked attention`, `feed-forward`, `parallelisation`, `d_model = 512`, `Attention Is All You Need`.

---

## 2. Self-Attention (Query, Key, Value)  ⭐ P1

### A. Definition
**Self-attention** is a mechanism in which each token in a sequence computes attention scores with every token of the *same* sequence, to build a context-aware representation of itself.

### B. Simple Explanation
In "The animal didn't cross the street because **it** was too tired", self-attention lets the word **"it"** look at all words and give highest weight to "animal". *Analogy – library search:* **Query** = what you are looking for, **Key** = labels on the books, **Value** = the content of the books. Match query with keys → collect values.

### C. Detailed Explanation
Each input embedding x is multiplied by three learned matrices:
- **Q = x·W_Q** (Query – "what am I looking for?")
- **K = x·W_K** (Key – "what do I contain?")
- **V = x·W_V** (Value – "what information do I give?")

**Scaled dot-product attention:** **Attention(Q, K, V) = softmax( Q·Kᵀ / √d_k ) · V**

**Steps:**
1. Compute Q, K, V for every token.
2. Score = Q·Kᵀ (similarity of every pair).
3. Divide by √d_k (prevents very large dot-products and vanishing softmax gradients).
4. Softmax → attention weights (sum = 1).
5. Weighted sum of V → output vector for each token.

```
 x ──► Q ┐
 x ──► K ┼─► Q·Kᵀ ─► ÷√dk ─► softmax ─► ×V ─► Output (context-aware vectors)
 x ──► V ┘
```
**Difference from Unit II attention:** here Q, K, V come from the *same* sequence (self), so the model relates words within the sentence; Unit II attention related decoder state to encoder states.

### H. Keywords
`query`, `key`, `value`, `dot product`, `scaling √d_k`, `softmax`, `attention weights`, `context-aware embedding`.

---

## 3. Multi-Head Attention  ⭐ P2

### A. Definition
**Multi-head attention** runs *h* self-attention operations ("heads") in parallel, each with its own W_Q, W_K, W_V, and concatenates their outputs followed by a linear projection.

### B. Simple Explanation
Like several people reading the same sentence, each focusing on a different aspect – one on grammar, one on meaning, one on nearby words. Combining their views gives a richer understanding.

### C. Detailed Explanation
**MultiHead(Q,K,V) = Concat(head₁,…,head_h)·W_O**, where head_i = Attention(QW_Qⁱ, KW_Kⁱ, VW_Vⁱ). In the original model h = 8, d_k = d_v = 512/8 = 64.

```
 x ─► [Head 1: Q1,K1,V1 → attention] ─┐
 x ─► [Head 2: Q2,K2,V2 → attention] ─┼─► Concat ─► Linear W_O ─► output
 x ─► [Head h: ...                 ] ─┘
```
**Purpose / how it improves performance:** captures multiple types of relationships (syntactic, semantic, long/short range) simultaneously; different representation sub-spaces; richer features; parallel computation; more stable learning.

### H. Keywords
`parallel heads`, `representation subspaces`, `concatenation`, `W_O projection`, `d_k = 64`.

---

## 4. Positional Encoding  ⭐ P2

### A. Definition
**Positional encoding** is a vector added to each input embedding that gives the model information about the position (order) of tokens in the sequence.

### B. Simple Explanation
Self-attention treats a sentence like a "bag of words" – "dog bites man" and "man bites dog" would look identical. Positional encoding gives each word a "seat number".

### C. Detailed Explanation
**Sinusoidal encoding (original paper):**
PE(pos, 2i) = sin( pos / 10000^(2i/d_model) ) ; PE(pos, 2i+1) = cos( pos / 10000^(2i/d_model) )
- Final input = word embedding + positional encoding.
- Different frequencies for different dimensions → unique pattern per position; relative positions can be learned (PE(pos+k) is a linear function of PE(pos)); works for unseen longer sequences.
- BERT/GPT use **learned** positional embeddings; newer models use rotary (RoPE) encodings.

**Why necessary:** Transformers have no recurrence/convolution; they process all tokens in parallel, so order must be injected explicitly.

### H. Keywords
`sinusoidal`, `sine/cosine`, `learned positional embedding`, `order information`, `permutation invariant`.

---

## 5. BERT  ⭐ P1

### A. Definition
**BERT (Bidirectional Encoder Representations from Transformers)** (Devlin et al., Google, 2018) is a pretrained language model that uses only the **encoder stack** of the Transformer to learn deep **bidirectional** contextual representations.

### B. Simple Explanation
BERT reads the whole sentence in both directions at once, so the word "bank" in "river bank" and "bank account" gets different vectors depending on the surrounding words.

### C. Detailed Explanation
**Architecture:** encoder-only Transformer.
| Version | Layers | Hidden size | Heads | Parameters |
|---|---|---|---|---|
| BERT-Base | 12 | 768 | 12 | ≈110M |
| BERT-Large | 24 | 1024 | 16 | ≈340M |

**Input representation:** Token embedding (WordPiece) + Segment embedding (sentence A/B) + Position embedding; special tokens **[CLS]** (classification summary) and **[SEP]** (separator).

**Pre-training objectives:**
1. **Masked Language Modelling (MLM):** randomly mask 15% of tokens (of those: 80% → [MASK], 10% → random token, 10% unchanged) and predict them – enables bidirectional learning.
2. **Next Sentence Prediction (NSP):** predict whether sentence B follows sentence A (IsNext/NotNext).
Data: BooksCorpus + English Wikipedia.

**Fine-tuning:** add a small task-specific output layer and train on labelled data (classification, QA, NER, entailment).

```
 [CLS] I love [MASK] language [SEP] ──► Embeddings (token+segment+position)
        └─► Transformer Encoder × 12 (bidirectional self-attention) ─► contextual vectors
              ├─ [CLS] vector → classification head
              └─ [MASK] vector → predicts "natural"
```
**Applications:** sentiment analysis, QA (SQuAD), NER, textual entailment, search ranking, sentence similarity.
**Limitations:** cannot generate text naturally, 512-token limit, mask mismatch between pre-training & fine-tuning, NSP found weak.

### H. Keywords
`bidirectional`, `encoder-only`, `MLM`, `NSP`, `[CLS]`, `[SEP]`, `WordPiece`, `segment embedding`, `Devlin 2018`, `fine-tuning`.

---

## 6. RoBERTa  ⭐ P1

### A. Definition
**RoBERTa (Robustly Optimized BERT Pretraining Approach)** (Liu et al., Facebook AI, 2019) is an optimised version of BERT with the same architecture but an improved training recipe.

### B. Simple Explanation
Same engine as BERT, but trained *longer, on more data, with better settings* – and with a useless task removed.

### C. Improvements over BERT
1. **Removed NSP** (only MLM).
2. **Dynamic masking** – mask pattern changes each epoch (BERT used static masking).
3. **More data** – ≈160 GB text (BooksCorpus, Wikipedia, CC-News, OpenWebText, Stories) vs ≈16 GB.
4. **Larger batch sizes** and **longer training**.
5. **Byte-level BPE** tokenizer (≈50K vocabulary) vs WordPiece (30K).
6. Result: better performance on GLUE, SQuAD, RACE.

---

## 7. GPT (Generative Pre-trained Transformer)  ⭐ P1

### A. Definition
**GPT** (OpenAI, 2018 onwards) is a family of **decoder-only** Transformer language models trained **autoregressively** (predict the next token) using **masked (causal) self-attention**, so each token sees only previous tokens.

### B. Simple Explanation
GPT is a super-powered autocomplete: given the words so far, predict the next word – again and again to generate paragraphs.

### C. Detailed Explanation
- **Architecture:** stack of Transformer *decoder* blocks (no cross-attention); unidirectional (left-to-right); BPE tokenizer.
- **Pre-training:** causal language modelling on large web text. **Adaptation:** fine-tuning (GPT-1), zero/few-shot prompting (GPT-2/3), instruction-tuning + RLHF (ChatGPT/GPT-4).
- **Scale:** GPT-1 ≈ 117M params, GPT-2 ≈ 1.5B, GPT-3 ≈ 175B parameters.
- **Applications:** text generation, chatbots, summarization, translation, code generation, QA.
- **Limitations:** unidirectional context, hallucination, bias, high compute.

```
 Token_1  Token_2  Token_3  ──► [Masked self-attn + FFN] × N ──► predict Token_4
 (each position can attend only to positions on its left)
```

### E. Comparison – BERT vs RoBERTa vs GPT  ⭐ P1 (Mid-2 Q2b)
| Basis | **BERT** | **RoBERTa** | **GPT** |
|---|---|---|---|
| Developer / year | Google, 2018 | Facebook AI, 2019 | OpenAI, 2018 → |
| Architecture | Transformer **encoder** only | Transformer **encoder** only (same as BERT) | Transformer **decoder** only |
| Direction | Bidirectional | Bidirectional | Unidirectional (left-to-right) |
| Attention | Full self-attention | Full self-attention | Masked (causal) self-attention |
| Pre-training task | MLM + NSP | MLM only (**dynamic masking**) | Next-token (causal LM) |
| Training data | ≈16 GB (Books + Wikipedia) | ≈160 GB | Large web-scale text (WebText, etc.) |
| Tokenizer | WordPiece (30K) | Byte-level BPE (50K) | BPE |
| Strength | Understanding tasks | Better understanding than BERT | **Generation** |
| Applications | Classification, QA, NER, entailment | Same tasks, higher accuracy | Text generation, chatbots, code, summarization |
| Usage | Fine-tuning | Fine-tuning | Prompting (zero/few-shot) & fine-tuning |

### G. Exam Answer (12-mark outline)
Intro (pretrained Transformer models) → BERT (architecture, MLM, NSP) → RoBERTa (improvements) → GPT (decoder-only, causal LM) → comparison table (architecture / training / applications) → conclusion: encoder models = understanding, decoder models = generation.

### H. Keywords
`encoder-only`, `decoder-only`, `bidirectional`, `autoregressive`, `causal mask`, `MLM`, `NSP`, `dynamic masking`, `byte-level BPE`, `pre-training`, `zero-shot`.

---

## 8. Fine-tuning vs Feature Extraction  ⭐ P2

### A. Definition
- **Fine-tuning:** updating (some or all) weights of a pretrained model by training it further on a labelled task-specific dataset.
- **Feature extraction:** using the pretrained model as a **frozen** encoder to produce embeddings, which are given to a separate classifier; pretrained weights are not changed.

### B. Simple Explanation
Pretrained model = a graduate with general education. *Fine-tuning* = sending the graduate to a specialised job training (they change). *Feature extraction* = using the graduate's notes as they are, and training only a small assistant on top.

### E. Comparison Table
| Basis | Fine-tuning | Feature extraction |
|---|---|---|
| Pretrained weights | Updated | **Frozen** |
| Trainable parameters | All / many | Only new classifier head |
| Computation & time | High (GPU) | Low |
| Data needed | More | Less |
| Accuracy | Usually higher | Slightly lower |
| Overfitting / forgetting risk | Higher (catastrophic forgetting) | Lower |
| Example | Fine-tune BERT for sentiment | Use BERT vectors + Logistic Regression |
| Variants | Full, partial, adapters/LoRA | Last-layer / layer-wise features |

### G. Exam Answer (2-mark)
"Fine-tuning updates the pretrained model's weights on task-specific data, giving higher accuracy at higher cost; feature extraction keeps the pretrained model frozen and only trains a classifier on its output embeddings – cheaper but usually less accurate."

### H. Keywords
`transfer learning`, `frozen layers`, `task head`, `catastrophic forgetting`, `adapters`, `LoRA`.

---

## 9. Applications of Pretrained Transformers  ⭐ P1 (entailment, embeddings)

### 9.1 Question Answering (QA)  ⭐ P3
**Definition:** automatically answering natural-language questions. **Types:** extractive (select answer span from a passage – BERT predicts start & end tokens on SQuAD), abstractive/generative (GPT/T5 generate the answer), open-domain (retrieve + read). **Example:** Passage: "Hyderabad is the capital of Telangana." Q: "What is the capital of Telangana?" → "Hyderabad".

```
 [CLS] Question [SEP] Passage [SEP] ─► BERT ─► Start-probability / End-probability ─► Answer span
```

### 9.2 Textual Entailment (Natural Language Inference)  ⭐ P1
**Definition:** Textual entailment is the task of determining whether a **hypothesis** can be logically inferred from a given **premise** (labels: entailment, contradiction, neutral).
**Example:**
- Premise: *"A man is playing a guitar on the stage."*
- Hypothesis: *"A man is playing a musical instrument."* → **Entailment** (follows from premise).
- Hypothesis: *"The man is sleeping."* → **Contradiction**; *"The man is a famous singer."* → **Neutral**.
**Model:** fine-tune BERT/RoBERTa with input `[CLS] premise [SEP] hypothesis [SEP]` and classify from the [CLS] vector. **Datasets:** SNLI, MNLI. **Uses:** fact-checking, QA validation, summarisation consistency, search.

### 9.3 Sentence Embeddings  ⭐ P1
**Definition:** Sentence embeddings are fixed-length dense vectors that represent the meaning of an entire sentence so that semantically similar sentences are close in vector space.
**How:** pool BERT token vectors (mean-pooling or [CLS]); **Sentence-BERT (SBERT)** uses a siamese network trained on sentence pairs so similarity can be computed with cosine similarity quickly.
**Applications (any two):** semantic search/information retrieval, duplicate-question or paraphrase detection, text clustering, recommendation, question answering retrieval, RAG (Unit IV).
**Example:** "How do I reset my password?" ≈ "I forgot my password" → high cosine similarity.

---

## 10. Quick 2-Mark Sheet (Unit III)

| Question | Crisp answer |
|---|---|
| Transformer; who introduced | Attention-based encoder–decoder model by Vaswani et al., 2017 ("Attention Is All You Need"); no recurrence. |
| Self-attention | Each token attends to all tokens of same sequence to build context-aware representation. |
| Q, K, V | Query = what is sought; Key = what each token offers for matching; Value = information passed on; output = softmax(QKᵀ/√d_k)V. |
| Multi-head purpose | Several attention heads learn different relationships in parallel; outputs concatenated. |
| Positional encoding need | Transformer has no recurrence, so order information is added via sinusoidal vectors. |
| BERT | Bidirectional encoder-only Transformer pretrained with MLM + NSP (12 layers/110M for Base). |
| BERT vs GPT | BERT: encoder, bidirectional, MLM, understanding; GPT: decoder, left-to-right, next-word, generation. |
| RoBERTa vs BERT | No NSP, dynamic masking, more data, larger batches, longer training. |
| Encoder / decoder roles | Encoder builds contextual representation of input; decoder generates output using masked self-attention and cross-attention to encoder output. |
| Fine-tuning vs feature extraction | Fine-tuning updates weights; feature extraction uses frozen embeddings. |
| Sentence embeddings | Dense vector for a whole sentence; apps: semantic search, paraphrase detection. |
| QA | Answering questions from context; BERT predicts start/end span. |
| Textual entailment | Determining whether hypothesis follows from premise (entailment/contradiction/neutral). |
