---
language: ur
tags:
- token-classification
- code-switching
- roman-urdu
license: cc-by-4.0
---

# Code-Switching Language ID — Roman Urdu / English

Token classification model that labels each word in Roman Urdu text as `URD` (Roman Urdu),
`ENG` (English), or `MIX` (nativized English loanword used as Urdu vocabulary).

Fine-tuned from [xlm-roberta-base](https://huggingface.co/xlm-roberta-base).

## Dataset
- Source: filtered from the [Roman Urdu Data Set](https://archive.ics.uci.edu/dataset/458/roman+urdu+data+set) (Sharf, 2017, UCI ML Repository, CC BY 4.0)
- 200 sentences, 3182 word-level labels
- Label distribution: URD 4367, ENG 271, MIX 178
- Dataset card: https://huggingface.co/datasets/qandeelasim13/code-switching-codesaviours-si26-qandeel

## Evaluation (held-out 20% split)
| Label | F1 | Precision | Recall | Support |
|---|---|---|---|---|
| URD | 0.964 | 0.990 | 0.938 | 859 |
| ENG | 0.715 | 0.638 | 0.815 | 54 |
| MIX | 0.627 | 0.485 | 0.889 | 36 |

Overall weighted F1: 0.937

## Limitations
- MIX is severely underrepresented in training data (12 of 3182 word labels), so MIX
  predictions should be treated as low-confidence until the dataset is expanded.
- Trained on informal social-media Roman Urdu; may not generalize well to formal text.

## Training
XLM-RoBERTa-base fine-tuned for 8 epochs with a class-weighted loss (weights: URD
0.37, ENG 5.92, MIX 9.02) to
counter the class imbalance above.

Built for Code Saviours Summer Internship 2026 (SI-26), Week 7.
