# LLM Context Compression in Agent Harnesses

## Overview

Large Language Model (LLM) agents operate within a **finite context window**.  
This window contains everything the model can “see” when generating a response.

Typical context includes:

- System instructions
- Conversation history
- Tool outputs
- Retrieved documents
- Current user request

The problem is simple:

> Agent workflows generate tokens faster than the context window can hold.

As agents perform tasks over many steps, their working memory grows until it eventually exceeds the model’s capacity.

To solve this, agent frameworks implement **context compression** (also called **compaction**).

Context compression reduces the size of historical context while preserving the **essential information required for reasoning**.

---

# The Context Window

The context window acts like the model's **working memory**.

Everything the model reasons about must fit inside this space.

```mermaid
flowchart LR

A[System Prompt] --> D
B[Conversation History] --> D
C[Tool Outputs / Retrieved Data] --> D
E[Current User Message] --> D

D[LLM Context Window]

D --> F[Model Reasoning]
F --> G[Assistant Response]

style D fill:#f9f,stroke:#333,stroke-width:2px
```

When the context window becomes too large, the system must either:

- discard information
- compress information
- retrieve information on demand

---

# The Context Growth Problem

Agent workflows naturally accumulate information:

- tool outputs
- observations
- intermediate reasoning
- task results

Without intervention, this eventually exceeds the context window.

```mermaid
flowchart TD

A[Start Conversation]

A --> B[User Message]
B --> C[Assistant Reply]

C --> D[More Messages]
D --> E[More Tool Outputs]
E --> F[More History]

F --> G[Context Window Limit Approaching]

G --> H[Context Overflow Risk]

style G fill:#ffe599
style H fill:#ff9999
```

This is why **context management becomes essential for long-running agents**.

---

# Context Rot

Even before the context window fills completely, reasoning quality can degrade.

As the context grows:

- important information becomes buried
- irrelevant tokens increase
- model attention becomes diluted

This phenomenon is commonly referred to as **context rot**.

```mermaid
flowchart LR

A[Short Context] --> B[Moderate Context]
B --> C[Long Context]
C --> D[Very Long Context]

A --> E[High Reasoning Accuracy]
B --> F[Slight Degradation]
C --> G[Context Rot Begins]
D --> H[Reasoning Breakdown]

E --> I[Relevant Information Easy to Find]
F --> J[Model Must Filter More Noise]
G --> K[Important Details Buried]
H --> L[Model Confusion / Hallucination]

style C fill:#ffe599
style D fill:#ff9999
style G fill:#ffd966
style H fill:#ff6666
```

Because of this, simply increasing context windows does **not fully solve long-horizon reasoning problems.

Agent frameworks must actively manage memory.**

---

# The Context Compression Pipeline

Most agent harnesses perform compression using summarization and pruning.

A typical pipeline looks like this:

```mermaid
flowchart LR

A[Full Conversation History]

A --> B{Context Too Large?}

B -->|No| C[Send to LLM]

B -->|Yes| D[Split Context]

D --> E[Recent Messages]
D --> F[Older Messages]

F --> G[Summarize / Compress]

G --> H[Compressed Summary]

H --> I[Rebuild Context]

E --> I

I --> J[New Prompt to LLM]

style G fill:#a0d6ff
style H fill:#d5f5e3
```

The key idea:

```
recent context remains unchanged
older context is compressed
```

The compressed summary replaces the original messages.

---

# Hierarchical Summarization

More advanced systems use **hierarchical summarization**.

Instead of compressing everything at once, the system creates summaries in layers.

Older data becomes progressively more condensed.

```mermaid
flowchart TD

A[Raw Conversation or Documents]

A --> B1[Chunk 1]
A --> B2[Chunk 2]
A --> B3[Chunk 3]
A --> B4[Chunk 4]

B1 --> C1[Summary 1]
B2 --> C2[Summary 2]
B3 --> C3[Summary 3]
B4 --> C4[Summary 4]

C1 --> D1[Higher-Level Summary]
C2 --> D1
C3 --> D2
C4 --> D2

D1 --> E[Global Summary]
D2 --> E

E --> F[Condensed Context Sent to LLM]

style E fill:#d5f5e3
style F fill:#a0d6ff
```

This approach allows agents to maintain **extremely long effective memory** while still operating within limited context windows.

---

# Agent Memory Architecture

Modern agent systems usually combine multiple memory layers.

```mermaid
flowchart TD

U[User]

U --> A[Agent Harness]

A --> B[Context Manager]

B --> C[Recent Context]

B --> D[Compressed History]

B --> E[External Memory]
E --> F[Vector Database / Files]

C --> G[LLM]
D --> G
F --> G

G --> H[Model Response]

style B fill:#ffe599
style G fill:#f9cb9c
```

Typical memory hierarchy:

```
recent messages
compressed summaries
external memory (RAG / databases / files)
```

This architecture allows agents to run **long multi-step workflows without exceeding the context window**.

---

# Tradeoffs of Context Compression

Compression provides major benefits but introduces tradeoffs.

### Advantages

- Enables long-running agent sessions
- Reduces token usage
- Faster inference
- Lower compute cost

### Disadvantages

- Loss of detail
- Possible summary drift
- Harder debugging
- Risk of missing important information

---

# Conclusion

Context compression is a foundational capability of modern AI agents.

By intelligently summarizing and managing historical context, agents can maintain continuity across long workflows while operating within finite context windows.

However, compression must be carefully designed to balance:

```
information fidelity
vs
context efficiency
```

Understanding this tradeoff is central to building reliable AI agent systems.

---
