# Code Switching Language Identification — Roman Urdu / English

Word-level language identification model that tags each word in a mixed Roman Urdu / English sentence as **URD** (Roman Urdu), **ENG** (English), or **MIX** (a nativized loanword like "phone", "call", "free" — English words absorbed into everyday Roman Urdu speech).

Built as part of the **Code Saviours Summer Internship 2026 (SI-26)**.

---

## Why This Matters

Roman Urdu — Urdu written in Latin script — is how most Pakistanis actually text, tweet, and chat, and it's almost always mixed with English mid-sentence ("code-switching"). Standard NLP tools are built for clean, single-language text and break down on this kind of data. Tagging *which* word belongs to which language is a foundational step for anything downstream: sentiment analysis, chatbots, or search that actually works on real Pakistani social media and messaging text — a problem space with very few existing solutions.

---

## Model

**[qandeelasim13/code-switching-codesaviours-si26-qandeel](https://huggingface.co/qandeelasim13/code-switching-codesaviours-si26-qandeel)** on Hugging Face Hub

## Dataset

**[qandeelasim13/code-switching-codesaviours-si26-qandeel](https://huggingface.co/datasets/qandeelasim13/code-switching-codesaviours-si26-qandeel)** on Hugging Face Hub

---

## How It Works

1. **Source data**: Real, naturally-written sentences pulled from the public [Roman Urdu Data Set](https://github.com/Smat26/Roman-Urdu-Dataset) (Sharf, 2017 — UCI ML Repository, CC BY 4.0) — ~19,600 sentences.
2. **Filtering**: Sentences are kept only if they contain both Urdu marker words (*hai, mera, bohot, lekin...*) and English words, and fall within a 5–25 word length range — this isolates genuinely code-switched text.
3. **Auto-labelling**: Every word is labelled URD, ENG, or MIX using curated word lists, with MIX words (nativized loanwords) checked first so they don't get swallowed into the ENG bucket.
4. **Balancing**: MIX is naturally very rare in everyday speech, so sentences containing a MIX word are deliberately oversampled (150 of 300 total sentences) to give the model enough signal to learn from.
5. **Training**: A pretrained `xlm-roberta-base` is fine-tuned as a token classifier on the labelled words, using **class-weighted loss** (capped) to stop the model from just predicting URD everywhere, and a **stratified train/test split** that guarantees MIX examples appear on both sides.
6. **Inference**: Feed in any sentence — the model tags every word independently.

```
Input:  "Please call me when you are free bhai"
Output: Please→ENG  call→MIX  me→ENG  when→ENG  you→ENG  are→ENG  free→MIX  bhai→URD
```

---

## Dataset Details (Week 6)

| | |
|---|---|
| Source | Roman Urdu Data Set (Sharf 2017, UCI ML Repository, CC BY 4.0) |
| Raw sentences pulled | 19,588 |
| Code-switched candidates found | 1,773 |
| Final sentences sampled | 300 (150 MIX-containing + 150 other) |
| Total word-level rows | 4,816 |
| Label distribution | URD: 4,367 · ENG: 271 · MIX: 178 |

*(An earlier version of this dataset had only 12 MIX-labelled words out of 3,182 — too few for any model to learn from. The word lists were widened and MIX-containing sentences were deliberately oversampled to fix this.)*

---

## Results (Week 7)

Fine-tuned `xlm-roberta-base` token classifier, evaluated on the held-out stratified test split:

| Label | Precision | Recall | F1 | Support |
|---|---|---|---|---|
| URD | 0.990 | 0.938 | **0.964** | 859 |
| ENG | 0.638 | 0.815 | **0.715** | 54 |
| MIX | 0.485 | 0.889 | **0.627** | ~18 |
| **Overall (weighted)** | — | — | **0.937** | — |

Eval loss: 0.348

**Note on MIX**: Precision is lower than recall for MIX — the model correctly *catches* most true MIX words (high recall) but also flags some extra ones as MIX (lower precision). This is expected given MIX is the rarest class by far in real Roman Urdu text, and is a reasonable trade-off for a first version.

---

## How to Run Locally

```bash
pip install transformers==4.46.3 torch datasets huggingface_hub scikit-learn
```

```python
from transformers import AutoTokenizer, AutoModelForTokenClassification
import torch

model_name = "qandeelasim13/code-switching-codesaviours-si26-qandeel"
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForTokenClassification.from_pretrained(model_name)

sentence = "Mujhe ye phone bohot pasand aya"
words = sentence.split()
inputs = tokenizer(words, is_split_into_words=True, truncation=True, return_tensors="pt")

with torch.no_grad():
    logits = model(**inputs).logits

preds = torch.argmax(logits, dim=2)[0].tolist()
word_ids = tokenizer(words, is_split_into_words=True, truncation=True).word_ids()

seen = set()
for idx, wid in zip(preds, word_ids):
    if wid is None or wid in seen:
        continue
    seen.add(wid)
    print(f"{words[wid]:15s} -> {model.config.id2label[idx]}")
```

---

## Repo Structure

```
├── SI26_Week6_Qandeel.ipynb   # Dataset collection, filtering, auto-labelling
├── SI26_Week7_Qandeel.ipynb   # Model fine-tuning, evaluation, Hub push
├── dataset.csv                # Final labelled dataset (4,816 word rows)
└── README.md
```

---

**Built by:** Qandeel | Code Saviours SI-26 | 2026
