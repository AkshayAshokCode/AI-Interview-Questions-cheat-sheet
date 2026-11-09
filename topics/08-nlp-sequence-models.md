# NLP & Sequence Models

[⬅ Back to index](../README.md)

Covers: text preprocessing, bag-of-words and TF-IDF, word embeddings (word2vec, GloVe, fastText), tokenisation (BPE, WordPiece, SentencePiece), RNNs, LSTMs and GRUs, seq2seq and the origin of attention, BERT vs GPT, contextual embeddings, common NLP tasks and their metrics (BLEU, ROUGE, perplexity).

---

### 1. What are bag-of-words and TF-IDF?

<details>
<summary>Answer</summary>

- **Bag-of-words** represents a document as word counts, ignoring order.
- **TF-IDF** weights each word by **term frequency** in the document × **inverse document frequency** (`log(N / docs containing the word)`), so words common everywhere ("the") get low weight and distinctive words get high weight.

They produce sparse, high-dimensional vectors; combined with logistic regression or Naive Bayes they're strong baselines for text classification, and TF-IDF's cousin **BM25** still powers keyword search (and hybrid search in RAG). Weakness: no semantics ("car" and "automobile" are unrelated) and no word order.

</details>

### 2. How do word embeddings like word2vec work?

<details>
<summary>Answer</summary>

Dense vectors (e.g. 300 dimensions) learned so that words in similar contexts get similar vectors ("you shall know a word by the company it keeps").
- **word2vec** – **skip-gram** predicts context words from a centre word; **CBOW** predicts the centre word from its context; trained efficiently with **negative sampling**.
- **GloVe** – factorises global word co-occurrence statistics.
- **fastText** – uses character n-grams, so it handles rare and misspelled words.

Famous property: vector arithmetic captures relations (`king − man + woman ≈ queen`). Limitation: **one vector per word**, so "bank" (river) and "bank" (money) share a vector – solved by **contextual** embeddings from transformers.

</details>

### 3. What is tokenisation in modern NLP? Explain BPE.

<details>
<summary>Answer</summary>

Models operate on **subword tokens**, balancing vocabulary size against sequence length and handling unknown words.

**Byte-Pair Encoding (BPE):** start with characters (or bytes), repeatedly **merge the most frequent adjacent pair** into a new token until reaching the target vocabulary size (e.g. 50K–200K). Common words become single tokens; rare words split into pieces ("unbelievable" → "un", "believ", "able"). **Byte-level BPE** (GPT models) can represent any text without unknown tokens.

Other schemes: **WordPiece** (BERT; merges by likelihood), **Unigram/SentencePiece** (language-independent, works on raw text including spaces). Practical effects: tokens ≠ words (roughly ¾ of a word in English on average), non-English languages and code often use more tokens (higher cost), and tokenisation explains odd LLM failures like counting letters in a word.

</details>

### 4. How do RNNs work, and what are their problems?

<details>
<summary>Answer</summary>

A recurrent neural network processes a sequence one step at a time, updating a hidden state `hₜ = f(W·xₜ + U·hₜ₋₁)` that summarises everything seen so far. Problems:
- **Vanishing/exploding gradients** through many time steps (backpropagation through time), so long-range dependencies are hard to learn.
- **Sequential computation** – can't parallelise across time steps, so training is slow.
- A single fixed-size hidden state is a bottleneck for long sequences.

</details>

### 5. How do LSTMs and GRUs fix the vanishing gradient problem?

<details>
<summary>Answer</summary>

- **LSTM** adds a **cell state** – a "conveyor belt" updated mostly additively – controlled by gates: the **forget gate** (what to drop), **input gate** (what to add) and **output gate** (what to expose). Additive updates let gradients flow across many steps.
- **GRU** – a simpler variant with **update** and **reset** gates and no separate cell state; fewer parameters, often similar performance.

They dominated NLP and speech before transformers and are still used for some streaming and on-device sequence tasks.

</details>

### 6. What is seq2seq, and how did attention originate?

<details>
<summary>Answer</summary>

**Seq2seq** uses an encoder RNN to compress the input sequence into a vector and a decoder RNN to generate the output (machine translation). The single fixed vector was a bottleneck for long sentences.

**Attention** (Bahdanau, 2014) let the decoder, at each output step, compute a **weighted average of all encoder hidden states**, with weights based on relevance to the current decoding step. Translation quality improved dramatically, and the weights were interpretable (alignments). The transformer (2017) then removed recurrence entirely and used **only attention** – see [Transformers](09-transformers.md).

</details>

### 7. BERT vs GPT – what's the difference?

<details>
<summary>Answer</summary>

| BERT | GPT |
|---|---|
| Transformer **encoder** | Transformer **decoder** |
| **Bidirectional**: each token sees left and right context | **Causal (autoregressive)**: each token sees only previous tokens |
| Pretrained with **masked language modelling** (predict masked tokens) + next-sentence prediction | Pretrained on **next-token prediction** |
| Great for understanding: classification, NER, embeddings, retrieval | Great for **generation**; at scale, does almost any task via prompting |

**T5/BART** are encoder-decoder models (good for translation and summarisation). Today, most frontier LLMs are decoder-only, while encoder models remain popular for embeddings, rerankers and cheap classification.

</details>

### 8. What are contextual embeddings?

<details>
<summary>Answer</summary>

Embeddings that depend on the surrounding text: a transformer produces a different vector for "bank" in "river bank" vs "bank account". Sentence- or document-level embeddings are produced by pooling token embeddings (mean pooling or a special token) from models trained with contrastive objectives (e.g. Sentence-BERT-style models), and they power semantic search and RAG. See [Embeddings & Vector Search](12-embeddings-vector-search.md).

</details>

### 9. What are the common NLP tasks?

<details>
<summary>Answer</summary>

- **Text classification** – sentiment, topic, intent, spam, toxicity.
- **Named entity recognition (NER)** – tag spans as person, organisation, location (token classification).
- **Question answering** – extractive (find the answer span) or generative.
- **Summarisation** – extractive or abstractive.
- **Machine translation.**
- **Semantic similarity / retrieval.**
- **Information extraction** – relations, structured data from documents.

Today many of these are handled by prompting an LLM (zero/few-shot) or fine-tuning a small model when cost, latency or privacy require it.

</details>

### 10. What are perplexity, BLEU and ROUGE?

<details>
<summary>Answer</summary>

- **Perplexity** – `exp(average negative log-likelihood per token)`; how "surprised" a language model is by text. Lower is better; comparable only between models with the same tokeniser.
- **BLEU** – n-gram **precision** of generated text against reference translations, with a brevity penalty; used for machine translation.
- **ROUGE** – n-gram/longest-common-subsequence **recall** against reference summaries; used for summarisation.

Limitations: BLEU/ROUGE reward surface overlap, not meaning, so a correct paraphrase can score poorly. Modern evaluation adds embedding-based metrics (BERTScore), **LLM-as-judge**, and human evaluation – see [LLM Evaluation](16-llm-evaluation.md).

</details>

### 11. What text preprocessing steps were common, and which still matter with LLMs?

<details>
<summary>Answer</summary>

Classic pipelines: lowercasing, removing punctuation and stop words, stemming/lemmatisation, handling numbers and URLs. With transformers and LLMs, aggressive preprocessing usually **hurts** (models use case, punctuation and stop words for meaning). What still matters: cleaning boilerplate and HTML, deduplication, language identification, removing PII, normalising Unicode, chunking long documents, and filtering low-quality data for training.

</details>

### 12. How do you handle text longer than a model's maximum length?

<details>
<summary>Answer</summary>

- **Truncate** (keep the most important part, e.g. the beginning).
- **Chunk** with overlap, process each chunk, then aggregate (max/mean pooling of predictions, map-reduce summarisation).
- Use a **long-context model**.
- **Retrieve** only the relevant chunks (RAG) instead of feeding everything.
- Hierarchical approaches: summarise sections, then summarise the summaries.

</details>

### 13. How do speech recognition (ASR) and text-to-speech (TTS) work today?

<details>
<summary>Answer</summary>

- **ASR** – audio is converted to a log-mel spectrogram; an encoder (transformer/Conformer) processes it; outputs are decoded with **CTC**, RNN-Transducer (good for streaming) or an attention decoder. **Whisper** is an encoder-decoder transformer trained on huge multilingual weakly-labelled data, robust to accents and noise. Metric: **word error rate (WER)**.
- **TTS** – text → linguistic/phoneme features → acoustic representation (mel spectrogram or discrete audio tokens) → waveform via a neural vocoder; modern systems use autoregressive transformers over audio codec tokens or diffusion/flow models, and can clone voices from short samples (a misuse risk).
- **Speech-native LLMs** – process and generate audio directly, enabling low-latency voice assistants with natural turn-taking.

Engineering concerns: streaming latency, voice activity detection and endpointing, background noise, code-switching (e.g. Hindi–English), and on-device vs cloud processing.

</details>

---

[⬅ Previous: Computer Vision](07-computer-vision.md) · [⬅ Back to index](../README.md) · [Next: Transformers Deep Dive ➡](09-transformers.md)
