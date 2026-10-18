# What My News Diet Leaves Out

CS156 Machine Learning, Pipeline Assignment 1. Almira Halkina.

This project compares the four Russian-language Telegram channels I read (NEXTA Live, Ateo Breaking, The Insider, Zerkalo) with a reference basket of 19 channels, and measures what my feed covers less: by topic, by place, by wording and by how strikes are framed.

- **Report:** [`report/CS156_Pipeline1_News_Diet.pdf`](report/CS156_Pipeline1_News_Diet.pdf) (LaTeX source in `report/`)
- **Code and outputs:** [`news_diet_pipeline.ipynb`](news_diet_pipeline.ipynb)
- **Interactive dashboard:** [link to the Vercel Demo(https://news-diet-dashboard.vercel.app/)]

## Contents

| Path | Description |
|---|---|
| `news_diet_pipeline.ipynb` | Full pipeline with outputs: data collection, preprocessing, classifiers, topic models, metrics, framing, geography, codebook labels, interactive selector and dashboard export |
| `requirements.txt` | Python packages |
| `data/` | Hand-labelled files used by the notebook (see below) |
| `report/` | Report (PDF), LaTeX source and figures |

## Pipeline

1. **Ingestion.** A crawler reads the public web preview of each channel (`t.me/s/<handle>`), parses the HTML with BeautifulSoup and stores every post in SQLite. 40,580 posts, 1 September – 4 October 2026.
2. **Preprocessing.** Time window, language filter, removal of channel signatures and outlet names, lemmatisation (razdel, pymorphy3), length filter and near-duplicate removal. 32,376 posts remain.
3. **Classifiers.** Majority baseline, multinomial and complement Naive Bayes, logistic regression, linear SVM and character n-grams; temporal split, leave-one-channel-out test and a tuned threshold.
4. **Topic models.** LDA and NMF, selected by NPMI coherence and stability across seeds (NMF, K = 20).
5. **Metrics.** Echo score (Jensen–Shannon divergence from the basket), coverage gaps, an exhaustive null model over all channel combinations, bootstrap, reading-weight sensitivity and blind-spot rates.
6. **Framing and geography.** Marker words, an attacker-naming rule (precision checked by hand), Fightin' Words, and named-entity recognition (Natasha) for places.
7. **Codebook labels.** 400 posts labelled against a 14-category codebook (200 by hand, 400 by an LLM, Cohen's kappa = 0.90) as a check on the topic model.

## Running the notebook

The notebook is written for Google Colab. Run the cells in order.

- The first cell installs the Russian NLP packages.
- The Drive cell mounts Google Drive and restores the database from `MyDrive/cs156-news-diet/news.db`. Slow intermediate results (preprocessed posts, fitted models, place names) are cached in the same folder, so a full run takes a few minutes.
- `RUN_CRAWL` is `False`; set it to `True` to collect the data again (about 2,000 requests, 1.5–2 hours).

## Data

The database (`news.db`, 63 MB) is not included in this repository because of its size; it contains public posts only. The notebook rebuilds it with the crawler.

`data/` holds the hand-labelled files that the notebook reads from the Drive folder:

- `actor_check_labeled.csv`: 60 matches of the attacker rule, marked correct or not by hand
- `label_sample_labeled.csv`: 400 posts with my codebook labels (200) and the LLM labels (400)
- `llm_labels.csv`: the LLM labels on their own
