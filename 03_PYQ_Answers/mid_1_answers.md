# Mid-1 Examination – Questions with Answers (Units I & II)

**Paper pattern (as printed):** Part-A: Answer all questions (5 × 2 = 10 M) · Part-B: Answer any two questions (2 × 10 = 20 M); each Part-B question has parts (a) and (b) of 5 marks each.
**Units covered:** Unit I (Foundations of NLP) and Unit II (Deep Learning for NLP).

> **Reading note:** The question paper photo is clear except that the last line of Q4(b) is partly cut off at the bottom edge; the question is reproduced as it can be read ("…Describe attention scores, weights and the formation of the context vector"). Handwritten tick marks on the paper are ignored.
> **Depth note:** Each Part-B part carries 5 marks here. In the semester paper each question is 12 marks (two parts), so the answers below are written at *full descriptive depth*; for a 5-mark answer, write the bold points, one diagram and one example.
> **Repetition note:** Topics of Q1(a), Q1(b), Q1(e), Q2(a), Q2(b), Q4(a) are Unit I; Q1(c), Q1(d), Q3(a), Q3(b), Q4(b) are Unit II. See `unit_1.md` and `unit_2.md` for the complete theory.

---

# PART – A (2 Marks each)

### Q1(a) Differentiate between stemming and lemmatization with examples.
**Unit I – Text preprocessing | 2 Marks**

| Basis | Stemming | Lemmatization |
|---|---|---|
| Meaning | Chops prefixes/suffixes using heuristic rules to get a **stem** | Converts a word to its dictionary base form (**lemma**) using vocabulary + POS |
| Output | May not be a valid word | Always a valid word |
| Speed / accuracy | Fast, less accurate | Slower, more accurate |
| Examples | studies → **studi**, caring → **car**, running → **run** | studies → **study**, caring → **care**, better → **good**, was → **be** |
| Tools | Porter, Snowball, Lancaster | WordNet lemmatizer, spaCy |

**Keywords:** `stem`, `lemma`, `rule-based`, `dictionary`, `POS`, `Porter stemmer`.

---

### Q1(b) What is perplexity? What does a lower perplexity indicate?
**Unit I – N-gram language models | 2 Marks**

**Perplexity** is the standard intrinsic evaluation metric of a language model. It is the inverse probability of the test set normalised by the number of words:

**PP(W) = P(w₁ w₂ … w_N)^(−1/N)**

It can be interpreted as the *weighted average branching factor* – how many words the model is "confused" between at each step.

**A lower perplexity indicates a better language model**: it assigns higher probability to the test data, i.e. it predicts the next word with more confidence and less "surprise". (Example: PP = 1.3 is far better than PP = 200.)

**Keywords:** `inverse probability`, `normalised by N`, `branching factor`, `intrinsic evaluation`.

---

### Q1(c) What is the limitation of a basic Encoder–Decoder model for long sequences
**Unit II – Seq2Seq | 2 Marks**

In a basic encoder–decoder (Seq2Seq) model the encoder compresses the **entire input sequence into a single fixed-length context vector**. For long sequences this creates an **information bottleneck**: the vector cannot hold all the information, earlier words are forgotten (also because of vanishing gradients in RNN/LSTM), and translation/generation quality drops sharply as input length increases. The decoder also cannot look back at specific input words. This is solved by the **attention mechanism**, which gives the decoder access to all encoder hidden states.

**Keywords:** `fixed-length context vector`, `bottleneck`, `long-range information loss`, `attention`.

---

### Q1(d) What are BLEU and ROUGE?
**Unit II – Evaluation metrics | 2 Marks**

- **BLEU (Bilingual Evaluation Understudy):** a **precision-based** metric that computes modified n-gram (1–4) overlap between machine output and human reference(s), multiplied by a **brevity penalty**; mainly used for **machine translation**. Formula: BLEU = BP·exp(Σ wₙ log pₙ).
- **ROUGE (Recall-Oriented Understudy for Gisting Evaluation):** a **recall-based** set of metrics (ROUGE-1, ROUGE-2, ROUGE-L based on longest common subsequence) comparing a generated summary with reference summaries; mainly used for **text summarization**.

**Keywords:** `n-gram overlap`, `precision`, `recall`, `brevity penalty`, `LCS`, `reference`, `candidate`.

---

### Q1(e) What is SVM? What is meant by a hyperplane and margin?
**Unit I – Text classification | 2 Marks**

**Support Vector Machine (SVM)** is a supervised learning algorithm that classifies data by finding the **optimal separating hyperplane that maximises the margin** between classes.
- **Hyperplane:** the decision boundary **w·x + b = 0** that separates the classes (a line in 2-D, a plane in 3-D, a hyperplane in higher dimensions).
- **Margin:** the distance between the hyperplane and the nearest data points of each class (the **support vectors**); margin = **2/‖w‖**. A larger margin gives better generalisation.

```
   o  o        ── w·x+b = +1
     o   o
 ─────────────  w·x+b = 0   (hyperplane)
     x   x
   x    x      ── w·x+b = −1      margin = 2/‖w‖
```
**Keywords:** `hyperplane`, `margin`, `support vectors`, `maximum margin`, `kernel`.

---

# PART – B (5 + 5 Marks per question; answer any TWO questions)

## Q2(a) Explain word representation techniques, one-hot encoding and TF-IDF. Illustrate TF-IDF calculation with a suitable numerical example.
**Unit I – Word representations | 5 Marks (P1)**

### 1. Introduction
Computers cannot process raw words; a **word representation** converts words into numerical vectors. Techniques are classified as:
```
Word Representation
├── Sparse / count-based : One-hot, Bag-of-Words, TF-IDF
└── Dense / predictive   : Word2Vec (CBOW, Skip-gram), GloVe, contextual (BERT)
```

### 2. One-hot encoding
Each word is represented by a vector of length |V| with a single 1 at its index.
Vocabulary = [cat, dog, king]: cat = [1,0,0], dog = [0,1,0], king = [0,0,1].
**Limitations:** very high dimensional and sparse; all vectors orthogonal (cat·dog = 0), so **no semantic similarity**; no context; cannot handle out-of-vocabulary words.

### 3. TF-IDF
**TF-IDF** weights a word by its importance in a document relative to the corpus.
- **TF(t,d)** = (occurrences of t in d) / (total terms in d)
- **IDF(t)** = log(N / df(t)) (N = number of documents; df = documents containing t)
- **TF-IDF = TF × IDF**
A word frequent in one document but rare in the corpus gets a high score; very common words (the, is) get ≈ 0.

### 4. Numerical example (log base 10)
- D1: *the cat sat on the mat*  D2: *the dog sat on the log*  D3: *the cat chased the dog*  (N = 3)

| Term | df | IDF = log10(3/df) | TF in D1 (6 words) | TF-IDF (D1) |
|---|---|---|---|---|
| the | 3 | log10(1) = 0 | 2/6 = 0.333 | **0** |
| cat | 2 | log10(1.5) = 0.176 | 1/6 = 0.167 | **0.0294** |
| sat | 2 | 0.176 | 0.167 | **0.0294** |
| on | 2 | 0.176 | 0.167 | **0.0294** |
| mat | 1 | log10(3) = 0.477 | 0.167 | **0.0795** |

**Interpretation:** "mat" is the most characteristic word of D1; "the" carries no information (0).

### 5. Comparison
| | One-hot | TF-IDF |
|---|---|---|
| Value | Binary | Weighted real value |
| Uses frequency | No | Yes |
| Importance of word | Not captured | Captured |
| Semantics | None | None |

### 6. Conclusion
One-hot and TF-IDF are simple sparse representations; TF-IDF improves on one-hot by weighting; both lack semantics, which is addressed by dense embeddings (Word2Vec, GloVe).

**Keywords:** `sparse vector`, `term frequency`, `inverse document frequency`, `corpus`, `weighting`, `orthogonal`.

---

## Q2(b) Explain POS tagging and NER in detail with suitable examples. Differentiate between their objectives and applications.
**Unit I – Sequence labelling | 5 Marks (P1)**

### 1. POS Tagging
**Definition:** assigning a grammatical category (part of speech) to each word of a sentence.
**Example (Penn Treebank):**
`The/DT  quick/JJ  brown/JJ  fox/NN  jumps/VBZ  over/IN  the/DT  lazy/JJ  dog/NN`
**Common tags:** NN (noun), VB (verb), JJ (adjective), RB (adverb), DT (determiner), IN (preposition), PRP (pronoun).
**Approaches:** rule-based; statistical (HMM + Viterbi, CRF); deep learning (BiLSTM-CRF, BERT).
**Challenge:** ambiguity ("book a flight" – verb; "read a book" – noun).

### 2. Named Entity Recognition (NER)
**Definition:** identifying and classifying real-world named entities (person, organisation, location, date, money …) in text.
**Example:** *"Sundar Pichai is the CEO of Google in California."*
`Sundar/B-PER Pichai/I-PER is/O the/O CEO/O of/O Google/B-ORG in/O California/B-LOC`
**BIO scheme:** B = beginning of entity, I = inside, O = outside.
**Approaches:** gazetteers/rules → CRF → BiLSTM-CRF → fine-tuned BERT.
**Challenges:** ambiguity (Apple – fruit/company), unseen names, nested entities.

### 3. Difference – Objectives and Applications
| Basis | POS Tagging | NER |
|---|---|---|
| **Objective** | Find the **grammatical role** of every word | Find and classify **named real-world entities** |
| Level | Syntactic | Semantic / information extraction |
| Labels | NN, VB, JJ, DT … | PER, ORG, LOC, DATE, MONEY … |
| Tokens tagged | All tokens | Only entity spans (others = O) |
| **Applications** | Syntactic parsing, lemmatization, machine translation, speech synthesis, grammar checking | Information extraction, question answering, search engines, resume parsing, news analytics, chatbots |

### 4. Conclusion
Both are sequence-labelling tasks; POS tags often serve as features for NER, and together they form the base of many NLP pipelines.

**Keywords:** `sequence labelling`, `tagset`, `HMM`, `Viterbi`, `CRF`, `BIO`, `gazetteer`, `entity`.

---

## Q3(a) Explain the architecture and working of RNN with a suitable sequential example. Also discuss its limitations.
**Unit II – RNN | 5 Marks (P1)**

### 1. Introduction / Definition
A **Recurrent Neural Network** is a neural network for sequential data in which a **hidden state** is passed from one time step to the next, giving the network a memory of previous inputs. The same weights are shared across all time steps.

### 2. Architecture
```
 Folded:  x → [ h ] → y  (h loops back to itself)

 Unrolled:   y1      y2      y3
              ↑       ↑       ↑
 h0 ──► [h1] ──► [h2] ──► [h3]
              ↑       ↑       ↑
             x1      x2      x3
```
**Components:** input xₜ, hidden state hₜ, output yₜ, weights W_xh, W_hh, W_hy.
**Equations:**
- hₜ = tanh( W_xh·xₜ + W_hh·hₜ₋₁ + b_h )
- yₜ = softmax( W_hy·hₜ + b_y )

### 3. Working with an example (next-word prediction)
Sentence: **"I love NLP"**
1. t = 1: input "I", h₀ = 0 → h₁ stores "I" → predicts "love".
2. t = 2: input "love" + h₁ → h₂ stores "I love" → predicts "NLP".
3. t = 3: input "NLP" + h₂ → h₃ stores "I love NLP" → predicts end-of-sentence.
The hidden state carries the sentence history forward.
**Training:** Backpropagation Through Time (BPTT) over the unrolled network.
**Types:** one-to-many (image captioning), many-to-one (sentiment), many-to-many (translation, NER).

### 4. Limitations
1. **Vanishing gradient** – cannot learn long-term dependencies.
2. **Exploding gradient** – unstable training (needs gradient clipping).
3. **Short-term memory** – forgets early words in long sequences.
4. **Sequential processing** – no parallelisation, slow training.
5. Difficult to train; limited capacity → replaced by LSTM/GRU and Transformers.

### 5. Applications
Language modelling, sentiment analysis, speech recognition, POS tagging, machine translation (as encoder/decoder).

**Keywords:** `hidden state`, `time step`, `BPTT`, `weight sharing`, `tanh`, `vanishing gradient`, `long-term dependency`.

---

## Q3(b) Explain LSTM architecture in detail, including cell state, forget gate, input gate and output gate.
**Unit II – LSTM | 5 Marks (P1)**

### 1. Introduction
**LSTM (Long Short-Term Memory)** (Hochreiter & Schmidhuber, 1997) is an improved RNN with a **cell state** and **three gates** that control what to forget, what to store and what to output. It solves the vanishing-gradient problem and learns long-term dependencies.

### 2. Architecture Diagram
```
                 ┌──────────── Cell state (long-term memory) ───────────┐
 C(t-1) ───────► ×(forget)──────────► + ───────────────────────────────► C(t)
                  ▲                    ▲                   │
                  │ f(t)               │ i(t)×C~(t)        ▼ tanh
                 [σ]            [σ]   [tanh]       [σ]     ×(output) ─► h(t)
                  ▲              ▲      ▲           ▲ o(t)
 h(t-1) ──────────┴──────────────┴──────┴───────────┘
 x(t)   ─────────── concatenated with h(t-1) and fed to every gate
```

### 3. Components and Equations
| Component | Equation | Function |
|---|---|---|
| **Cell state Cₜ** | Cₜ = fₜ ⊙ Cₜ₋₁ + iₜ ⊙ C̃ₜ | Long-term memory "conveyor belt"; additive update keeps gradients stable |
| **Forget gate fₜ** | σ(W_f[hₜ₋₁, xₜ] + b_f) | Decides what fraction of the old cell state to keep (0 = forget, 1 = keep) |
| **Input gate iₜ** | σ(W_i[hₜ₋₁, xₜ] + b_i) | Decides how much of the new candidate information to write |
| **Candidate C̃ₜ** | tanh(W_C[hₜ₋₁, xₜ] + b_C) | Proposed new memory content |
| **Output gate oₜ** | σ(W_o[hₜ₋₁, xₜ] + b_o) | Decides which part of the cell state is output |
| **Hidden state hₜ** | oₜ ⊙ tanh(Cₜ) | Short-term output passed to next step/layer |

### 4. Working
1. Forget gate removes irrelevant old memory.
2. Input gate and candidate add new relevant information.
3. Cell state is updated.
4. Output gate filters the cell state to produce the new hidden state.
**Example:** In "Ravi lives in Hyderabad. **He** speaks Telugu", the LSTM keeps "Ravi/male" in the cell state across words until "He" is predicted.

### 5. Advantages / Disadvantages / Applications
Advantages: handles long-range dependencies, mitigates vanishing gradients. Disadvantages: more parameters, slower, still sequential. Applications: translation, speech recognition, text generation, sentiment analysis.

**Keywords:** `cell state`, `forget gate`, `input gate`, `output gate`, `sigmoid`, `tanh`, `Hadamard product`, `candidate memory`.

---

## Q4(a) Explain text classification using Naive Bayes. Illustrate the classification of a simple Spam/Ham message using probabilities.
**Unit I – Text classification | 5 Marks (P1)**

### 1. Text Classification
Assigning predefined labels to text (spam/ham, positive/negative, topic). Pipeline: **text → preprocessing → features (BoW/TF-IDF) → classifier → label.**

### 2. Naive Bayes Classifier
Probabilistic classifier based on **Bayes' theorem**:
**P(c | d) = P(d | c)·P(c) / P(d)**
With the **naive assumption** that words are conditionally independent given the class (hence "naive"):
**ĉ = argmax_c  P(c) · Π P(wᵢ | c)**
**Laplace smoothing:** P(w | c) = (count(w,c) + 1) / (N_c + |V|)

### 3. Numerical example – Spam/Ham
| Training message | Class |
|---|---|
| win cash now | Spam |
| win prize now | Spam |
| meeting at noon | Ham |
| lunch at noon | Ham |
| call me now | Ham |

- **Priors:** P(Spam) = 2/5 = 0.4; P(Ham) = 3/5 = 0.6
- **Vocabulary** |V| = 10 (win, cash, now, prize, meeting, at, noon, lunch, call, me)
- **Word totals:** Spam = 6 words (win 2, cash 1, now 2, prize 1); Ham = 9 words (meeting 1, at 2, noon 2, lunch 1, call 1, me 1, now 1)

**Test message: "win now"**
| | Spam | Ham |
|---|---|---|
| P(win \| c) | (2+1)/(6+10) = 3/16 = 0.1875 | (0+1)/(9+10) = 1/19 = 0.0526 |
| P(now \| c) | (2+1)/16 = 0.1875 | (1+1)/19 = 0.1053 |
| Score = P(c)·ΠP(w\|c) | 0.4 × 0.1875 × 0.1875 = **0.01406** | 0.6 × 0.0526 × 0.1053 = **0.00332** |

Spam score > Ham score → **the message is classified as SPAM** (≈ 81% after normalising).

### 4. Advantages / Disadvantages
Advantages: simple, very fast, works well with small data and high-dimensional text. Disadvantages: independence assumption is unrealistic, zero-frequency problem (solved by smoothing).

### 5. Applications
Spam filtering, sentiment analysis, news categorisation, document tagging.

**Keywords:** `Bayes theorem`, `prior`, `likelihood`, `posterior`, `conditional independence`, `Laplace smoothing`, `argmax`.

---

## Q4(b) Explain the attention mechanism in detail. Describe attention scores, weights and the formation of the context vector.
**Unit II – Attention | 5 Marks (P1)**
*(Question as read from the paper; the bottom line of the image is slightly cut off.)*

### 1. Need for attention
In a basic encoder–decoder, the whole input is compressed into **one fixed-length context vector**, which becomes a bottleneck for long sentences. **Attention** lets the decoder look at **all encoder hidden states** and focus on the most relevant ones at each output step. *Intuition:* a human translator looks back at the relevant source words while writing each target word.

### 2. Definition
Attention is a mechanism that computes, for every decoder step, a **weighted sum of encoder hidden states**, where weights reflect the relevance of each input position to the current output.

### 3. Step-by-step working
Let encoder states be h₁…h_T and decoder previous state sᵢ₋₁.

1. **Attention (alignment) scores:** measure how well input position j matches output step i.
   - Additive (Bahdanau): **eᵢⱼ = vᵀ tanh(W_s sᵢ₋₁ + W_h hⱼ)**
   - Dot-product (Luong): eᵢⱼ = sᵢᵀ hⱼ
2. **Attention weights:** apply softmax so weights are positive and sum to 1:
   **αᵢⱼ = exp(eᵢⱼ) / Σₖ exp(eᵢₖ)**
3. **Context vector:** weighted sum of encoder states:
   **cᵢ = Σⱼ αᵢⱼ hⱼ** (a new vector for every output step)
4. **Decoder update and output:** sᵢ = f(sᵢ₋₁, yᵢ₋₁, cᵢ); yᵢ predicted from [sᵢ ; cᵢ].

### 4. Diagram
```
 Encoder:     h1(I)    h2(love)   h3(NLP)
               │         │          │
 s(i-1) ──► score e_i1  e_i2      e_i3
                    │ softmax
              α1=0.1  α2=0.8  α3=0.1
                    │
        c_i = 0.1·h1 + 0.8·h2 + 0.1·h3  (context vector)
                    │
        [s(i-1), y(i-1), c_i] ─► Decoder ─► y_i  ("encanta")
```
**Example:** while generating the Spanish word "encanta" the weight on "love" is highest (0.8), so the context vector is mostly h₂.

### 5. Advantages / Disadvantages
Advantages: removes the bottleneck, handles long sequences, gives interpretable alignment, shortens gradient paths. Disadvantages: extra computation (O(T) per output step). Attention led to **self-attention and the Transformer** (Unit III).

### 6. Applications
Machine translation, summarization, image captioning, speech recognition.

**Keywords:** `alignment score`, `softmax`, `attention weights`, `context vector`, `Bahdanau`, `Luong`, `weighted sum`, `bottleneck`.
