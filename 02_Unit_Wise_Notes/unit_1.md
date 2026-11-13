# UNIT I – Foundations of NLP

**Syllabus:** Text preprocessing (tokenization, stemming, lemmatization) · Word embeddings (one-hot, TF-IDF, Word2Vec, GloVe) · N-gram language models, perplexity · POS tagging · Named Entity Recognition · Text classification (Naive Bayes, SVM, Logistic Regression)

> **Recommended use:** Unit 1 is *not* in the top-4 for descriptive answers (it is broad, with numericals), but it is **mandatory for the compulsory 2-mark Q1**. Learn Section 0 + the "Quick 2-Mark Sheet" at the end. If you have extra time, add TF-IDF, Naive Bayes and POS/NER long answers (these were asked in Mid-1).

---

## 0. Question Paper Analysis for Unit 1

### 0.1 Actual questions seen in the supplied papers (Mid-1)
| Mid-1 Q.No | Question (original wording) | Marks | Topic |
|---|---|---|---|
| 1(a) | Differentiate between stemming and lemmatization with examples. | 2 | Preprocessing |
| 1(b) | What is perplexity? What does a lower perplexity indicate? | 2 | N-gram LM |
| 1(e) | What is SVM? What is meant by a hyperplane and margin? | 2 | Classification |
| 2(a) | Explain word representation techniques, one-hot encoding and TF-IDF. Illustrate TF-IDF calculation with a suitable numerical example. | 5 | Embeddings |
| 2(b) | Explain POS tagging and NER in detail with suitable examples. Differentiate between their objectives and applications. | 5 | POS / NER |
| 4(a) | Explain text classification using Naive Bayes. Illustrate the classification of a simple Spam/Ham message using probabilities. | 5 | Naive Bayes |

> Note: In Mid-1 these questions carry 5 marks each (two parts of 5 = 10 marks). In the semester paper each part may carry about 6 marks (12 per question). Write the **12-mark depth** answer given below.

### 0.2 Question Bank (supplied by you – *expected*, not confirmed past papers)
Short: NLP definition, tokenization, stemming vs lemmatization, one-hot limitation, TF/IDF, CBOW vs Skip-gram, GloVe, N-grams, perplexity, POS tag examples, NER types, text classification applications, why Naive Bayes is "naive", SVM hyperplane/margin, sigmoid in Logistic Regression.
Long: preprocessing pipeline; one-hot + TF-IDF numerical; Word2Vec; GloVe vs Word2Vec; N-gram + perplexity numerical; POS+NER; Naive Bayes spam/ham; SVM; Logistic Regression; comparison NB/SVM/LR.

### 0.3 Priority classification
| Priority | Topics |
|---|---|
| **P1 (very important)** | TF-IDF (with numerical) · Naive Bayes spam/ham numerical · POS & NER · Stemming vs Lemmatization · Perplexity · SVM hyperplane/margin |
| **P2 (important)** | Word2Vec (CBOW/Skip-gram) · N-gram + sentence probability · Logistic Regression & sigmoid · One-hot limitation · GloVe vs Word2Vec |
| **P3 (additional)** | Tokenization types (BPE/WordPiece) · NB vs SVM vs LR comparison · NLP applications · Smoothing |

---

## 1. Text Preprocessing

### A. Definition
**Text preprocessing** is the process of cleaning and converting raw, unstructured text into a normalized, structured form that can be consumed by NLP algorithms and machine-learning models.

### B. Simple Explanation
Raw text is messy: capital letters, punctuation, emojis, "running/ran/runs". A computer cannot learn well from this noise. Preprocessing is like *washing and cutting vegetables before cooking* – we clean and standardise text before feeding it to a model.

### C. Detailed Explanation – The Pipeline
| Step | What it does | Example |
|---|---|---|
| 1. Data cleaning | Remove HTML tags, URLs, special symbols, extra spaces | `<p>Visit http://x.com!</p>` → `Visit` |
| 2. Lower-casing | Same word, same form | `Apple`, `APPLE` → `apple` |
| 3. **Tokenization** | Split text into tokens | `"I love NLP"` → `["I","love","NLP"]` |
| 4. Stop-word removal | Remove very frequent, low-information words (is, the, of) | `"the cat is on the mat"` → `cat mat` |
| 5. **Stemming / Lemmatization** | Reduce words to a base form | `studies, studying` → `studi` / `study` |
| 6. Normalisation | Expand contractions, correct spelling, handle numbers | `don't` → `do not` |

```
Raw Text ──► Cleaning ──► Lower-case ──► Tokenization ──► Stop-word ──► Stem/Lemma ──► Clean Tokens
                                                          removal                    (to vectorizer)
```

**Tokenization types**
- **Sentence tokenization** – split a paragraph into sentences.
- **Word tokenization** – split a sentence into words (`"Don't stop"` → `Do`, `n't`, `stop`).
- **Sub-word tokenization** – split rare words into pieces: **BPE** (used by GPT), **WordPiece** (used by BERT). Example: `unhappiness` → `un ##happi ##ness`. Solves the *out-of-vocabulary (OOV)* problem.
- **Character tokenization** – each character is a token.

**Challenges:** contractions, hyphenated words, abbreviations (`U.S.A.`), languages without spaces (Chinese), social-media text.

### D. Diagram
(The pipeline above is easy to redraw as boxes with arrows.)

### F. Real-world Example
Search engine: query `"Running shoes!"` → `running shoes` → tokens `running`, `shoes` → lemma `run`, `shoe` → matches documents about *run/runs/running shoe*.

### G. University Examination Answer – "Explain the NLP text preprocessing pipeline. Discuss tokenization, stemming and lemmatization."
1. **Introduction:** definition of preprocessing; why needed (noise, vocabulary size, consistency).
2. **Pipeline:** draw the flow diagram; one line per step with the example table.
3. **Tokenization:** types (sentence, word, sub-word, character) with one example each; mention BPE/WordPiece.
4. **Stemming** – heuristic suffix chopping (Porter) ; **Lemmatization** – dictionary + POS based (see Section 2).
5. **Advantages:** reduces vocabulary, improves accuracy, lowers computation. **Limitations:** information loss (removing "not" changes meaning), over-stemming.
6. **Conclusion:** Preprocessing quality directly affects downstream model performance.

### H. Important Keywords
`tokenization`, `corpus`, `token`, `vocabulary`, `stop words`, `normalization`, `OOV`, `BPE`, `WordPiece`, `n't clitic`, `noise removal`.

---

## 2. Stemming and Lemmatization

### A. Definition
- **Stemming:** A rule-based heuristic process that removes prefixes/suffixes from a word to obtain its *stem*, which may not be a valid dictionary word.
- **Lemmatization:** The process of reducing a word to its *lemma* (dictionary base form) using vocabulary and morphological analysis, considering the word's part of speech.

### B. Simple Explanation
Stemming = *"chop the ending with scissors"* (fast, crude). Lemmatization = *"look up the word in a dictionary"* (slower, correct).

### C. Detailed Explanation
- **Stemmers:** Porter (most common), Snowball (multi-language), Lancaster (aggressive).
- **Over-stemming:** different words → same stem (`university`, `universe` → `univers`).
- **Under-stemming:** related words → different stems (`data`, `datum`).
- **Lemmatizer** needs POS: `better` (adj) → `good`; `meeting` (noun) → `meeting`, `meeting` (verb) → `meet`. Tools: WordNet Lemmatizer, spaCy.

### E. Comparison Table
| Basis | Stemming | Lemmatization |
|---|---|---|
| Method | Rule-based suffix stripping | Dictionary/morphology + POS |
| Output | Stem (may be non-word) | Lemma (valid word) |
| Speed | Fast | Slower |
| Accuracy | Lower | Higher |
| Example | `studies`→`studi`, `caring`→`car` | `studies`→`study`, `caring`→`care` |
| Example 2 | `better`→`better` | `better`→`good` |
| Use | Search engines, IR | Chatbots, QA, sentiment |

### F. Real-world Example
`was, were, is, are` → lemma `be`; a stemmer cannot do this.

### G. Exam Answer (2-mark)
"Stemming is a crude rule-based process of chopping word endings to get a stem (`running`→`run`, `studies`→`studi`) – fast but may produce non-words. Lemmatization uses a dictionary and POS to return the valid base word (`studies`→`study`, `better`→`good`) – slower but more accurate."

### H. Keywords
`Porter stemmer`, `stem`, `lemma`, `morphological analysis`, `WordNet`, `over-stemming`, `under-stemming`.

---

## 3. Word Representations (Word Embeddings) – Overview

### A. Definition
A **word representation** (embedding) is a numerical vector that represents a word so that machines can process it. *Word embedding* usually means a dense, low-dimensional vector capturing meaning.

### B. Simple Explanation
Computers understand numbers, not words. We convert each word into a list of numbers. Good representations place *similar words close together*.

### C. Types / Classification
```
Word Representations
├── Sparse / Count-based : One-hot, Bag-of-Words, TF-IDF
└── Dense / Predictive   : Word2Vec (CBOW, Skip-gram), GloVe, (FastText, contextual BERT – Unit III)
```

---

## 3.1 One-Hot Encoding

### A. Definition
One-hot encoding represents each word as a binary vector of length |V| (vocabulary size) with a single `1` at the word's index and `0` everywhere else.

### B. Simple Explanation
If the vocabulary has 5 words, each word is a 5-slot switchboard with exactly one switch ON.

### C. Detailed Explanation
Vocabulary: `[cat, dog, king, queen, apple]`
| Word | cat | dog | king | queen | apple |
|---|---|---|---|---|---|
| cat | 1 | 0 | 0 | 0 | 0 |
| dog | 0 | 1 | 0 | 0 | 0 |
| king | 0 | 0 | 1 | 0 | 0 |

**Limitations (very important):**
1. **High dimensionality** – vector length = |V| (50,000+).
2. **Sparsity** – almost all zeros; memory-wasteful.
3. **No semantic similarity** – `cat·dog = 0`, same as `cat·apple = 0` (all vectors are orthogonal).
4. **No context / word order**.
5. **OOV problem** – new word has no index.

### G. Exam Answer (2-mark)
"One-hot encoding represents each word as a |V|-dimensional binary vector with a single 1. Major limitation: vectors are sparse, very high-dimensional, and all orthogonal, so they capture no semantic similarity between words."

### H. Keywords
`sparse`, `|V|-dimensional`, `orthogonal`, `curse of dimensionality`, `no semantics`.

---

## 3.2 TF-IDF (Term Frequency – Inverse Document Frequency)  ⭐ P1

### A. Definition
**TF-IDF** is a statistical weighting scheme that measures how important a word is to a document within a collection (corpus): it increases with the word's frequency in the document and decreases with its frequency across the corpus.

### B. Simple Explanation
A word is *important* if it appears **often in this document** (TF high) but **rarely in other documents** (IDF high). Words like "the" appear everywhere, so they get weight ≈ 0.

### C. Detailed Explanation
- **TF(t, d)** = (number of times t appears in d) / (total terms in d)
- **IDF(t)** = log( N / df(t) ) where N = total documents, df(t) = documents containing t
- **TF-IDF(t, d)** = TF(t, d) × IDF(t)

(Smoothed version used in libraries: `IDF = ln((1+N)/(1+df)) + 1`.)

**Advantages:** simple, fast, down-weights common words, good baseline for search and classification.
**Disadvantages:** ignores word order and semantics, sparse high-dimensional vectors, cannot handle synonyms.
**Applications:** search ranking, document similarity, keyword extraction, spam filtering.

### Numerical Example (log base 10)
Corpus (N = 3):
- D1: `the cat sat on the mat`
- D2: `the dog sat on the log`
- D3: `the cat chased the dog`

**Step 1 – Document frequency & IDF**
| Term | df | IDF = log10(3/df) |
|---|---|---|
| the | 3 | log10(1) = **0** |
| cat | 2 | log10(1.5) = **0.176** |
| sat | 2 | 0.176 |
| on | 2 | 0.176 |
| mat | 1 | log10(3) = **0.477** |

**Step 2 – TF in D1** (6 words): the = 2/6 = 0.333; cat = sat = on = mat = 1/6 = 0.167

**Step 3 – TF-IDF in D1**
| Term | TF | IDF | TF×IDF |
|---|---|---|---|
| the | 0.333 | 0 | **0** |
| cat | 0.167 | 0.176 | **0.0294** |
| sat | 0.167 | 0.176 | 0.0294 |
| on | 0.167 | 0.176 | 0.0294 |
| mat | 0.167 | 0.477 | **0.0795** |

**Interpretation:** "mat" is the most distinctive word of D1 (appears only there); "the" is useless (weight 0).

### F. Real-world Example
Google-like search: for query "transformer architecture", documents where these words are frequent but the words are rare in the whole web get ranked higher.

### G. University Exam Answer (12-mark outline)
1. **Introduction:** need for word representation; classification (sparse vs dense).
2. **One-hot encoding:** definition, example table, limitations.
3. **TF-IDF:** definition, formulas for TF, IDF, TF-IDF.
4. **Numerical example:** the 3-document example above with steps.
5. **Comparison one-hot vs TF-IDF** (table below).
6. **Advantages/limitations, applications. Conclusion** – leads to dense embeddings (Word2Vec, GloVe).

| Feature | One-hot | TF-IDF |
|---|---|---|
| Value | 0/1 | Real-valued weight |
| Word importance | No | Yes |
| Frequency used | No | Yes (TF + IDF) |
| Semantics | None | None |
| Sparsity | Very high | High |

### H. Keywords
`term frequency`, `inverse document frequency`, `corpus`, `weighting`, `sparse vector`, `bag of words`, `document similarity`, `log scaling`.

---

## 3.3 Word2Vec  ⭐ P2

### A. Definition
**Word2Vec** (Mikolov et al., Google, 2013) is a shallow two-layer neural network model that learns dense, low-dimensional word vectors from large text such that words appearing in similar contexts have similar vectors.

### B. Simple Explanation
"You shall know a word by the company it keeps." Word2Vec reads a lot of text and learns that `king` and `queen` appear in similar sentences, so their vectors become close.

### C. Detailed Explanation
Two architectures:

**1. CBOW (Continuous Bag of Words)** – predicts the **centre word from surrounding context words**.
**2. Skip-gram** – predicts the **context words from the centre word**.

```
CBOW                                   Skip-gram
 w(t-2) ─┐                                      ┌─► w(t-2)
 w(t-1) ─┤                                      ├─► w(t-1)
         ├─► [Projection/Average] ─► w(t)  w(t) ─┤
 w(t+1) ─┤                                      ├─► w(t+1)
 w(t+2) ─┘                                      └─► w(t+2)
 (Input: context → Output: centre)    (Input: centre → Output: context)
```

**Working (both):** One-hot input → hidden layer (embedding matrix W, size |V|×d, no activation) → output layer (softmax over |V|). After training, the rows of W are the word vectors. **Training tricks:** *negative sampling* and *hierarchical softmax* make training fast.

**Example:** window = 2, sentence `I love natural language processing`. For centre `natural`: context = {I, love, language, processing}. Skip-gram pairs: (natural, love), (natural, language), …

**Famous property:** vector(`king`) − vector(`man`) + vector(`woman`) ≈ vector(`queen`).

### E. Comparison Table
| Basis | CBOW | Skip-gram |
|---|---|---|
| Predicts | Centre from context | Context from centre |
| Speed | Faster | Slower |
| Works best with | Large data, frequent words | Small data, **rare words** |
| Accuracy | Good | Generally better semantic quality |

### Advantages / Limitations
**Adv.:** dense, captures semantic + syntactic relations, fast. **Lim.:** one vector per word (cannot handle polysemy: `bank` river vs money), OOV words, uses only local window context.

### G. Exam Answer (outline)
Definition → intuition → CBOW architecture + diagram → Skip-gram architecture + diagram → training (softmax, negative sampling) → comparison table → advantages, limitations, applications (similarity, analogy, input features to classifiers) → conclusion.

### H. Keywords
`distributional hypothesis`, `context window`, `embedding matrix`, `negative sampling`, `hierarchical softmax`, `dense vector`, `analogy`, `polysemy`.

---

## 3.4 GloVe  ⭐ P2

### A. Definition
**GloVe (Global Vectors for Word Representation)** (Pennington, Socher, Manning – Stanford, 2014) is an unsupervised model that learns word vectors by factorising the **global word–word co-occurrence matrix** of a corpus.

### B. Simple Explanation
Word2Vec looks at small windows one at a time. GloVe first *counts how often every pair of words occurs together in the entire corpus*, then learns vectors so that the dot product of two vectors matches the log of their co-occurrence count.

### C. Detailed Explanation
1. Build co-occurrence matrix **X**, where X_ij = number of times word j occurs in the context of word i.
2. Learn vectors by minimising the weighted least-squares objective:
   **J = Σ f(X_ij) ( w_iᵀ w̃_j + b_i + b̃_j − log X_ij )²**
   where f(·) down-weights very frequent pairs.
3. Final vector = w + w̃.

**Key idea:** *ratios* of co-occurrence probabilities encode meaning. E.g. P(solid|ice)/P(solid|steam) is large; P(gas|ice)/P(gas|steam) is small.

**How GloVe represents words:** each word → a dense vector (typically 50/100/200/300 dimensions) that captures global statistics and linear relationships (king − man + woman ≈ queen).

### E. Comparison – GloVe vs Word2Vec
| Basis | Word2Vec | GloVe |
|---|---|---|
| Type | Predictive (neural network) | Count-based + regression (matrix factorisation) |
| Statistics used | Local context window | **Global** co-occurrence matrix |
| Training | Online, window by window | Offline on pre-computed matrix |
| Objective | Predict word/context | Fit log co-occurrence |
| Memory | Low | Needs co-occurrence matrix (high) |
| Polysemy / OOV | Not handled | Not handled |

### G. Exam Answer (outline)
Definition → co-occurrence matrix example → objective function → intuition (ratio) → representation → comparison table with Word2Vec → advantages (uses global stats, parallelisable) / limitations (memory, static vectors) → conclusion.

### H. Keywords
`co-occurrence matrix`, `global statistics`, `log-bilinear`, `weighting function`, `matrix factorisation`, `static embedding`.

---

## 4. N-gram Language Models and Perplexity  ⭐ P1/P2

### A. Definition
- A **language model (LM)** assigns a probability to a sequence of words.
- An **N-gram model** approximates the probability of a word using only the previous **N−1** words (Markov assumption).

### B. Simple Explanation
After "I want to eat ___" an N-gram model guesses the next word by looking at only the last one or two words and counting how often each word followed them in training text.

### C. Detailed Explanation
- **Unigram (N=1):** P(w) – no context. Tokens: `I`, `love`, `NLP`.
- **Bigram (N=2):** P(wₙ | wₙ₋₁). Tokens: `I love`, `love NLP`.
- **Trigram (N=3):** P(wₙ | wₙ₋₂, wₙ₋₁). Tokens: `I love NLP`.

**Chain rule:** P(w₁…wₙ) = Π P(wᵢ | w₁…wᵢ₋₁) ≈ Π P(wᵢ | wᵢ₋₁) (bigram).
**MLE estimate:** P(wᵢ | wᵢ₋₁) = Count(wᵢ₋₁ wᵢ) / Count(wᵢ₋₁).
**Problem – zero probability** for unseen n-grams → **smoothing** (Laplace/add-one, backoff, interpolation, Kneser-Ney). Use `<s>` and `</s>` tags for sentence boundaries.

**Limitations:** cannot capture long-range dependencies; data sparsity; memory grows quickly with N.

### Numerical Example – Sentence Probability and Perplexity
Training corpus: (1) `<s> I love NLP </s>` (2) `<s> I love AI </s>` (3) `<s> I like NLP </s>`

Counts: `<s>`=3, `I`=3, `love`=2, `like`=1, `NLP`=2, `AI`=1.
Bigram counts: (`<s> I`)=3, (`I love`)=2, (`I like`)=1, (`love NLP`)=1, (`love AI`)=1, (`like NLP`)=1, (`NLP </s>`)=2, (`AI </s>`)=1.

Test sentence: `<s> I love NLP </s>`
- P(I | `<s>`) = 3/3 = 1
- P(love | I) = 2/3
- P(NLP | love) = 1/2
- P(`</s>` | NLP) = 2/2 = 1

**P(sentence) = 1 × 2/3 × 1/2 × 1 = 1/3 ≈ 0.333**

**Perplexity** PP = P(w₁…w_N)^(−1/N), N = number of predicted tokens = 4
PP = (1/3)^(−1/4) = 3^(0.25) ≈ **1.32**
*(Interpretation: on average the model is as uncertain as choosing among ≈1.3 equally likely words – very good.)*

### Perplexity – Definition (2-mark answer)  ⭐ P1
**Perplexity** is the exponentiated average negative log-likelihood of a test set: PP(W) = P(w₁…w_N)^(−1/N). It measures how well a language model predicts unseen text (the "weighted average branching factor"). **A lower perplexity indicates a better model** – it is less "surprised" by the test data and assigns higher probability to it.

### G. Exam Answer (outline)
Language model definition → N-gram types with examples → chain rule + Markov assumption → MLE formula → smoothing → numerical example (sentence probability) → perplexity formula + meaning → advantages/limitations → applications (speech recognition, spelling correction, machine translation, autocomplete).

### H. Keywords
`language model`, `Markov assumption`, `chain rule`, `MLE`, `smoothing`, `Laplace`, `backoff`, `perplexity`, `branching factor`, `sparsity`.

---

## 5. Part-of-Speech (POS) Tagging  ⭐ P1

### A. Definition
**POS tagging** is the process of assigning a grammatical category (noun, verb, adjective, etc.) to every token in a sentence based on its definition and context.

### B. Simple Explanation
Labelling each word with its grammatical role: *"fish"* can be a noun or verb – the tagger decides from context.

### C. Detailed Explanation
**Example (Penn Treebank tags):**
```
The/DT  quick/JJ  brown/JJ  fox/NN  jumps/VBZ  over/IN  the/DT  lazy/JJ  dog/NN
```
Common tags: `NN` noun, `NNS` plural noun, `NNP` proper noun, `VB` verb, `VBD` past verb, `VBG` gerund, `JJ` adjective, `RB` adverb, `DT` determiner, `IN` preposition, `PRP` pronoun, `CC` conjunction.

**Approaches:**
1. **Rule-based** – hand-written rules (e.g., word after "the" is noun/adjective).
2. **Stochastic / statistical** – Hidden Markov Model (HMM) with Viterbi decoding; CRF.
3. **Machine/Deep learning** – BiLSTM-CRF, BERT-based taggers (accuracy ≈ 97%).

**Challenge:** *ambiguity* – "Time flies like an arrow" ; "book a flight" (verb) vs "read a book" (noun).
**Applications:** parsing, NER, information extraction, machine translation, speech synthesis, lemmatization.

---

## 6. Named Entity Recognition (NER)  ⭐ P1

### A. Definition
**NER** is an information-extraction task that locates and classifies named entities in text into predefined categories such as person, organisation, location, date, and monetary value.

### B. Simple Explanation
Highlight the "names of things" in a sentence and say what kind each is.

### C. Detailed Explanation
**Example:** `Sundar Pichai is the CEO of Google, headquartered in California.`
- Sundar Pichai → **PERSON**, Google → **ORGANIZATION**, California → **LOCATION**

**BIO tagging:** `Sundar/B-PER Pichai/I-PER is/O the/O CEO/O of/O Google/B-ORG ,/O in/O California/B-LOC`
(B = beginning, I = inside, O = outside)

**Entity types:** PERSON, ORGANIZATION, LOCATION/GPE, DATE, TIME, MONEY, PERCENT, PRODUCT.
**Approaches:** rule/gazetteer-based → HMM/CRF → BiLSTM-CRF → Transformer (BERT) fine-tuning.
**Challenges:** ambiguity (`Apple` fruit vs company; `Washington` person vs place), new entities, nested entities, domain shift.
**Applications:** search engines, question answering, resume parsing, news analytics, medical/legal information extraction, chatbots.

### E. Comparison – POS Tagging vs NER
| Basis | POS Tagging | NER |
|---|---|---|
| Objective | Assign **grammatical category** to each word | Identify & classify **real-world named entities** |
| Output labels | NN, VB, JJ, DT … | PER, ORG, LOC, DATE … |
| Granularity | Every token tagged | Only entity spans (others `O`) |
| Level | Syntactic | Semantic / information extraction |
| Example | `Google/NNP` | `Google → ORG` |
| Applications | Parsing, lemmatization, MT | IE, QA, search, analytics |
| Relation | POS tags are often **features** for NER | Uses POS context |

### G. University Exam Answer (12-mark outline for POS + NER)
1. Intro: sequence-labelling tasks.
2. POS: definition, example sentence with tags, tagset table, approaches (rule, HMM, neural), ambiguity, applications.
3. NER: definition, example with tags, BIO scheme, types, approaches, challenges, applications.
4. Comparison table (objective & applications).
5. Conclusion.

### H. Keywords
`sequence labelling`, `Penn Treebank`, `tagset`, `HMM`, `Viterbi`, `CRF`, `BIO scheme`, `gazetteer`, `entity span`, `ambiguity`, `information extraction`.

---

## 7. Text Classification

### A. Definition
**Text classification** is the task of assigning one or more predefined labels (classes) to a piece of text.

### B. Simple Explanation
Reading an email and deciding "spam or not spam"; reading a review and deciding "positive or negative".

### C. Detailed Explanation
**Pipeline:**
```
Raw text → Preprocessing → Feature extraction (BoW / TF-IDF / embeddings) → Classifier (NB / SVM / LR) → Label
```
**Applications:** spam detection, sentiment analysis, topic/news categorisation, intent detection in chatbots, language identification, fake-news detection.

---

## 7.1 Naive Bayes Classifier  ⭐ P1

### A. Definition
**Naive Bayes** is a probabilistic classifier based on **Bayes' theorem** that assumes all features (words) are conditionally independent given the class.

### B. Simple Explanation
It asks: "Given these words, which class is most probable?" It is called **"naive"** because it *naively assumes words are independent of each other* given the class (e.g., "New" and "York" are treated as unrelated) – unrealistic, but it works surprisingly well.

### C. Detailed Explanation
**Bayes' theorem:** P(c | d) = P(d | c) · P(c) / P(d)
**Naive assumption:** P(d | c) = Π P(wᵢ | c)
**Decision rule:** ĉ = argmax_c [ P(c) · Π P(wᵢ | c) ] (P(d) is constant, ignored)
**Laplace smoothing:** P(w | c) = (count(w, c) + 1) / (total words in c + |V|)
(In practice use sum of logs to avoid underflow.)

**Types:** Multinomial NB (word counts – text), Bernoulli NB (word present/absent), Gaussian NB (continuous).

### Numerical Example – Spam/Ham
Training data:
| Message | Class |
|---|---|
| win cash now | Spam |
| win prize now | Spam |
| meeting at noon | Ham |
| lunch at noon | Ham |
| call me now | Ham |

- Priors: P(Spam) = 2/5 = 0.4; P(Ham) = 3/5 = 0.6
- Vocabulary (|V| = 10): win, cash, now, prize, meeting, at, noon, lunch, call, me
- Spam words: win(2), cash(1), now(2), prize(1) → 6 words
- Ham words: meeting(1), at(2), noon(2), lunch(1), call(1), me(1), now(1) → 9 words

**Classify the new message: "win now"**

| | Spam | Ham |
|---|---|---|
| P(win\|c) | (2+1)/(6+10) = 3/16 = 0.1875 | (0+1)/(9+10) = 1/19 = 0.0526 |
| P(now\|c) | (2+1)/16 = 3/16 = 0.1875 | (1+1)/19 = 2/19 = 0.1053 |
| Prior | 0.4 | 0.6 |
| **Score** | 0.4 × 0.1875 × 0.1875 = **0.01406** | 0.6 × 0.0526 × 0.1053 = **0.00332** |

Spam score > Ham score → **"win now" is classified as SPAM** (normalised probability ≈ 0.01406/(0.01406+0.00332) ≈ 81%).

**Advantages:** simple, fast, works with small data, good baseline. **Disadvantages:** independence assumption, zero-frequency problem (needs smoothing), poor probability calibration.

### G. Exam Answer (outline)
Definition of text classification → Bayes' theorem → naive assumption → decision rule → Laplace smoothing → spam/ham numerical (table) → advantages / disadvantages → applications → conclusion.

### H. Keywords
`Bayes' theorem`, `prior`, `likelihood`, `posterior`, `conditional independence`, `Laplace smoothing`, `argmax`, `multinomial NB`, `log probabilities`.

---

## 7.2 Support Vector Machine (SVM)  ⭐ P1

### A. Definition
**SVM** is a supervised learning algorithm that finds the **optimal separating hyperplane** that maximises the margin between data points of different classes.

### B. Simple Explanation
Draw a line (in 2-D) or plane (in high-D) separating spam from ham so that the line stays as *far as possible from the nearest points of both classes*. A wide "safety street" generalises better.

### C. Detailed Explanation
- **Hyperplane:** decision boundary **w·x + b = 0**. Points with w·x + b > 0 → class +1, else class −1.
- **Margin:** distance between the hyperplane and the closest points of each class; **margin = 2 / ‖w‖**. SVM maximises it (i.e., minimises ‖w‖).
- **Support vectors:** the training points lying on the margin boundaries; only they define the hyperplane.
- **Soft margin (C):** allows some misclassification for noisy data; large C → narrow margin, small C → wide margin.
- **Kernel trick:** maps data to higher dimension (linear, polynomial, RBF) for non-linear separation. For text, a **linear kernel** works best because TF-IDF features are high-dimensional and sparse.

```
        class +1   o  o              w·x+b = +1  ─ ─ ─ ─
                      o   o
  ───────────────────────────────    w·x+b =  0  (hyperplane)
                  x   x
        class -1    x     x          w·x+b = -1  ─ ─ ─ ─
        |<------ margin = 2/||w|| ------>|
        Support vectors = points on the dashed lines
```

**Advantages:** effective in high dimensions, robust to overfitting, strong for text. **Disadvantages:** slow on very large data, needs feature scaling/tuning, no direct probability output.

### G. Exam Answer (2-mark)
"SVM is a supervised classifier that finds the hyperplane (w·x+b=0) that best separates classes with maximum margin. The **hyperplane** is the decision boundary; the **margin** is the distance between the hyperplane and the nearest data points (support vectors) of each class, equal to 2/‖w‖."

### H. Keywords
`hyperplane`, `margin`, `support vectors`, `maximum-margin`, `kernel trick`, `soft margin`, `C parameter`, `linear kernel`.

---

## 7.3 Logistic Regression  ⭐ P2

### A. Definition
**Logistic Regression** is a supervised classification algorithm that models the probability of a class by applying the **sigmoid function** to a linear combination of features.

### B. Simple Explanation
It first calculates a score z = w·x + b (like a weighted vote of words) and then squeezes the score into a probability between 0 and 1.

### C. Detailed Explanation
- **Sigmoid:** σ(z) = 1 / (1 + e^(−z)); output ∈ (0,1); σ(0) = 0.5.
- **Model:** P(y=1 | x) = σ(w·x + b). If P ≥ 0.5 → class 1 (e.g., Spam) else class 0.
- **Loss:** binary cross-entropy; weights learned by gradient descent; **regularisation** (L1/L2) prevents overfitting.
- **Multi-class:** softmax (multinomial LR).
- **Role of sigmoid:** converts any real-valued score into a valid probability and makes the model differentiable for training.

**Example:** word weights: "win" +2.0, "meeting" −1.5, bias −0.5; message "win" → z = 1.5 → σ(1.5) ≈ 0.82 → Spam.

**Advantages:** probabilistic output, no independence assumption, interpretable weights. **Disadvantages:** linear boundary, needs feature engineering.

### H. Keywords
`sigmoid`, `logit`, `cross-entropy`, `gradient descent`, `regularisation`, `decision threshold`, `softmax`.

---

## 7.4 Comparison – Naive Bayes vs SVM vs Logistic Regression  ⭐ P3

| Basis | Naive Bayes | SVM | Logistic Regression |
|---|---|---|---|
| Type | Generative, probabilistic | Discriminative, margin-based | Discriminative, probabilistic |
| Assumption | Feature independence | Maximise margin | Linear log-odds |
| Output | Class probability | Class (distance to boundary) | Class probability |
| Training speed | Very fast | Slower | Moderate |
| Small data | Excellent | Good | Good |
| High-dim text | Good | **Best** | Very good |
| Non-linear | No | Yes (kernels) | No |

**Using TF-IDF features:** Convert each document to a TF-IDF vector (dimension = |V|); feed the vector into any classifier – LR learns weights per word, linear SVM finds the max-margin hyperplane in TF-IDF space, NB (usually with raw counts or TF-IDF) estimates P(word|class).

---

## 8. Quick 2-Mark Sheet (Unit I – for compulsory Q1)

| Question | Crisp answer |
|---|---|
| Define NLP; 2 applications | NLP is the field of AI that enables computers to understand, interpret and generate human language. Apps: machine translation, chatbots, sentiment analysis. |
| Text preprocessing / tokenization | Cleaning & normalising raw text; tokenization splits text into tokens: `"I love NLP"` → `[I, love, NLP]`. |
| Stemming vs lemmatization | Stemming chops suffixes (`studies→studi`); lemmatization returns dictionary form (`studies→study`). |
| One-hot limitation | Sparse, |V|-dimensional, no semantic similarity. |
| TF-IDF; TF; IDF | TF = freq of term in doc ÷ doc length; IDF = log(N/df); weight = TF×IDF. |
| CBOW vs Skip-gram | CBOW: context → centre word; Skip-gram: centre → context; skip-gram better for rare words. |
| GloVe | Learns vectors from the global co-occurrence matrix; each word = dense vector. |
| N-gram | Sequence of N words; unigram `NLP`, bigram `love NLP`, trigram `I love NLP`. |
| Perplexity | PP = P(W)^(−1/N); lower = better LM. |
| POS tagging | Assigning grammatical tags: `NN`, `VB`. |
| NER types | PERSON, ORGANIZATION, LOCATION, DATE. |
| Text classification apps | Spam detection, sentiment analysis. |
| Naive Bayes "naive" | Assumes features are conditionally independent given the class. |
| SVM, hyperplane, margin | Max-margin classifier; hyperplane = decision boundary; margin = distance to nearest points (2/‖w‖). |
| Logistic regression / sigmoid | Classifier using σ(z)=1/(1+e^−z) to map score to probability. |
