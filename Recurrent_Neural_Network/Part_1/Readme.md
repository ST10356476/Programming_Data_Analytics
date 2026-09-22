# Author Identification with an LSTM

Guessing which Victorian-era author wrote a passage, from writing style alone.

Part 1 of the PDAN Portfolio of Evidence. Built for a book press that wants a
fan-facing bot: paste in a passage, get a guess and a confidence score.


---

## Dataset

[Victorian Era Authorship Attribution](https://archive.ics.uci.edu/dataset/454/victorian+era+authorship+attribution)
— UCI Machine Learning Repository (dataset 454), donated by Gungor (2018).

| | |
|---|---|
| Records available | 53,678 labelled passages, 45 authors |
| Records used | 28,433 passages, 10 best-represented authors |
| Each record | 1,000 words of prose + an author ID |

Only the **training** file is used. The official test file has no labels and
includes authors absent from training, so it cannot support supervised
evaluation. The labelled file is split 70/15/15 instead.

The source corpus is lowercased, stripped of punctuation, and limited to the
10,000 most frequent words. Character and place names are therefore absent —
the model cannot identify an author by recognising their characters.

The dataset is not committed to this repository. Download `dataset.zip` from
the link above and place the training CSV in `data/`.


---

## Method

1. **EDA in PySpark** — quality checks, class balance, passage length, lexical
   richness, function-word rates, frequent words per author.
2. **Feature selection** — chi-square test on word presence: 9,979 of 10,000
   vocabulary words significant at p < 0.05. The 40 most discriminative words
   are all function words (*the, and, of, that*), confirming the authors
   separate on style rather than subject matter. Stopwords were therefore
   **kept** for training.
3. **Training in TensorFlow/Keras** — embedding → LSTM → dropout → softmax,
   Adam, class weights, early stopping.
4. **Evaluation** — accuracy, macro F1, per-author precision/recall, confusion
   matrix, learning curves.

Spark handles all analysis; training runs in TensorFlow, as the brief allows.

---

## Running it

**Requires Python 3.11 and Java 17.** Neither is optional:

- TensorFlow has no release for Python 3.14.
- Spark 4.x does not support Java 26; the notebook sets `JAVA_HOME` to a JDK 17
  installation in its first cell.

```bash
py -3.11 -m venv .venv
.venv\Scripts\activate          # Windows
pip install -r requirements.txt
```

Then open the notebook, select the `.venv` kernel and run from the top. Set
`SAMPLE_FRACTION = 0.2` in Section 5.1 for a quick end-to-end check before the
full run.

On Windows, keep the project in a short path such as `C:\PDAN\Task_1`.
TensorFlow's nested files exceed the 260-character path limit from a deep
folder, and the install fails partway through.

---

## Limitations

- Recognises only its ten trained authors, and will attribute *any* text to one
  of them — including work by an author it has never seen.
- Trained on 19th-century prose; performance on modern writing is untested.
- The source data removed punctuation, capitalisation and rare words, all of
  which carry stylistic signal.
- Passages from the same book may fall on both sides of the split, since the
  published file exposes no book ID. Scores are likely optimistic as a result.

---

## References

Gungor, A. (2018). *Victorian Era Authorship Attribution* [Dataset]. UCI Machine
Learning Repository.
https://archive.ics.uci.edu/dataset/454/victorian+era+authorship+attribution

Gungor, A. (2018). *Benchmarking authorship attribution techniques using over a
thousand books by fifty Victorian era novelists*. MSc thesis, Purdue University.
https://scholarworks.iupui.edu/handle/1805/15938

Author ID → name mapping from `author_list.txt` in the dataset creator's
repository: https://github.com/agungor2/Authorship_Attribution
