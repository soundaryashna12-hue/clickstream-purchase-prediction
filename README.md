Clickstream Analytics for Predicting Online Purchase Behaviour

Python replication of the Weka analysis from the MSc Business Analytics dissertation
"The effectiveness of clickstream data analytics in predicting consumer online behaviour"

The pipeline compares classification and clustering techniques across two structurally
different e-commerce datasets.

What it does

For each dataset the script runs the same four stages:

1. Data preparation: loads the data and builds model-ready features.
2. Classification: Logistic Regression, Random Forest and Multilayer Perceptron, evaluated with
   10-fold stratified cross-validation (precision, recall, F1 and ROC-AUC for the purchase class),
   both as a baseline and with class balancing.
3. Clustering: K-means for k = 2 to 5, scored by classes-to-clusters error, then the k = 2 cluster
   label is added as a feature and the classifiers are re-evaluated.
4. Feature importance: mutual information (equivalent of Weka's InfoGain) with a Random Forest
   importance cross-check.

 Datasets

| Dataset | Source | Unit of analysis | Target |
|---|---|---|---|
| Online Shoppers Purchasing Intention | [UCI ML Repository](https://archive.ics.uci.edu/dataset/468/online+shoppers+purchasing+intention+dataset) (CC BY 4.0), Sakar & Kastro (2018) | Session (12,330 rows) | `Revenue` |
| eCommerce Events History in Cosmetics Shop | [Kaggle](https://www.kaggle.com/datasets/mkechinov/ecommerce-events-history-in-cosmetics-shop), Kechinov (2020) | Event log, rebuilt into sessions from a 12,000-event random sample | `Purchase` |

Data is **not** included in this repository. It is downloaded at run time.

Installation

```bash
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

Kaggle credentials

The cosmetics dataset needs a Kaggle account. Create an API token at
*Kaggle > Settings > API > Create New Token* and either save `kaggle.json` to
`~/.kaggle/` or set the `KAGGLE_USERNAME` and `KAGGLE_KEY` environment variables.
 Usage

```bash
python clickstream_analysis.py
```

Results for both datasets are printed to the console. To run a single dataset, or to reuse
the functions in a notebook:

```python
import clickstream_analysis as c

X, y = c.load_uci()
c.run_pipeline(X, y, "UCI Online Shoppers")
```

Differences from the Weka analysis

Results will be close to, but not identical to, the dissertation tables:

- Information gain: Weka discretises numeric attributes; scikit-learn estimates mutual information
  with a nearest-neighbour method. Scores differ in scale, and rankings should be similar.
- Class balancing: Weka's `ClassBalancer` re-weights instances. Here, Logistic Regression and
  Random Forest use `class_weight="balanced"`, and the MLP uses minority oversampling inside each
  training fold.
- Algorithms and seeds: model internals and random seeds differ between Weka and scikit-learn.
- Label leakage: purchase events are excluded from `TotalEvents` when building cosmetics sessions.

Limitations

- Both datasets pre-date the COVID-19 disruption to online retail.
- Sampling 12,000 events from about 20 million produces mostly very short, partially captured sessions.
- Purchases are a minority class in both datasets, so accuracy alone is misleading; use recall and F1.

Project structure

```
.
├── clickstream_analysis.py   # full pipeline
├── requirements.txt
└── README.md
```

Citation and acknowledgements

- Sakar, C. O. & Kastro, Y. (2018). *Online Shoppers Purchasing Intention Dataset*. UCI Machine Learning Repository.
- Kechinov, M. (2020). *eCommerce Events History in Cosmetics Shop*. Kaggle / REES46 Open CDP.

Licence

Code released under the MIT Licence (add a `LICENSE` file). The datasets keep their original licences.
