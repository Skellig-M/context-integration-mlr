# Context Engineering Techniques for GenAI-Enabled Software Engineering

A multivocal literature review (MLR) of how software systems give large language models access to the data they need: the Model Context Protocol (MCP), Retrieval-Augmented Generation (RAG), LangChain and LlamaIndex.

**Published in:** *Systems, Software and Services Process Improvement and Innovation* (EuroSPI 2026), Communications in Computer and Information Science, vol. 3137, Springer, pp. 304–320
**Paper:** [doi.org/10.1007/978-3-032-36816-4_20](https://doi.org/10.1007/978-3-032-36816-4_20)
**Presented:** 10 September 2026, at the EuroSPI 2026 workshop [Emerging and Multidisciplinary Approaches to Software Engineering](https://conference.eurospi.net/index.php/en/workshop?id=73)
**Authors:** Adam Gilroy, Abiya Varghese, Finian Duffy, Olivia Chukwu, Gerard Marks, Murat Yilmaz, Andrew McCarren, Paul M. Clarke (Dublin City University)

The review began as a four-student MLR at DCU and was developed into the published chapter with the academic co-authors. I contributed to the analysis across the techniques. This repository summarises the review from our original write-up, with MCP covered in more depth; the full chapter is at the link above.

## The problem

LLMs are capable, but on their own they can't reach real-time, private or domain-specific data. Larger context windows don't solve this by themselves: very long inputs are inefficient and suffer from the "lost in the middle" effect, where material in the middle of a long context is used less reliably. The review asks how current frameworks close that gap.

## Method

An MLR covers both peer-reviewed research and grey literature: documentation and engineering write-ups from the teams building these frameworks. That pairing shows the theory and the way the tools are used in practice. Sources were limited to 2023 onwards, screened on abstract and then full text, and kept only where they said something substantive about architecture, implementation or framework use.

Each framework was reviewed against the same question: how is it adopted into GenAI-enabled software systems?

## Model Context Protocol

MCP is an open standard released by Anthropic in November 2024. It replaces one-off integrations between AI applications and external systems with a single protocol.

**Architecture.** MCP uses a client-server model over JSON-RPC 2.0.

- **Hosts** are AI applications such as Claude Desktop, VS Code or Cursor. A host creates one client per server it connects to.
- **Clients** live inside the host. Each holds a dedicated connection to one server and handles capability negotiation and message exchange.
- **Servers** expose context and capabilities through standard interfaces, either locally or over a network.

The protocol has two layers. The data layer defines messages, lifecycle and the core primitives. The transport layer carries them, over stdio for local processes or streamable HTTP (with optional server-sent events) for remote servers.

**Primitives.** Servers offer three kinds of thing to clients:

- **Tools:** functions the model can call, such as a database query or an API call. Each has a name, a description and a JSON Schema for its inputs, so the model knows what it can do and how to call it.
- **Resources:** data the model can read, such as file contents, records or API responses.
- **Prompts:** reusable templates, such as system prompts and few-shot examples.

A connection starts with capability negotiation. The client declares what it supports, the server replies with its own capabilities, and only then does the exchange begin.

**Implementation.** Teams either build servers, to expose their data and functions, or build clients into their AI applications, to consume them. Official SDKs exist for Python, TypeScript, Go, Kotlin and C#. For example, a team with a database can publish one MCP server that has a query tool, a schema resource and example-query prompts. Any MCP-compatible application can then use the database without custom integration code. Ready-made servers already exist for common services such as Google Drive, Slack, GitHub and PostgreSQL.

**Why teams adopt it.**

- **Standardisation:** one protocol instead of a connector per data source.
- **Provider independence:** the integration layer stays the same when the model changes.
- **Grounding:** the model reads current, reliable data instead of relying only on what it was trained on, which reduces hallucinations.

## What the review found across all four

- **RAG** grounds answers in retrieved, domain-specific data without retraining the model. It is moving from fixed retrieve-then-generate pipelines towards modular and agentic designs, where agents decide when to retrieve and from which source. Query rewriting and context compression are used to filter out noise.
- **LangChain** is the orchestration layer: modular components for multi-step workflows, agents and memory.
- **LlamaIndex** structures enterprise data for precise retrieval.
- **MCP** standardises how all of this connects to the systems that hold the data.

The conclusion: context orchestration is now a basic requirement for reliable GenAI software, not an optional extra, and context integration is becoming an architectural decision rather than a low-level implementation detail. The next steps the review identifies are self-regulating retrieval, automated evaluation, infrastructure that scales to large context volumes, and multimodal context.

## Citation

```
Gilroy, A., Varghese, A., Duffy, F., Chukwu, O., Marks, G., Yilmaz, M., McCarren, A., Clarke, P.M.:
Context Engineering Techniques for GenAI-Enabled Software Engineering: Results from an Adapted
Multivocal Literature Review. In: Systems, Software and Services Process Improvement and Innovation
(EuroSPI 2026). Communications in Computer and Information Science, vol. 3137, pp. 304–320.
Springer, Cham (2026). https://doi.org/10.1007/978-3-032-36816-4_20
```
