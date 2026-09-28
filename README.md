# Decomposing the Predictability of Stock-Ticker Attention Bursts on Reddit
 
Final Project (Project Idea 2: Predictive Modelling of Social Media Trend Emergence).
 
For each post that mentions a stock ticker on r/stocks, the pipeline predicts whether mentions of that ticker will surge over the next 24 hours, using only information available before the post. It compares gradient-boosted models with simple baselines, tests which feature groups carry the signal, checks whether co-mention network position adds anything beyond popularity, and repeats the analysis under two surge definitions.
 
## Files
 
| File | Purpose |
|---|---|
| `main.ipynb` | Builds the dataset, runs the unit tests and every experiment, and writes all results. |
| `finbert_scores.ipynb` | Scores posts with FinBERT on a GPU. Optional, needed only for the FinBERT comparison. |
 
## Data
 
Create this folder structure in Google Drive and place the input files in `data/`:
 
```
Final-Project
├── data/
│   ├── stocks.csv, pennystocks.csv, investing.csv
│   ├── finance.csv, stockmarket.csv, wallstreetbets.csv    (label comparison only)
│   ├── nasdaqlisted.txt, otherlisted.txt
│   └── nasdaq_screener.xlsx
├── expensive/    (created automatically, holds slow cached stages)
└── results/      (created automatically, one folder per run)
```
 
- Subreddit CSVs come from the Kaggle dataset at https://www.kaggle.com/datasets/leukipp/reddit-finance-data
- `nasdaqlisted.txt` and `otherlisted.txt` come from the Nasdaq Trader symbol directory at http://www.nasdaqtrader.com/dynamic/SymDir/
- `nasdaq_screener.xlsx` is the Nasdaq stock screener export at https://www.nasdaq.com/market-activity/stocks/screener
- Daily trading volumes are downloaded automatically with `yfinance`.
## How to run
 
1. **FinBERT scores** Open `finbert_scores.ipynb` in Colab with a GPU runtime and run all cells. It installs Transformers 5.17.0, so restart the runtime when prompted and run all cells again. The scores are saved to `expensive/sentiment_finbert_stocks.parquet` and checkpointed, so an interrupted run resumes where it stopped. Without this file, the main notebook skips the FinBERT comparison.
2. **Main notebook** Open `main.ipynb` in Colab with a standard CPU runtime and choose Runtime ->Run all. The notebook stops early with a clear error if an input file or package is missing.
The required packages are numpy, pandas, matplotlib, scikit-learn, imbalanced-learn, networkx, textblob, pyarrow, openpyxl, statsmodels, scipy and yfinance. Colab includes most of them. Install any the notebook reports as missing with `%pip install <package>`.
 
## Outputs
 
Each run writes to `results/<run ID>/`, where the run ID is the UTC start time.
 
- `figures/` holds numbered figures.
- `tables/` holds CSV result tables.
- `audit/` holds the ticker-extraction audits.
- `results.json` holds every logged number, the configuration and the package versions.
Exported tables contain no usernames or post text.
 
## Reproducibility
 
- All randomness uses seed 42, and all settings live in one frozen configuration object near the top of the notebook.
- Slow stages are cached. Set `RECOMPUTE = True` to rebuild everything, and increase `CACHE_VERSION` after changing the code of a cached stage.
- Fifteen unit tests run on synthetic data before any real data is loaded, and four checks run after the experiments. Any failure stops the notebook.
## Privacy/Ethics
 
Usernames are replaced by salted SHA-256 identifiers before analysis. This is pseudonymisation,not anonymisation, and only aggregated results should be shared.