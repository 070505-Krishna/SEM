# UNIT IV – Prompting, LLMs and NLP Agents

**Syllabus:** Prompt engineering: zero-shot, few-shot, chain-of-thought · In-context learning and function calling · OpenAI GPT-4, Claude, Gemini – capabilities and limitations · Retrieval-Augmented Generation (RAG) · Agents using LLMs (LangChain, ReAct, AutoGen)

> **Recommended for detailed preparation (Top-4 unit).** Reasons: purely conceptual (no numericals), diagram-friendly (RAG, agent loop), and Mid-2 tested RAG and agents as full 5+5 questions plus two short questions (zero-shot vs few-shot, function calling).

---

## 0. Question Paper Analysis for Unit 4

### 0.1 Actual questions seen in the supplied papers (Mid-2)
| Mid-2 Q.No | Question (original wording) | Marks | Topic |
|---|---|---|---|
| 1(c) | Differentiate between zero-shot and few-shot prompting with suitable examples. | 2 | Prompting |
| 1(d) | What is function calling in Large Language Models? | 2 | Function calling |
| 3(a) | Explain Retrieval-Augmented Generation (RAG) with a neat architecture diagram. Describe the retrieval and generation processes. | 5 | RAG |
| 3(b) | Explain LLM-based agents. Discuss the roles of LangChain, ReAct and AutoGen in developing intelligent agents. | 5 | Agents |

### 0.2 Question Bank (supplied – *expected*, not confirmed past papers)
Short: prompt engineering, zero-shot, few-shot, CoT, zero vs few-shot, ICL, function calling, GPT-4 capabilities, Claude, Gemini, LLM limitations, RAG, AI agent, LangChain, ReAct & AutoGen.
Long: prompt engineering with zero/few/CoT; ICL; function calling with example; GPT-4 vs Claude vs Gemini; RAG diagram; RAG advantages & applications; LLM agents architecture; LangChain; ReAct; AutoGen.

### 0.3 Priority classification
| Priority | Topics |
|---|---|
| **P1** | RAG (diagram, retrieval, generation) · LLM-based agents + LangChain/ReAct/AutoGen · Zero-shot vs few-shot vs CoT · Function calling |
| **P2** | In-context learning · GPT-4 / Claude / Gemini capabilities & limitations · RAG advantages |
| **P3** | Prompt engineering best practices · Agent memory/planning details |

---

## 1. Prompt Engineering  ⭐ P1

### A. Definition
**Prompt engineering** is the practice of designing and refining the input text (prompt) given to a Large Language Model (LLM) so that it produces accurate, relevant and well-formatted outputs, without changing the model's weights.

### B. Simple Explanation
An LLM is like a very capable assistant who follows your wording closely. A vague request gives a vague answer; a clear request with context, examples and format gives a good answer.

### C. Detailed Explanation
**Elements of a good prompt:** instruction (task), context/background, input data, examples, output format/constraints, role/persona ("You are a Python tutor…").

**Techniques:** zero-shot, few-shot, chain-of-thought, role prompting, self-consistency, prompt chaining, retrieval-augmented prompting.

**Best practices:** be specific, give examples, specify format (JSON/table), break the task into steps, state constraints, iterate.

### 1.1 Zero-shot Prompting
**Definition:** asking the model to perform a task with **only an instruction and no examples**.
**Example:** *"Classify the sentiment of this review: 'The movie was fantastic!'"* → Positive.
Relies on knowledge learned during pre-training. Good for simple/common tasks.

### 1.2 Few-shot Prompting
**Definition:** providing **a few (typically 2–5) input–output examples** in the prompt before the actual query so the model learns the pattern/format.
**Example:**
```
Review: "Loved it"        → Positive
Review: "Worst service"   → Negative
Review: "Absolutely great food" → ?
```
→ Positive.

### 1.3 Chain-of-Thought (CoT) Prompting
**Definition:** prompting the model to **generate intermediate reasoning steps** before the final answer (Wei et al., 2022). Zero-shot CoT adds "Let's think step by step."
**Example:** *"Ravi has 5 pens, buys 2 packs of 3 pens. Total?"* → "2 packs × 3 = 6; 5 + 6 = 11. **Answer: 11**."
Improves arithmetic, logic and multi-step reasoning; works best in large models.

### E. Comparison – Zero-shot vs Few-shot (Mid-2 Q1c)  ⭐ P1
| Basis | Zero-shot | Few-shot | (CoT) |
|---|---|---|---|
| Examples in prompt | None | 2–5 examples | Examples/instruction showing reasoning steps |
| Prompt length | Short | Longer | Longest |
| Accuracy on complex tasks | Lower | Higher | Highest for reasoning |
| Effort | Minimal | Need to craft examples | Need reasoning demos |
| Example | "Translate 'good morning' to French." | "cat→chat, dog→chien, house→?" | "Think step by step…" |

### G. Exam Answer (2-mark)
"**Zero-shot** prompting gives only the task instruction (e.g., 'Translate "hello" to French'), relying on pre-trained knowledge. **Few-shot** prompting also supplies a few solved examples (cat→chat, dog→chien, house→?) so the model infers the pattern, usually improving accuracy."

### H. Keywords
`prompt`, `instruction`, `zero-shot`, `few-shot`, `CoT`, `demonstrations`, `role prompting`, `temperature`, `context window`.

---

## 2. In-Context Learning (ICL)  ⭐ P2

### A. Definition
**In-context learning** is the ability of an LLM to learn and perform a new task **from the examples and instructions given in the prompt at inference time, without any gradient update or retraining**.

### B. Simple Explanation
Show the model a few worked examples inside the prompt, and it "figures out" the task on the spot – like a worker who understands a new form after seeing two filled-in samples.

### C. Detailed Explanation
- Emerged with large models like GPT-3 (Brown et al., 2020).
- **Forms:** zero-shot, one-shot, few-shot ICL.
- **How it works (theory):** the model recognises the task pattern from the context via attention and conditions its next-token predictions on it (implicit pattern matching/meta-learning from pre-training).
- **Fine-tuning vs ICL:** ICL changes no weights; it is temporary (only within the context window).
- **Advantages:** no retraining, fast adaptation, low cost, flexible. **Limitations:** limited by context length, sensitive to example choice/order, less reliable than fine-tuning.

### H. Keywords
`in-context learning`, `no weight update`, `demonstrations`, `context window`, `emergent ability`, `meta-learning`.

---

## 3. Function Calling in LLMs  ⭐ P1

### A. Definition
**Function calling (tool calling)** is a capability in which an LLM, instead of answering in plain text, outputs a **structured request (usually JSON) specifying which external function/API to call and with what arguments**; the application executes it and returns the result to the model.

### B. Simple Explanation
An LLM cannot know today's weather or run a calculation reliably. With function calling the model says "please call `get_weather(city='Hyderabad')`", your program does it, and the model uses the result to answer.

### C. Detailed Explanation – Working
1. Developer describes available functions (name, description, parameter JSON schema) to the model.
2. User asks: "What is the weather in Hyderabad?"
3. Model decides a function is needed → returns `{"name":"get_weather","arguments":{"city":"Hyderabad"}}`.
4. Application executes the real function/API.
5. Result (`32°C, sunny`) is sent back to the model.
6. Model generates the final natural-language answer.

```
 User ──► LLM ──► (function name + JSON args) ──► App/Tool (API, DB, calculator)
                                                          │ result
 User ◄── final answer ◄── LLM ◄─────────────────────────┘
```
**Uses:** real-time data, database queries, calculators, booking/email actions, structured data extraction, agents.
**Benefits:** overcomes knowledge cut-off, reduces hallucination, enables actions, reliable structured output.

### G. Exam Answer (2-mark)
"Function calling lets an LLM generate a structured JSON call to an external tool/API (e.g., `get_weather(city)`) which the application executes; the result is fed back to the LLM to produce the final response. It extends LLMs with real-time data and actions."

### H. Keywords
`tool use`, `JSON schema`, `API`, `arguments`, `structured output`, `external tool`, `orchestration`.

---

## 4. Large Language Models: GPT-4, Claude, Gemini – Capabilities and Limitations  ⭐ P2

### A. Definition
A **Large Language Model (LLM)** is a Transformer-based neural network with billions of parameters, pretrained on massive text data to understand and generate human-like language.

### C. Detailed Explanation
| Aspect | OpenAI GPT-4 | Anthropic Claude | Google Gemini |
|---|---|---|---|
| Developer | OpenAI | Anthropic | Google DeepMind |
| Nature | Large multimodal model (text + image input) | Family of LLMs focused on helpfulness, honesty, safety (Constitutional AI) | Natively multimodal model family (text, image, audio, video) |
| Key capabilities | Strong reasoning, coding, summarization, exam-level performance, function calling, long context | Long context window, strong writing/coding/analysis, careful/safe responses, tool use | Multimodal understanding, very long context, integrated with Google services, tool use |
| Limitations | Hallucination, knowledge cut-off, costly, bias | Can be over-cautious, hallucination possible, cut-off | Hallucination, bias, variable performance, cut-off |

> Keep model comparisons general in the exam – version details change quickly.

### Common Capabilities of LLMs
Text generation, summarization, translation, QA, code generation, reasoning, tool use/agents, multimodal understanding.

### Common Limitations of LLMs  (answer for "any two limitations")
1. **Hallucination** – produces fluent but false information.
2. **Knowledge cut-off** – no awareness of events after training (solved partly by RAG).
3. **Limited context window** – cannot read unlimited text.
4. **Bias and toxicity** from training data.
5. **Weak at precise maths/logic** without tools; **high cost** and energy use; **prompt sensitivity**; **privacy/security risks** (prompt injection, data leakage); lack of true understanding/explainability.

### G. Exam Answer (outline)
Definition of LLM → overview of each model (developer, features) → comparison table of capabilities → common limitations → mitigation (RAG, fine-tuning, guardrails, human oversight) → conclusion.

### H. Keywords
`LLM`, `parameters`, `multimodal`, `context window`, `Constitutional AI`, `hallucination`, `knowledge cut-off`, `RLHF`, `tool use`.

---

## 5. Retrieval-Augmented Generation (RAG)  ⭐ P1

### A. Definition
**RAG** (Lewis et al., Facebook AI, 2020) is a technique that combines an **information-retrieval system with a generative LLM**: relevant documents are retrieved from an external knowledge source and added to the prompt, so the LLM generates answers **grounded** in that retrieved information.

### B. Simple Explanation
Like an **open-book exam**: instead of answering from memory only (and possibly making mistakes), the model first looks up the relevant pages in a library and then writes the answer using them.

### C. Detailed Explanation
**Phase 1 – Indexing (offline):**
1. Collect documents (PDFs, web pages, databases).
2. **Chunk** them into small passages.
3. Convert each chunk into a vector using an **embedding model**.
4. Store vectors in a **vector database** (FAISS, Pinecone, Chroma).

**Phase 2 – Retrieval (online):**
1. User query is converted into an embedding.
2. **Similarity search** (cosine similarity / nearest neighbour) finds the **top-k** relevant chunks (optional re-ranking).

**Phase 3 – Augmentation & Generation:**
1. Retrieved chunks + user query are combined into an augmented prompt (*"Answer using the context below…"*).
2. The LLM generates a grounded answer (optionally with citations).

### D. Architecture Diagram
```
 OFFLINE INDEXING                              ONLINE QUERY
 Documents ─► Chunking ─► Embedding model ─► [ Vector Database ]
                                                     ▲   │ top-k chunks
 User Query ─► Embedding model ─► Query vector ──────┘   ▼
                                           ┌─ Prompt = Query + Retrieved Context ─┐
                                           ▼                                      │
                                         [ LLM / Generator ] ─► Final grounded answer ─► User
```

### Retrieval process vs Generation process (for the "describe" part)
| Retrieval process | Generation process |
|---|---|
| Query → embedding → vector search → top-k relevant chunks (dense/sparse/hybrid) | Query + retrieved chunks → prompt → LLM produces fluent answer conditioned on evidence |
| Done by retriever (embedding model + vector DB) | Done by generator (LLM) |
| Determines *what knowledge* is available | Determines *how well it is expressed* |

### E. Advantages of RAG over conventional LLM generation  ⭐ P2
| Conventional LLM | RAG |
|---|---|
| Fixed knowledge cut-off | Up-to-date via external data |
| Hallucination risk high | Grounded, lower hallucination |
| No source citation | Can cite sources |
| Updating needs retraining/fine-tuning | Just update the document store |
| Cannot use private data safely | Can use private/enterprise documents |
| Costly to adapt | Cheaper |

**Limitations:** retrieval quality dependency, latency, chunking issues, context-length limits, extra infrastructure.
**Applications:** enterprise chatbots, customer support, legal/medical QA, research assistants, "chat with your PDF", code documentation search.

### G. University Exam Answer (12-mark outline)
Intro (LLM limitations → need for RAG) → definition → architecture diagram → indexing, retrieval, generation processes → example (HR policy chatbot) → advantages vs conventional LLM → limitations → applications → conclusion.

### H. Keywords
`retriever`, `generator`, `embedding`, `vector database`, `chunking`, `top-k`, `cosine similarity`, `grounding`, `augmented prompt`, `FAISS`, `hallucination reduction`.

---

## 6. LLM-based Agents  ⭐ P1

### A. Definition
An **LLM-based agent** is an autonomous system that uses an LLM as its "brain" to **reason, plan, use tools, remember and act** in order to achieve a goal over multiple steps.

### B. Simple Explanation
A plain LLM only answers. An agent is like an employee: you give it a goal ("book the cheapest flight and email me"), and it decides the steps, uses tools (search, email), checks results and continues until done.

### C. Detailed Explanation – Architecture
| Component | Role |
|---|---|
| **LLM (brain/controller)** | Understands goal, reasons, decides next action |
| **Planning** | Breaks goal into sub-tasks; reflects/corrects (CoT, ReAct) |
| **Memory** | Short-term (conversation context) and long-term (vector DB) |
| **Tools / Actions** | Search, calculators, code interpreter, APIs, databases (via function calling) |
| **Environment / Perception** | User input, tool results (observations) |

```
        Goal / User request
               ▼
        ┌─────────────┐   plan    ┌──────────┐
        │  LLM Agent  │◄─────────►│  Memory  │
        │ (reasoning) │           └──────────┘
        └──────┬──────┘
         action│   ▲ observation
               ▼   │
          [ Tools: Search | API | Code | DB ]
               ▼
          Final answer / action
```
**Working loop:** Perceive → Think/Plan → Act (tool call) → Observe → Repeat until goal is reached.
**Applications:** research assistants, customer support, coding assistants, data analysis, workflow automation. **Challenges:** errors compounding, cost, safety, infinite loops, hallucinated actions.

---

## 6.1 LangChain  ⭐ P1

### A. Definition
**LangChain** is an open-source **framework** for building applications powered by LLMs by connecting models with prompts, memory, data sources, tools and agents.

### C. Detailed Explanation – Components and Role
| Component | Role |
|---|---|
| **Models** | Unified interface to LLMs (OpenAI, Claude, Gemini, open-source) |
| **Prompt templates** | Reusable, parameterised prompts |
| **Chains** | Sequences of steps (prompt → LLM → parser → next step) |
| **Memory** | Stores conversation history/state |
| **Retrievers / Vector stores / Document loaders** | Build RAG pipelines |
| **Tools & Agents** | LLM decides which tool to call (search, calculator, API) |

**Role in agents:** provides the building blocks and orchestration (agent executor, tool integration, memory) to build agents quickly. **Example:** a "chat with PDF" app = document loader → splitter → embeddings → vector store → retriever → LLM chain.

---

## 6.2 ReAct (Reasoning + Acting)  ⭐ P1

### A. Definition
**ReAct** (Yao et al., 2022) is an agent framework/prompting paradigm in which the LLM **interleaves reasoning traces ("Thought") with actions ("Action") and observations**, so reasoning guides actions and tool results update reasoning.

### C. Detailed Explanation
**Loop:** Thought → Action → Observation → Thought → … → Final Answer.
**Example:** *Question: "Who is older, A or B?"*
```
Thought: I need the birth year of A.
Action:  Search[A birth year]
Observation: 1970
Thought: Now I need B's birth year.
Action:  Search[B birth year]
Observation: 1985
Thought: 1970 < 1985, so A is older.
Final Answer: A
```
**Benefits:** reduces hallucination (facts fetched from tools), interpretable reasoning, handles multi-step tasks, can recover from errors. **Limitation:** more LLM calls (cost/latency), can loop.

---

## 6.3 AutoGen  ⭐ P1

### A. Definition
**AutoGen** (Microsoft) is an open-source framework for building applications in which **multiple LLM agents (and humans/tools) converse and collaborate** to solve complex tasks.

### C. Detailed Explanation
- **Agents:** `AssistantAgent` (LLM-powered), `UserProxyAgent` (represents user, can execute code/ask for human input), custom role agents (planner, coder, reviewer, critic).
- **Multi-agent conversation:** agents send messages to each other (group chat with a manager) until the task is solved or termination condition is met.
- **Features:** code generation + execution, human-in-the-loop, tool use, flexible conversation patterns.
- **Example:** Coder agent writes code → Executor agent runs it → Critic reports error → Coder fixes → loop until correct.
```
 User ─► [Planner Agent] ─► [Coder Agent] ⇄ [Executor/Tool Agent] ─► [Reviewer Agent] ─► Result
                  (agents collaborate by conversation; human can intervene)
```

### E. Comparison – LangChain vs ReAct vs AutoGen  ⭐ P1
| Basis | LangChain | ReAct | AutoGen |
|---|---|---|---|
| Nature | Application development **framework** | Reasoning + acting **paradigm/prompting pattern** | **Multi-agent** conversation framework |
| Role in agents | Provides tools, memory, chains, agent executor | Defines *how an agent thinks and acts* (Thought–Action–Observation) | Lets *several agents collaborate* via messages |
| Agents | Single or multi-step | Single agent loop | Multiple agents |
| Typical use | RAG apps, chatbots | Tool-using QA, reasoning | Coding, complex workflows, automation |

### G. Exam Answer (12-mark outline for agents question)
Definition of LLM agent → architecture (LLM, planning, memory, tools) with diagram → working loop → **LangChain** (components, role) → **ReAct** (loop, example) → **AutoGen** (multi-agent) → comparison table → applications/challenges → conclusion.

### H. Keywords
`agent`, `planning`, `memory`, `tools`, `Thought–Action–Observation`, `chain`, `agent executor`, `multi-agent`, `AssistantAgent`, `UserProxyAgent`, `orchestration`, `human-in-the-loop`.

---

## 7. Quick 2-Mark Sheet (Unit IV)

| Question | Crisp answer |
|---|---|
| Prompt engineering | Designing/optimising prompts to obtain desired LLM output without retraining. |
| Zero-shot | Task given with instruction only, no examples. |
| Few-shot | Instruction + a few examples in prompt. |
| CoT | Prompting model to show step-by-step reasoning before final answer. |
| ICL | LLM learns task from prompt examples at inference, no weight updates. |
| Function calling | LLM outputs structured call (JSON) to external function/API; result used for final answer. |
| GPT-4 capabilities | Multimodal (text+image) input, strong reasoning, coding, summarization. |
| Claude | Anthropic's LLM family; emphasises safety (Constitutional AI) and long context. |
| Gemini | Google's natively multimodal LLM family. |
| LLM limitations | Hallucination, knowledge cut-off, bias, limited context. |
| RAG | Retrieval of external documents + LLM generation grounded in them. |
| AI agent | LLM-driven system that plans, uses tools and acts to reach a goal. |
| LangChain | Framework to build LLM apps with chains, memory, tools, retrievers. |
| ReAct / AutoGen | ReAct: interleaved Thought–Action–Observation; AutoGen: multi-agent conversation framework by Microsoft. |
