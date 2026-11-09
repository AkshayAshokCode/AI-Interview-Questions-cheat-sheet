# Embeddings & Vector Search

[⬅ Back to index](../README.md)

Covers: what embeddings are, how embedding models are trained, choosing an embedding model, similarity measures, exact vs approximate nearest neighbour search, HNSW, IVF, product quantisation, vector databases, metadata filtering, hybrid search, rerankers (bi-encoder vs cross-encoder), multimodal embeddings, and operating a vector index.

---

### 1. What is an embedding?

<details>
<summary>Answer</summary>

A dense vector (typically 256–4,096 numbers) produced by a model so that **semantically similar inputs are close together**. Texts, images, users, products and code can all be embedded. Uses: semantic search, RAG retrieval, recommendations, clustering, deduplication, classification features and anomaly detection. Unlike keyword matching, embeddings match meaning ("cheap flights" ≈ "low-cost airfare").

</details>

### 2. How are text embedding models trained?

<details>
<summary>Answer</summary>

Usually a transformer encoder (or an LLM adapted for embeddings) trained with **contrastive learning**: pull together embeddings of related pairs (question–answer, query–clicked document, paraphrases) and push apart unrelated ones, using in-batch negatives and **hard negatives** (similar-looking but wrong documents). The InfoNCE loss is standard. Many models also support **Matryoshka representations**, where the first k dimensions form a usable smaller embedding – letting you trade accuracy for storage.

</details>

### 3. How do you choose an embedding model?

<details>
<summary>Answer</summary>

- **Quality on your data** – build a small retrieval test set (queries + relevant documents) and measure recall@k; public leaderboards like MTEB are a starting point only.
- **Domain and language** – multilingual? code? legal/medical?
- **Dimensions** – larger vectors cost more storage and search time.
- **Max input length** – affects chunk size.
- **Cost and latency** – API vs self-hosted; embedding millions of documents adds up.
- **Stability** – changing the model means **re-embedding everything** (vectors from different models aren't comparable).

</details>

### 4. Cosine similarity vs dot product vs Euclidean distance?

<details>
<summary>Answer</summary>

- **Cosine similarity** – angle only; ignores vector length. The most common for text.
- **Dot product** – angle and magnitude; with **normalised** vectors it equals cosine similarity (and is faster to compute).
- **Euclidean (L2) distance** – straight-line distance; for normalised vectors it ranks the same as cosine.

Use the metric the embedding model was trained with (documented by the model provider).

</details>

### 5. Why do we need approximate nearest neighbour (ANN) search?

<details>
<summary>Answer</summary>

Exact search compares the query with **every** vector: O(N·d). For 100 million 768-dimensional vectors, that's ~77 billion multiply-adds per query – too slow at scale. **ANN** indexes find most of the true nearest neighbours (e.g. 95–99% **recall**) in milliseconds by searching only a small part of the data. The trade-off is tunable: more search effort → higher recall, higher latency. For small collections (up to roughly hundreds of thousands of vectors) exact search on a GPU or with optimised libraries is often fine.

</details>

### 6. How does HNSW work?

<details>
<summary>Answer</summary>

**Hierarchical Navigable Small World** builds a multi-layer graph: each vector is a node linked to its near neighbours; upper layers are sparse "express" layers with few nodes, lower layers are dense.

Search: start at an entry point in the top layer, **greedily move** to the neighbour closest to the query, drop down a layer when no closer neighbour exists, and repeat until the bottom layer, keeping a candidate list (size `ef`) for the final results.

Pros: excellent recall/latency, supports incremental inserts. Cons: **memory-heavy** (vectors + graph links kept in RAM), slow to build, deletions are awkward. Parameters: `M` (links per node), `efConstruction`, `efSearch`. The default index in many vector databases and in pgvector.

</details>

### 7. How do IVF and product quantisation work?

<details>
<summary>Answer</summary>

- **IVF (inverted file index)** – cluster vectors with k-means into many lists (e.g. 4,096 centroids). At query time, search only the `nprobe` closest clusters. Faster with more probes = higher recall.
- **Product quantisation (PQ)** – split each vector into sub-vectors and replace each with the ID of its nearest centroid in a small codebook, compressing e.g. 3 KB vectors into ~64 bytes. Distances are approximated from lookup tables.

**IVF-PQ** combines both to search **billions** of vectors with limited memory (FAISS), at some accuracy cost – often fixed by **re-ranking** the top candidates with full-precision vectors. Other options: scalar/binary quantisation, and disk-based indexes (DiskANN) for huge collections.

</details>

### 8. What does a vector database provide beyond an ANN library?

<details>
<summary>Answer</summary>

Libraries (FAISS, hnswlib, ScaNN) give fast in-memory search. **Vector databases** (Pinecone, Weaviate, Milvus, Qdrant, Chroma, or extensions like **pgvector**, and search engines like Elasticsearch/OpenSearch) add: persistence, CRUD updates, **metadata storage and filtering**, hybrid keyword + vector search, horizontal scaling and replication, multi-tenancy, access control and backups.

Choosing: pgvector if you already run Postgres and data fits (simple, transactional); a dedicated vector DB for very large scale or advanced features; Elasticsearch/OpenSearch if you need strong keyword search too.

</details>

### 9. How does metadata filtering work with vector search, and what's the catch?

<details>
<summary>Answer</summary>

Real queries combine similarity with filters (`tenant_id = 42`, `language = "en"`, `date > 2024`, user permissions). Approaches:
- **Pre-filtering** – filter first, then search among matching vectors. Accurate, but can break ANN graph traversal or be slow with very selective filters.
- **Post-filtering** – search first, then drop non-matching results. Fast, but may return too few results when the filter is selective.
- **Filtered/integrated search** – the index understands filters during traversal (supported by most modern vector DBs).

For multi-tenant apps, separate namespaces/partitions per tenant are often simpler and safer than filters.

</details>

### 10. What is hybrid search and why use it?

<details>
<summary>Answer</summary>

Combining **keyword search (BM25)** and **vector search**. Embeddings capture meaning but can miss exact terms (product codes, names, error messages, rare jargon); BM25 nails exact matches but misses paraphrases. Run both and merge results, commonly with **Reciprocal Rank Fusion** (`score = Σ 1/(k + rank)` across result lists) or weighted score combination, then optionally rerank. Hybrid search consistently improves RAG retrieval quality.

</details>

### 11. Bi-encoders vs cross-encoders – what's a reranker?

<details>
<summary>Answer</summary>

- **Bi-encoder** – embeds query and document **separately**; document vectors are precomputed, so search over millions is fast. Less precise because query and document never "see" each other.
- **Cross-encoder** – feeds the query and document **together** through a transformer and outputs a relevance score. Much more accurate, but must run for every candidate at query time.

So systems **retrieve** top 50–200 candidates with a bi-encoder (+ BM25), then **rerank** with a cross-encoder (or an LLM) and keep the top 5–10. This two-stage pattern is standard in search and RAG.

</details>

### 12. What are multimodal embeddings?

<details>
<summary>Answer</summary>

Models like **CLIP/SigLIP** embed images and text into the **same space**, enabling text-to-image search ("red sneakers on a white background"), image-to-image similarity and zero-shot classification. Newer models also embed audio, video and documents (as page images). For documents with charts and tables, embedding page images can work better than extracting text.

</details>

### 13. How do you keep a vector index up to date and healthy?

<details>
<summary>Answer</summary>

- **Incremental updates** – embed and upsert changed documents (driven by change events/CDC); delete removed ones.
- Store **document IDs, versions, source and timestamps** as metadata so you can trace and refresh results.
- **Re-embedding** – required when you change the embedding model or chunking; build a new index alongside the old and switch atomically (blue-green).
- Monitor recall on a fixed test set, latency, index size and memory.
- Some index types degrade with many updates/deletes and need periodic rebuilding or compaction.

</details>

### 14. How much storage do embeddings need? Give an estimate.

<details>
<summary>Answer</summary>

`vectors × dimensions × bytes per value`. Example: 10 million chunks × 1,024 dimensions × 4 bytes (float32) ≈ **41 GB** of raw vectors, plus index overhead (HNSW graph links can add roughly 10–50% or more) and metadata. Reduce with float16 (½), int8 scalar quantisation (¼), binary quantisation (1/32, with reranking), Matryoshka truncation to fewer dimensions, or PQ.

</details>

### 15. Embeddings vs fine-tuning a classifier – when to use which for classification?

<details>
<summary>Answer</summary>

- **Embeddings + simple classifier** (logistic regression or k-NN on embeddings) – fast, cheap, needs modest labelled data, easy to retrain, good baseline.
- **Fine-tuning a transformer** – best accuracy with enough labelled data and for subtle distinctions.
- **LLM zero/few-shot prompting** – no training data needed, flexible labels; more expensive per call and slower.

A common path: prompt an LLM to bootstrap labels → train an embeddings-based or small fine-tuned classifier for cheap, high-volume production use.

</details>

---

[⬅ Previous: Prompt Engineering](11-prompt-engineering.md) · [⬅ Back to index](../README.md) · [Next: Retrieval-Augmented Generation (RAG) ➡](13-rag.md)
