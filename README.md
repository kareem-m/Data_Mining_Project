# 🧑‍💻 Developer Career Profiler — Stack Overflow Survey Analysis

A complete end-to-end data mining pipeline that analyzes the **Stack Overflow Developer Survey** dataset to uncover hidden patterns in developer careers, cluster professionals into meaningful groups, infer job satisfaction using fuzzy logic, and optimize feature selection with a genetic algorithm.

---

## 👥 Team Members

| Name | GitHub |
|------|--------|
| [Member 1 Name] | [@FarahHossam126](https://github.com/FarahHossam126) |
| [Member 2 Name] | [@kareem-m](https://github.com/kareem-m) |
| [Member 3 Name] | [@username](https://github.com/username) |
| [Member 4 Name] | [@username](https://github.com/username) |
| [Member 5 Name] | [@username](https://github.com/username) |

---

## 📌 Project Overview

This project conducts a full data mining study on the Stack Overflow Developer Survey — a rich, real-world dataset capturing the experiences of thousands of software professionals worldwide. The goal is to build a unified system that:

1. Explores and visualizes key developer trends (salaries, age, satisfaction, remote work)
2. Cleans and preprocesses the raw survey data for analysis
3. Groups developers into natural clusters using K-Medoid and Hierarchical Clustering
4. Infers a **job satisfaction score** for any developer profile using a Fuzzy Logic Inference System
5. Optimizes the feature set used for clustering using a **Genetic Algorithm**
6. Ties everything together in a `apply_system()` pipeline that produces a meaningful **Developer Career Score** for any new survey record

---

## 📂 Repository Structure

```
Data_Mining_Project/
│
├── main.ipynb                  # Main Jupyter Notebook (fully executed)
├── stackoverflow_survey.csv    # Stack Overflow Developer Survey dataset
├── flowchart.jpeg              # Pipeline flowchart (Task 7)
├── environment.yml             # Conda environment specification
└── README.md                   # This file
```

---

## 📊 Dataset

| Property | Detail |
|----------|--------|
| **Source** | [Stack Overflow Developer Survey](https://survey.stackoverflow.co/) |
| **Domain** | Software Engineering / Developer Workforce |
| **Features** | 80+ attributes (experience, education, job role, salary, technologies, remote work, satisfaction, etc.) |
| **Instances** | 1,000+ respondents (after deduplication and cleaning) |
| **Type** | Mixed — numeric and categorical |

---

## 🔬 Pipeline Overview

![Pipeline Flowchart](flowchart.jpeg)

---

## 🗂️ Tasks Summary

### Task 1 — Exploratory Data Analysis & Visualization

Conducted an in-depth exploration of the dataset with **6+ visualizations**:

- 📊 **Salary by Job Title** — Bar chart comparing average annual salaries across developer roles; leadership and infrastructure roles earn the most
- 🥧 **Age Distribution** — Pie chart showing the dataset is dominated by young-to-mid-career professionals (under 45)
- 📈 **Job Satisfaction Distribution** — Histogram revealing a left-skewed distribution, with most respondents rating satisfaction 7–9/10
- 🥧 **Remote Work Distribution** — Pie chart showing 80%+ of respondents have at least partial remote flexibility
- 📦 **Salary Outlier Detection** — Box plot exposing extreme compensation outliers in the dataset
- 🥧 **Company Size Distribution** — Balanced spread of respondents across startups, mid-sized firms, and large enterprises

---

### Task 2 — Data Preprocessing

| Step | Method | Justification |
|------|--------|---------------|
| **Deduplication** | Remove duplicate rows | 2,926 exact duplicates removed |
| **Missing Values — Critical Columns** | Row removal | Preserves dataset reliability |
| **Missing Values — Numerical** | Median imputation | Robust to outliers |
| **Missing Values — Categorical** | Mode imputation | Preserves the most common category |
| **Outlier Treatment** | IQR method on `ConvertedCompYearly` | Removes extreme compensation values |
| **Encoding — Ordinal** | Label Encoding | For features with natural order (e.g., education level) |
| **Encoding — Nominal** | One-Hot Encoding | For unordered categories (e.g., job type, industry) |
| **Encoding — Multi-label** | Multi-label One-Hot | For `LearnCode` (respondents may select multiple answers) |
| **Scaling** | MinMax Scaler → [0, 1] | Normalizes all features for distance-based algorithms |
| **Dimensionality Reduction** ⭐ | PCA | Created composite `AIScore` and `TechScore` from related features; original columns removed |

---

### Task 3 — K-Medoid Clustering

- **Optimal k** determined using the **Elbow Method** and **Silhouette Score** → **k = 3**
- Metric: **Manhattan distance** | Init: **random** | `random_state=42`
- Silhouette Score (full features): **~0.333**
- Cluster centers are real data points (actual survey respondents), not mathematical averages
- Each cluster represents a distinct developer career profile

---

### Task 4 — Hierarchical Clustering

- Applied **Agglomerative Clustering** with three linkage methods: **Single**, **Complete**, **Average**
- Optimal k confirmed at **k = 3** via Silhouette Score across all linkage methods
- Dendrograms visualize how clusters form at different distance thresholds
- Cluster assignments compared across linkage methods to validate stability

---

### Task 5 — Fuzzy Logic Inference System

**Objective:** Predict a developer's **Job Satisfaction Score** given their profile

| Component | Detail |
|-----------|--------|
| **Inputs** | `ConvertedCompYearly`, `WorkExp`, `RemoteWork`, `TechScore`, `AIScore`, `dev_profile` (cluster label) |
| **Output** | `JobSat` (job satisfaction level) |
| **Membership Functions** | Auto-generated: `poor`, `average`, `good` for each input |
| **Output Labels** | `Very satisfied`, `Slightly satisfied`, `Neutral`, `Slightly dissatisfied`, `Very dissatisfied` |
| **Rule Base** | 9+ IF-THEN rules grounded in domain knowledge |
| **Defuzzification** | Centroid method |
| **Validation** | Tested against real samples from the dataset |

**Example rules:**
- IF `Salary=good` AND `WorkExp=good` AND `TechScore=good` AND `AIScore=good` → `Very satisfied`
- IF `RemoteWork=good` AND `Salary=average` → `Slightly satisfied`
- IF `Salary=poor` AND `WorkExp=poor` → `Very dissatisfied`

---

### Task 6 — Genetic Algorithm Optimization

**Objective:** Find the optimal feature subset for K-Medoid clustering (feature selection)

| GA Component | Definition |
|--------------|------------|
| **Chromosome** | Binary string of length = total features (60) |
| **Gene** | 1 = use feature, 0 = drop feature |
| **Fitness Function** | Silhouette Score of K-Medoid clustering on selected features |
| **Selection** | Tournament / top-k selection |
| **Crossover** | Single-point crossover |
| **Mutation** | Random bit flip with 10% probability |
| **Elitism** | Top 2 chromosomes carried forward unchanged each generation |
| **Population** | 30 chromosomes |
| **Generations** | 5 |

**Results:**
- Features reduced: **60 → 28**
- GA-selected features include: `EdLevel`, `RemoteWork`, `TechScore`, various `DevType_*` and `Industry_*` one-hot columns
- Dropped features: `Age`, `WorkExp`, `OrgSize`, `ConvertedCompYearly`, `JobSat`, `AIScore`, and 26 others

---

### Task 7 — Unified `apply_system()` Pipeline

A single function `apply_system(df_sample)` that takes any new survey record and produces a full career analysis:

```
Input: New developer survey record
       ↓
[Part 1] Before GA → K-Medoid cluster assignment using all features
       ↓
[Part 2] Fuzzy Inference → Job satisfaction score
       ↓
[Part 3] After GA → K-Medoid cluster assignment using GA-optimized features
       ↓
Output: Developer Career Score + Cluster Profile
```

---

## ⚙️ Setup & Installation

### Prerequisites
- [Anaconda](https://www.anaconda.com/) or [Miniconda](https://docs.conda.io/en/latest/miniconda.html)

### Create Environment

```bash
conda env create -f environment.yml
conda activate data_mining
```

### Key Dependencies

| Package | Purpose |
|---------|---------|
| `pandas` | Data manipulation |
| `numpy` | Numerical computing |
| `scikit-learn` | Preprocessing, PCA, Agglomerative Clustering |
| `scikit-learn-extra` | K-Medoids implementation |
| `scikit-fuzzy` | Fuzzy Logic Inference System |
| `matplotlib` | Visualizations |
| `seaborn` | Statistical plots |
| `scipy` | Hierarchical clustering, dendrograms |
| `openpyxl` | Excel I/O support |

### Run the Notebook

```bash
jupyter notebook main.ipynb
```

---

## 🔑 Key Findings

- **Developer clustering** reveals 3 distinct career profiles based on experience, compensation, role type, and work environment
- **Job satisfaction** is strongly influenced by compensation, remote work flexibility, and technical skill breadth — not just salary alone
- **The Genetic Algorithm** reduced the feature space by ~53% (60 → 28 features) while maintaining clustering quality, identifying that demographic features like `Age` and `WorkExp` contribute less than role-type and industry features
- **Over 80%** of surveyed developers have at least some remote work flexibility, reflecting a fundamental shift in the industry
- **Leadership and infrastructure roles** (engineering managers, cloud architects, senior executives) consistently command the highest salaries

---

## 📝 Notes

- The dataset used is the real-world Stack Overflow Developer Survey — not a standard tutorial dataset (no Iris, no Titanic)
- All preprocessing transformations are documented with justifications in the notebook
- PCA-based dimensionality reduction was applied to create `AIScore` and `TechScore` composite features
- Fuzzy rules are grounded in domain knowledge about developer career dynamics
