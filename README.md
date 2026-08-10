# 🐍 Python for Machine Learning & Data Science

> A structured, beginner-to-intermediate teaching repository covering the **core Python data science stack** — from NumPy fundamentals all the way to your first scikit-learn ML models.

---

## 📚 Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Repository Structure](#repository-structure)
- [Learning Path](#learning-path)
  - [1. Functions and Modules](#1-functions--modules)
  - [2. NumPy Fundamentals](#2-numpy-fundamentals)
  - [3. Pandas — DataFrames](#3-pandas--dataframes)
  - [4. Matplotlib — Data Visualization](#4-matplotlib--data-visualization)
  - [5. Scikit-Learn — Machine Learning](#5-scikit-learn--machine-learning)
- [Getting Started](#getting-started)
- [Tech Stack](#tech-stack)

---

## Overview

This repository is a **teaching-oriented** collection of Jupyter notebooks designed to walk learners through the Python data science ecosystem step-by-step. Each topic includes:

- Concept notebook — theory, explanations, and worked examples
- Exercise solutions notebook — hands-on problems with full solutions

---

## Prerequisites

| Skill | Level Required |
|---|---|
| Python basics (variables, loops, lists) | Required |
| Understanding of functions | Required |
| Linear algebra / statistics | Helpful but not required |
| Prior ML experience | Not required |

---

## Repository Structure

```text
python_for_ML_&_Data_Science/
│
├── revisiting_functions.ipynb       <- Functions & Modules (lesson)
├── functions_exercise_solve.ipynb   <- Functions exercises (solutions)
│
├── numPy.ipynb                      <- NumPy Fundamentals (lesson)
├── numpy_exercise_solve.ipynb       <- NumPy exercises (solutions)
│
├── pandas.ipynb                     <- Pandas DataFrames (lesson)
├── pandas_exercise_solve.ipynb      <- Pandas exercises (solutions)
│
├── matplotlib.ipynb                 <- Matplotlib Visualization (lesson)
├── matplotlib_exercise_solve.ipynb  <- Matplotlib exercises (solutions)
│
├── scikitlearn.ipynb                <- Scikit-Learn ML Models (lesson)
└── scikit_learn_solve.ipynb         <- Scikit-Learn exercises (solutions)
```


---


### 1. Functions & Modules

| Notebook | Description |
|---|---|
| 
evisiting_functions.ipynb | Why functions matter, defining and calling functions, arguments, return values, scope, and importing modules |
| unctions_exercise_solve.ipynb | Practice exercises with full solutions |

**Key topics covered:**
- Defining reusable functions
- Positional vs. keyword arguments
- Return values and scope
- Importing standard and third-party modules
- Building modular data pipelines

---

### 2. NumPy Fundamentals

| Notebook | Description |
|---|---|
| 
umPy.ipynb | The bedrock of the Python data science stack — arrays, vectorised operations, indexing, broadcasting, and ufuncs |
| 
umpy_exercise_solve.ipynb | Practice exercises with full solutions |

**Key topics covered:**
- Why NumPy (50-500x faster than Python lists)
- Creating arrays: np.array, np.arange, np.linspace, np.zeros/ones
- Array shapes, dtypes, and reshaping
- Indexing, slicing, and boolean masking
- Vectorised arithmetic and universal functions (ufuncs)
- Broadcasting rules
- Aggregations: sum, mean, std, min, max

---

### 3. Pandas — DataFrames

| Notebook | Description |
|---|---|
| pandas.ipynb | Your first real DataFrame — loading, exploring, filtering, grouping, and cleaning tabular data |
| pandas_exercise_solve.ipynb | Practice exercises using a sales-transactions dataset with full solutions |

**Key topics covered:**
- Series vs DataFrame mental model
- Reading data: pd.read_csv
- Exploring: .head(), .shape, .describe(), .info()
- Filtering rows and selecting columns
- groupby and aggregation
- Handling missing values
- Merging and joining DataFrames

---

### 4. Matplotlib — Data Visualization

| Notebook | Description |
|---|---|
| matplotlib.ipynb | Creating publication-quality plots — line charts, scatter plots, histograms, subplots, and styling |
| matplotlib_exercise_solve.ipynb | Practice exercises (multi-line plots, labelling, layout) with full solutions |

**Key topics covered:**
- fig, ax = plt.subplots() workflow
- Line plots, scatter plots, bar charts, histograms
- Titles, axis labels, legends, and grids
- Subplots and figure layout (plt.tight_layout())
- Customising colours, line styles, and markers
- Common pitfalls (overlapping labels, squished figures)

---

### 5. Scikit-Learn — Machine Learning

| Notebook | Description |
|---|---|
| scikitlearn.ipynb | End-to-end ML pipelines — classification (Iris) and regression (California Housing) using scikit-learn's unified API |
| scikit_learn_solve.ipynb | Practice exercises (KNN classifier, different models) with full solutions |

**Key topics covered:**

The ML workflow:

`
Load & Explore -> Prepare X, y -> Train/Test Split -> Fit -> Predict -> Evaluate
`

- Loading datasets: load_iris, fetch_california_housing
- Train/test split: train_test_split
- Preprocessing: StandardScaler
- Classifiers: Logistic Regression, k-Nearest Neighbours
- Regressors: Linear Regression
- Evaluation: accuracy, classification report, confusion matrix, RMSE
- Cross-validation: cross_val_score
- Hyperparameter tuning: GridSearchCV
- Pipelines: sklearn.pipeline.Pipeline
- Common ML pitfalls: data leakage, overfitting, train/test contamination

---

## Getting Started

### 1. Clone the repository
`
git clone https://github.com/Aman554-EQ/Teaching-Python-for-ML-and-Data-Science.git
cd Teaching-Python-for-ML-and-Data-Science
`

### 2. Create a virtual environment (recommended)
```
# Using conda (recommended for data science)
conda create -n ds-env python=3.11
conda activate ds-env

# Or using venv
python -m venv ds-env
ds-env\Scripts\activate      # Windows
source ds-env/bin/activate   # macOS / Linux
```

### 3. Install dependencies
```
pip install numpy pandas matplotlib scikit-learn jupyter
```

### 4. Launch Jupyter
```
jupyter notebook
```

Then open any notebook from the browser interface and follow the Learning Path order above.

---

## Tech Stack

| Library | Version (tested) | Purpose |
|---|---|---|
| Python | 3.10+ | Language |
| NumPy | 1.24+ | Numerical arrays & linear algebra |
| Pandas | 2.0+ | Tabular data manipulation |
| Matplotlib | 3.7+ | Data visualisation |
| Scikit-learn | 1.3+ | Machine learning models & pipelines |
| Jupyter | 7.0+ | Interactive notebook environment |

---

> Tip: If you are completely new to Python, work through revisiting_functions.ipynb first — the rest of the stack will make much more sense!
