# CODE-SWITCHING-CODESAVIOURS-SI26-QANDEEL
# Code Switching NLP | Code Saviours SI-26 | Qandeel Asim

## Overview

This repository contains a word-level labelled dataset of Roman Urdu–English code-switched sentences, built as part of Project 2 (Week 6) of the Code Saviours Summer Internship 2026 (SI-26). The goal is to produce a dataset that captures how Pakistani speakers naturally mix Roman Urdu and English in everyday text — a pattern common in social media, messaging apps, and informal writing, and one that few existing NLP resources handle well.

## Dataset

- **File:** `dataset.csv`
- **Format:** flat, word-level rows — `sentence`, `word`, `label`
- **Size:** 200 sentences, 3,182 labelled word entries
- **Labels:**
  | Label | Meaning |
  |-------|---------|
  | `URD` | Urdu word written in Roman script |
  | `ENG` | English word |
  | `MIX` | A widely-nativized English-origin loanword that functions as everyday Urdu vocabulary (e.g. `phone`, `internet`, `mobile`) |

### Source and Collection Method

Sentences were drawn from the **Roman Urdu Data Set** (Sharf, 2017), a public research corpus of 20,000+ real sentences gathered from e-commerce reviews, public Facebook page comments, and Twitter posts, hosted on the [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/458/roman+urdu+data+set) under a CC BY 4.0 license.

From this corpus, sentences were programmatically filtered to keep only those that are genuinely code-switched — i.e. contain both recognizable Roman Urdu markers and English words — using a keyword-matching heuristic. A random sample of 200 qualifying sentences was selected and each word in every sentence was labelled `URD`, `ENG`, or `MIX`.

Auto-assigned labels were spot-checked and corrected manually where the heuristic mislabelled informal spellings or slang.

### Attribution

> Sharf, Z. (2017). *Roman Urdu Data Set* [Dataset]. UCI Machine Learning Repository. https://doi.org/10.24432/C58325

## Repository Contents

| File | Description |
|------|--------------|
| `SI26-Week6-Qandeel.ipynb` | Colab notebook — downloads the source corpus, filters for code-switching, applies word-level labels, and exports `dataset.csv` |
| `dataset.csv` | Final labelled dataset |
| `README.md` | This file |

## How to Reproduce

1. Open `SI26-Week6-Qandeel.ipynb` in Google Colab.
2. Run all cells in order — the source dataset downloads automatically, no login or API key required.
3. The final labelled dataset is saved as `dataset.csv` in the Colab working directory.

## Label Distribution

| Label | Count |
|-------|-------|
| URD | 2,936 |
| ENG | 234 |
| MIX | 12 |

## Links

- Hugging Face dataset: *[add link after publishing]*
- Submitted for: Code Saviours Summer Internship 2026 — Week 6, Project 2
