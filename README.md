# xFedAlign

This repository contains the experimental code for **xFedAlign**, a framework for aligning explanations in federated learning using adaptive surrogates, sparse explanation artifacts, and a Global Explanation Prior.

---

## 1. Repository Layout

Based on the implemented scripts, the repository is structured as follows:

```text
xFedAlign/
  vision-experiments/
    cifar-iid.py
    cifar-non-iid.py
    mnist-iid.py
    mnist-non-iid.py
  text-experiments/
    ag-news-iid.py
    ag-news-non-iid.py
    imdb-iid.py
    imdb-non-iid.py
  tabular-experiments/
    uci-adult-iid.py
    uci-adult-non-iid.py
    uci-credit-iid.py
    uci-credit-non-iid.py
  threat-models/
    attacks-iid.py
    attacks-non-iid.py
  README.md
  requirements.txt
```

Each script is a self-contained entry point. When executed, it automatically:
1. Sets random seeds for reproducibility.
2. Loads and partitions the dataset.
3. Runs a suite of 5 experiments (one per seed).
4. Compares **xFedAlign** against baselines:
   - **BL-A**: Plain FL with no XAI (FedAvg).
   - **BL-B**: Local Post-hoc Interpretability (Local-XAI).
   - **BL-C**: Server-side Explanation Summary (FedAttr-Agg).
   - **BL-D**: Interpretable-only FL (Fed-XAI).
5. Aggregates metrics (Accuracy, EDI, Deletion/Insertion AUC) and saves plots/CSVs.

---

## 2. Environment Setup

Create an isolated environment and install dependencies:

```bash
# Example with conda
conda create -n xfedalign python=3.10 -y
conda activate xfedalign

pip install -r requirements.txt
```

---

## 3. Datasets

- **MNIST**, **CIFAR-10**, and **IMDb** are downloaded automatically via libraries (`torchvision`, `keras.datasets`).
- **UCI Adult**: [https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.data](https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.data)
- **UCI Credit**: [https://archive.ics.uci.edu/static/public/144/statlog+german+credit+data.zip](https://archive.ics.uci.edu/static/public/144/statlog+german+credit+data.zip)
- **AG News**: [https://www.kaggle.com/datasets/amananandrai/ag-news-classification-dataset](https://www.kaggle.com/datasets/amananandrai/ag-news-classification-dataset)

---

## 4. Running Experiments

Unlike typical scripts that require many command-line flags, these scripts are pre-configured to run a full evaluation suite (5 seeds) to ensure statistical significance.

Simply run the python script corresponding to the experiment you want to perform (examples given below).

### 4.1 Vision (MNIST)

Located in `vision-experiments/`.

```bash
# Run MNIST IID experiments
python vision-experiments/mnist-iid.py
```

### 4.2 Text (IMDb)

Located in `text-experiments/`.

```bash
# Run IMDb IID experiments
python text-experiments/imdb-iid.py
```

### 4.3 Tabular (German Credit)

Located in `tabular-experiments/`.

```bash
# Run German Credit IID experiments
python tabular-experiments/uci-credit-iid.py
```

---

## 5. Threat Models and Attacks

The attack scripts evaluate the privacy and robustness of xFedAlign compared to baselines (on MNIST). They perform:
1. **Membership Inference Attacks (MIA)**: Using Gaussian Naive Bayes on logit scores.
2. **Attribution Poisoning**: Simulating malicious clients injecting trigger patterns into explanations.

```bash
# Run attacks on IID MNIST setup
python attacks-iid.py

# Run attacks on Non-IID MNIST setup
python attacks-non-iid.py
```

**Outputs:**
- `mia_results.csv`: Accuracy and Advantage of MIA (lower is better privacy).
- `atk_summary.csv`: Robustness against poisoning (lower drop in overlap is better).
- Plots saved to `outputs_iid/` or `outputs_non_iid/`.

---

## 6. Configuration

Configuration is handled via `dataclass` objects at the top of each script (e.g., `FLConfig`, `XFLConfig`). To change hyperparameters like batch size, learning rate, or privacy noise (`dp_sigma`), edit the class definitions directly in the script.

Example from `mnist-iid.py`:

```python
@dataclass
class FLConfig:
    n_clients: int = 8
    rounds: int = 15
    ...

@dataclass
class XFLConfig:
    topk: int = 128
    dp_sigma: float = 0.1
    ...
```

---

## 7. Outputs

After running a script, check the generated output directory (e.g., `outputs/`, `imdb_outputs/`). You will find:

- **PNG Plots**: Bar charts for AUC/EDI, line charts for attack success.
- **CSV Files**: Raw metric data for tables.
- **Console Logs**: Summary of mean ± std over 5 seeds.

---

## 8. License
This project is licensed under the MIT License.

---
