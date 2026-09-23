# NYC Airbnb Price EDA

Short exploratory analysis of the **New York City Airbnb Open Data** dataset (Inside Airbnb detailed listings), answering: **what factors appear to be associated with Airbnb price?**

The full analysis lives in [`airbnb_eda.ipynb`](airbnb_eda.ipynb), which runs top to bottom and produces every figure in [`figures/`](figures/).

## Findings (short version)

- **Room type** is the strongest lever: entire homes/apartments (~$223 median) run more than double private rooms (~$103) and well above shared rooms (~$55). Hotel rooms sit highest (~$447) but are a small slice.
- **Location** stacks on top of it: Manhattan is the priciest borough (~$241 median), then Brooklyn (~$151), Queens (~$120), with Staten Island (~$107) and the Bronx (~$106) cheapest. A lat/long map shows a clear gradient centered on lower/midtown Manhattan.
- **The numeric columns are weak linear predictors** — nothing correlates with price above ~0.15. Reviews, minimum nights and host listing counts barely move price on their own.
- **Availability** has only a mild positive association with price.
- **Price is heavily right-skewed** (median ~$175 vs mean ~$278); the median and log/trimmed views are used throughout.

Bottom line: price is best explained by *what you rent* (room type) and *where it is* (borough/neighbourhood).

## How to run

Requires Python 3.9+.

```bash
pip install pandas numpy matplotlib seaborn jupyter
jupyter notebook airbnb_eda.ipynb   # then Run All
```

Or execute headless to regenerate the notebook outputs and figures:

```bash
jupyter nbconvert --to notebook --execute --inplace airbnb_eda.ipynb
```

The dataset (`data/listings.csv`) is read from `data/`; the notebook writes PNGs to `figures/` (which it creates automatically).

## File path / working directory

The notebook loads the CSV with a **relative path**:

```python
df = pd.read_csv("data/listings.csv")
```

Relative paths resolve from wherever the notebook is launched, not from the notebook file itself. To make this work, **open the project folder (`airbnb-eda`) as your working directory** before running — e.g. in VS Code use **File → Open Folder** and pick `airbnb-eda`, then open the notebook from the explorer. As long as `data/listings.csv` sits next to the notebook and you open the folder (not just the loose `.ipynb`), the path resolves on any machine.

Expected layout:

```
airbnb-eda/
├── airbnb_eda.ipynb
└── data/
    └── listings.csv
```

If you see a `FileNotFoundError`, the file isn't where the notebook is looking — either open the project folder as above, or change the line to a full path, e.g. `pd.read_csv(r"C:\Users\anish\Downloads\listings.csv")`.

## Design decisions

- **Dataset**: the full Inside Airbnb NYC "listings" file (~30k listings, ~90 columns). It's a fixed snapshot, which keeps the analysis fully reproducible.
- **Schema adapter**: the full file uses different names and formats than the older AB_NYC summary set, so the first cell adapts it — drops the raw `neighbourhood` in favor of `neighbourhood_cleansed`, renames the `*_cleansed` columns, and converts `price` from text (`"$113.97"`) to a float.
- **Cleaning**: dropped rows with `price == 0` or blank price (~8.7k here — many current listings carry no price quote); filled `reviews_per_month` NaN with 0 (these are zero-review listings) and parsed `last_review` as a date. About 21.5k listings remain.
- **Outliers**: kept the raw cleaned frame for medians and correlations, but built a `df_trim` view dropping the top 1% of price (> ~$1,714) for distribution and relationship plots, so a few very expensive listings don't dominate the axes. Nothing is permanently deleted.
- **Medians over means**: price is right-skewed, so group summaries use the median.
- **Neighbourhood ranking** requires at least 30 listings per neighbourhood so tiny samples don't distort the top/bottom lists.

## Files

```
airbnb_eda.ipynb   # the analysis
data/listings.csv
figures/           # generated PNGs
requirements.txt
```

## Data source

New York City Airbnb Open Data — from [Inside Airbnb](https://insideairbnb.com/get-the-data/).
