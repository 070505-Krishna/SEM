# UNIT II – Deep Learning for NLP

**Syllabus:** Recurrent Neural Networks (RNN) · GRU · LSTM · Sequence-to-Sequence (Encoder–Decoder) · Attention mechanism and its intuition · Applications: text generation, summarization, machine translation · Evaluation metrics: BLEU, ROUGE

> **Recommended for detailed preparation (Top-4 unit).** Reasons: compact syllabus, highly diagram-based answers, repeated questions in Mid-1, and the attention concept is reused in Unit III (Transformers).

---

## 0. Question Paper Analysis for Unit 2

### 0.1 Actual questions seen in the supplied papers (Mid-1)
| Mid-1 Q.No | Question (original wording) | Marks | Topic |
|---|---|---|---|
| 1(c) | What is the limitation of a basic Encoder–Decoder model for long sequences | 2 | Seq2Seq |
| 1(d) | What are BLEU and ROUGE? | 2 | Metrics |
| 3(a) | Explain the architecture and working of RNN with a suitable sequential example. Also discuss its limitations. | 5 | RNN |
| 3(b) | Explain LSTM architecture in detail, including cell state, forget gate, input gate and output gate. | 5 | LSTM |
| 4(b) | Explain the attention mechanism in detail. Describe attention scores, weights and the formation of the context vector. *(the last line of the paper image is partly cut off; text read as shown)* | 5 | Attention |

### 0.2 Question Bank (supplied – *expected*, not confirmed past papers)
Short: RNN & hidden state, vanishing/exploding gradients, GRU gates, LSTM gates, RNN vs LSTM vs GRU, Seq2Seq roles, encoder–decoder limitation, attention & score/context vector, 3 Seq2Seq applications, text generation, summarization/MT, BLEU vs ROUGE.
Long: RNN architecture; vanishing/exploding gradients & LSTM/GRU; LSTM; GRU vs LSTM; Seq2Seq with MT example; attention; Seq2Seq vs attention-Seq2Seq; applications; BLEU & ROUGE; numerical BLEU/ROUGE.

### 0.3 Priority classification
| Priority | Topics |
|---|---|
| **P1** | RNN architecture & limitations · LSTM (gates, cell state) · Attention (scores, weights, context vector) · BLEU/ROUGE · Encoder–Decoder limitation |
| **P2** | Vanishing/exploding gradients · GRU & GRU vs LSTM · Seq2Seq architecture · Seq2Seq vs Attention comparison · BLEU/ROUGE numerical |
| **P3** | Applications (text generation, summarization, MT) · BPTT · Types of attention (Bahdanau vs Luong) |

---

## 1. Recurrent Neural Network (RNN)  ⭐ P1

### A. Definition
A **Recurrent Neural Network (RNN)** is a neural network designed for sequential data, in which the output at each time step depends on the current input and a **hidden state** carrying information from previous time steps.

### B. Simple Explanation
A normal network forgets the previous input. An RNN is like *reading a sentence word by word while keeping a running memory* of what you read so far. That memory is the hidden state.

### C. Detailed Explanation
**Equations (at time step t):**
- Hidden state: **hₜ = tanh( W_xh·xₜ + W_hh·hₜ₋₁ + b_h )**
- Output: **yₜ = softmax( W_hy·hₜ + b_y )**
- The *same weights* (W_xh, W_hh, W_hy) are shared across all time steps.

**Components:** input xₜ (word embedding), hidden state hₜ (memory), output yₜ, shared weight matrices.

**Training:** *Backpropagation Through Time (BPTT)* – unroll the network over time and backpropagate errors across all steps.

**Types:** one-to-many (image captioning), many-to-one (sentiment analysis), many-to-many (translation, POS tagging).

**Why suitable for sequential data:** processes variable-length input, order-aware, parameter sharing, remembers earlier context.

### D. Diagram
```
  Folded                         Unrolled in time
   y                      y1        y2        y3        y4
   ↑                      ↑         ↑         ↑         ↑
 ┌─────┐ ◄─┐            ┌────┐    ┌────┐    ┌────┐    ┌────┐
 │  h  │   │ loop       │ h1 │──►│ h2 │──►│ h3 │──►│ h4 │
 └─────┘ ──┘            └────┘    └────┘    └────┘    └────┘
   ↑                      ↑         ↑         ↑         ↑
   x                      x1        x2        x3        x4
 h0 (initial state, zeros) feeds h1;  hidden state passes left → right
```

### Worked Sequential Example
Predict the next word for **"I love NLP"** (input words fed one at a time):
1. t=1: x₁ = "I", h₀ = 0 → h₁ encodes "I" → predicts "love".
2. t=2: x₂ = "love", uses h₁ → h₂ encodes "I love" → predicts "NLP".
3. t=3: x₃ = "NLP", uses h₂ → h₃ encodes "I love NLP" → predicts "<end>".
Hidden state carries the history forward.

### Limitations of RNN
1. **Vanishing gradient** – gradients shrink over many steps → cannot learn long-term dependencies.
2. **Exploding gradient** – gradients grow exponentially → unstable training (fix: gradient clipping).
3. **Short-term memory** – forgets information from far-back words ("I grew up in France… I speak fluent ___").
4. **Sequential computation** – cannot be parallelised; slow on long text.
5. **Fixed hidden size** – limited capacity; difficult to train.

### E. Table – Types of RNN task
| Type | Input → Output | Example |
|---|---|---|
| One-to-many | 1 → sequence | Image captioning |
| Many-to-one | sequence → 1 | Sentiment classification |
| Many-to-many (same length) | seq → seq | POS tagging, NER |
| Many-to-many (different length) | seq → seq | Machine translation |

### G. University Exam Answer (12-mark outline)
Definition → why RNN (feed-forward limitations) → architecture (folded + unrolled diagram) → equations → working with example "I love NLP" → BPTT → types → advantages (handles variable length, shared weights) → limitations (vanishing/exploding gradients etc.) → applications → conclusion (LSTM/GRU solve the problem).

### H. Keywords
`hidden state`, `time step`, `weight sharing`, `BPTT`, `tanh`, `sequential data`, `unrolling`, `vanishing gradient`, `long-term dependency`.

---

## 2. Vanishing and Exploding Gradients  ⭐ P2

### A. Definition
- **Vanishing gradient:** during BPTT, gradients are repeatedly multiplied by small numbers (<1) and shrink exponentially toward zero, so early layers/time steps stop learning.
- **Exploding gradient:** gradients are multiplied by large numbers (>1) and grow exponentially, making weights unstable (NaN).

### B. Simple Explanation
Like whispering a message through 50 people: it fades (vanishing) – or each person shouts louder until it becomes noise (exploding).

### C. Detailed Explanation
Gradient w.r.t. an early state = product of many Jacobians ∂hₜ/∂hₜ₋₁ = W_hhᵀ · diag(tanh′). If the largest eigenvalue/derivative < 1 → vanishing; > 1 → exploding. tanh′ ≤ 1 worsens vanishing.

| Problem | Effect | Remedy |
|---|---|---|
| Vanishing | Cannot learn long-range dependencies | **LSTM / GRU** gates, ReLU, residual links, good initialisation, attention |
| Exploding | Unstable loss, NaN | **Gradient clipping**, weight regularisation |

**How LSTM/GRU solve it:** the **cell state** (LSTM) acts as a "conveyor belt" with additive updates (Cₜ = fₜ⊙Cₜ₋₁ + iₜ⊙C̃ₜ). Gradients flow through addition rather than repeated multiplication, and gates learn what to keep or erase.

### H. Keywords
`BPTT`, `Jacobian`, `gradient clipping`, `long-term dependency`, `additive update`, `gating`.

---

## 3. Long Short-Term Memory (LSTM)  ⭐ P1

### A. Definition
**LSTM** (Hochreiter & Schmidhuber, 1997) is a special RNN architecture with a **cell state** and three **gates** (forget, input, output) that regulate the flow of information, enabling it to learn long-term dependencies.

### B. Simple Explanation
Think of a notebook (cell state). The **forget gate** decides what to erase, the **input gate** decides what new notes to write, and the **output gate** decides what to read out right now.

### C. Detailed Explanation
Inputs at step t: xₜ, previous hidden state hₜ₋₁, previous cell state Cₜ₋₁.

| Component | Equation | Purpose |
|---|---|---|
| **Forget gate** | fₜ = σ(W_f·[hₜ₋₁, xₜ] + b_f) | Value 0–1 per cell element: 0 = forget, 1 = keep old memory |
| **Input gate** | iₜ = σ(W_i·[hₜ₋₁, xₜ] + b_i) | Decides how much of new candidate to write |
| **Candidate memory** | C̃ₜ = tanh(W_C·[hₜ₋₁, xₜ] + b_C) | New information proposed |
| **Cell state update** | **Cₜ = fₜ ⊙ Cₜ₋₁ + iₜ ⊙ C̃ₜ** | Long-term memory |
| **Output gate** | oₜ = σ(W_o·[hₜ₋₁, xₜ] + b_o) | Decides which part of memory to expose |
| **Hidden state** | **hₜ = oₜ ⊙ tanh(Cₜ)** | Short-term output to next step/layer |

(σ = sigmoid, ⊙ = element-wise multiplication)

### D. Diagram (easy to redraw)
```
                 ┌───────────── Cell state (long-term memory) ─────────────┐
 C(t-1) ───────► ×(forget) ─────────► + ──────────────────────────────────► C(t)
                  ▲                    ▲                    │
                  │ f(t)               │ i(t) × C~(t)       ▼ tanh
                 [σ]            [σ]   [tanh]       [σ]      ×(output) ──► h(t)
                  ▲              ▲      ▲           ▲ o(t)
 h(t-1) ──────────┴──────────────┴──────┴───────────┘
 x(t)   ──────────┘ (h(t-1) and x(t) are concatenated and fed to all gates)
```
Forget gate → Input gate (+candidate) → Update cell → Output gate → New hidden state.

### Working (step-wise)
1. Forget gate removes irrelevant old information from Cₜ₋₁.
2. Input gate + candidate adds new relevant information.
3. Cell state is updated (addition → stable gradients).
4. Output gate filters Cₜ to produce hₜ.

**Advantages:** learns long dependencies, mitigates vanishing gradient. **Disadvantages:** more parameters (4× RNN), slower, sequential, still struggles with very long sequences.
**Applications:** language modelling, translation, speech recognition, text generation.

### G. Exam Answer (12-mark outline)
Definition → need (RNN's vanishing gradient) → architecture diagram → each gate (purpose + equation) → cell state & hidden state update → step-wise working → comparison with RNN → advantages/limitations → applications.

### H. Keywords
`cell state`, `forget gate`, `input gate`, `output gate`, `candidate memory`, `sigmoid`, `tanh`, `Hadamard product`, `long-term memory`, `constant error carousel`.

---

## 4. Gated Recurrent Unit (GRU)  ⭐ P2

### A. Definition
**GRU** (Cho et al., 2014) is a simplified gated RNN with two gates – **update gate** and **reset gate** – and no separate cell state.

### B. Simple Explanation
GRU is a "lighter LSTM": it merges memory and hidden state and uses fewer gates, so it trains faster but still remembers well.

### C. Detailed Explanation
- **Update gate:** zₜ = σ(W_z·[hₜ₋₁, xₜ]) – how much of the past to keep vs. new candidate.
- **Reset gate:** rₜ = σ(W_r·[hₜ₋₁, xₜ]) – how much past to ignore when forming candidate.
- **Candidate:** h̃ₜ = tanh(W·[rₜ ⊙ hₜ₋₁, xₜ])
- **Hidden state:** hₜ = (1 − zₜ) ⊙ hₜ₋₁ + zₜ ⊙ h̃ₜ

```
 h(t-1) ──► [reset r(t)] ──► candidate h~(t) ──┐
    │                                         ▼
    └──────────────► [update z(t)] ──► blend ──► h(t)
 x(t) feeds both gates and the candidate
```

### E. Comparison – RNN vs LSTM vs GRU  ⭐ P2
| Basis | RNN | LSTM | GRU |
|---|---|---|---|
| Gates | None | 3 (forget, input, output) | 2 (update, reset) |
| Memory | Hidden state only | Cell state + hidden state | Hidden state only |
| Parameters | Fewest | Most | Medium |
| Long-term dependency | Poor | Very good | Good |
| Vanishing gradient | Severe | Reduced | Reduced |
| Training speed | Fast | Slowest | Faster than LSTM |
| Best for | Short sequences | Long, complex sequences | Smaller data, speed |

### G. Exam Answer (2-mark)
"GRU is a gated RNN variant using two gates – the update gate (balances old vs new information) and the reset gate (decides how much past information to forget) – without a separate cell state."

### H. Keywords
`update gate`, `reset gate`, `candidate hidden state`, `fewer parameters`, `Cho et al.`.

---

## 5. Sequence-to-Sequence (Encoder–Decoder) Model  ⭐ P1/P2

### A. Definition
A **Sequence-to-Sequence (Seq2Seq)** model maps an input sequence to an output sequence (possibly of different length) using an **encoder** that compresses the input into a fixed-length **context vector** and a **decoder** that generates the output sequence from it.

### B. Simple Explanation
Translation: a human first *listens to and understands the whole French sentence* (encoder), keeps the meaning in mind (context vector), and then *speaks it in English* (decoder).

### C. Detailed Explanation
- **Encoder:** RNN/LSTM/GRU reads tokens x₁…x_T one by one; final hidden state = **context vector c**.
- **Decoder:** another RNN initialised with c; generates yₜ one token at a time, feeding previous output as next input, until `<EOS>`.
- Training uses **teacher forcing** (feed the true previous word); inference uses greedy/beam search.

### D. Diagram
```
 Input: "I love NLP"                                      Output: "Me encanta NLP"
 ┌────────── ENCODER ──────────┐  context   ┌──────────── DECODER ───────────┐
 x1:I  → [LSTM] → [LSTM] → [LSTM] ──► c ──► [LSTM] → [LSTM] → [LSTM] → <EOS>
          h1        h2       h3            ↓ "Me"    ↓ "encanta" ↓ "NLP"
                       (final hidden state = fixed-length vector)
```

### Limitation of a basic Encoder–Decoder for long sequences  ⭐ P1 (2 marks, Mid-1 Q1c)
All information in the input sentence must be squeezed into **one fixed-length context vector** (information bottleneck). For long sentences this vector cannot store all details, early words are forgotten, and performance (e.g., BLEU) **drops sharply as the length increases**. The decoder also cannot "look back" at specific input words. → **Solution: Attention mechanism.**

### Applications of Seq2Seq
Machine translation, text summarization, chatbots/dialogue, speech recognition, image captioning, code generation, question answering.

### G. Exam Answer (12-mark outline)
Definition → architecture diagram → encoder → context vector → decoder → training (teacher forcing) & inference (beam search) → machine translation example → limitations (bottleneck) → applications → conclusion (attention).

### H. Keywords
`encoder`, `decoder`, `context vector`, `bottleneck`, `<SOS>/<EOS>`, `teacher forcing`, `beam search`, `variable-length`.

---

## 6. Attention Mechanism  ⭐ P1

### A. Definition
**Attention** is a mechanism that lets the decoder, at every output step, **focus on the most relevant parts of the input sequence** by computing a weighted sum of all encoder hidden states, instead of relying on a single fixed context vector.

### B. Simple Explanation
When translating a long sentence, a human *looks back at the specific source words* related to the word being written. Attention gives the model the same ability. *Intuition:* "pay more attention to relevant words, less to irrelevant ones."

### C. Detailed Explanation – Step by Step
For decoder step *i*, with decoder state sᵢ₋₁ and encoder states h₁…h_T:

1. **Attention scores (alignment scores):** how well input word j matches the current output position.
   - Bahdanau (additive): **eᵢⱼ = vᵀ tanh(W_s·sᵢ₋₁ + W_h·hⱼ)**
   - Luong (dot-product): eᵢⱼ = sᵢᵀ·hⱼ
2. **Attention weights:** normalise using softmax → **αᵢⱼ = exp(eᵢⱼ) / Σₖ exp(eᵢₖ)** ; weights are between 0 and 1 and sum to 1.
3. **Context vector:** weighted sum → **cᵢ = Σⱼ αᵢⱼ · hⱼ** (different for every output step).
4. **Decoder output:** sᵢ = f(sᵢ₋₁, yᵢ₋₁, cᵢ) ; predict yᵢ from [sᵢ ; cᵢ].

**Example:** Translating "I love NLP" → "Me encanta NLP": while producing "encanta", weights may be α = (0.1, 0.8, 0.1) over (I, love, NLP) → context vector ≈ mostly h(love).

### D. Diagram
```
 Encoder states:   h1(I)    h2(love)   h3(NLP)
                    │         │          │
 Decoder state s(i-1) ─► [Score e_ij] each ─► e1, e2, e3
                              │
                         [Softmax] ─► α1=0.1  α2=0.8  α3=0.1
                              │
                  c_i = α1·h1 + α2·h2 + α3·h3   (context vector)
                              │
                    [s(i-1), y(i-1), c_i] ─► Decoder ─► y_i
```

### E. Comparison – Basic Seq2Seq vs Attention-based Seq2Seq  ⭐ P2
| Basis | Basic Seq2Seq | Seq2Seq with Attention |
|---|---|---|
| Context | One fixed vector for entire output | New context vector at every step |
| Encoder states used | Only the last | **All** hidden states |
| Long sequences | Poor (bottleneck) | Good |
| Alignment | None | Learns soft alignment (interpretable) |
| Computation | Cheaper | More costly |
| Performance | Drops with length | Stable |

**Why attention improves long sequences:** removes the bottleneck, gives direct access to every source word, shortens gradient paths (helps vanishing gradient), and gives interpretability through alignment maps.

### G. University Exam Answer (12-mark outline)
Introduction (bottleneck problem) → definition & intuition → architecture diagram → steps: scores → softmax weights → context vector → decoder → example with numbers → types (additive/Bahdanau, multiplicative/Luong, self-attention) → advantages (long sequences, interpretability) / disadvantages (extra computation) → applications (MT, summarization) → conclusion (leads to Transformer).

### H. Keywords
`alignment score`, `attention weights`, `softmax`, `context vector`, `Bahdanau`, `Luong`, `soft alignment`, `weighted sum`, `bottleneck`, `query–key`.

---

## 7. Applications: Text Generation, Summarization, Machine Translation  ⭐ P3

### 7.1 Text Generation
**Definition:** automatically producing coherent text by predicting the next token repeatedly. **Working:** train an RNN/LSTM language model; given a seed, sample next word (greedy/beam/top-k) and feed back. **Example:** autocomplete "How are" → "you". **Uses:** chatbots, story/poetry writing, email suggestions, code completion.

### 7.2 Text Summarization
**Definition:** condensing a document while preserving key information.
- **Extractive:** selects important sentences (TextRank).
- **Abstractive:** generates new sentences with Seq2Seq + attention (like a human).
**Metric:** ROUGE. **Challenges:** factual errors, repetition, long documents.

### 7.3 Machine Translation (MT)
**Definition:** automatic translation from a source language to a target language. **Evolution:** rule-based → statistical (SMT) → neural (NMT: Seq2Seq + attention) → Transformer. **Metric:** BLEU. **Example:** "Good morning" → "Subha udayam" (Telugu).

| Application | Model | Input → Output | Metric |
|---|---|---|---|
| Text generation | LSTM/GRU LM | Seed → text | Perplexity |
| Summarization | Seq2Seq + attention | Long text → short text | ROUGE |
| Machine translation | Seq2Seq + attention | Source lang → target lang | BLEU |

---

## 8. Evaluation Metrics: BLEU and ROUGE  ⭐ P1

### A. Definition
- **BLEU (Bilingual Evaluation Understudy):** precision-based metric that measures n-gram overlap between machine-generated (candidate) text and human reference(s), with a brevity penalty. Used mainly for **machine translation**.
- **ROUGE (Recall-Oriented Understudy for Gisting Evaluation):** recall-oriented metrics that measure overlap of n-grams / longest common subsequence between a generated summary and reference summary. Used mainly for **summarization**.

### B. Simple Explanation
Both compare *machine output with human reference*. BLEU asks "how much of what the machine wrote is correct?" (precision). ROUGE asks "how much of the reference content did the machine capture?" (recall).

### C. Detailed Explanation
**BLEU formula:** BLEU = BP · exp( Σₙ wₙ log pₙ ), usually N = 4, wₙ = 1/4
- pₙ = modified (clipped) n-gram precision.
- **Brevity penalty:** BP = 1 if c > r; BP = e^(1 − r/c) if c ≤ r (c = candidate length, r = reference length). Penalises too-short outputs.
- Range 0–1 (or 0–100).

**ROUGE variants**
- **ROUGE-1:** unigram overlap. **ROUGE-2:** bigram overlap. **ROUGE-L:** Longest Common Subsequence (captures sentence-level word order, no need for consecutive match).
- Recall = overlapping n-grams ÷ n-grams in reference; Precision = overlapping ÷ n-grams in candidate; F1 = 2PR/(P+R).

### Numerical Example (BLEU and ROUGE)
Reference: **"the cat is on the mat"** (6 words)  Candidate: **"the cat sat on the mat"** (6 words)

| Metric | Calculation | Result |
|---|---|---|
| Unigram matches | the(2), cat, on, mat = 5 (clipped: ref has "the" twice) | p₁ = 5/6 = 0.833 |
| Bigrams: cand = {the cat, cat sat, sat on, on the, the mat}; ref = {the cat, cat is, is on, on the, the mat} | matches = the cat, on the, the mat = 3 | p₂ = 3/5 = 0.60 |
| Trigrams: cand = {the cat sat, cat sat on, sat on the, on the mat}; ref = {the cat is, cat is on, is on the, on the mat} | matches = on the mat = 1 | p₃ = 1/4 = 0.25 |
| 4-grams | matches = 0 | p₄ = 0 (BLEU-4 = 0 without smoothing) |
| Brevity penalty | c = r = 6 → BP = 1 | 1 |
| **BLEU-2** | BP·√(p₁·p₂) = √(0.833×0.6) | **≈ 0.707** |
| **ROUGE-1** | Recall = 5/6, Precision = 5/6, F1 | **0.833** |
| **ROUGE-2** | Recall = 3/5, Precision = 3/5, F1 | **0.60** |
| **ROUGE-L** | LCS = "the cat on the mat" = 5; R = 5/6, P = 5/6, F1 | **0.833** |

**Interpretation:** the candidate is fairly close to the reference; the single wrong word ("sat" for "is") reduces higher-order overlap.

### E. Comparison – BLEU vs ROUGE  ⭐ P1
| Basis | BLEU | ROUGE |
|---|---|---|
| Focus | **Precision** | **Recall** (F-score also reported) |
| Primary task | Machine translation | Text summarization |
| Measures | Modified n-gram precision (1–4) + brevity penalty | ROUGE-N overlap, ROUGE-L (LCS) |
| Length handling | Brevity penalty | Recall rewards covering reference |
| Limitation (both) | Surface overlap only – ignores meaning/synonyms | Same |

### G. Exam Answer (2-mark)
"BLEU is a precision-based n-gram overlap metric (with brevity penalty) used to evaluate machine translation. ROUGE is a recall-oriented metric (ROUGE-N, ROUGE-L) used to evaluate summaries by comparing them with reference summaries."

### H. Keywords
`n-gram precision`, `clipping`, `brevity penalty`, `reference`, `candidate`, `recall`, `LCS`, `ROUGE-N`, `ROUGE-L`, `smoothing`.

---

## 9. Quick 2-Mark Sheet (Unit II)

| Question | Crisp answer |
|---|---|
| What is RNN? Why suitable for sequential data? | NN with feedback loop; hidden state carries earlier context; weights shared across time steps; handles variable-length order-dependent data. |
| Hidden state | hₜ = tanh(W_xh xₜ + W_hh hₜ₋₁ + b): memory summarising all inputs seen so far. |
| Vanishing / exploding gradient | Gradients shrink to 0 / blow up during BPTT; fixes: LSTM/GRU, gradient clipping. |
| GRU gates | Update gate, reset gate. |
| LSTM gates | Forget, input, output gates (+ cell state). |
| Role of gates | Forget: erase old memory; Input: write new memory; Output: expose memory as hidden state. |
| Seq2Seq roles | Encoder compresses input into context vector; decoder generates output sequence. |
| Encoder–decoder limitation | Fixed-length context vector bottleneck → poor for long sequences. |
| Attention; score; context vector | Focus on relevant input words; score = relevance of each encoder state; context = weighted sum of encoder states. |
| Seq2Seq applications | MT, summarization, chatbot. |
| BLEU vs ROUGE | BLEU = precision (MT); ROUGE = recall (summarization). |
