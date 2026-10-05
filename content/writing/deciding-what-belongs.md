# The First Problem in an NLP Pipeline: Deciding What Belongs

When people think about building an NLP intelligence system, the interesting components usually come to mind first: embeddings, semantic search, clustering, entity extraction, summarization, retrieval, and increasingly, large language models.

One of the earliest lessons I learned while building an AI news intelligence pipeline was that another problem comes before all of them:

**Should this document be processed at all?**

That sounds like a simple question.

It isn’t.

## The deceptively simple problem

Suppose you’re continuously ingesting documents from many different sources.

In my case, those documents were news articles and the target domain was artificial intelligence.

The first requirement therefore sounded straightforward:

> Determine whether an incoming article is actually about AI.

A naive implementation might begin with a collection of keywords:

```text
AI
artificial intelligence
machine learning
LLM
neural network
```

If one appears, keep the document. That approach can get surprisingly far, but it also fails in predictable ways.

An article may mention AI once while primarily discussing something unrelated.

Another article may describe a highly relevant machine learning development without repeatedly using the exact terminology expected by the rules.

What initially appears to be a string matching problem quickly becomes a familiar classification tradeoff: precision versus recall.

Make the filter too permissive and irrelevant documents enter the system.

Make it too restrictive and useful documents disappear before the rest of the pipeline ever sees them.

## Why an early mistake propagates

The importance of this decision becomes clearer when you look at what happens next.

A domain-specific NLP pipeline might look something like this:

```text
raw documents
      ↓
relevance filtering
      ↓
topic classification
      ↓
embedding generation
      ↓
semantic clustering
      ↓
entity extraction
      ↓
summaries / trends / higher-level intelligence
```

A false positive at the beginning doesn’t merely create one bad database record.

That document may receive an embedding, be compared against other documents, influence a semantic cluster, contribute entities to an analytics system, and eventually be summarized by an LLM.

A small error at the entrance can therefore propagate through several layers of downstream processing.

That makes relevance filtering disproportionately important.

```text
irrelevant document
        ↓
incorrectly accepted
        ↓
downstream processing
   ↙         ↓          ↘
compute   cluster      noisy
waste     pollution    signals
```

## Treat relevance as its own ML problem

Instead of treating relevance detection purely as a collection of string rules, I began treating it as a classification problem of its own.

Conceptually:

```text
document
    ↓
domain relevance classifier
    ↓
relevant?
   ↙       ↘
 no         yes
 ↓           ↓
stop      downstream
          processing
```

The first model doesn’t need to understand everything about the document.

Its objective is much narrower:

**Does this document belong to the universe this system cares about?**

That distinction is useful because narrow problems often don’t require the most sophisticated model available.

A lightweight classifier can be a better architectural choice than sending every incoming document through an expensive general-purpose model.

The goal isn’t to use the most powerful model at every stage.

The goal is to use enough intelligence at each stage to make the next one more reliable.

## This pattern generalizes beyond news

This is where the problem becomes much more interesting.

The same architecture appears across many domains.

| Domain | Gating question |
| --- | --- |
| Cybersecurity | Does this document describe a security threat relevant to the organization? |
| Financial intelligence | Does this document contain a market event involving an entity we monitor? |
| Biotech | Is this paper relevant to a specific therapeutic area or research program? |
| Legal intelligence | Does this filing involve one of the jurisdictions, doctrines, companies, or issues we care about? |
| Customer intelligence | Does this message contain actionable product feedback? |

The downstream systems may be completely different, but the gating problem is remarkably similar: first determine what belongs, then spend additional computation understanding it.

## Relevance filtering is also a systems optimization

There’s another reason this architecture matters: computational efficiency.

Suppose every accepted document eventually passes through several operations:

- topic classification
- embedding generation
- entity extraction
- semantic comparisons
- indexing or vector storage
- clustering
- LLM-based analysis

An irrelevant document consumes resources at every stage it reaches.

Filtering one bad document near the beginning can therefore prevent several unnecessary operations later.

At scale, relevance classification becomes more than a data quality measure.

It becomes a systems optimization.

## The broader lesson

Building production NLP systems has repeatedly reinforced something for me:

The most impressive model in the architecture isn’t necessarily the component that determines the quality of the system. The seemingly boring stages matter: filtering, data quality, orchestration, and evaluation.

A sophisticated clustering system operating on poorly selected documents is still operating on poorly selected documents.

Before asking:

> “What intelligence can we extract from this data?”

it’s worth asking an earlier question:

> “Why is this data in the system at all?”

Sometimes the first useful model isn’t the one generating the intelligence.

It’s the one deciding what deserves to reach it.
