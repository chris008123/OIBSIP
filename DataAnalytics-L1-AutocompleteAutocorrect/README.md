# Autocomplete and Autocorrect Data Analytics

**Oasis Infobyte Data Analytics Internship — Task 5**

## Overview

An NLP data analytics project analysing the efficiency and accuracy of autocomplete and autocorrect algorithms using frequency-based n-gram models and edit-distance spelling correction, tested on a real text corpus and a controlled set of deliberate misspellings.

## Objectives

- Load and analyse a real-world text corpus.
- Build and compare bigram vs. trigram frequency-based autocomplete.
- Build and compare two autocorrect approaches (general-dictionary vs. corpus-restricted edit distance).
- Measure and report real accuracy, precision, recall, and F1 for both tasks.
- Discuss limitations relative to production systems (Gboard, SwiftKey, AI writing tools).

## Dataset

- **Corpus:** *Emma* by Jane Austen (1816).
- **Source:** NLTK's bundled Project Gutenberg corpus (`nltk.corpus.gutenberg`, `austen-emma.txt`) — full public-domain text.
- **Format:** plain text (`data/corpus.txt`).
- **Size:** 887,071 characters; 191,855 raw tokens; 161,516 cleaned word tokens (7,293 unique, stopwords included); 74,097 tokens / 7,168 unique words after stopword removal.
- **Why selected:** a single, complete, coherent, dialogue-rich novel — large enough for meaningful n-gram statistics, small enough to process quickly, with natural conversational phrasing well suited to autocomplete demonstration.

## Technologies

```
Python
Pandas, NumPy
Matplotlib, Seaborn
NLTK (tokenization, stopwords)
pyspellchecker
textdistance (Levenshtein distance)
Jupyter Notebook
```

## NLP Preprocessing

Lowercase → tokenize (`word_tokenize`) → strip surrounding punctuation → filter to real word tokens. Stopwords were **retained** for the autocomplete models (removing them would strip out essential, high-frequency prediction targets like "the", "to", "of") but **removed** for the general word-frequency analysis, so the Top-20 chart shows meaningful content words rather than function words. This decision is documented explicitly in the notebook.

## Autocomplete

- **Bigram model:** predicts the next word from the single preceding word.
- **Trigram model:** predicts the next word from the preceding two words.
- **Evaluation:** a sequential 80/20 train/test split (no shuffling, to preserve word order); Top-1 and Top-3 accuracy measured by sliding a window across held-out test tokens; precision@3/recall@3 defined explicitly for the single-relevant-item ranking case.
- **Result:** Bigram outperformed trigram overall (Top-3 accuracy 24.3% vs 21.2% on attempted cases) — not because less context is better, but because trigram coverage on unseen text was only 66.6% vs bigram's 97.7%, a direct sparsity effect of a modest-sized corpus.

## Autocorrect

- **Approach 1:** `pyspellchecker` — general English dictionary and frequency model.
- **Approach 2:** custom Levenshtein-distance corrector, restricted to this project's own corpus vocabulary, with frequency-based tie-breaking.
- **Test set:** 25 deliberately misspelled words, each with a correct answer verified to exist in the corpus vocabulary.
- **Evaluation:** accuracy, precision, recall, F1 (documented as numerically identical here, given the single-relevant-answer, always-a-prediction evaluation setup), plus a labelled outcome matrix (Correct / Incorrect / Unchanged) in place of a conventional multi-class confusion matrix.
- **Result:** pyspellchecker reached 88.0% accuracy (22/25) vs. 84.0% (21/25) for the custom corpus-restricted approach — though the custom approach uniquely got one case right ("leter" → "letter") that the general-dictionary approach missed, showing the trade-off runs both ways.

## Results

- Top content words (stopwords removed): dominated by character names (emma, harriet, weston, knightley, elton) and modal verbs (could, would, must).
- Autocomplete: bigram coverage 97.7% vs trigram 66.6%; Top-1 accuracy ~13% for both on attempted cases; Top-3 accuracy 24.3% (bigram) vs 21.2% (trigram).
- Autocorrect: pyspellchecker 88.0% vs custom Levenshtein 84.0% accuracy; error analysis identified 5 genuine miss cases, all attributable to either ambiguous edit-distance ties or the custom approach's vocabulary restriction.
- Execution time: custom Levenshtein correction (~289 ms/call) is roughly 950x slower than pyspellchecker (~0.31 ms/call) due to its brute-force vocabulary scan — a measured, environment-specific result, not a general performance claim.

## Visualisations

1. Top 20 Most Frequent Words (bar chart)
2. Autocomplete Model Comparison — Bigram vs Trigram (bar chart)
3. Autocorrect Model Comparison — both approaches across 4 metrics (bar chart)
4. Autocorrect Outcome Matrix (stacked bar chart)

All saved under `outputs/screenshots/`.

## Limitations

- Single, dated (1816), single-author corpus — narrow, unrepresentative vocabulary and style versus modern informal text.
- Small (25-word) misspelling test set — enough to demonstrate the method, not statistically robust on its own.
- No smoothing applied to n-gram models — unseen contexts return nothing rather than a small non-zero probability.
- No semantic understanding, no personalization, and no use of surrounding sentence context in autocorrect.
- Precision/recall/F1 definitions are deliberately simplified for this project's single-relevant-item evaluation setup and documented as such.
- Custom Levenshtein approach is a brute-force scan and would not scale without additional indexing (e.g. a BK-tree).

## Real-World Applications

Smartphone keyboards, search-engine query completion/correction, email and messaging compose-assist, writing assistants, customer support query handling, accessibility tools, and document-editor spellcheck all rely on the same underlying techniques demonstrated here, at much greater scale and sophistication.

## How to Run

```bash
pip install -r requirements.txt
jupyter notebook Autocomplete_Autocorrect_Analysis.ipynb
```

## Project Structure

```
DataAnalytics-L1-AutocompleteAutocorrect/
├── data/
│   └── corpus.txt
├── outputs/
│   └── screenshots/
├── Autocomplete_Autocorrect_Analysis.ipynb
├── README.md
└── requirements.txt
```
