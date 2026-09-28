# Anatomy of a Deceptive Review

### Comparing Linguistic Cue Profiles across Humans, a Zero-Shot LLM, and Fine-Tuned RoBERTa

This repository contains the code and experimental report developed for the
**Human Language Technologies** course at the **University of Trento (UniTN)**,
within the Master's Degree in **Cognitive Science**, track
**Computational and Theoretical Modelling of Language and Cognition (CLC)**.

The project investigates how **humans, a zero-shot Large Language Model, and a
fine-tuned transformer classifier** detect deceptive online reviews, with a
particular focus on the **linguistic cues associated with their decisions**.

Rather than considering classification accuracy alone, the project asks a
broader question:

> **What linguistic cues are reported by humans and behaviorally associated
> with human and machine detection of deceptive reviews, and how do these cue
> profiles differ?**

---

## Overview

Deceptive language detection is commonly approached as a binary classification
problem. However, high predictive accuracy does not necessarily explain
**which linguistic patterns a model relies on**, nor whether those patterns are
similar to the strategies used by human readers.

This project therefore compares three different perspectives on deception:

- **Human judgments**, collected through an experimental questionnaire;
- **Qwen3-4B-Instruct-2507**, evaluated in a zero-shot setting;
- **RoBERTa-base**, fine-tuned for truthful/deceptive review classification.

Their decisions are then analyzed through a set of transparent and
interpretable linguistic features.

The aim is not only to determine *which system classifies deception better*,
but to understand **how differently humans, general-purpose LLMs, and supervised
language models respond to linguistic evidence**.

---

## Dataset

The project uses the **Deceptive Opinion Spam Corpus** introduced by
**Ott et al. (2011)**.

The original corpus contains 1,600 hotel reviews. For this project, only the
positive subset is used in order to reduce sentiment polarity as a potential
confounding factor.

The resulting dataset contains:

- **800 positive hotel reviews**
- **400 truthful reviews**
- **400 deceptive reviews**
- Reviews covering **20 Chicago hotels**
- Five predefined hotel-based folds

Truthful reviews originate from **TripAdvisor**, while deceptive reviews were
written by **Amazon Mechanical Turk workers**.

This distinction is particularly important for the interpretation of the
results, since linguistic differences may reflect not only deception itself
but also differences in **data source and collection procedure**.

---

## Models

### Qwen3 — Zero-Shot LLM

The general-purpose LLM used in the project is:

**Qwen3-4B-Instruct-2507**

The model receives only the review text and performs the task in a
**zero-shot setting**, without access to the gold label, hotel identity,
dataset fold, or source.

Because preliminary experiments showed substantial prompt sensitivity, the
final protocol uses an **anonymous and A/B-counterbalanced prompt**.

Model output scores are converted into a continuous **deception score**, which
captures the direction and strength of the model decision rather than being
interpreted as a calibrated probability.

---

### RoBERTa — Supervised Classifier

The supervised model is:

**FacebookAI/roberta-base**

RoBERTa is fine-tuned to perform binary classification between:

- `0` → truthful
- `1` → deceptive

Evaluation is performed using **5-fold hotel-based cross-validation**.

For every fold:

- four hotel-based folds are used for training;
- the remaining fold is used for testing;
- a new RoBERTa model is initialized and fine-tuned.

The final analysis therefore uses **out-of-fold predictions for all 800
reviews**, avoiding evaluation on examples seen during training.

---

## Human Experiment

A human deception-detection experiment was conducted on a subset of the corpus.

The experiment included:

- **38 participants**
- **120 reviews**
- 60 truthful and 60 deceptive reviews
- Six questionnaires containing 20 reviews each
- **760 individual judgments**

For each review, participants were asked to:

1. classify it as **truthful or deceptive**;
2. report their **confidence** on a 1–5 scale.

Participants were also asked which linguistic strategies they used when making
their judgments through:

- an **open-ended response**;
- a predefined **linguistic cue checklist**.

This makes it possible to compare what humans **say they use** with the
linguistic properties that are actually associated with their decisions.

An example of the questionnaire provided to humans is available directly in the repository.
---

## Linguistic Cue Analysis

To compare human and machine behavior, the project defines
**17 transparent computational proxies** representing linguistic dimensions
commonly associated with deception and credibility.

The features include:

- number of words;
- mean sentence length;
- Automated Readability Index (ARI);
- first-person pronouns;
- positive affect;
- negative affect;
- certainty markers;
- hedging;
- cognitive-process language;
- perceptual language;
- temporal references;
- spatial references;
- numerical references;
- contrast markers;
- promotional language;
- exclamation marks;
- repetition.

These features are intentionally simple and interpretable. They are not
intended as complete psychological measures, but as reproducible proxies that
allow the behavior of different systems to be compared.

---

## Experimental Analysis

The repository implements several complementary analyses.

### 1. Predictive Performance

Classification performance is compared across:

- human majority judgments;
- zero-shot Qwen3;
- fine-tuned RoBERTa.

### 2. Corpus-Level Linguistic Regularities

The distribution of the 17 linguistic features is compared between truthful
and deceptive reviews.

The analysis uses:

- **Mann–Whitney U tests**
- **Cliff's delta**
- **Benjamini–Hochberg FDR correction**

### 3. Behavioral Cue Profiles

To investigate which linguistic properties are associated with each system's
decisions, the project computes correlations between linguistic features and
continuous deception scores.

A **partial Spearman correlation controlling for the gold label** is also used
to reduce the effect of correlations that arise simply because a feature is
associated with the true class.

### 4. Cue Conflict

A further analysis measures how strongly the linguistic profile of a review
conflicts with the typical profile of its true class.

This makes it possible to investigate whether models become more error-prone
when reviews violate the linguistic regularities normally associated with
their class.

---

## Main Findings

The results suggest that **deception detection does not correspond to a single
linguistic strategy**.

RoBERTa achieves substantially higher classification accuracy than both human
participants and the zero-shot Qwen model. However, its predictions are also
strongly aligned with the linguistic regularities of the specific training
corpus.

Qwen shows a different behavioral profile. Several linguistic associations in
its decisions do not match the patterns observed in the dataset, suggesting
that the zero-shot model may rely more heavily on general linguistic
knowledge or previously learned heuristics.

Human judgments are considerably more heterogeneous. Participants report using
a variety of cues, but no individual computational proxy consistently explains
their aggregate decisions after multiple-comparison correction.

One of the central conclusions of the project is therefore that:

> **Humans, zero-shot LLMs, and supervised language models can approach the same
> deception-detection problem through substantially different linguistic cue
> profiles.**

High classification accuracy should consequently not be interpreted as direct
evidence that a model has learned a general theory of deception.

---

## Repository Contents

This repository contains:

- the code used for dataset preprocessing;
- zero-shot Qwen inference;
- RoBERTa fine-tuning and cross-validation;
- linguistic feature extraction;
- statistical analyses;
- human experiment analysis;
- visualizations and result generation;
- the final experimental report.

> The exact repository structure may evolve as the project is cleaned and
> reorganized for reproducibility.

---

## Reproducibility

The experiments were designed with an emphasis on transparent and reproducible
analysis.

The main pipeline can be summarized as:
```text

Deceptive Opinion Spam Corpus
            │
            ├── Human experiment
            │       ├── Classification judgments
            │       ├── Confidence scores
            │       └── Self-reported linguistic cues
            │
            ├── Qwen3-4B-Instruct-2507
            │       └── Zero-shot deception scores
            │
            └── RoBERTa-base
                    └── 5-fold supervised predictions
                            │
                            ▼
                 Linguistic feature extraction
                            │
                            ▼
              Behavioral cue profile analysis
                            │
                            ▼
                    Cue-conflict analysis
```
---

## License

### Code

Unless otherwise stated, all **source code** in this repository is licensed under the [MIT License](LICENSE).

You are free to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the code, subject to the terms of the MIT License.

### Reports, Papers, and Written Content

Unless otherwise stated, all **reports, papers, reviews, documentation, and other original written content** in this repository are licensed under the **Creative Commons Attribution 4.0 International License (CC BY 4.0)**.

Under CC BY 4.0, you are free to:

* **Share** — copy and redistribute the material in any medium or format.
* **Adapt** — remix, transform, and build upon the material, including for commercial purposes.

The following conditions apply:

* **Attribution** — you must give appropriate credit to the author, provide a link to the license, and indicate if changes were made.
* You may not imply that the author endorses you or your use of the material.

For the full license terms, see the [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/).

Unless otherwise stated, © 2026 Chiara Tosadori. All rights reserved for materials not covered by the licenses above.
