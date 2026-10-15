# F1 Performance & Regulation Analysis

A Python and SQLite project exploring Formula 1 qualifying performance from **2017–2025**. It combines qualifying results, championship standings, regulation summaries, and estimated aerodynamic testing allocations to examine changes in lap times and field competitiveness across regulation eras.

Developed for the **Data Management course at the University of Milano-Bicocca**.

[Analysis notebook](progetto_data_man_1.1.ipynb) · [Project report](DM%20report%20Mato%2C%20Dawra%2C%20Romano.pdf) · [Presentation](F1_SLIDES.pptx)

## Project highlights

The saved notebook run includes:

- **3,853 qualifying results** across **193 Grands Prix** and nine seasons.
- **46 model-generated regulation summaries**, supplied as a CSV file.
- **40 estimated team-season aerodynamic testing allocations** for 2022–2025.
- Team-name normalization, data integration, completeness checks, and SQL queries.
- Comparisons of qualifying field spread and the fastest pole laps at **nine selected circuits**.

## Research question

How did qualifying performance and field competitiveness differ between **2017–2021** and **2022–2025**?

The project explores these differences in the context of regulatory changes and spending constraints. Its comparisons are descriptive and do not isolate the causal effect of an individual rule.

## Data and methodology

| Component | Source and treatment |
| --- | --- |
| Qualifying results | Jolpica F1 API; paginated retrieval of results and Q1/Q2/Q3 times |
| Championship winners | Jolpica F1 API; driver and constructor standings |
| Regulation summaries | Included `f1_regulations_openai.csv`; generated with an OpenAI model from its internal knowledge, without retrieval of FIA documents |
| Testing allocations | Estimates calculated from prior-season constructor standings and fixed allocation assumptions in the notebook |
| Team identities | Explicit mappings that connect historical team names |

The workflow retrieves and cleans data, converts lap times to seconds, assigns regulation eras, joins the datasets, assesses missing values, and exports analytical tables. SQLite provides persistent storage and queries for season, team, driver, event, and field-spread analysis.

The field-spread calculation uses the latest available qualifying-segment time for each driver, prioritizing Q3, then Q2, then Q1. It is a proxy for qualifying competitiveness rather than a comparison under identical session conditions.

## Results

The saved output in **“Analysis – Field Spread by Era”** reports:

| Period | Mean qualifying spread proxy |
| --- | ---: |
| 2017–2021 | 2.72 seconds |
| 2022–2025 | 2.15 seconds |

![Qualifying field spread by year and regulation era](assets/qualifying-field-spread.png)

*Figure extracted from the notebook's saved output.*

The separate SQL calculation reports **2.55 seconds** for 2017–2021 and **2.15 seconds** for 2022–2025. The analysis section measures gaps relative to the minimum time across all classified drivers; the SQL section calculates the range within the top ten. These definitions can produce different results.

For the nine-circuit comparison, the fastest recorded pole lap in 2022–2025 was faster at **four circuits** and slower at **five**, relative to each circuit's fastest pole lap in 2017–2021. This compares the best observed laps in each period, not average lap times.

## Getting started

### 1. Clone the repository

```bash
git clone https://github.com/TonyDawra/Data-Management-Project.git
cd Data-Management-Project
```

### 2. Install dependencies

Use a Python environment with Jupyter and the following packages:

```bash
python -m pip install notebook pandas numpy requests matplotlib beautifulsoup4 pyarrow
```

`pyarrow` supports the notebook's Parquet export. SQLite is included with Python. The optional regulation-generation cells additionally require the `openai` package and an API key; neither is needed when using the supplied regulation CSV.

### 3. Open the notebook

```bash
jupyter notebook progetto_data_man_1.1.ipynb
```

Run it from the repository directory so that relative data paths resolve correctly. Internet access is required for Jolpica requests.

### 4. Follow the notebook dependencies

The current notebook has a plotting cell that depends on a later calculation. Use this order:

1. Run the imports, qualifying-data acquisition, and team-name mapping.
2. Skip both API-key setup and regulation-generation cells. Load the included CSV in **“LOAD REGULATIONS EXTRACTED”**.
3. Run the testing-allocation, preparation, integration, data-quality, and field-spread sections. Defer the nine-panel plot that references `df_fastest`.
4. Run **“Storage & Export”**, **“WDC/WCC Championship Winners”**, and **“SQL STORAGE (SQLite)”**.
5. Run the final **“Circuit Performance Comparison”** calculation, then return to the deferred nine-panel plot.

The SQL storage cell recreates `f1_data_management.db` when rerun. Updated API responses or regenerated summaries may change the outputs.

## Repository contents

| File | Purpose |
| --- | --- |
| `progetto_data_man_1.1.ipynb` | Data acquisition, transformations, analysis, charts, and SQL storage |
| `f1_regulations_openai.csv` | Supplied regulation summaries |
| `DM report Mato, Dawra, Romano.pdf` | Project report |
| `F1_SLIDES.pptx` | Presentation slides |
| `assets/qualifying-field-spread.png` | README figure extracted from the notebook |

Running the relevant cells generates CSV and Parquet exports, chart images, and two SQLite files:

| Database | Tables |
| --- | --- |
| `f1_data_management.db` | `seasons`, `teams`, `drivers`, `events`, `regulations`, `field_spread` |
| `f1_data.db` | `circuit_performance` |

These generated exports and databases are not included in the original repository snapshot.

## Limitations and next steps

- **Comparability:** Weather, track conditions, tyres, circuit changes, and qualifying formats can affect lap-time comparisons. Weather exclusions are manually specified for one plot and are not applied consistently across all analyses.
- **Regulation provenance:** The CSV contains model-generated summaries. Its source labels are not evidence of retrieval or verification against official FIA documents.
- **Testing assumptions:** Annual allocations use fixed percentages and prior-season standings; they do not reproduce every in-season adjustment.
- **Reproducibility:** Dependencies are not pinned, API data can change, and the two field-spread definitions should be reconciled before presenting a single headline estimate.

Next steps include validating regulation summaries against primary sources, aligning the field-spread calculations, improving session comparability, and making the notebook executable from top to bottom.

## Contributors

**Tony Dawra · Ilaria Mato · Raffaele Romano**  
University of Milano-Bicocca · Data Management · January 2026
