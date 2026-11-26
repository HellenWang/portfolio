---
title: "A knowledge base that works with the network unplugged"
description: "I put contracts, policies and quotes into a local RAG stack on a CPU-only Intel Mac. Here's the setup that worked and the five things that wasted my time."
pubDatetime: 2026-11-26T04:00:00Z
modDatetime: 2026-11-26T05:30:00Z
tags: ["ai", "local-llm", "rag"]
draft: false
---

I've spent 11 years building infrastructure, most of it around automation. Recently I wanted to ask questions about my own contracts and internal docs without shipping anything to a cloud API. So I tried the fully local route on an Intel Mac with no discrete GPU.

It runs offline. Answers take tens of seconds, and the model still invents things if the prompt lets it. This is the setup I settled on, plus the parts that cost me time.

## What RAG is, without the jargon

A model hasn't read your contract. Ask it what the penalty clause says and it has no way to know. It will answer anyway, and the answer will sound plausible. That's the hallucination problem.

RAG just hands the model a few passages from your own documents before it answers. Here's the source, now talk about this.

You can't dump a 5,000-word contract into a 3B model on CPU. The context window fills up, the model loses the point, and everything gets slow. So you cut the document into chunks. Cut it too cleanly and you split a condition in half, so you leave about 15% to 20% overlap between neighbours.

bge-m3 turns each chunk into a vector, a set of numbers that represents meaning rather than exact words. Those vectors live in LanceDB on your own disk. Ask a question and the system pulls the chunks whose meaning is closest.

Top-K of 4 to 6. Temperature 0.1. Query mode only, so the model reads from the workspace instead of freelancing. I left hybrid search and reranking alone. With a few dozen to a few hundred documents, chunking and Top-K mattered far more. Add the extra layers once you actually have the scale for them.

## The tools

- **Ollama** runs the models locally. [ollama.com](https://ollama.com), [download](https://ollama.com/download). Once installed there's a little llama in the menu bar. Keep it in Local mode. No account, works offline.
- **AnythingLLM Desktop** is the knowledge base front end. [anythingllm.com](https://anythingllm.com), [download](https://anythingllm.com/download). Grab the macOS Intel build. It keeps LanceDB on your own disk and has a Show Citations button, which is the main reason I picked it.
- Two models, pulled from the terminal:

```bash
ollama pull qwen2.5:3b
ollama pull bge-m3
ollama list
```

## The config that actually ran

Ollama service address: `http://127.0.0.1:11434`

LLM preferences:

- Provider: Ollama
- Model: qwen2.5:3b
- Context window: 4096 to 8192
- Temperature: 0.1

Embedder preferences:

- Provider: Ollama
- Model: bge-m3
- Base URL: `http://127.0.0.1:11434`
- Max embedding chunk length: 2048

Text splitting and chunking:

- Chunk size: 500. Try 800 if your clauses run long and keep getting cut up.
- Chunk overlap: 100, or 150 when you move to 800. That's the 15% to 20% range.

Workspace:

- Chat mode: Query
- Similarity threshold: No restriction (0.0)
- Top-K: 4 to 6

The clicking order matters too. Import a document, hit Move to Workspace, then Save and Embed. Start a new thread for each question so old context doesn't drag a small model off course. Click Show Citations every time. It's the only reliable check I found on whether the answer came from your document.

## Five things that bit me

**1. Use 127.0.0.1, not localhost.** On my Mac, localhost sometimes hit an IPv6 resolution conflict and embedding quietly stalled. Hardcoding `http://127.0.0.1:11434` fixed it.

**2. Turn off the proxy.** Any TUN mode or system proxy that intercepts local ports will produce weird errors when pulling models, computing vectors, or testing the offline setup. Quit it before you start measuring anything.

**3. A 4B model was too much for this CPU.** I tried qwen3:4b first. Its default context window pushed memory up until the machine stalled, and thinking was slow. The 1.5b model was quick but bad at following instructions. qwen2.5:3b with context capped at 4096 to 8192 is the compromise I landed on.

**4. I switched clients, and the reason wasn't the UI.** With Cherry Studio, a 3B model didn't reliably trigger `/api/embed` when it had to decide intent or call a tool. It kept falling back to plain chat. AnythingLLM does deterministic retrieval, puts the parameters where you can see them, and makes citations one click away.

**5. Don't set the threshold too tight, and don't ask two things at once.** bge-m3 doesn't always produce high Chinese similarity scores. A strict threshold throws away useful chunks. No restriction (0.0) plus Top-K and Query mode was steadier. Also, one sentence with two unrelated questions confuses a small model. If you're asking about tiered rates or ranges, make it list every tier first and then compare. It makes far fewer mistakes that way.

## The prompts I use

These sit in the system prompt, tuned for qwen2.5:3b in Query mode.

Strict, source-only answers:

```text
You are a strict document retrieval and fact-checking assistant. Answer only from the reference text provided. Do not use outside knowledge or guess.

1. Stay faithful to the numbers, percentages, dates, conditions and qualifiers in the reference text. Do not change or add anything.
2. If the reference text does not cover the question, answer: "The document does not mention this information." Do not force an answer.
3. Keep it short. Lead with the conclusion and the clause it came from.
```

Tiered rates, amounts and ranges:

```text
You are a strict clause-checking assistant. Answer only from the reference text provided. Do not invent anything.

When the question involves tiered percentages, fee discounts, amount thresholds or percentage ranges, follow these steps:

1. First copy out every relevant tier or range from the reference text, in full.
2. Then match the number in the question against those tiers, one by one.
3. Finally give the conclusion and the applicable penalty, payout or calculation rule.

If there is no range rule in the reference text, answer: "The document does not mention this information."
```

Red lines, breaches and exceptions:

```text
You are a strict compliance and clause-extraction assistant. Extract only from the reference text provided. Do not make things up.

1. Focus on breach of contract, penalty standards, approval authority, disclaimers, invalidation conditions, exceptions and other hard constraints.
2. Whenever the original text says "except", "but", "unless", "unauthorised", "over", "under" or similar qualifiers, include them. Do not stop halfway.
3. If the document does not cover the question, say: "The document does not mention this information."
```

## Where I'd use it, and where I wouldn't

This is a good fit for a few dozen to a few hundred contracts, policies, quote sheets, manuals or tech notes that you need to search offline. You can trace every answer back to a paragraph. I treat it as a paragraph-finding assistant. A human still makes the call after reading the source.

It's a bad fit if you want instant replies or multi-step arithmetic. On CPU, a 3B model often takes tens of seconds to answer. Cross-document fine-grained comparison and classification into close numeric ranges will trip it up. For that, I'd rather use a bigger model with a bigger machine.

If you're trying the same thing, the settings above are the ones that actually ran on my Intel Mac. Questions or war stories: [wanghanlin12@gmail.com](mailto:wanghanlin12@gmail.com).
