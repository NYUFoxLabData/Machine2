# Machine 2

Analysis of the **Machine 2** study (NYU Fox Lab), in which participants rated music clips and completed a battery
of personality, mood, morality and social tasks. This repo builds the study's SQLite database from the raw MATLAB
export and uses the music ratings to:

- predict demographics and Big Five personality, and
- replicate Myers (2019) on music taste and political ideology.

## Repository layout

```
Machine2/
├── data/                  # inputs: raw export, built database, metadata, prescreen survey
├── notebooks/             # all analysis notebooks (run them from this folder)
├── results/               # outputs written by the notebooks, one folder per analysis
│   ├── music_prediction/  #   multiclass / continuous targets (LightGBM)
│   ├── music_binary/      #   binary targets (LightGBM + logistic regressions)
│   └── music_politics/    #   Myers (2019) replication + written takeaways
└── references/            # Myers (2019) thesis PDF and its Fig. 5 values transcribed to CSV
```

## Data (`data/`)

| File | What it is |
|---|---|
| `nydt_7389415331.mat` | Raw MATLAB export of the experiment (all participants, all tasks) |
| `MusicTable.xlsx` | Clip metadata (title, artist, year, genre, source) |
| `machine2_new_aug.db` | SQLite database built from the two files above by `Machine2_dbBuilding.ipynb` |
| `Music preference prescreening survey.xlsx` | Prescreen survey (gender, race, age, area, religion, political view, SES, …), joined to the DB on a 4-character participant code |

The database has one table per task: `music`, `personality` (BFI-44), `depression`, `LSRP`, `dark`, `IPQ`,
`suffering`, `moral`, `social`, `mood` and `jury`. It also has three lookup tables: `music_meta` (1,000 clips),
`music_genre` (9 genres) and `music_source`. The `music` table holds 1,072 participants' ratings of each clip's
liking, familiarity, arousal and 11 emotions, plus reaction times. All prediction notebooks use the **320 core clips**
that every participant heard exactly once. Clip 1000 is an exposure manipulation and is excluded.

## Notebooks (`notebooks/`)

| Notebook | Purpose | Writes to |
|---|---|---|
| `Machine2_dbBuilding.ipynb` | Parses the `.mat` export and `MusicTable.xlsx` into tidy tables and writes `machine2_new_aug.db` | `data/` |
| `Machine2_db_starter.ipynb` | Starter queries against the database: what each table holds, plus simple summaries such as average ratings | — |
| `Data Team.ipynb` | Early prototype of the `.mat` → SQLite pipeline (superseded by `Machine2_dbBuilding`) | — |
| `music_prediction.ipynb` | Predicts the original multiclass/continuous targets from the 320 clip ratings: LightGBM with RFE clip selection and Optuna tuning, nested 5-fold × 3 CV | `results/music_prediction/` |
| `music_prediction_binary.ipynb` | Same targets split into two classes; LightGBM vs. vanilla/L2/L1/elastic-net logistic regression, best model per target, diagnostics | `results/music_binary/` |
| `music_politics_replication.ipynb` | Replicates Myers (2019): liberal vs. non-liberal song differences and reclassification accuracy, then tests on held-out participants | `results/music_politics/` |

## Running

1. Use Python 3 with: `pandas`, `numpy`, `scipy`, `scikit-learn`, `lightgbm`, `optuna`, `joblib`, `matplotlib`,
   `openpyxl` (for the `.xlsx` files). The DB-building notebooks also use `sqlalchemy`, `pandasql` and `tqdm`.
2. Open the notebooks with `notebooks/` as the working directory (the Jupyter and VS Code default). All paths are
   relative, e.g. `../data/machine2_new_aug.db`.
3. The prediction notebooks run nested CV with Optuna and take a long time. Their saved outputs and the CSVs in
   `results/` are from the last full run.

Notes:

- `Machine2_dbBuilding.ipynb` was first written for Google Colab. The `create_db()` helper and the "Data Cleaning
  for Ibrahim" cells at the end still point to `/content/drive/...`. The main build cells use `../data/`.
- `Data Team.ipynb` writes to `../data/Machine2.db`, an early database that is no longer kept in the repo.

## Key results

- **Binary prediction** (`results/music_binary/music_binary_best_models.csv`, held-out folds): the best model for
  most targets is L2 logistic regression. Race (balanced accuracy 0.81, AUC 0.88) and gender (0.74, AUC 0.86) are the
  most predictable from music ratings. They are followed by lyrics vs. instruments, area and political view
  (AUC ≈ 0.74–0.76). Big Five traits are weakly predictable (AUC 0.56–0.69).
- **Politics replication** (`results/music_politics/music_politics_takeaways.md`): the paper's in-sample accuracy
  reproduces (70.5% vs. 71.5%). On held-out participants, though, the gain over always guessing "liberal" shrinks to
  about 4 points (AUC 0.69). Song-level differences agree with the paper (r = 0.62 over 132 shared songs), but most of
  its genre-level claims do not replicate.

## Reference

Myers, C. (2019). *The songs of my people: appraisal differences of popular music as a function of ideology.*
NYU undergraduate honors thesis, advisor P. Wallisch. PDF: `references/myers2019_songs_of_my_people.pdf`.

## License

MIT. See [LICENSE](LICENSE).
