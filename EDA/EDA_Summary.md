# Exploratory Data Analysis (EDA) Guide

A personal reference for approaching any new dataset — what each core library is for, and a repeatable step-by-step workflow.

---

## 1. Core Libraries

### pandas
The backbone of EDA. Loads data into DataFrames and handles inspection, cleaning, filtering, grouping, and reshaping.
- `pd.read_csv()`, `.head()`, `.info()`, `.describe()` — first look at the data
- `.isna()`, `.duplicated()`, `.drop_duplicates()` — data quality checks
- `.groupby()`, `.pivot_table()`, `.merge()` — aggregation and joining
- `.astype()`, `.apply()`, `.map()` — type conversion and transformation

### numpy
Underlies pandas; used directly for numerical operations, array math, and handling missing values (`np.nan`).
- `np.where()` for conditional logic
- `np.log()`, `np.sqrt()` for transformations (e.g. skewed distributions)
- Random sampling / seeding for reproducibility

### matplotlib
Low-level plotting library. Gives full control over figure layout — best when you need custom, precise, or multi-panel charts.
- `plt.subplots()` for grid layouts
- `.hist()`, `.scatter()`, `.plot()` for quick built-in pandas plotting (which uses matplotlib under the hood)
- Use when you need fine control over axes, annotations, or combining multiple chart types

### seaborn
Built on matplotlib; provides statistical plots with better defaults and less code. Preferred for exploring distributions and relationships quickly.
- `sns.histplot()`, `sns.boxplot()`, `sns.violinplot()` — distributions
- `sns.scatterplot()`, `sns.regplot()`, `sns.pairplot()` — relationships
- `sns.heatmap()` — correlation matrices
- `sns.countplot()` — categorical frequency

### Other useful libraries (worth knowing even if not always used)
- **scipy.stats** — statistical tests (normality, correlation significance, t-tests)
- **plotly / plotly.express** — interactive charts, useful for dashboards or deeper drill-down exploration
- **missingno** — quick visual summary of missing data patterns
- **ydata-profiling / sweetviz** — auto-generated EDA reports for a fast first pass on a new dataset
- **scikit-learn** — `preprocessing` (scaling, encoding) once EDA moves toward modeling prep

---

## 2. Step-by-Step EDA Workflow

Use this as a checklist for every new dataset.

### Step 1 — Load and get oriented
- Load the data (`pd.read_csv`, `pd.read_excel`, etc.)
- `.shape` — rows and columns
- `.head()` / `.tail()` — sample rows
- `.info()` — dtypes, non-null counts
- `.columns` — check naming (spaces, casing, inconsistent conventions)

### Step 2 — Assess data quality
- `.isna().sum()` — missing values per column, and as a percentage of total rows
- `.duplicated().sum()` — duplicate rows
- Check for placeholder junk values (e.g. `"Unknown"`, `-1`, `999`, empty strings) that mean "missing" but aren't `NaN`
- Check dtypes make sense (dates stored as strings, numbers stored as objects, etc.)

### Step 3 — Clean and prepare
- Fix column names (lowercase, underscores, strip whitespace)
- Convert dtypes (`pd.to_datetime`, `.astype()`)
- Decide on missing value strategy: drop, impute, or flag — document the reasoning
- Remove or investigate duplicates
- Handle outliers only after Step 4 (don't blindly drop before you've seen the distribution)

### Step 4 — Univariate analysis (one variable at a time)
- **Numerical:** `.describe()`, histograms (`sns.histplot`), boxplots for outliers, skewness check
- **Categorical:** `.value_counts()`, bar/count plots, check cardinality (too many unique categories?)
- **Dates:** range check, gaps, frequency over time

### Step 5 — Bivariate / multivariate analysis (relationships)
- Numerical vs numerical: scatterplots, correlation matrix (`.corr()` + `sns.heatmap`)
- Numerical vs categorical: boxplots/violin plots grouped by category
- Categorical vs categorical: crosstabs (`pd.crosstab`), stacked bar charts
- Look for multicollinearity if heading toward modeling

### Step 6 — Ask questions of the data
- Frame the business/analysis question explicitly before plotting (e.g. "Which manufacturer has the fastest coasters?")
- Use `.groupby()` + aggregation to answer directly
- Visualize the answer, don't just eyeball raw tables

### Step 7 — Document findings
- Note key patterns, anomalies, and surprises as you go (not just at the end)
- Record any assumptions or cleaning decisions made, so they're reproducible
- Keep a running summary (like this file) of what the dataset "is" — shape, key columns, quirks

### Step 8 — Decide next steps
- Is the data ready for modeling / reporting / dashboarding?
- What further cleaning, feature engineering, or external data is needed?
- What questions remain unanswered?

---

## 3. Quick Mental Checklist

1. What are the rows? What are the columns?
2. What's missing, duplicated, or mistyped?
3. What does each variable look like on its own?
4. How do variables relate to each other?
5. What's the actual question I'm trying to answer?
6. What did I find, and what do I still not know?
