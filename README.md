## 📓 View Full Notebook
👉 [Click here to view the complete notebook]https://colab.research.google.com/drive/1LX546OpJMG489nvB5jV8vwSEJVou0U3J?usp=sharing

# T5-Base Text Simplification

Fine-tuning a **T5-Base** model for automatic text simplification using the **WikiLarge-clean** dataset. The model takes complex English sentences and rewrites them in simpler, more readable language — achieving results competitive with published research baselines.

---

## 🎯 Task

**Text Simplification** — Given a complex sentence, generate a simpler version that preserves the original meaning while reducing vocabulary complexity and sentence length.

| Input (Complex) | Output (Simplified) |
|---|---|
| "Stream ciphers can be susceptible to serious security problems if used incorrectly: see stream cipher attacks — in particular, the same starting state must never be used twice." | "Stream ciphers can be vulnerable to serious security problems if used incorrectly." |
| "Gnathostomulids, or jaw worms, are a small phylum of nearly microscopic marine animals." | "Gnathostomulids are a small phylum of almost microscopic marine animals." |

---

## 📊 Results

| Metric | Score |
|---|---|
| BLEU Score | 40.29 |
| SARI Score | 32.38 |
| Samples Evaluated(validation_used) | 417 |
| Avg. Length Reduction | ~26% |

### Comparison with Published Baselines

| Model | BLEU | SARI | Notes |
|---|---|---|---|
| MUSS (Martin et al. 2022) | 42.53 | 40.29 | BART-large, WikiLarge |
| ACCESS (Martin et al. 2020) | 40.91 | 41.87 | Ctrl tokens on BART |
| LS-Seq2Seq (Nisioi 2017) | 37.25 | 37.11 | Vanilla LSTM baseline |
| **T5-Base (This Project)** | **40.29** | **32.38** | T5-base, WikiLarge-clean |

---

## 🗂️ Dataset

**WikiLarge-clean** (`eilamc14/wikilarge-clean`) from Hugging Face Datasets

| Split | Samples |
|---|---|
| Train (used) | 30,000 |
| Validation | 417 |
| Test | 121 |

- **Source column:** complex sentences
- **Target column:** simplified sentences
- Mean complex sentence length: **27.3 words**
- Mean simple sentence length: **18.8 words**
- Natural dataset reduction: **~30%**

---

## 🧠 Model

**T5-Base** — Text-To-Text Transfer Transformer by Google Research

- Parameters: **222.9M** (all trainable)
- Architecture: Encoder-Decoder Transformer
- Input prefix: `"simplify: "`
- Device: CUDA (Tesla T4, 15.6 GB)

---

## ⚙️ Training Configuration

| Parameter | Value |
|---|---|
| Epochs | 4 |
| Train batch size | 8 |
| Gradient accumulation | 2 (effective batch: 16) |
| Learning rate | 3e-05 |
| Warmup steps | 374 |
| FP16 mixed precision | ✅ |
| Early stopping | ✅ |
| Max input length | 256 tokens |
| Max output length | 128 tokens |
| Train runtime | ~6803s (~1h 53m) |

### Training Loss per Epoch

| Epoch | Training Loss | Validation Loss |
|---|---|---|
| 1 | 5.1048 | 2.2903 |
| 2 | 4.6675 | 2.2401 |
| 3 | 4.6346 | 2.2113 |
| 4 | 4.5111 | 2.2274 |

---

## 🔧 Inference

The `simplify()` function uses **beam search** decoding:

```python
def simplify(text: str,
             num_beams: int = 5,
             max_length: int = 128,
             min_length: int = 5,
             length_penalty: float = 1.0,
             no_repeat_ngram_size: int = 3) -> str:
    """Simplify a complex English sentence using the fine-tuned T5-base model."""
```

---

## 🛠️ Technologies Used

| Tool | Purpose |
|---|---|
| Python | Core language |
| PyTorch | Deep learning framework |
| Hugging Face Transformers | T5 model & Seq2SeqTrainer |
| Hugging Face Datasets | WikiLarge-clean dataset |
| NLTK | BLEU score computation |
| CUDA / GPU | Accelerated training |
| NumPy / Matplotlib | Analysis & visualization |
| SentencePiece | T5 tokenization |

---

## 📦 Installation

```bash
pip install -q transformers datasets sacrebleu sacremoses sentencepiece nltk torch
```

---

## ▶️ Running the Project

Run the notebook on:
- **Kaggle** (recommended — Tesla T4 GPU available free)
- Google Colab
- Jupyter Notebook (GPU required for reasonable training time)

---

## 📈 Evaluation Metrics

**BLEU** — measures n-gram overlap between generated and reference sentences. Rewards outputs that are close to the reference but can be gamed by copying the source.

**SARI** — measures the quality of simplification by evaluating add, delete, and keep operations against the reference. SARI is the **preferred metric** for text simplification as it better reflects actual simplification quality. Models with high BLEU but low SARI tend to copy the source without simplifying.

---

## 🔮 Future Improvements

- Add controllability tokens (length ratio, lexical complexity) as in MUSS/ACCESS
- Fine-tune larger variants: `t5-large`, `flan-t5`
- Pre-train on paraphrase data (ParaBank2) before simplification fine-tuning
- Deploy as a web API using FastAPI
- Add multilingual simplification support

---

## 📜 References

1. Martin et al. (2022). *MUSS: Multilingual Unsupervised Sentence Simplification by Mining Paraphrases.* ACL Findings. https://arxiv.org/abs/2005.00352
2. Martin et al. (2020). *Controllable Sentence Simplification.* LREC. https://arxiv.org/abs/1910.02677
3. Xu et al. (2016). *Optimizing Statistical Machine Translation for Text Simplification.* TACL. (Introduced SARI metric and WikiLarge dataset)

---

## 📜 License

Developed for educational and research purposes.
