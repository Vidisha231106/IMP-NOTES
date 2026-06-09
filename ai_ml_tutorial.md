# 🧠 The Complete AI & Machine Learning Mastery Guide
### From Intermediate to Expert — Every Concept, Model, Pipeline & Trend (2026 Edition)

---

> **How to use this guide:** This document is structured from fundamentals → advanced. Each section builds on the last. Use the Table of Contents to jump around. Code snippets are in Python unless noted.

---

## 📚 Table of Contents

1. [Foundations of Machine Learning](#1-foundations-of-machine-learning)
2. [Statistical Learning Theory](#2-statistical-learning-theory)
3. [Core ML Algorithms (Classical)](#3-core-ml-algorithms-classical)
4. [Feature Engineering & Data Preprocessing](#4-feature-engineering--data-preprocessing)
5. [Deep Learning Fundamentals](#5-deep-learning-fundamentals)
6. [Convolutional Neural Networks (CNNs)](#6-convolutional-neural-networks-cnns)
7. [Recurrent Networks: RNNs, LSTMs, GRUs](#7-recurrent-networks-rnns-lstms-grus)
8. [The Transformer Architecture](#8-the-transformer-architecture)
9. [Natural Language Processing (NLP)](#9-natural-language-processing-nlp)
10. [Computer Vision](#10-computer-vision)
11. [Generative Models](#11-generative-models)
12. [Reinforcement Learning](#12-reinforcement-learning)
13. [Large Language Models (LLMs)](#13-large-language-models-llms)
14. [Prompt Engineering](#14-prompt-engineering)
15. [RAG Pipelines — All Types](#15-rag-pipelines--all-types)
16. [AI Agents & Agentic Systems](#16-ai-agents--agentic-systems)
17. [Vector Databases & Embeddings](#17-vector-databases--embeddings)
18. [MLOps & Production ML](#18-mlops--production-ml)
19. [Evaluation Metrics — Complete Reference](#19-evaluation-metrics--complete-reference)
20. [AI Safety, Alignment & Ethics](#20-ai-safety-alignment--ethics)
21. [Latest AI Models & News (2025–2026)](#21-latest-ai-models--news-20252026)
22. [Hands-On Cheat Sheets](#22-hands-on-cheat-sheets)

---

## 1. Foundations of Machine Learning

### 1.1 What is Machine Learning?

Machine Learning (ML) is a subfield of Artificial Intelligence where systems learn patterns from data rather than being explicitly programmed with rules. Formally:

> **ML Definition:** A computer program is said to learn from experience **E** with respect to task **T** and performance measure **P**, if its performance on T, as measured by P, improves with experience E. — Tom Mitchell (1997)

### 1.2 The Three Paradigms of Learning

#### Supervised Learning
The model learns a mapping from inputs **X** to outputs **Y**, given labeled training pairs `{(x₁,y₁), ..., (xₙ,yₙ)}`.

- **Goal:** Minimize prediction error on unseen data
- **Key assumption:** Training and test data are i.i.d. (independent and identically distributed)
- **Examples:** Classification, Regression, Sequence labeling

```
Input X ──► [Model f(x)] ──► Prediction ŷ
                ↑
         Training: minimize Loss(ŷ, y)
```

#### Unsupervised Learning
No labels. The model discovers structure, patterns, or compressed representations in raw data.

- **Goal:** Find hidden structure — clusters, manifolds, generative factors
- **Examples:** Clustering (K-Means), Dimensionality Reduction (PCA, t-SNE), Generative Modeling (VAEs, GANs)

#### Reinforcement Learning (RL)
An **agent** interacts with an **environment**, takes **actions**, receives **rewards**, and learns a **policy** to maximize cumulative reward.

- **Key components:** State, Action, Reward, Policy, Value Function
- **Examples:** Game playing (AlphaGo), robotics, RLHF for LLMs

#### Semi-Supervised & Self-Supervised Learning
- **Semi-supervised:** Few labeled + many unlabeled samples
- **Self-supervised:** Labels generated automatically from raw data (e.g., masked language modeling, contrastive learning — the backbone of GPT, BERT, CLIP)

---

### 1.3 The ML Workflow (End-to-End)

```
1. Problem Definition
       ↓
2. Data Collection & Labeling
       ↓
3. Exploratory Data Analysis (EDA)
       ↓
4. Feature Engineering & Preprocessing
       ↓
5. Model Selection & Training
       ↓
6. Evaluation & Validation
       ↓
7. Hyperparameter Tuning
       ↓
8. Deployment & Monitoring
       ↓
9. Feedback Loop & Retraining
```

---

### 1.4 Inductive Bias

Every learning algorithm makes assumptions about the form of the target function. This is called **inductive bias** — it's what allows generalization beyond training data.

| Algorithm | Inductive Bias |
|---|---|
| Linear Regression | Target is a linear function |
| Decision Trees | Shorter trees are preferred (Occam's Razor) |
| K-Nearest Neighbors | Similar inputs have similar outputs |
| Neural Networks | Hierarchical compositional structure |
| SVMs | Maximum-margin separating hyperplane |

---

## 2. Statistical Learning Theory

### 2.1 Bias-Variance Tradeoff

The generalization error of a model decomposes as:

```
Expected MSE = Bias² + Variance + Irreducible Noise
```

- **Bias:** Error from wrong assumptions in the learning algorithm. High bias → underfitting
- **Variance:** Error from sensitivity to small fluctuations in training data. High variance → overfitting
- **Irreducible Noise:** Inherent noise in the data — cannot be reduced

```
           High Bias              Low Bias
           (Underfitting)        (May overfit)

Complexity: |—————————————————————————→
             Simple               Complex
             
Test Error:  \_______/ (U-shaped curve)
              Sweet spot = optimal complexity
```

**Practical implication:**
- More data → reduces variance (helps complex models)
- Regularization → increases bias, decreases variance
- Ensemble methods → reduce variance (bagging) or bias (boosting)

---

### 2.2 The Curse of Dimensionality

As the number of features **d** grows:
1. Volume of the space grows exponentially — data becomes sparse
2. Distance metrics lose meaning (all points equidistant)
3. More data needed to cover the space: O(n^d)

**Consequences:**
- KNN fails in high dimensions
- Density estimation becomes intractable
- Manifold hypothesis: Real data lies on a low-dimensional manifold

**Solutions:**
- Dimensionality reduction (PCA, UMAP, autoencoders)
- Feature selection
- Deep learning (learns the manifold implicitly)

---

### 2.3 PAC Learning Theory (Probably Approximately Correct)

A concept class **C** is **PAC-learnable** if there exists an algorithm that, for any distribution D and any ε, δ > 0, produces a hypothesis h such that:

```
P[error(h) ≤ ε] ≥ 1 - δ
```
with sample complexity polynomial in 1/ε, 1/δ, and the size of the concept.

**VC Dimension:** The largest set of points that can be shattered (classified correctly in all possible ways) by a hypothesis class.

- VC dim of linear classifiers in d dimensions = d+1
- Higher VC dim → more expressive → more data needed to generalize

---

### 2.4 Cross-Validation

Never evaluate on training data. Use held-out sets.

#### k-Fold Cross-Validation
```
Data: [Fold 1 | Fold 2 | Fold 3 | Fold 4 | Fold 5]

Round 1: Train on [2,3,4,5], Test on [1]
Round 2: Train on [1,3,4,5], Test on [2]
...
Round 5: Train on [1,2,3,4], Test on [5]

Final score = mean of all 5 test scores
```

#### Stratified k-Fold
Preserves class distribution in each fold. **Always use for imbalanced datasets.**

#### Leave-One-Out (LOOCV)
k = n. Every sample is a test set once. High variance, computationally expensive.

#### Time Series Split
For temporal data — never shuffle! Future cannot leak into past.

---

### 2.5 Regularization

Regularization reduces overfitting by adding a penalty to model complexity.

#### L1 Regularization (Lasso)
```
Loss_regularized = Loss + λ Σ|wᵢ|
```
- Induces **sparsity** — drives weights to exactly zero
- Useful for feature selection
- Non-differentiable at 0 → subgradient or coordinate descent

#### L2 Regularization (Ridge)
```
Loss_regularized = Loss + λ Σwᵢ²
```
- Shrinks weights toward zero but rarely to exactly zero
- Closed-form solution exists
- Equivalent to a Gaussian prior on weights (MAP estimation)

#### Elastic Net
```
Loss_regularized = Loss + λ₁Σ|wᵢ| + λ₂Σwᵢ²
```
Combines L1 and L2. Best for correlated features.

#### Dropout (Neural Networks)
Randomly set activations to zero during training (probability p). At test time, scale by (1-p).

```python
# PyTorch
import torch.nn as nn
self.dropout = nn.Dropout(p=0.5)
x = self.dropout(x)  # training only
```
Interpretation: Averaging over an exponential ensemble of thinned networks.

#### Batch Normalization
Normalize activations within a mini-batch. Acts as regularizer AND accelerates training.

```
x̂ = (x - μ_batch) / sqrt(σ²_batch + ε)
y = γ * x̂ + β   # learnable scale and shift
```

---

### 2.6 Information Theory Foundations

#### Entropy
Measures uncertainty (information content) of a random variable:
```
H(X) = -Σ p(x) log₂ p(x)    [bits]
```
- H(X) = 0 → perfectly predictable
- H(X) = log₂(n) → maximum uncertainty for n outcomes

#### Cross-Entropy (Most Important ML Loss!)
```
H(p, q) = -Σ p(x) log q(x)
```
The loss function for classification. Measures how well predicted distribution q matches true distribution p.

#### KL Divergence
```
KL(p || q) = Σ p(x) log(p(x)/q(x))
```
Asymmetric "distance" between distributions. KL(p||q) ≠ KL(q||p).
Used in VAEs, information bottleneck, RLHF.

#### Mutual Information
```
I(X;Y) = H(X) - H(X|Y) = H(Y) - H(Y|X)
```
Measures how much knowing Y tells you about X. Key in feature selection and contrastive learning.

---

## 3. Core ML Algorithms (Classical)

### 3.1 Linear & Logistic Regression

#### Linear Regression

Assumes: `y = wᵀx + b + ε` where ε ~ N(0, σ²)

**Ordinary Least Squares (OLS) solution:**
```
w* = (XᵀX)⁻¹ Xᵀy
```
(closed-form, O(d³) for d features)

**Assumptions (GAUSS-MARKOV):**
1. Linearity: E[y|x] = wᵀx
2. No multicollinearity (XᵀX invertible)
3. Homoscedasticity: constant variance of ε
4. No autocorrelation in residuals

#### Logistic Regression (Classification)

Uses sigmoid to squash linear output to probability:
```
P(y=1|x) = σ(wᵀx) = 1 / (1 + e^(-wᵀx))
```

**Loss:** Binary Cross-Entropy:
```
L = -[y log(ŷ) + (1-y) log(1-ŷ)]
```

**Multi-class:** Softmax regression:
```
P(y=k|x) = exp(wₖᵀx) / Σⱼ exp(wⱼᵀx)
```

---

### 3.2 Decision Trees

A tree that partitions feature space using axis-aligned splits.

**Splitting criteria:**
- **Gini Impurity:** `Gini = 1 - Σ pₖ²` (CART)
- **Information Gain:** `IG = H(parent) - Σ wᵢ H(childᵢ)` (ID3, C4.5)

**Tree construction (greedy):**
```
At each node:
  For each feature f, for each threshold t:
    Compute impurity reduction for split (f, t)
  Choose best (f*, t*)
  Recurse on left and right children
  Stop when: max_depth, min_samples, or pure node
```

**Hyperparameters:**
- `max_depth`: Controls overfitting
- `min_samples_split / min_samples_leaf`: Minimum data in nodes
- `max_features`: Random subspace (used in Random Forests)

**Pros:** Interpretable, handles mixed types, no scaling needed
**Cons:** High variance, unstable, poor extrapolation

---

### 3.3 Ensemble Methods

#### Bagging (Bootstrap Aggregating)

Train B models on bootstrapped datasets (sampling with replacement). Average predictions (regression) or majority vote (classification).

**Effect:** Reduces variance, roughly constant bias.

```
Bootstrap samples: D₁, D₂, ..., Dᴮ (each is 63.2% unique samples)
Models:           f₁, f₂, ..., fᴮ
Prediction:       ŷ = (1/B) Σ fᵢ(x)
```

#### Random Forest

Bagging of decision trees + **random feature subsets** at each split.

Key insight: Decorrelating the trees improves ensemble performance.

```python
from sklearn.ensemble import RandomForestClassifier
rf = RandomForestClassifier(
    n_estimators=100,
    max_features='sqrt',    # sqrt(d) features per split
    max_depth=None,         # grow full trees
    min_samples_leaf=1,
    oob_score=True          # out-of-bag error estimate (free cross-val!)
)
```

**Out-of-bag (OOB) error:** Each tree is tested on ~36.8% of samples not in its bootstrap. No separate validation set needed.

#### Boosting

Train models sequentially, each focusing on errors of the previous.

**AdaBoost:**
```
1. Initialize weights wᵢ = 1/n
2. For t = 1 to T:
   a. Train weak learner hₜ on weighted samples
   b. Compute weighted error: εₜ = Σ wᵢ I(hₜ(xᵢ) ≠ yᵢ) / Σ wᵢ
   c. Compute learner weight: αₜ = 0.5 ln((1-εₜ)/εₜ)
   d. Update sample weights: wᵢ ← wᵢ exp(-αₜ yᵢ hₜ(xᵢ))
3. Final: H(x) = sign(Σ αₜ hₜ(x))
```

**Gradient Boosting (GBM):**
Fits each new tree to the negative gradient (residuals) of the loss function.

```
F₀(x) = initial prediction (e.g., mean)
For m = 1 to M:
  rᵢ = -∂L(yᵢ, F_{m-1}(xᵢ)) / ∂F_{m-1}(xᵢ)   [pseudo-residuals]
  Fit tree hₘ to residuals rᵢ
  Fₘ(x) = F_{m-1}(x) + η * hₘ(x)               [η = learning rate]
```

#### XGBoost (eXtreme Gradient Boosting)

The most widely used tabular ML algorithm. Key innovations:
1. **Regularized objective:** L(θ) + Ω(f) where Ω = γT + (λ/2)Σwⱼ²
2. **Second-order Taylor expansion** of loss
3. **Sparsity-aware split** (handles missing values natively)
4. **Column subsampling** (like Random Forest)
5. **Tree pruning** by depth-first then prune

```python
import xgboost as xgb
model = xgb.XGBClassifier(
    n_estimators=500,
    learning_rate=0.05,
    max_depth=6,
    subsample=0.8,
    colsample_bytree=0.8,
    reg_alpha=0.1,      # L1
    reg_lambda=1.0,     # L2
    use_label_encoder=False,
    eval_metric='logloss'
)
```

#### LightGBM

Faster than XGBoost via:
- **Histogram-based** splitting (buckets continuous features)
- **Gradient-based One-Side Sampling (GOSS):** Keep large-gradient samples
- **Exclusive Feature Bundling (EFB):** Bundle sparse features

**Grows leaf-wise** (best leaf first) vs XGBoost's level-wise → faster convergence.

---

### 3.4 Support Vector Machines (SVMs)

Find the maximum-margin hyperplane separating classes.

**Hard-margin SVM (linearly separable):**
```
Minimize: (1/2)||w||²
Subject to: yᵢ(wᵀxᵢ + b) ≥ 1 for all i
```

**Soft-margin SVM (with slack variables ξᵢ):**
```
Minimize: (1/2)||w||² + C Σ ξᵢ
Subject to: yᵢ(wᵀxᵢ + b) ≥ 1 - ξᵢ, ξᵢ ≥ 0
```

**The Kernel Trick:**
Replace xᵢᵀxⱼ with K(xᵢ, xⱼ) = φ(xᵢ)ᵀφ(xⱼ) — compute inner products in high-dimensional space without explicit mapping!

| Kernel | Formula | Use Case |
|---|---|---|
| Linear | xᵀz | Text classification |
| RBF (Gaussian) | exp(-γ||x-z||²) | Most common, general purpose |
| Polynomial | (γxᵀz + r)^d | Image classification |
| Sigmoid | tanh(γxᵀz + r) | Neural net analog |

**Key insight:** Only **support vectors** (points on the margin boundary) define the decision boundary. Very efficient for small datasets.

---

### 3.5 K-Nearest Neighbors (KNN)

Non-parametric, lazy learning. Prediction = majority vote (classification) or mean (regression) of k nearest training points.

**Distance metrics:**
- Euclidean: √Σ(xᵢ - zᵢ)²
- Manhattan: Σ|xᵢ - zᵢ|
- Cosine: 1 - (xᵀz)/(||x|| ||z||) — for text/embeddings

**Choosing k:**
- Small k → high variance, complex boundary
- Large k → high bias, smooth boundary
- Use cross-validation; odd k for binary classification

**Scaling is mandatory** — KNN is distance-based.

---

### 3.6 Naive Bayes

Applies Bayes' theorem with strong independence assumption:
```
P(y|x) ∝ P(y) Π P(xᵢ|y)   [assumes features are conditionally independent]
```

**Variants:**
- **Gaussian NB:** P(xᵢ|y) ~ N(μ_ky, σ_ky²)
- **Multinomial NB:** For discrete counts (text classification with TF)
- **Bernoulli NB:** Binary features (document-word occurrence)
- **Complement NB:** Better for imbalanced text classification

Despite the "naive" assumption, works surprisingly well for text. Very fast to train and predict.

---

### 3.7 Clustering Algorithms

#### K-Means
```
1. Initialize k centroids randomly
2. E-step: Assign each point to nearest centroid
3. M-step: Recompute centroids = mean of assigned points
4. Repeat until convergence
```

**Objective:** Minimize within-cluster sum of squares (WCSS):
```
WCSS = Σₖ Σ_{x ∈ Cₖ} ||x - μₖ||²
```

**K-Means++ initialization:** Choose centroids proportional to squared distance from existing centroids. Improves convergence.

**Determining k:** Elbow method (plot WCSS vs k), Silhouette score.

#### DBSCAN (Density-Based Spatial Clustering)

Finds clusters as dense regions separated by low-density areas.

```
Parameters: ε (radius), MinPts (minimum neighbors)

For each point x:
  If |N_ε(x)| ≥ MinPts: x is a CORE POINT
  If |N_ε(x)| < MinPts but x ∈ N_ε(core): x is a BORDER POINT
  Otherwise: x is NOISE (outlier)
```

**Advantages:** Discovers arbitrary shapes, handles outliers, no k needed.

#### Hierarchical Clustering (Agglomerative)
```
1. Start: n clusters (each point is a cluster)
2. Merge the two most similar clusters
3. Repeat until 1 cluster
4. Visualize as dendrogram — cut at desired level
```

**Linkage criteria:**
- Single: min pairwise distance (chaining)
- Complete: max pairwise distance (compact clusters)
- Average: mean pairwise distance
- Ward: minimize total within-cluster variance (most used)

---

### 3.8 Dimensionality Reduction

#### Principal Component Analysis (PCA)

Finds orthogonal directions of maximum variance.

```
1. Center data: X_centered = X - μ
2. Compute covariance: Σ = (1/n) X_centered^T X_centered
3. Eigendecompose: Σ = V D V^T
4. Project: X_reduced = X_centered @ V[:, :k]
```

**Key properties:**
- Principal components are eigenvectors of covariance matrix
- Eigenvalues = variance explained
- PCA is the optimal linear encoder (MSE sense)
- Whitening: divide by √eigenvalue → unit variance in each PC

#### t-SNE (t-Distributed Stochastic Neighbor Embedding)

Visualizes high-dimensional data in 2D/3D by preserving local structure.

```
1. Compute pairwise Gaussian similarities in high-d: pᵢⱼ
2. Initialize 2D embeddings
3. Compute pairwise t-distribution similarities in 2D: qᵢⱼ
4. Minimize KL(P||Q) via gradient descent
```

**Key parameters:**
- **Perplexity** (5-50): Effective number of neighbors. Higher → more global structure
- Not convex — different runs give different results
- Not deterministic, cannot extrapolate to new points (use UMAP instead)

#### UMAP (Uniform Manifold Approximation and Projection)

Better than t-SNE for preserving global structure, faster, and can generalize.

Uses Riemannian geometry and fuzzy simplicial sets. Generally preferred over t-SNE for large datasets.

---

## 4. Feature Engineering & Data Preprocessing

### 4.1 Data Cleaning

#### Handling Missing Values

```python
import pandas as pd
from sklearn.impute import SimpleImputer, KNNImputer

# Statistical imputation
imputer = SimpleImputer(strategy='median')   # or 'mean', 'most_frequent', 'constant'
X_imputed = imputer.fit_transform(X)

# KNN imputation (uses similar rows)
knn_imputer = KNNImputer(n_neighbors=5)

# Multiple imputation (gold standard)
from sklearn.experimental import enable_iterative_imputer
from sklearn.impute import IterativeImputer
iter_imputer = IterativeImputer(max_iter=10, random_state=42)
```

**When to remove vs impute:**
- >40% missing in a column → consider dropping
- MCAR (Missing Completely At Random) → mean/median imputation OK
- MAR (Missing At Random) → model-based imputation
- MNAR (Missing Not At Random) → investigate; flag as separate feature

#### Outlier Detection

```python
# IQR method
Q1, Q3 = df.quantile([0.25, 0.75])
IQR = Q3 - Q1
mask = ~((df < Q1 - 1.5*IQR) | (df > Q3 + 1.5*IQR))

# Z-score
from scipy import stats
z_scores = np.abs(stats.zscore(df))
mask = (z_scores < 3).all(axis=1)

# Isolation Forest
from sklearn.ensemble import IsolationForest
iso = IsolationForest(contamination=0.05, random_state=42)
outlier_labels = iso.fit_predict(X)  # -1 = outlier
```

---

### 4.2 Feature Scaling

**Why:** Distance-based algorithms (KNN, SVM, KMeans) and gradient descent are sensitive to scale.

| Scaler | Formula | Use When |
|---|---|---|
| StandardScaler | (x - μ) / σ | Normally distributed, SVM, PCA |
| MinMaxScaler | (x - min) / (max - min) | Need bounded [0,1], image pixels |
| RobustScaler | (x - median) / IQR | Outliers present |
| MaxAbsScaler | x / max(|x|) | Sparse data |
| Normalizer | x / ||x|| | Text/NLP, cosine similarity |

```python
from sklearn.preprocessing import StandardScaler, RobustScaler
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)   # NEVER fit_transform on test!
```

---

### 4.3 Encoding Categorical Variables

#### One-Hot Encoding
```python
pd.get_dummies(df['color'], drop_first=True)   # drop_first avoids multicollinearity
```
Creates d binary columns for d categories. Use for low-cardinality (<15) nominal features.

#### Target/Mean Encoding
Replace category with mean of target variable.
```python
means = df.groupby('city')['price'].mean()
df['city_encoded'] = df['city'].map(means)
```
⚠️ Risk of target leakage — use with cross-val mean encoding.

#### Label Encoding
Assigns integers 0, 1, 2... — **only use for ordinal features** (size: small < medium < large).

#### Embeddings for High Cardinality
Use learned embeddings (entity embeddings) for features with thousands of categories (zip codes, user IDs). Works in neural networks.

---

### 4.4 Feature Creation & Selection

#### Interaction Features
```python
from sklearn.preprocessing import PolynomialFeatures
poly = PolynomialFeatures(degree=2, interaction_only=False)
X_poly = poly.fit_transform(X)   # adds x₁², x₂², x₁x₂, etc.
```

#### Feature Selection Methods

**Filter methods** (independent of model):
```python
from sklearn.feature_selection import SelectKBest, f_classif, mutual_info_classif
selector = SelectKBest(mutual_info_classif, k=20)
X_selected = selector.fit_transform(X, y)
```

**Wrapper methods** (use model performance):
- Recursive Feature Elimination (RFE)
- Forward/Backward selection

**Embedded methods** (built into model):
- Lasso (L1 regularization) → zero out irrelevant features
- Random Forest feature importance
- Gradient Boosting feature importance

```python
from sklearn.inspection import permutation_importance
result = permutation_importance(model, X_val, y_val, n_repeats=10)
# Most reliable: permutation importance on validation set
```

---

### 4.5 Handling Imbalanced Datasets

#### Resampling Techniques

```python
from imblearn.over_sampling import SMOTE, ADASYN
from imblearn.under_sampling import RandomUnderSampler
from imblearn.combine import SMOTETomek

# SMOTE: Synthetic Minority Oversampling
# Creates synthetic samples by interpolating between minority class neighbors
smote = SMOTE(k_neighbors=5, random_state=42)
X_resampled, y_resampled = smote.fit_resample(X_train, y_train)

# Combined approach (best practice)
smotetomek = SMOTETomek(random_state=42)
X_res, y_res = smotetomek.fit_resample(X_train, y_train)
```

**Algorithm-level solutions:**
- `class_weight='balanced'` in sklearn models
- `scale_pos_weight` in XGBoost
- Focal Loss in deep learning

**Evaluation metrics for imbalance:** Always use Precision, Recall, F1, PR-AUC, ROC-AUC — never raw accuracy.

---

## 5. Deep Learning Fundamentals

### 5.1 The Artificial Neuron

A single neuron computes:
```
z = Σ wᵢxᵢ + b   [linear combination]
a = φ(z)          [activation function]
```

Biologically inspired by the McCulloch-Pitts neuron (1943), formalized into the Perceptron by Rosenblatt (1958).

### 5.2 Activation Functions

| Function | Formula | Derivative | Use Case | Problem |
|---|---|---|---|---|
| Sigmoid | 1/(1+e^-x) | σ(1-σ) | Binary output, old | Vanishing gradient |
| Tanh | (e^x-e^-x)/(e^x+e^-x) | 1-tanh² | Hidden layers (older) | Vanishing gradient |
| ReLU | max(0, x) | 1 if x>0, else 0 | Default hidden | Dying ReLU |
| Leaky ReLU | max(αx, x), α=0.01 | 1 if x>0, else α | Fixes dying ReLU | — |
| ELU | x if x>0, α(e^x-1) else | Smooth | Better than ReLU | Slower |
| GELU | x·Φ(x) | Complex | Transformers, BERT | Computationally heavy |
| Swish | x·σ(x) | Complex | Modern deep nets | — |
| SiLU | x·σ(x) | Same as Swish | LLaMA, Mistral | — |
| Softmax | e^xᵢ/Σe^xⱼ | Complex | Multi-class output | Numerical instability |
| Mish | x·tanh(softplus(x)) | Complex | YOLOv4 | — |

**Why GELU dominates in modern LLMs:**
- Smooth approximation to ReLU
- Stochastic interpretation: GELU(x) = x · P(X ≤ x) where X ~ N(0,1)
- Better gradient flow than ReLU in very deep networks

---

### 5.3 Backpropagation

The algorithm that makes deep learning possible. Computes gradients of the loss w.r.t. all parameters using the **chain rule** of calculus.

**Forward pass:**
```
x → [Layer 1] → a₁ → [Layer 2] → a₂ → ... → [Output] → Loss L
```

**Backward pass (chain rule):**
```
∂L/∂w₁ = ∂L/∂a₂ · ∂a₂/∂a₁ · ∂a₁/∂z₁ · ∂z₁/∂w₁
```

**Computational graph view:**
Every operation (add, multiply, matrix multiply, ReLU) has:
- A forward function
- A backward function (computes local gradient)

Gradients accumulate via the chain rule as they flow backward.

```python
# PyTorch autograd (automatic differentiation)
import torch
x = torch.tensor([2.0], requires_grad=True)
y = x ** 3 + 2*x
y.backward()
print(x.grad)   # dy/dx = 3x² + 2 = 14.0 at x=2
```

**Gradient flow problem:**
- Sigmoid/Tanh squash gradients → vanishing gradients in deep nets
- ReLU can "die" if gradients become exactly 0

**Solutions:**
- Skip connections (ResNets)
- Layer/Batch Normalization
- Better initialization (He, Xavier/Glorot)
- Gradient clipping (for RNNs)

---

### 5.4 Weight Initialization

Poor initialization → exploding/vanishing gradients before training even begins.

**Xavier/Glorot initialization** (for Tanh/Sigmoid):
```
w ~ U[-1/√n, 1/√n]   or   w ~ N(0, 1/n)
where n = number of input units
```

**He initialization** (for ReLU):
```
w ~ N(0, 2/n)
```
Factor of 2 compensates for ReLU zeroing half the outputs.

```python
import torch.nn as nn
nn.init.kaiming_normal_(layer.weight, mode='fan_in', nonlinearity='relu')
nn.init.xavier_normal_(layer.weight)
```

---

### 5.5 Optimization Algorithms

#### Stochastic Gradient Descent (SGD)
```
w ← w - η · ∇L(w; xᵢ, yᵢ)   [one sample at a time]
```

**Mini-batch SGD** (standard practice):
```
w ← w - η · (1/B) Σᵢ∈B ∇L(w; xᵢ, yᵢ)
```
Typical batch size: 32-512. Larger batches → smoother gradients but may converge to sharp minima.

#### Momentum
Accumulates gradient direction, dampens oscillations:
```
v ← β·v - η·∇L
w ← w + v
```
β = 0.9 typical. Accelerates along consistent gradient directions.

#### Adam (Adaptive Moment Estimation)

The most widely used optimizer for deep learning:
```
m ← β₁·m + (1-β₁)·∇L         [first moment: mean of gradients]
v ← β₂·v + (1-β₂)·∇L²        [second moment: variance of gradients]
m̂ = m / (1-β₁^t)              [bias correction]
v̂ = v / (1-β₂^t)
w ← w - η · m̂ / (√v̂ + ε)
```

Default: β₁=0.9, β₂=0.999, ε=1e-8, η=1e-3

**Why Adam works:** Adaptive learning rate per parameter — large updates for rarely-seen features, small updates for common features.

#### AdamW (Adam + Weight Decay)

Decouples weight decay from gradient update (fixes L2 regularization in Adam):
```
w ← w - η · m̂/(√v̂ + ε) - η·λ·w
```
**Standard choice for LLMs and modern neural networks.**

#### Learning Rate Schedulers

```python
# Cosine annealing (most popular for LLMs)
scheduler = torch.optim.lr_scheduler.CosineAnnealingLR(optimizer, T_max=100)

# Warmup + cosine (transformer standard)
from transformers import get_cosine_schedule_with_warmup
scheduler = get_cosine_schedule_with_warmup(
    optimizer, 
    num_warmup_steps=1000, 
    num_training_steps=100000
)

# One-cycle (super-convergence)
scheduler = torch.optim.lr_scheduler.OneCycleLR(optimizer, max_lr=0.01, total_steps=1000)
```

---

### 5.6 Normalization Techniques

| Technique | Normalizes Over | Best For |
|---|---|---|
| Batch Norm | Batch dimension | CNNs, large batches |
| Layer Norm | Feature dimension | Transformers, RNNs |
| Instance Norm | Spatial dimension per sample | Style transfer |
| Group Norm | Groups of channels | Small batches, detection |
| RMS Norm | Feature dimension (no mean) | LLaMA, modern LLMs |

**RMSNorm** (used in LLaMA, Mistral):
```
RMSNorm(x) = x / RMS(x) * γ
where RMS(x) = √(1/d Σ xᵢ²)
```
Faster than LayerNorm — removes mean-centering operation, empirically equivalent.

---

### 5.7 Training a Neural Network End-to-End (PyTorch)

```python
import torch
import torch.nn as nn
import torch.optim as optim
from torch.utils.data import DataLoader, TensorDataset

# 1. Define model
class MLP(nn.Module):
    def __init__(self, input_dim, hidden_dim, output_dim):
        super().__init__()
        self.net = nn.Sequential(
            nn.Linear(input_dim, hidden_dim),
            nn.BatchNorm1d(hidden_dim),
            nn.GELU(),
            nn.Dropout(0.3),
            nn.Linear(hidden_dim, hidden_dim // 2),
            nn.GELU(),
            nn.Linear(hidden_dim // 2, output_dim)
        )
    
    def forward(self, x):
        return self.net(x)

# 2. Setup
model = MLP(128, 256, 10).to('cuda')
optimizer = optim.AdamW(model.parameters(), lr=1e-3, weight_decay=0.01)
criterion = nn.CrossEntropyLoss()
scheduler = optim.lr_scheduler.CosineAnnealingLR(optimizer, T_max=50)

# 3. Training loop
for epoch in range(50):
    model.train()
    for X_batch, y_batch in train_loader:
        X_batch, y_batch = X_batch.cuda(), y_batch.cuda()
        optimizer.zero_grad()
        logits = model(X_batch)
        loss = criterion(logits, y_batch)
        loss.backward()
        torch.nn.utils.clip_grad_norm_(model.parameters(), 1.0)  # gradient clipping
        optimizer.step()
    scheduler.step()
    
    # 4. Validation
    model.eval()
    with torch.no_grad():
        val_loss = sum(criterion(model(xb.cuda()), yb.cuda()) for xb, yb in val_loader)
    print(f"Epoch {epoch}: Val Loss = {val_loss:.4f}")
```

---

## 6. Convolutional Neural Networks (CNNs)

### 6.1 The Convolution Operation

A convolution applies a **kernel** (filter) across an input, computing dot products at every position:

```
(f * g)[i,j] = Σₘ Σₙ f[m,n] · g[i-m, j-n]
```

**3 Key properties that make CNNs work:**
1. **Translation equivariance:** Same features detected regardless of position
2. **Parameter sharing:** One filter reused across all positions (vs fully-connected)
3. **Local connectivity:** Each neuron sees only a local receptive field

```
Input: 32×32×3
Kernel: 3×3×3×64 → 64 filters
Output feature map: 30×30×64 (with no padding)
Output size formula: (H - Fh + 2P) / S + 1
```

**Hyperparameters:**
- **Kernel size:** 3×3 (most common), 5×5, 7×7
- **Stride:** How much kernel moves per step (default 1)
- **Padding:** "same" (output = input size) or "valid" (no padding)
- **Depth:** Number of filters = number of output channels

---

### 6.2 CNN Architectural Blocks

**Pooling layers:**
- Max pooling: Takes maximum in each window — preserves dominant features
- Average pooling: Averages each window — smoother
- Global Average Pooling (GAP): Averages entire feature map → vector

**Batch Normalization → ReLU → Convolution** (modern ordering)

**Depthwise Separable Convolution** (MobileNet):
```
Standard conv: H×W×Cin × Kh×Kw×Cin × Cout  ← expensive
Depthwise separable: 
  Step 1 (Depthwise): H×W×Cin filters of Kh×Kw×1
  Step 2 (Pointwise): H×W×Cin → H×W×Cout with 1×1 convs
  
Cost reduction: ~8-9× fewer operations
```

---

### 6.3 Landmark CNN Architectures

#### AlexNet (2012) — The CNN Revolution
- First to win ImageNet decisively (15.3% top-5 error vs 26% prior)
- Key innovations: ReLU, Dropout, data augmentation, GPU training

#### VGG (2014)
- Simple: only 3×3 convolutions, max pooling, increasing depth
- VGG-16, VGG-19 — still widely used as feature extractors

#### GoogLeNet / Inception (2014)
**Inception module:** Parallel 1×1, 3×3, 5×5 convolutions + pooling in one block. 
- 1×1 convolutions reduce dimensionality before expensive 3×3, 5×5 ops

#### ResNet (2015) — Residual Networks

**Skip connections** solve vanishing gradient in very deep networks:
```
output = F(x, {Wᵢ}) + x    [residual/skip connection]
```
If the optimal function is close to identity, it's easier to learn F(x) → 0 than to learn identity directly.

Versions: ResNet-18, -34, -50, -101, -152.
ResNet-50 uses **bottleneck blocks**: 1×1 → 3×3 → 1×1 (reduces parameters)

#### DenseNet (2017)
Each layer connects to ALL subsequent layers:
```
xₗ = Hₗ([x₀, x₁, ..., x_{l-1}])   [concatenation]
```
Better gradient flow, feature reuse, compact parameters.

#### EfficientNet (2019)
**Compound scaling:** Scale width, depth, and resolution together using a coefficient φ:
```
depth: d = α^φ
width: w = β^φ  
resolution: r = γ^φ
α·β²·γ² ≈ 2   (found by NAS)
```
EfficientNet-B0 to B7 — state-of-the-art efficiency/accuracy tradeoff.

---

### 6.4 Object Detection

#### Two-Stage Detectors (Region-based)

**R-CNN (2014):** Extract ~2000 region proposals (selective search) → CNN on each → classify

**Fast R-CNN:** Share CNN computation across proposals, add ROI Pooling.

**Faster R-CNN:** Replace selective search with **Region Proposal Network (RPN)** — fully convolutional, slides over feature map.

```
Image → Backbone CNN → Feature Map
                             ↓            ↓
                           RPN          ROI Pooling
                        (proposals)    (proposal features)
                             ↓
                       Classification + Regression
```

#### One-Stage Detectors (YOLO Family)

**YOLOv1 (2016):** Divide image into S×S grid. Each cell predicts B boxes + confidence + C class probs.

**YOLO3–8 evolutionary improvements:**
- Multi-scale detection (3 scales)
- Feature Pyramid Networks (FPN) for small objects
- Anchor boxes
- Darknet-53 / CSPDarknet backbone
- Label smoothing, Mosaic augmentation
- Decoupled head, Task-Aligned Learning

**YOLOv8/v10/v11 (2024-2025):**
- Anchor-free detection
- New backbone architectures
- ~50ms inference on edge devices

**SSD (Single Shot MultiBox Detector):**
Predicts at multiple scales directly from backbone feature maps.

---

## 7. Recurrent Networks: RNNs, LSTMs, GRUs

### 7.1 Vanilla RNN

Processes sequential data by maintaining a hidden state:
```
hₜ = tanh(Wₕhₜ₋₁ + Wₓxₜ + b)
yₜ = Wᵧhₜ
```

**Problems:**
1. **Vanishing gradients:** For long sequences, ∂L/∂h₀ = Π (∂hₜ/∂hₜ₋₁) → 0 or ∞
2. **Short-term memory:** Can't capture long-range dependencies

---

### 7.2 LSTM (Long Short-Term Memory)

Introduces a **cell state** (cₜ) — a highway for gradients to flow with minimal manipulation:

```
Forget gate:  fₜ = σ(Wf·[hₜ₋₁, xₜ] + bf)       [what to forget from cell]
Input gate:   iₜ = σ(Wi·[hₜ₋₁, xₜ] + bi)        [what new info to store]
Cell update:  c̃ₜ = tanh(Wc·[hₜ₋₁, xₜ] + bc)     [candidate values]
Cell state:   cₜ = fₜ ⊙ cₜ₋₁ + iₜ ⊙ c̃ₜ           [update cell]
Output gate:  oₜ = σ(Wo·[hₜ₋₁, xₜ] + bo)        [what to output]
Hidden state: hₜ = oₜ ⊙ tanh(cₜ)                 [filtered cell state]
```

**Key insight:** Sigmoid gates control information flow. The additive cell state update prevents gradient vanishing along the "CEC" (Constant Error Carousel).

---

### 7.3 GRU (Gated Recurrent Unit)

Simplified LSTM with fewer parameters:
```
Reset gate:  rₜ = σ(Wr·[hₜ₋₁, xₜ])
Update gate: zₜ = σ(Wz·[hₜ₋₁, xₜ])
New content: h̃ₜ = tanh(W·[rₜ ⊙ hₜ₋₁, xₜ])
Hidden:      hₜ = (1-zₜ) ⊙ hₜ₋₁ + zₜ ⊙ h̃ₜ
```

GRU merges cell state and hidden state. Similar performance to LSTM in practice, 25% fewer parameters.

---

### 7.4 Bidirectional RNNs

Process sequence in both directions — capture past AND future context:
```
Forward:   →  h₁, h₂, h₃, ..., hₙ
Backward:  ←  h₁', h₂', h₃', ..., hₙ'
Combined:  [hₜ; hₜ'] at each timestep
```
Used in BERT, original seq2seq models, NER, POS tagging.

---

### 7.5 Sequence-to-Sequence (Seq2Seq)

Encoder-decoder architecture for translation, summarization, Q&A:

```
Input: "Hello world" → Encoder (LSTM) → context vector c
Output: "Bonjour monde" ← Decoder (LSTM conditioned on c)
```

**Bottleneck problem:** Fixed-length context vector c loses information for long sequences.

**Solution: Attention Mechanism (Bahdanau, 2015)**
```
eₜₛ = vᵀ tanh(Wₕhₛ + Wₛdₜ)    [alignment score]
αₜₛ = softmax(eₜₛ)               [attention weights]
cₜ  = Σₛ αₜₛhₛ                   [context vector for each decoder step]
```
The decoder looks at ALL encoder states at each step, weighted by relevance.

---

## 8. The Transformer Architecture

The most important architecture in modern AI, introduced in "Attention Is All You Need" (Vaswani et al., 2017).

### 8.1 Self-Attention (The Core Mechanism)

```
Q = X · Wq   (Queries)
K = X · Wk   (Keys)
V = X · Wv   (Values)

Attention(Q, K, V) = softmax(QKᵀ / √dₖ) · V
```

**Intuition:**
- Each token creates a **query** ("what am I looking for?")
- Each token creates a **key** ("what do I contain?")
- Dot product Q·Kᵀ scores relevance
- Scaling by √dₖ prevents softmax saturation
- Result: weighted sum of **values** V based on relevance

**Complexity:** O(n²·d) where n = sequence length, d = dimension.
For n=4096, this is expensive — motivates FlashAttention, linear attention, etc.

---

### 8.2 Multi-Head Attention

Run h attention heads in parallel, each with different projections:
```
head_i = Attention(Q·Wq_i, K·Wk_i, V·Wv_i)
MultiHead(Q,K,V) = Concat(head₁,...,headₕ) · Wₒ
```

**Why multiple heads?** Each head can attend to different aspects:
- Head 1: syntactic relationships
- Head 2: semantic similarity
- Head 3: coreference
- ...

Typical: h=8 heads, d_model=512, d_head=d_model/h=64.

---

### 8.3 Transformer Block

Each block consists of:
```
x → LayerNorm → Multi-Head Attention → (+residual) → LayerNorm → FFN → (+residual)
```

**Feed-Forward Network (FFN):**
```
FFN(x) = max(0, xW₁ + b₁)W₂ + b₂
```
Dimension: d_model → 4×d_model → d_model (fan out by 4×).

**Pre-norm vs Post-norm:**
- Original paper: post-norm (normalize AFTER residual)
- Modern LLMs (GPT-3, LLaMA): pre-norm (normalize BEFORE attention) — more stable training

---

### 8.4 Positional Encoding

Transformers have no inherent sense of order. Must inject position information.

**Sinusoidal (original, fixed):**
```
PE(pos, 2i)   = sin(pos / 10000^(2i/d))
PE(pos, 2i+1) = cos(pos / 10000^(2i/d))
```
Can extrapolate to unseen lengths, but not as good as learned.

**Learned Positional Embeddings:**
Trainable vectors for each position. GPT-2 style.

**Relative Positional Encoding:**
Encode position as relative offsets between tokens, not absolute positions.
- T5 uses learned relative biases
- **ALiBi:** Add linear bias to attention logits (fast, good extrapolation)
- **RoPE (Rotary Position Embedding):** Used in LLaMA, Mistral, GPT-NeoX

**RoPE:** Rotates Q and K vectors in complex space by position angle:
```
f(x, m) = x · e^(imθ)   [multiply by rotation matrix at position m]
```
Relative positions preserved: (Q_m · K_n)* = (Q_0 · K_{n-m})
Excellent extrapolation beyond training length (with YaRN/LongRoPE extensions).

---

### 8.5 Encoder-Only, Decoder-Only, Encoder-Decoder

**Encoder-only (BERT family):**
- Bidirectional — every token attends to all others
- Best for: Classification, NER, sentence embeddings, understanding tasks
- Examples: BERT, RoBERTa, DeBERTa, ELECTRA

**Decoder-only (GPT family):**
- Causal (autoregressive) — each token only attends to past tokens
- Best for: Text generation, code, dialogue, completion
- Examples: GPT series, LLaMA, Mistral, Falcon, Claude, Gemini

**Encoder-Decoder (T5/BART family):**
- Encoder processes input fully; decoder generates output autoregressively
- Best for: Translation, summarization, Q&A with source document
- Examples: T5, BART, Flan-T5, mT5

---

### 8.6 FlashAttention

Standard attention: O(n²) memory, multiple HBM reads/writes.

**FlashAttention (Dao et al., 2022):**
- Fuses the attention computation into a single GPU kernel
- Tiling: computes in SRAM blocks, never materializes full n×n matrix
- IO-complexity: O(n²/B) HBM reads (B = SRAM block size)

**Result:** 2-4× speedup, 5-20× memory reduction. Now standard in all production transformers.

**FlashAttention-2/3 (2023/2024):**
- Better parallelism across sequence dimension
- Optimize for H100 Tensor Cores
- 2-3× faster than FA1

---

## 9. Natural Language Processing (NLP)

### 9.1 Text Preprocessing Pipeline

```python
import re
from nltk.tokenize import word_tokenize
from nltk.corpus import stopwords
from nltk.stem import PorterStemmer, WordNetLemmatizer

# Standard preprocessing
def preprocess(text):
    # 1. Lowercase
    text = text.lower()
    # 2. Remove HTML, URLs, special chars
    text = re.sub(r'<[^>]+>', ' ', text)
    text = re.sub(r'http\S+', ' ', text)
    text = re.sub(r'[^a-z0-9\s]', ' ', text)
    # 3. Tokenize
    tokens = word_tokenize(text)
    # 4. Remove stopwords
    stops = set(stopwords.words('english'))
    tokens = [t for t in tokens if t not in stops and len(t) > 2]
    # 5. Lemmatize (better than stemming for NLP)
    lemmatizer = WordNetLemmatizer()
    tokens = [lemmatizer.lemmatize(t) for t in tokens]
    return tokens
```

---

### 9.2 Word Representations

#### Bag of Words (BoW) & TF-IDF

```
TF-IDF(t, d) = TF(t,d) × IDF(t)
TF(t,d)  = count(t,d) / total_tokens(d)
IDF(t)   = log(N / df(t))   [N = docs, df = docs containing t]
```

Sparse, high-dimensional, no word order, no semantics.

#### Word2Vec (2013)

Neural network trained to predict context from word (Skip-gram) or word from context (CBOW).

**Skip-gram objective:**
```
Maximize: Σ Σ log P(context_word | center_word)
          sentences w ∈ context
```

**Negative sampling:** Instead of normalizing over full vocabulary, sample k "negative" words.

**Resulting embeddings:**
- Similar words cluster together: `king - man + woman ≈ queen`
- Dimension: typically 100-300
- Captures semantic and syntactic relationships

#### GloVe (Global Vectors)
```
Jₒ = Σᵢⱼ f(Xᵢⱼ) (wᵢᵀw̃ⱼ + bᵢ + b̃ⱼ - log Xᵢⱼ)²
```
Trains on co-occurrence matrix. Combines local (Word2Vec) and global (matrix factorization) statistics.

#### FastText
Represents words as bags of character n-grams. Handles OOV words (morphology-aware).

---

### 9.3 Subword Tokenization

Modern LLMs don't tokenize by word — they use subword tokenization.

**Byte Pair Encoding (BPE):**
```
1. Start with character vocabulary
2. Count all adjacent pairs
3. Merge most frequent pair into new token
4. Repeat until target vocab size (e.g., 50,000)

"unrelated" → "un" "rel" "ated"  (if those are learned merges)
```

**WordPiece (BERT):**
Similar to BPE but maximizes language model likelihood instead of frequency.
Prefix unknown subwords with "##": `playing → play ##ing`

**SentencePiece (T5, LLaMA):**
Language-agnostic, treats text as raw bytes, handles spaces, multilingual.

**Tiktoken (GPT-4, GPT-3.5):**
Fast BPE implementation with GPT-specific optimizations.

**Byte-level BPE (GPT-2):**
Operates on raw UTF-8 bytes. Never produces UNK tokens.

---

### 9.4 BERT and the Encoder Revolution

**BERT (Bidirectional Encoder Representations from Transformers, 2018)**

Pre-training tasks:
1. **Masked Language Modeling (MLM):** Mask 15% of tokens, predict them
2. **Next Sentence Prediction (NSP):** Predict if two sentences are consecutive

```
Input: [CLS] sentence_A [SEP] sentence_B [SEP]
Mask:  15% tokens randomly → [MASK]

Loss = MLM loss + NSP loss (binary CE)
```

**BERT-base:** 12 layers, 768 hidden, 12 heads → 110M params
**BERT-large:** 24 layers, 1024 hidden, 16 heads → 340M params

**Fine-tuning BERT for downstream tasks:**
```python
from transformers import BertForSequenceClassification, BertTokenizer
import torch

tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')
model = BertForSequenceClassification.from_pretrained('bert-base-uncased', num_labels=2)

inputs = tokenizer("I love NLP!", return_tensors='pt', truncation=True, padding=True)
outputs = model(**inputs, labels=torch.tensor([1]))
loss = outputs.loss
logits = outputs.logits
```

**Variants:**
- **RoBERTa:** More data, dynamic masking, no NSP → better
- **DeBERTa:** Disentangled attention (position and content separate), Virtual Adversarial Training
- **ALBERT:** Parameter sharing across layers → smaller, faster
- **DistilBERT:** Knowledge distillation → 60% of params, 97% performance

---

### 9.5 Text Generation: GPT Architecture

GPT (Generative Pre-trained Transformer) uses a decoder-only transformer trained with causal language modeling:

```
P(x₁, x₂, ..., xₙ) = Π P(xₜ | x₁, ..., xₜ₋₁)
```

**Autoregressive generation:**
```python
from transformers import GPT2LMHeadModel, GPT2Tokenizer

tokenizer = GPT2Tokenizer.from_pretrained('gpt2')
model = GPT2LMHeadModel.from_pretrained('gpt2')

inputs = tokenizer("The future of AI is", return_tensors='pt')
outputs = model.generate(
    **inputs,
    max_new_tokens=100,
    temperature=0.8,        # controls randomness
    top_k=50,               # top-k sampling
    top_p=0.95,             # nucleus sampling
    do_sample=True,
    repetition_penalty=1.1  # penalize repeated tokens
)
print(tokenizer.decode(outputs[0]))
```

**Decoding strategies:**
- **Greedy:** Always pick argmax → deterministic but repetitive
- **Beam search:** Maintain k best sequences at each step → good for translation
- **Top-k sampling:** Sample from top k tokens by probability
- **Nucleus (top-p):** Sample from smallest set of tokens summing to probability p
- **Temperature:** T < 1 = more focused; T > 1 = more random

---

### 9.6 Instruction Tuning & RLHF

**Supervised Fine-Tuning (SFT):**
Fine-tune a base language model on (instruction, response) pairs.

**RLHF (Reinforcement Learning from Human Feedback):**
```
Phase 1: Supervised Fine-Tuning (SFT)
  Base model → Fine-tune on instruction-following demos

Phase 2: Reward Model (RM) Training
  Collect human preferences: prefer response A over B
  Train RM: r(x, y) predicts human preference score

Phase 3: PPO Training
  Use RM to score model outputs
  Update policy to maximize reward via PPO
  KL penalty: keep close to SFT model (prevent reward hacking)

L_PPO = E[r(x,y)] - β·KL[π_θ(y|x) || π_sft(y|x)]
```

**Direct Preference Optimization (DPO):**
Skips the separate reward model — reformulates RLHF as supervised learning:
```
L_DPO(θ) = -E[(x,yw,yl)] log σ(β log(π_θ(yw|x)/π_ref(yw|x)) - β log(π_θ(yl|x)/π_ref(yl|x)))
```
Where `yw` = preferred, `yl` = dispreferred response. Simpler, more stable.

---

## 10. Computer Vision

### 10.1 Vision Transformers (ViT)

"An Image is Worth 16×16 Words" (Dosovitskiy et al., 2020).

**Key idea:** Split image into patches, treat as token sequence.

```
Image: 224×224×3
Patches: 14×14 patches of 16×16 pixels each → 196 patch tokens
Flatten each patch: 16×16×3 = 768 dimensions
Add position embedding
[CLS] token + 196 patch tokens → Transformer encoder
```

**Advantages:**
- Global receptive field from layer 1
- Scales better with data than CNNs
- Pretrained ViTs: ViT-B/16, ViT-L/16, ViT-H/14

**Disadvantages:**
- Needs large datasets (overcomes lack of CNN inductive bias)
- Quadratic attention complexity

**DeiT, Swin Transformer, MAE:**
- **DeiT:** Efficient training of ViT with knowledge distillation
- **Swin Transformer:** Hierarchical ViT with window-based attention (O(n) complexity)
- **MAE (Masked Autoencoders):** Mask 75% of patches, reconstruct — powerful self-supervised pretraining

---

### 10.2 Semantic & Instance Segmentation

**Semantic segmentation:** Assign class label to every pixel.
- FCN (Fully Convolutional Network): Remove final FC layers, use transposed conv for upsampling
- U-Net: Encoder-decoder with skip connections (medical imaging standard)
- DeepLab series: Atrous (dilated) convolutions, ASPP module

**Instance segmentation:** Detect individual objects + segment each.
- **Mask R-CNN:** Extends Faster R-CNN with a mask prediction head
- **YOLACT:** Real-time instance segmentation

**Panoptic segmentation:** Combines semantic + instance.
- Every pixel labeled (things + stuff)

---

### 10.3 Image Generation

**Generative Adversarial Networks (GANs):** See Section 11.
**Diffusion Models:** See Section 11.
**CLIP (Contrastive Language-Image Pretraining):** See Section 11.

---

## 11. Generative Models

### 11.1 Variational Autoencoders (VAEs)

Encoder-decoder architecture with a **probabilistic bottleneck**.

```
Encoder: q_φ(z|x) = N(μ_φ(x), σ_φ(x)²)    [approximate posterior]
Decoder: p_θ(x|z)                             [generative model]
Prior:   p(z) = N(0, I)                       [latent space prior]

ELBO = E_q[log p_θ(x|z)] - KL(q_φ(z|x) || p(z))
       ──────────────────   ──────────────────────
        reconstruction         regularization
           term                    term
```

**Reparameterization trick:**
```
z = μ + σ * ε,  ε ~ N(0, I)
```
Allows backprop through the sampling operation.

**Use cases:** Image generation, data augmentation, representation learning, anomaly detection.

---

### 11.2 Generative Adversarial Networks (GANs)

Two networks in competition:
```
Generator G(z; θg):      Noise z → Fake data
Discriminator D(x; θd):  Data x → Real/Fake probability

Minimax game:
min_G max_D E[log D(x)] + E[log(1 - D(G(z)))]
```

**Training dynamics:**
1. Train D: maximize distinguishing real vs fake
2. Train G: fool D (minimize D's accuracy on fakes)
3. Nash equilibrium: D(x) = 0.5 everywhere (can't distinguish)

**GAN variants:**

| Variant | Key Idea | Best For |
|---|---|---|
| DCGAN | Conv/Deconv architecture | Basic image synthesis |
| WGAN | Wasserstein distance, gradient penalty | Training stability |
| StyleGAN/2/3 | Disentangled style control, progressive growing | High-quality faces |
| BigGAN | Large-scale, class-conditional | ImageNet synthesis |
| Pix2Pix | Paired image-to-image translation | Image editing |
| CycleGAN | Unpaired image translation (cycle consistency) | Style transfer |
| GauGAN/SPADE | Semantic image synthesis | Landscape generation |

**GAN training challenges:**
- **Mode collapse:** Generator produces limited diversity
- **Training instability:** Loss oscillation
- **Evaluation:** Use FID (Fréchet Inception Distance), IS (Inception Score)

---

### 11.3 Diffusion Models

The dominant paradigm for image generation (DALL-E 2, Stable Diffusion, Midjourney, FLUX).

**Forward process (adding noise):**
```
q(xₜ|xₜ₋₁) = N(xₜ; √(1-βₜ)xₜ₋₁, βₜI)

After T steps: xT ~ N(0, I)  [pure Gaussian noise]
```

**Reverse process (learned denoising):**
```
p_θ(xₜ₋₁|xₜ) = N(xₜ₋₁; μ_θ(xₜ,t), Σ_θ(xₜ,t))
```

**Training objective:**
```
L = E[||ε - ε_θ(√(ᾱₜ)x₀ + √(1-ᾱₜ)ε, t)||²]
```
Predict the noise ε added at timestep t. Surprisingly simple!

**Key architectures:**
- **U-Net with time embedding** — original DDPM, Stable Diffusion
- **DiT (Diffusion Transformer)** — patches + transformers, Sora, Stable Diffusion 3, Flux

**Samplers (inference acceleration):**
- DDPM: 1000 steps
- DDIM: 50-100 steps (deterministic)
- DPM-Solver: 20-30 steps
- Flow Matching (Rectified Flow): Straight paths, ~8 steps (Stable Diffusion 3)

**Classifier-Free Guidance (CFG):**
```
ε_guided = ε_uncond + w * (ε_cond - ε_uncond)
```
w = guidance scale (7-12 typical). Higher w → more adherent to prompt, less diversity.

---

### 11.4 CLIP & Multimodal Models

**CLIP (Contrastive Language-Image Pre-training, OpenAI 2021):**

Trains vision encoder + text encoder to maximize cosine similarity of matching image-text pairs:
```
L = -Σᵢ log(exp(sim(vᵢ, tᵢ)/τ) / Σⱼ exp(sim(vᵢ, tⱼ)/τ))
```
Trained on 400M image-text pairs from the internet.

**Zero-shot classification:**
```python
import clip, torch
model, preprocess = clip.load("ViT-B/32")

image = preprocess(image_pil).unsqueeze(0)
text = clip.tokenize(["a dog", "a cat", "a car"])

image_features = model.encode_image(image)
text_features = model.encode_text(text)

probs = (100.0 * image_features @ text_features.T).softmax(dim=-1)
# No training needed! Just compare embeddings.
```

**CLIP successors:**
- **SigLIP (Google):** Sigmoid loss instead of softmax — scales better
- **DINOv2 (Meta):** Self-supervised ViT, excellent dense features
- **OpenCLIP:** Open-source CLIP with better data curation

---

## 12. Reinforcement Learning

### 12.1 Markov Decision Process (MDP) Formalism

An MDP is defined by (S, A, P, R, γ):
- **S:** State space
- **A:** Action space
- **P(s'|s,a):** Transition dynamics
- **R(s,a,s'):** Reward function
- **γ:** Discount factor (0 < γ < 1)

**Goal:** Find policy π(a|s) that maximizes expected cumulative return:
```
G_t = Σ_{k=0}^{∞} γᵏ R_{t+k+1}
```

**Value functions:**
```
V^π(s) = E_π[G_t | S_t = s]    [state value]
Q^π(s,a) = E_π[G_t | S_t=s, A_t=a]    [action value]

Bellman equation: V^π(s) = Σₐ π(a|s) Σ_{s'} P(s'|s,a)[R(s,a,s') + γV^π(s')]
```

**Optimal policy:** π*(s) = argmax_a Q*(s,a)

---

### 12.2 Q-Learning

Model-free, off-policy algorithm that learns Q*(s,a) directly:

```
Q(s,a) ← Q(s,a) + α[r + γ max_{a'} Q(s',a') - Q(s,a)]
                                     ─────────────────
                                      TD target        TD error
```

**ε-greedy exploration:**
```
With prob ε: random action (explore)
With prob 1-ε: argmax_a Q(s,a) (exploit)
Decay ε over time.
```

**DQN (Deep Q-Network, DeepMind 2015):**
Replaces Q-table with neural network Q(s, a; θ).

Key innovations:
1. **Experience replay buffer:** Store (s,a,r,s') tuples, sample random batches
2. **Target network:** Separate frozen network θ⁻ for targets, updated periodically
3. **Reward clipping:** Clip to [-1, 1]

```
L(θ) = E[(r + γ max_{a'} Q(s',a'; θ⁻) - Q(s,a; θ))²]
```

**Double DQN:** Decouple action selection and evaluation to reduce overestimation:
```
Target = r + γ Q(s', argmax_{a'} Q(s',a'; θ); θ⁻)
```

**Dueling DQN:** Separate streams for V(s) and A(s,a):
```
Q(s,a; θ) = V(s; θ) + A(s,a; θ) - (1/|A|) Σ A(s,a'; θ)
```

---

### 12.3 Policy Gradient Methods

Instead of learning Q-values, directly optimize the policy π_θ:

```
J(θ) = E_π[G_t]

Policy Gradient Theorem:
∇_θ J(θ) = E_π[∇_θ log π_θ(a|s) · Q^π(s,a)]
```

**REINFORCE:**
```
θ ← θ + α Σ_t ∇_θ log π_θ(aₜ|sₜ) · Gₜ
```

**Baseline subtraction (variance reduction):**
```
∇_θ J = E[∇_θ log π_θ(a|s) · (Q(s,a) - V(s))]
              ──────────────────────────────────
                    advantage A(s,a)
```

---

### 12.4 Actor-Critic Methods

Maintain two networks:
- **Actor:** π_θ(a|s) — the policy
- **Critic:** V_w(s) — estimates state value

```
Advantage: A(s,a) = r + γV(s') - V(s)   [TD error as advantage]
Actor update:  θ ← θ + α ∇_θ log π_θ(a|s) · A(s,a)
Critic update: w ← w - α_c ∇_w (A(s,a))²
```

#### Proximal Policy Optimization (PPO)

The dominant RL algorithm for LLM fine-tuning and robotics:

```
L_clip(θ) = E[min(rₜ(θ) Aₜ, clip(rₜ(θ), 1-ε, 1+ε) Aₜ)]

rₜ(θ) = π_θ(aₜ|sₜ) / π_θ_old(aₜ|sₜ)   [probability ratio]
```

The clip prevents too-large policy updates — stable, efficient.

**PPO in RLHF (InstructGPT, ChatGPT):**
```
Reward = RM(x, y) - β·KL[π_θ(y|x) || π_ref(y|x)]
```

---

### 12.5 Model-Based RL & World Models

**Model-free:** Learn directly from interaction — sample inefficient.
**Model-based:** Learn environment model P(s'|s,a), plan with it.

**World Models approach:**
1. Learn compact latent representation of observations
2. Learn transition model in latent space
3. Plan using imagined trajectories

**DreamerV3:** State-of-the-art world model — learns from raw pixels, works across domains (Atari, continuous control, Minecraft).

---

## 13. Large Language Models (LLMs)

### 13.1 Scaling Laws

**Chinchilla scaling laws (Hoffmann et al., 2022):**

For optimal performance given compute budget C:
```
N_opt ∝ C^0.5    [model parameters]
D_opt ∝ C^0.5    [training tokens]

Rule of thumb: ~20 tokens per parameter (equal scaling of N and D)
```

**Key insight:** GPT-3 (175B params, 300B tokens) was undertrained. Chinchilla (70B, 1.4T tokens) performs comparably.

**LLaMA-2/3:** Applied Chinchilla to produce 7B, 13B, 70B models trained on 2T tokens.

**Power law:** L ∝ N^{-0.076} (loss decreases smoothly with model size — no clear plateau observed).

---

### 13.2 LLM Architecture Deep Dive (Modern)

**Modern LLM stack (LLaMA-3 style):**

```
Embedding Layer
    ↓
[Transformer Block] × N:
    ├─ RMSNorm (pre-norm)
    ├─ Grouped Query Attention (GQA)
    │      ├─ Rotary Position Embeddings (RoPE)
    │      └─ KV Cache for inference
    ├─ Residual connection
    ├─ RMSNorm
    ├─ SwiGLU FFN (d_model → 8/3 × d_model → d_model)
    └─ Residual connection
    ↓
RMSNorm
    ↓
LM Head (tied with embedding weights)
```

**Grouped Query Attention (GQA):**
```
Multi-Head: Q heads = K heads = V heads = h
Multi-Query (MQA): Q heads = h, K/V heads = 1
Grouped Query (GQA): Q heads = h, K/V heads = g (1 < g < h)
```
GQA reduces KV cache memory by h/g× — critical for inference efficiency.

**SwiGLU FFN:**
```
SwiGLU(x, W, V, W₂) = (Swish(xW) ⊙ xV) W₂
```
Gate mechanism — competitive with standard FFN at same compute.

---

### 13.3 Efficient Fine-Tuning

#### LoRA (Low-Rank Adaptation)

Instead of updating W ∈ ℝ^{d×k}, add low-rank decomposition:
```
W' = W + ΔW = W + BA

where B ∈ ℝ^{d×r}, A ∈ ℝ^{r×k}, rank r << min(d,k)
```

**Parameter reduction:** Original: d×k params. LoRA: r×(d+k) params.
For d=k=4096, r=16: 16M → 131K params (120× reduction!)

```python
from peft import LoraConfig, get_peft_model

lora_config = LoraConfig(
    r=16,                    # rank
    lora_alpha=32,           # scale factor (LoRA_alpha / r)
    target_modules=["q_proj", "v_proj"],   # which layers
    lora_dropout=0.05,
    bias="none",
    task_type="CAUSAL_LM"
)

model = get_peft_model(base_model, lora_config)
model.print_trainable_parameters()
# trainable params: 4,194,304 || all params: 7,243,436,288 || trainable%: 0.058
```

**Variants:**
- **QLoRA:** Quantize base model to 4-bit NF4, LoRA in fp16 — fine-tune 65B on single GPU
- **AdaLoRA:** Adaptive rank allocation (more rank to important layers)
- **LoRA+:** Different learning rates for A and B matrices
- **DoRA:** Decompose weights into magnitude + direction, apply LoRA to direction

---

### 13.4 Quantization

Reduce numerical precision to save memory and accelerate inference.

**Methods:**
```
FP32 (32-bit float) → FP16/BF16 (16-bit) → INT8 → INT4 → INT2

FP32:   ████████ 4 bytes per parameter
BF16:   ████ 2 bytes (same exponent as FP32, less mantissa)
INT8:   ██ 1 byte
INT4:   █ 0.5 bytes  ← QLoRA, GPTQ, AWQ
```

**Post-Training Quantization (PTQ):**
- **GPTQ:** Quantize each layer by minimizing weight reconstruction error
- **AWQ (Activation-Aware Weight Quantization):** Identify and protect salient weights
- **GGUF (llama.cpp format):** Mixed-precision quantization, CPU-friendly

**Effects:** 4-bit quantization reduces quality ~2-5 MMLU points vs FP16.

---

### 13.5 Inference Optimization

**KV Cache:**
Store key-value computations for previously generated tokens — avoids recomputation:
```
Prefill: Process prompt in parallel → store K, V for all prompt tokens
Decode: Append new K, V for each generated token, retrieve all cached K,V
```

**Speculative Decoding:**
Use small draft model to generate k tokens, verify with large model in parallel:
- Draft model: 7B generates 5 token candidates
- Target model: 70B verifies all 5 in one forward pass
- Accept 3 (if distribution matches), regenerate from token 4
- ~2-3× speedup with no quality loss

**Continuous Batching:**
Traditional batching waits for all requests to finish. Continuous batching iterates at token level — much better GPU utilization.

**PagedAttention (vLLM):**
Manages KV cache like OS virtual memory:
- No fragmentation, no over-allocation
- Memory sharing across parallel sequences
- ~3× higher throughput than Hugging Face naive serving

---

## 14. Prompt Engineering

### 14.1 Core Principles

1. **Be specific and explicit** — ambiguity leads to misinterpretation
2. **Provide context** — role, audience, constraints
3. **Use positive instructions** — "write concisely" > "don't write too long"
4. **Iterate** — prompting is empirical

### 14.2 Key Techniques

#### Zero-Shot
```
"Classify the sentiment of this review as Positive, Negative, or Neutral:
'The product arrived on time but the quality was disappointing.'
Answer:"
```

#### Few-Shot (In-Context Learning)
```
"Classify sentiment:
Review: 'Amazing product, exactly as described!' → Positive
Review: 'Broke after one day, terrible quality.' → Negative
Review: 'It works, nothing special.' → Neutral
Review: 'I expected better for this price.' →"
```

**Why it works:** Transformers infer the task from examples via in-context learning — no weight updates!

#### Chain-of-Thought (CoT)
```
"Q: If a train travels at 60mph for 2.5 hours, then at 80mph for 1.5 hours, 
what is the total distance?

A: Let me solve step by step.
Step 1: Distance in first segment = 60 × 2.5 = 150 miles
Step 2: Distance in second segment = 80 × 1.5 = 120 miles
Step 3: Total = 150 + 120 = 270 miles"
```

Add "Let's think step by step" or "Think through this carefully" to elicit reasoning.

#### Zero-Shot CoT
"Solve this problem. Think step by step before giving the final answer."

#### Self-Consistency
Generate multiple CoT answers → take majority vote. Improves reasoning accuracy significantly.

#### Least-to-Most Prompting
Break complex problems into subproblems:
1. "What subproblems need to be solved to answer X?"
2. Solve each subproblem
3. Combine solutions

#### ReAct (Reasoning + Acting)
Interleave thought/action/observation:
```
Thought: I need to find the current population of Tokyo
Action: search("Tokyo current population")
Observation: Tokyo's population is approximately 13.96 million (2023)
Thought: Now I can answer the question
Answer: Tokyo has approximately 14 million people
```

#### Role Prompting
```
"You are a senior software engineer with 15 years of experience in distributed systems. 
Review the following code for correctness, efficiency, and scalability..."
```

#### Structured Output Prompting
```
"Extract the following fields from this job posting and respond ONLY with valid JSON:
{
  "job_title": string,
  "company": string,
  "required_skills": [string],
  "salary_range": string | null,
  "location": string
}

Job posting: [...]"
```

### 14.3 System Prompts & Meta-Prompting

```python
from anthropic import Anthropic

client = Anthropic()
response = client.messages.create(
    model="claude-opus-4-6-20250514",
    max_tokens=1024,
    system="""You are an expert ML engineer. 
    Always:
    - Provide working Python code examples
    - Explain the intuition before the math
    - Note potential pitfalls
    - Suggest when NOT to use each approach""",
    messages=[
        {"role": "user", "content": "Explain gradient descent variants"}
    ]
)
```

---

## 15. RAG Pipelines — All Types

### 15.1 What is RAG?

**Retrieval-Augmented Generation (RAG)** addresses two key LLM limitations:
1. **Hallucination:** LLMs confidently generate false information
2. **Knowledge cutoff:** LLMs can't access real-time or private data

**Basic RAG formula:**
```
P(answer | question, context) where context = retrieve(question, knowledge_base)
```

---

### 15.2 Naive RAG (Standard RAG)

The original, simplest form:

```
[Question]
    ↓
[Query Encoder] → embedding
    ↓
[Vector Database] → retrieve top-k chunks
    ↓
[Prompt Constructor] → "Context: {chunks}\n\nQuestion: {q}\n\nAnswer:"
    ↓
[LLM] → Answer
```

**Implementation:**
```python
from langchain.vectorstores import Chroma
from langchain.embeddings import OpenAIEmbeddings
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain.chains import RetrievalQA
from langchain.llms import OpenAI

# 1. Load and split documents
splitter = RecursiveCharacterTextSplitter(chunk_size=512, chunk_overlap=50)
chunks = splitter.split_documents(docs)

# 2. Build vector store
embeddings = OpenAIEmbeddings()
vectordb = Chroma.from_documents(chunks, embeddings)

# 3. Create retriever
retriever = vectordb.as_retriever(search_kwargs={"k": 5})

# 4. Chain
qa = RetrievalQA.from_chain_type(
    llm=OpenAI(temperature=0),
    retriever=retriever,
    chain_type="stuff"  # stuff all chunks into context
)
answer = qa.run(question)
```

**Limitations:**
- One-shot retrieval — if it misses, response fails
- Coarse chunking loses context
- No query reformulation
- No re-ranking

---

### 15.3 Advanced RAG

Addresses limitations through pre-retrieval, retrieval, and post-retrieval improvements.

#### Pre-Retrieval Improvements

**Query Rewriting:**
```python
rewrite_prompt = """Rewrite this question for better document retrieval.
Original: {question}
Rewritten:"""
```

**Query Decomposition:**
```
"What are the differences between BERT and GPT?"
→ Sub-queries:
   1. "What is BERT?"
   2. "What is GPT?"
   3. "BERT vs GPT architecture differences"
   4. "BERT vs GPT use cases"
```

**HyDE (Hypothetical Document Embeddings):**
```python
# Generate a hypothetical answer → embed it → use for retrieval
hyde_prompt = f"Write a paragraph that answers: {question}"
hypothetical_doc = llm(hyde_prompt)
query_embedding = embed(hypothetical_doc)   # Better alignment with doc space
```

**Step-Back Prompting:**
```
Original: "What are the effects of X on Y in condition Z?"
Step-back: "What are the general principles governing X?"
```

#### Retrieval Improvements

**Hybrid Search (Dense + Sparse):**
```python
# BM25 (sparse, keyword-based) + Dense embeddings
from langchain.retrievers import BM25Retriever, EnsembleRetriever

bm25 = BM25Retriever.from_documents(docs, k=5)
dense = vectordb.as_retriever(search_kwargs={"k": 5})

hybrid = EnsembleRetriever(
    retrievers=[bm25, dense],
    weights=[0.3, 0.7]   # Weight toward dense
)
```

**Multi-vector retrieval:**
- Store document at multiple granularities (summary + chunks)
- Retrieve by summary, return full doc

**Parent-child chunking:**
```
Parent chunks (512 tokens): stored for retrieval context
Child chunks (128 tokens): used for embedding/search

Retrieve by child match → return parent context
```

#### Post-Retrieval Improvements

**Re-ranking:**
```python
from sentence_transformers import CrossEncoder

reranker = CrossEncoder('cross-encoder/ms-marco-MiniLM-L-6-v2')
scores = reranker.predict([(query, chunk) for chunk in retrieved_chunks])
ranked = sorted(zip(chunks, scores), key=lambda x: -x[1])
top_k = [c for c, s in ranked[:3]]
```

Cross-encoders score (query, document) jointly — much more accurate than cosine similarity alone.

**Contextual Compression:**
```python
from langchain.retrievers.document_compressors import LLMChainExtractor

compressor = LLMChainExtractor.from_llm(llm)
# Only keep relevant sentences from each chunk
```

**Lost in the Middle:** LLMs attend poorly to middle of context. Put most important chunks at beginning or end.

---

### 15.4 Modular RAG

Decomposes the pipeline into independent, reusable modules:

```
┌──────────────────────────────────────────┐
│           MODULAR RAG PIPELINE           │
├──────────┬──────────────┬────────────────┤
│  MODULE  │   FUNCTION   │   EXAMPLES     │
├──────────┼──────────────┼────────────────┤
│ Query    │ Process query│ Decompose,     │
│ Planning │              │ Rewrite, Expand│
├──────────┼──────────────┼────────────────┤
│ Routing  │ Select route │ DB routing,    │
│          │              │ Expert routing │
├──────────┼──────────────┼────────────────┤
│Retrieval │ Find chunks  │ Dense, Sparse, │
│          │              │ Hybrid, Web    │
├──────────┼──────────────┼────────────────┤
│Re-rank   │ Score chunks │ Cross-encoder, │
│          │              │ Cohere Rerank  │
├──────────┼──────────────┼────────────────┤
│Filter    │ Remove noise │ Relevance score│
│          │              │ threshold      │
├──────────┼──────────────┼────────────────┤
│Generate  │ Produce ans  │ Stuff, MapRed, │
│          │              │ Refine, Iter.  │
├──────────┼──────────────┼────────────────┤
│Verify    │ Check output │ Factuality,    │
│          │              │ Citation check │
└──────────┴──────────────┴────────────────┘
```

**Chain-of-thought context synthesis methods:**

- **Stuff:** Put all chunks in context at once (simple, limited by window)
- **MapReduce:** Summarize each chunk separately → combine summaries
- **Refine:** Process chunk 1 → refine answer with chunk 2 → refine with chunk 3 → ...
- **Map-Rerank:** Score and select best synthesis from per-chunk answers

---

### 15.5 Corrective RAG (CRAG)

Adds a **relevance grader** to evaluate retrieved documents:

```
Query → Retrieve → Grade relevance of each doc
                           ↓
                   Relevant? → Generate answer
                   Ambiguous? → Refine + re-retrieve
                   Irrelevant? → Web search for fresh docs
                                       ↓
                              Knowledge refinement
                                       ↓
                              Generate corrected answer
```

```python
from langchain.prompts import ChatPromptTemplate

grade_prompt = ChatPromptTemplate.from_template("""
You are a grader assessing relevance of a retrieved document to a user question.
Document: {document}
Question: {question}
Answer with a JSON: {{"score": "yes" | "no"}}
""")

def grade_document(question, doc):
    result = grader_llm.invoke(grade_prompt.format(document=doc, question=question))
    return result["score"] == "yes"
```

---

### 15.6 Self-RAG

Trains the LLM to make retrieval decisions and critique its own output using **special reflection tokens**:

```
Tokens: [Retrieve], [ISREL], [ISSUP], [ISUSE]

Generation flow:
1. Model generates → if [Retrieve] token emitted → retrieve
2. Retrieved docs → model rates relevance [ISREL: relevant/irrelevant]
3. Model generates with context → rates support [ISSUP: fully/partial/no]
4. Model rates overall utility [ISUSE: 1-5]
5. Best-scoring segment selected
```

Key innovation: The model learns WHEN to retrieve (not all queries need retrieval), not just HOW.

---

### 15.7 GraphRAG

Uses knowledge graphs to capture relationships between entities:

```
Text corpus
    ↓
[Entity Extraction] (LLM)
    ↓
[Relation Extraction] (LLM)  
    ↓
[Knowledge Graph] → (node: entity, edge: relation, property: description)
    ↓
[Community Detection] → hierarchical summaries of entity clusters
    ↓
For queries:
  Local search: → entity-centric → traverse graph
  Global search: → map-reduce over community summaries
```

**Microsoft GraphRAG (2024):**
```
Two query modes:
1. Local: "What specific things does Alice do?" → entity subgraph
2. Global: "What are the main themes in the dataset?" → community summaries
```

**When to use GraphRAG vs standard RAG:**

| Scenario | RAG | GraphRAG |
|---|---|---|
| Factual lookups | ✅ | ✅ |
| Multi-hop reasoning | ❌ | ✅ |
| Relationship queries | ❌ | ✅ |
| Global summaries | ❌ | ✅ |
| Single document QA | ✅ | Overkill |
| Latency sensitive | ✅ | ❌ |

---

### 15.8 Agentic RAG (2025–2026 Standard)

The current dominant paradigm. The LLM acts as an **agent** that decides when, what, and how to retrieve:

```python
# LangGraph-based Agentic RAG
from langgraph.graph import Graph, END

# State: {question, context, drafts, iterations, answer}

def plan(state):
    """Decompose query into sub-questions"""
    ...

def retrieve(state):
    """Retrieve for current sub-question"""
    ...

def generate(state):
    """Generate answer draft"""
    ...

def grade(state):
    """Grade answer quality"""
    # Check: grounded? complete? accurate?
    ...

def decide(state):
    """Route: finalize, retrieve_more, web_search, or clarify"""
    if state["grade"] == "pass": return "finalize"
    elif state["iterations"] > 3: return "finalize"  # prevent loops
    else: return "retrieve"

graph = Graph()
graph.add_node("plan", plan)
graph.add_node("retrieve", retrieve)
graph.add_node("generate", generate)
graph.add_node("grade", grade)
graph.add_conditional_edges("grade", decide, {
    "finalize": END,
    "retrieve": "retrieve"
})
```

**Key capabilities of Agentic RAG:**
- **Multi-hop retrieval:** Chain multiple retrievals for complex reasoning
- **Self-correction:** Re-retrieve when output is insufficient
- **Tool use:** Web search, SQL, API calls as retrieval sources
- **Planning:** Decompose queries before retrieving
- **Memory:** Maintain conversation context across turns

---

### 15.9 Multimodal RAG

Extends RAG to images, tables, code, audio:

```
Document types → specialized extraction:
  PDF images → vision model → image summaries → embed
  Tables → extract as text + schema → embed
  Charts → vision model → data extraction → embed
  Code → AST-based chunking → embed with code-specific model

Multi-modal retrieval:
  Text query → retrieve text + images (via CLIP embeddings)
  Image query → CLIP embed → retrieve similar images + related text
```

**ColPali (2024):** Embeds PDF pages as images — no text extraction needed. Retrieves visual pages directly.

---

### 15.10 RAG Evaluation Metrics

**RAGAS Framework:**
```
Faithfulness:      Is the answer grounded in the retrieved context?
Answer Relevance:  Does the answer address the question?
Context Precision: What fraction of retrieved context was actually useful?
Context Recall:    How much of the ground truth was covered by retrieved context?
```

```python
from ragas import evaluate
from ragas.metrics import faithfulness, answer_relevancy, context_precision

result = evaluate(
    dataset=test_ds,
    metrics=[faithfulness, answer_relevancy, context_precision]
)
```

**TruLens / DeepEval:** Alternative evaluation frameworks with LLM-as-judge.

---

## 16. AI Agents & Agentic Systems

### 16.1 What is an AI Agent?

An AI agent is an LLM-powered system that can:
1. **Perceive** the environment (tools, memory, other agents)
2. **Plan** multi-step actions to achieve a goal
3. **Act** by calling tools or generating text
4. **Reflect** on results and adapt

```
[Environment: Documents, APIs, Code, Web, DBs]
         ↕ (observations)
  [Agent: LLM + Memory + Tool Use]
         ↓ (actions)
[Tools: Search, Python, SQL, Browser, Email...]
```

### 16.2 ReAct Framework

```
Thought: I need to find the current stock price of AAPL
Action: search_web("AAPL stock price today")
Observation: AAPL is trading at $189.43 (+0.8%)
Thought: Now I have the price. Should I check the P/E ratio too?
Action: financial_data("AAPL P/E ratio")
Observation: AAPL P/E ratio is 28.4
Thought: I have enough information to answer the question
Answer: Apple (AAPL) is trading at $189.43 with a P/E ratio of 28.4
```

### 16.3 Tool Use / Function Calling

```python
import anthropic

client = anthropic.Anthropic()

tools = [
    {
        "name": "get_weather",
        "description": "Get current weather for a city",
        "input_schema": {
            "type": "object",
            "properties": {
                "city": {"type": "string"},
                "unit": {"type": "string", "enum": ["celsius", "fahrenheit"]}
            },
            "required": ["city"]
        }
    }
]

response = client.messages.create(
    model="claude-opus-4-6-20250514",
    max_tokens=1024,
    tools=tools,
    messages=[{"role": "user", "content": "What's the weather in Tokyo?"}]
)

# Check if model wants to call a tool
if response.stop_reason == "tool_use":
    tool_call = response.content[-1]
    # Execute: result = get_weather(**tool_call.input)
    # Send back result...
```

### 16.4 Memory Systems in Agents

**4 types of memory:**

```
1. SENSORY (In-context window):
   Current conversation, retrieved chunks, tool outputs
   
2. SHORT-TERM (Working memory):
   Recent conversation history (last k turns)
   Scraatchpad for intermediate reasoning
   
3. LONG-TERM (Persistent):
   a. Semantic: Facts about the world/user (vector DB)
   b. Episodic: Records of past interactions (vector DB + timestamps)
   c. Procedural: How to do tasks (fine-tuned weights or retrieved instructions)

4. EXTERNAL (Knowledge stores):
   Vector databases, SQL, documents, APIs
```

### 16.5 Multi-Agent Systems

```
Orchestrator Agent
      ├── Researcher Agent (web search, document retrieval)
      ├── Analyst Agent (data processing, calculations)
      ├── Writer Agent (drafts, formats output)
      └── Critic Agent (reviews, fact-checks)

Communication patterns:
  - Supervisor: One orchestrator directs all
  - Peer-to-peer: Agents communicate directly
  - Blackboard: Shared working memory
  - Pipeline: Each agent processes output of previous
```

**AutoGen, CrewAI, LangGraph** are the main frameworks for multi-agent orchestration.

---

## 17. Vector Databases & Embeddings

### 17.1 Embeddings Deep Dive

**Text embedding models:**

| Model | Dim | Context | Best For |
|---|---|---|---|
| text-embedding-3-small (OpenAI) | 1536 | 8191 | General purpose, cheap |
| text-embedding-3-large (OpenAI) | 3072 | 8191 | Best quality |
| embed-english-v3 (Cohere) | 1024 | 512 | English, retrieval |
| all-MiniLM-L6-v2 (SBERT) | 384 | 256 | Fast, local |
| bge-large-en-v1.5 | 1024 | 512 | MTEB top performer |
| nomic-embed-text-v1.5 | 768 | 8192 | Long context, open |
| jina-embeddings-v3 | 1024 | 8192 | Multilingual |

**Matryoshka Representation Learning (MRL):**
Embeddings that work at multiple dimensionalities. text-embedding-3 supports 256/512/1536/3072 — truncate without reembedding!

### 17.2 Approximate Nearest Neighbor (ANN) Search

Exact NN search is O(n·d). ANN trades accuracy for speed:

#### HNSW (Hierarchical Navigable Small World)
```
Hierarchical graph where:
- Level 0: All nodes (densest graph)
- Level k: Exponentially fewer nodes (longer connections)

Search: Start at level max → greedy search → descend to level 0
Insert: Assign max_layer ∝ -ln(uniform(0,1)) → insert with bidirectional links
```
Parameters: `M` (connections per node), `ef_construction` (search width during build), `ef_search`

**Best for:** High recall, fast updates (dynamic insertion).

#### IVF-PQ (Inverted File + Product Quantization)
```
IVF: Cluster vectors into k centroids (Voronoi cells)
Search: Only compare query to vectors in nearest centroids (nprobe cells)

PQ: Compress each vector by splitting into m subvectors → quantize each
Compression: 128-dim FP32 (512B) → PQ8 (8B) = 64× compression
```
**Best for:** Billion-scale datasets, memory-constrained environments.

#### ScaNN (Google)
Combination of tree-based partitioning + scoring compression — state-of-the-art ANN at scale.

### 17.3 Vector Database Comparison

| DB | Best For | Features | Self-host? |
|---|---|---|---|
| **Chroma** | Local dev, prototyping | Simple API, embedded | ✅ Open source |
| **FAISS** | Research, in-memory | Fastest raw ANN, no server | ✅ Library only |
| **Pinecone** | Production serverless | Managed, auto-scale | ❌ Cloud only |
| **Weaviate** | Multi-modal + metadata | GraphQL, BM25 hybrid, Docker | ✅ |
| **Qdrant** | Production self-hosted | Fast, Rust-based, rich filtering | ✅ |
| **Milvus** | Billion-scale | Best scalability, Kubernetes | ✅ |
| **pgvector** | PostgreSQL extension | Postgres SQL + vectors | ✅ |
| **Redis** | Cache + vectors | Low-latency, familiar | ✅ |

---

## 18. MLOps & Production ML

### 18.1 The ML Systems Stack

```
┌────────────────────────────────────────────┐
│              SERVING LAYER                 │
│   REST/gRPC APIs, Model Servers, Caches   │
├────────────────────────────────────────────┤
│              MODEL REGISTRY               │
│   MLflow, W&B, Hugging Face Hub           │
├────────────────────────────────────────────┤
│            EXPERIMENT TRACKING            │
│   MLflow, Weights & Biases, ClearML       │
├────────────────────────────────────────────┤
│            TRAINING PIPELINE              │
│   Data loading, Training loop, Logging    │
├────────────────────────────────────────────┤
│            FEATURE STORE                  │
│   Feast, Tecton, Hopsworks                │
├────────────────────────────────────────────┤
│              DATA PIPELINE                │
│   ETL, Validation, Versioning            │
├────────────────────────────────────────────┤
│               DATA LAKE                   │
│   S3, GCS, Snowflake, Delta Lake          │
└────────────────────────────────────────────┘
```

### 18.2 Experiment Tracking with MLflow

```python
import mlflow
import mlflow.sklearn

with mlflow.start_run():
    # Log params
    mlflow.log_param("learning_rate", 0.01)
    mlflow.log_param("n_estimators", 100)
    
    # Train model
    model.fit(X_train, y_train)
    
    # Log metrics
    mlflow.log_metric("accuracy", acc)
    mlflow.log_metric("f1", f1)
    
    # Log model
    mlflow.sklearn.log_model(model, "model")
    
    # Log artifacts
    mlflow.log_artifact("feature_importance.png")
```

### 18.3 Model Serving

**BentoML (framework-agnostic serving):**
```python
import bentoml

@bentoml.service(
    resources={"cpu": "2", "memory": "2Gi"},
    traffic={"timeout": 60}
)
class SentimentService:
    model = bentoml.models.get("sentiment_model:latest")
    
    @bentoml.api
    def classify(self, text: str) -> dict:
        result = self.model.predict([text])
        return {"label": result[0], "confidence": result[1]}
```

**TorchServe, Triton Inference Server** for high-throughput deep learning.

### 18.4 Data & Model Monitoring

**Data drift detection:**
```python
from evidently.report import Report
from evidently.metrics import DataDriftPreset, DataQualityPreset

report = Report(metrics=[DataDriftPreset(), DataQualityPreset()])
report.run(reference_data=train_df, current_data=production_df)
```

**Model performance monitoring:**
- Shadow mode: Run new model alongside old, compare outputs
- Canary deployment: Route 5% of traffic to new model
- A/B testing: Random assignment + statistical testing
- Champion-challenger: Keep best performing model live

### 18.5 CI/CD for ML (GitOps)

```
Code change → Unit tests → Integration tests
                                ↓
                   Data validation (Great Expectations)
                                ↓
                      Model training (triggered)
                                ↓
                    Model evaluation vs baseline
                                ↓
                    Model registry update (if better)
                                ↓
                    Deployment (canary → full rollout)
                                ↓
                    Monitoring dashboard update
```

Tools: **DVC** (data versioning), **Prefect/Airflow** (workflow), **GitHub Actions** (CI/CD triggers).

---

## 19. Evaluation Metrics — Complete Reference

### 19.1 Classification Metrics

**Confusion Matrix:**
```
                 Predicted
                 Pos    Neg
Actual  Pos  │TP (True Pos)│FN (False Neg)│
        Neg  │FP (False Pos)│TN (True Neg)│
```

**Derived metrics:**
```
Accuracy    = (TP + TN) / (TP + TN + FP + FN)
Precision   = TP / (TP + FP)    [of positives predicted, how many are right]
Recall      = TP / (TP + FN)    [of actual positives, how many caught]
Specificity = TN / (TN + FP)    [recall for negatives]
F1          = 2 × (Prec × Rec) / (Prec + Rec)
F-beta      = (1+β²) × (Prec × Rec) / (β²×Prec + Rec)
```

**ROC Curve:** Plot TPR (recall) vs FPR (1-specificity) at all thresholds.
**AUC-ROC:** Area under ROC curve. 0.5 = random, 1.0 = perfect. Threshold-independent.

**Precision-Recall Curve:** Better for imbalanced datasets (ROC is optimistic when negatives dominate).

**Average Precision (AP):** Area under PR curve. Use for detection tasks.
**mAP:** Mean AP across classes or IoU thresholds (object detection standard).

---

### 19.2 Regression Metrics

```
MAE  = (1/n) Σ|yᵢ - ŷᵢ|                    [interpretable, robust to outliers]
MSE  = (1/n) Σ(yᵢ - ŷᵢ)²                   [penalizes outliers, differentiable]
RMSE = √MSE                                  [same units as target]
MAPE = (1/n) Σ|yᵢ-ŷᵢ|/yᵢ × 100%           [percentage error, avoid if yᵢ ≈ 0]
R²   = 1 - SS_res/SS_tot                     [fraction of variance explained]
```

---

### 19.3 NLP / LLM Evaluation Metrics

**BLEU (Bilingual Evaluation Understudy):**
```
BLEU = BP × exp(Σ wₙ log pₙ)
pₙ = n-gram precision
BP = brevity penalty (penalize short outputs)
```
Used for machine translation. Range [0,1]. Higher is better.

**ROUGE (Recall-Oriented Understudy for Gisting Evaluation):**
```
ROUGE-N: n-gram recall overlap with reference
ROUGE-L: Longest Common Subsequence F-score
```
Used for summarization.

**METEOR:** Better than BLEU — includes synonym matching, paraphrase handling.

**BERTScore:**
```
Compute cosine similarity between contextual BERT embeddings
of reference and generated text.
Precision_bert, Recall_bert, F1_bert
```
Correlates better with human judgment than n-gram metrics.

**Perplexity:**
```
PPL = exp(-1/N Σ log P_model(xₜ|x<t))
```
Measures how well a language model predicts a corpus. Lower = better. (Not comparable across different tokenizers!)

**LLM Benchmarks:**

| Benchmark | What it Tests | Score (Claude Opus 4.8, 2026) |
|---|---|---|
| MMLU | General knowledge, 57 subjects | ~90%+ |
| GPQA Diamond | Graduate-level science | 94.6% |
| HumanEval | Python coding | ~95% |
| SWE-Bench | Real GitHub issues | ~70%+ |
| MATH-500 | Competition math | ~95%+ |
| AIME 2025 | Hard competition math | Top tier |
| LMSYS Arena | Human preference | ELO-based |

---

### 19.4 Retrieval Metrics

```
Hit Rate: fraction of queries where correct doc is in top-k
MRR@k (Mean Reciprocal Rank): (1/|Q|) Σ 1/rank_of_first_relevant
NDCG@k: considers rank position + relevance grade
MAP (Mean Average Precision): averages precision at each recall level
```

---

## 20. AI Safety, Alignment & Ethics

### 20.1 The Alignment Problem

Training powerful AI systems that reliably pursue goals humans actually want.

**Key challenges:**
1. **Specification problem:** Reward functions are hard to specify fully (e.g., Goodhart's Law: "when a measure becomes a target, it ceases to be a good measure")
2. **Generalization problem:** Behavior in training ≠ behavior in deployment
3. **Scalable oversight:** Humans can't evaluate superhuman AI outputs
4. **Goal stability:** Advanced AI might modify its own objectives

---

### 20.2 Constitutional AI (Anthropic)

A technique for self-supervised alignment:
1. Initial helpful response (possibly harmful)
2. **Critique:** Model critiques its response against a constitutional principle
3. **Revision:** Model revises based on critique
4. Final response is much better aligned

Reduces reliance on human feedback labels for preference data.

---

### 20.3 Types of AI Safety Risks

**Near-term risks:**
- Bias and fairness (training data bias → discriminatory outputs)
- Hallucination and factual errors
- Prompt injection attacks
- Data poisoning
- Model stealing / extraction

**Long-term risks:**
- Deceptive alignment (model performs well in training but not deployment)
- Goal misgeneralization
- Treacherous turn (advanced AI changing behavior when not monitored)
- Emergent capabilities (unexpected abilities at scale)

### 20.4 Fairness in ML

**Group fairness metrics:**

```
Demographic Parity: P(ŷ=1 | A=0) = P(ŷ=1 | A=1)     [equal positive rates]
Equal Opportunity:  P(ŷ=1 | Y=1, A=0) = P(ŷ=1 | Y=1, A=1)  [equal TPR]
Equalized Odds:     Equal TPR AND FPR across groups
Individual Fairness: Similar individuals treated similarly
```

**Impossibility theorem:** Calibration, equal FPR, and equal FNR cannot all hold simultaneously when base rates differ across groups (Chouldechova, 2017).

---

## 21. Latest AI Models & News (2025–2026)

### 21.1 The Spring 2026 LLM Sprint

The AI model landscape underwent a major shift in early 2026. Multiple frontier labs shipped new flagships within weeks of each other:

**OpenAI GPT-5.5 (April 2026):**
- Unified intelligent routing — automatically adjusts reasoning depth
- 128K output token window (largest among hosted models)
- Top performance on coding and reasoning benchmarks

**Anthropic Claude Opus 4.8 (May 2026):**
- #1 on the Artificial Analysis Intelligence Index (61.4 score)
- Leads GPQA Diamond at 94.6% — best reasoning benchmark performance
- Best coding model for sustained complex software engineering
- Excels in agentic workflows when paired with Claude Code

**Google Gemini 3.1 Pro (February 2026):**
- Focus on video, images, spatial reasoning
- Deep integration into Google Workspace and Cloud
- ~57 on the AI Intelligence Index

**DeepSeek V4 Pro:**
- MIT-licensed open-source
- 75% API price reduction vs previous generation
- Competitive with frontier models on coding/reasoning at dramatically lower cost

**Alibaba Qwen 3.7 Max:**
- Top 10 on GPQA Diamond while being among cheapest
- Strong multilingual performance

### 21.2 Context Window Explosion

All major models now support massive context:

| Model | Context Window |
|---|---|
| Llama 4 Scout | **10M tokens** |
| GPT-5.5 | 1M tokens |
| Claude Opus 4.8 | 1M tokens |
| Gemini 3.5 Flash | 1M tokens |
| Grok 4 Fast | 2M tokens |
| DeepSeek V4 | 1M tokens |

Context windows are no longer a differentiator — the competition has shifted to reasoning quality, agentic capabilities, and cost.

### 21.3 Benchmarks That Matter in 2026

Modern evaluations have moved beyond saturated academic multiple-choice:
- **SWE-Bench:** Fix real GitHub issues (Claude Opus 4.8 ~70%+)
- **GPQA Diamond:** Graduate-level science (Claude Mythos Preview 94.6%)
- **Terminal-Bench:** Real terminal tasks
- **Agentic task completion:** Multi-step real-world task success rates
- **LMSYS Chatbot Arena:** Human preference ELO rankings

### 21.4 Key Architectural Trends (2025–2026)

**Mixture of Experts (MoE):**
Use only a subset of parameters for each forward pass:
```
For each token: Router selects top-k experts
Active params ≪ Total params → same quality, less compute

Mixtral 8×7B: 47B total, 13B active
Grok-1: 314B total, ~85B active
GPT-4: Rumored ~1.8T total MoE
```

**Multimodal as Default:**
- Text + Image: Now universal (GPT-4V, Claude, Gemini, LLaMA 4)
- Video understanding: Gemini 1.5+, GPT-4o
- Audio native: GPT-4o voice mode
- Real-time: Latency <500ms for voice

**Test-Time Compute (o1, DeepSeek-R1 style):**
Allocate more computation at inference (thinking/reasoning) rather than only at training:
```
Chain-of-thought tokens before answer
Process Reward Models (PRMs) guide search
Monte Carlo Tree Search or beam search over reasoning paths
```

**Speculative reasoning:** o3, DeepSeek-R1, Gemini 2.0 Flash Thinking demonstrate that spending more compute thinking before answering dramatically improves hard reasoning.

### 21.5 Open vs Closed Models

**Open-weight leaders (2025-2026):**
- **LLaMA 4 (Meta):** Scout (10M context), Maverick (competitive flagship)
- **Qwen 3.7 Max (Alibaba):** Top benchmarks, open-source
- **DeepSeek V4 (DeepSeek):** MIT license, near-frontier quality
- **Mistral Large 3:** Strong coding and multilingual
- **Phi-4 (Microsoft):** Efficient small models (3.8B-14B)

**The cost gap is closing:** Open models at 70B-405B scale now match or exceed what closed models offered in 2023.

---

## 22. Hands-On Cheat Sheets

### 22.1 When to Use Which Model Type

```
┌─────────────────────────────────────────────────────────────┐
│                    TASK → MODEL SELECTION                   │
├─────────────────────────┬───────────────────────────────────┤
│ Text classification     │ BERT/DeBERTa + fine-tune          │
│ Sentiment analysis      │ Same or few-shot LLM              │
│ Text generation         │ GPT/LLaMA decoder-only            │
│ Translation             │ mBART, NLLB, or GPT               │
│ Summarization           │ T5, BART, or GPT                  │
│ Q&A (closed domain)     │ RAG + GPT/Claude                  │
│ Code generation         │ Claude/GPT-5.5/Codestral          │
│ Image classification    │ EfficientNet, ViT                 │
│ Object detection        │ YOLO, Faster R-CNN                │
│ Image segmentation      │ SAM 2, Mask2Former                │
│ Image generation        │ Stable Diffusion 3, FLUX          │
│ Tabular data            │ XGBoost, LightGBM, TabNet         │
│ Time series forecast    │ N-BEATS, TFT, TimesNet            │
│ Recommendation          │ Two-tower model, NCF              │
│ Anomaly detection       │ Isolation Forest, Autoencoder     │
│ Graph learning          │ GCN, GAT, GraphSAGE               │
└─────────────────────────┴───────────────────────────────────┘
```

### 22.2 Hyperparameter Quick Reference

| Hyperparameter | Typical Range | Effect |
|---|---|---|
| Learning rate | 1e-5 to 1e-2 | Most critical; use LR finder |
| Batch size | 16-512 | Larger = stable gradients, may overfit |
| Dropout | 0.1-0.5 | Higher = more regularization |
| Weight decay | 1e-4 to 0.1 | Prevents overfitting |
| Clip grad norm | 0.5-1.0 | Prevents explosion |
| Warmup steps | 1%-5% of total | Stabilizes early training |
| LoRA rank | 4-64 | Higher = more params but more expressive |
| Beam width | 4-6 | Higher = better quality, slower |
| Temperature | 0.1-1.5 | Lower = focused, higher = creative |
| Top-p | 0.9-0.99 | Nucleus sampling cutoff |

### 22.3 Python Quick Reference

```python
# ===========================
# DATA SCIENCE ESSENTIALS
# ===========================

# Pandas - Quick operations
df.describe()                          # Stats summary
df.isnull().sum()                      # Missing values
df['col'].value_counts(normalize=True) # Class distribution
df.corr()                              # Correlation matrix
df.groupby('cat')['val'].agg(['mean', 'std', 'count'])

# Sklearn pipeline (always use this!)
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler
from sklearn.ensemble import RandomForestClassifier

pipe = Pipeline([
    ('scaler', StandardScaler()),
    ('classifier', RandomForestClassifier(n_estimators=100))
])
pipe.fit(X_train, y_train)
pipe.score(X_test, y_test)

# Cross-validation
from sklearn.model_selection import cross_val_score
scores = cross_val_score(pipe, X, y, cv=5, scoring='f1_macro')
print(f"F1: {scores.mean():.3f} ± {scores.std():.3f}")

# Hyperparameter tuning
from sklearn.model_selection import RandomizedSearchCV
param_dist = {'classifier__n_estimators': [50, 100, 200],
              'classifier__max_depth': [None, 5, 10]}
search = RandomizedSearchCV(pipe, param_dist, n_iter=10, cv=3)
search.fit(X_train, y_train)
best = search.best_estimator_

# ===========================
# PYTORCH TRAINING BOILERPLATE
# ===========================

device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')

def train_epoch(model, loader, optimizer, criterion):
    model.train()
    total_loss = 0
    for X, y in loader:
        X, y = X.to(device), y.to(device)
        optimizer.zero_grad()
        pred = model(X)
        loss = criterion(pred, y)
        loss.backward()
        torch.nn.utils.clip_grad_norm_(model.parameters(), 1.0)
        optimizer.step()
        total_loss += loss.item()
    return total_loss / len(loader)

@torch.no_grad()
def evaluate(model, loader, criterion):
    model.eval()
    total_loss, correct = 0, 0
    for X, y in loader:
        X, y = X.to(device), y.to(device)
        pred = model(X)
        total_loss += criterion(pred, y).item()
        correct += (pred.argmax(1) == y).sum().item()
    return total_loss / len(loader), correct / len(loader.dataset)

# ===========================
# HUGGING FACE QUICK START
# ===========================

from transformers import AutoTokenizer, AutoModelForSequenceClassification
from transformers import Trainer, TrainingArguments
from datasets import load_dataset

# Load and tokenize
dataset = load_dataset("imdb")
tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")

def tokenize(examples):
    return tokenizer(examples["text"], truncation=True, padding="max_length", max_length=256)

tokenized = dataset.map(tokenize, batched=True)

# Fine-tune
model = AutoModelForSequenceClassification.from_pretrained("bert-base-uncased", num_labels=2)

training_args = TrainingArguments(
    output_dir="./results",
    num_train_epochs=3,
    per_device_train_batch_size=16,
    per_device_eval_batch_size=32,
    warmup_steps=500,
    weight_decay=0.01,
    evaluation_strategy="epoch",
    load_best_model_at_end=True,
    fp16=True,   # Mixed precision
)

trainer = Trainer(model=model, args=training_args,
                  train_dataset=tokenized["train"],
                  eval_dataset=tokenized["test"])
trainer.train()
```

### 22.4 The ML Debugging Checklist

```
FIRST: Check the basics
 □ Is loss decreasing at all? If not: check learning rate, data loading
 □ Are you accidentally evaluating on training data?
 □ Data leakage? (test data statistics used in preprocessing)
 □ Target variable correct? Not shifted by 1?
 □ NaN/Inf in loss? → exploding gradients, bad init

OVERFITTING (high val loss, low train loss):
 □ More data (augmentation counts!)
 □ Increase regularization (dropout, weight decay, L1/L2)
 □ Reduce model complexity
 □ Early stopping
 □ Cross-validation

UNDERFITTING (both train and val loss high):
 □ More complex model
 □ More epochs
 □ Reduce regularization
 □ Feature engineering
 □ Increase learning rate

CONVERGENCE ISSUES:
 □ Learning rate too high → loss spikes or diverges
 □ Learning rate too low → very slow convergence
 □ Use learning rate finder
 □ Gradient clipping for RNN/Transformer

CLASSIFICATION IMBALANCE:
 □ Using accuracy? → Switch to F1/AUC
 □ class_weight='balanced' in sklearn
 □ SMOTE or class-weighted sampling

LLM FINE-TUNING:
 □ Catastrophic forgetting? → Lower LR, LoRA instead of full FT
 □ Loss not decreasing? → Check tokenizer EOS/padding
 □ Nonsense output? → Check chat template format
```

---

## Appendix A: Important Research Papers Timeline

| Year | Paper | Impact |
|---|---|---|
| 2012 | AlexNet | CNNs + GPU training revolution |
| 2013 | Word2Vec | Neural word embeddings |
| 2014 | GANs (Goodfellow) | Generative modeling revolution |
| 2015 | ResNet | Skip connections, deep networks |
| 2015 | Attention (Bahdanau) | Sequence-to-sequence with attention |
| 2017 | Transformer (Vaswani) | "Attention Is All You Need" |
| 2018 | BERT (Devlin) | Bidirectional pre-training |
| 2018 | GPT-1 (OpenAI) | Generative pre-training |
| 2019 | XLNet, RoBERTa | BERT improvements |
| 2020 | GPT-3 | Few-shot in-context learning |
| 2020 | DDPM | Denoising diffusion models |
| 2021 | CLIP | Vision-language alignment |
| 2021 | LoRA | Efficient fine-tuning |
| 2022 | InstructGPT | RLHF alignment |
| 2022 | Chinchilla | Scaling laws revision |
| 2022 | FlashAttention | IO-efficient attention |
| 2022 | ChatGPT | Conversational AI breakthrough |
| 2023 | LLaMA-2 | Open LLMs |
| 2023 | GPT-4 | Multimodal frontier |
| 2023 | Mixtral | Mixture of Experts |
| 2024 | LLaMA-3 | 405B open model |
| 2024 | Gemini Ultra | Frontier multimodal |
| 2024 | o1 (OpenAI) | Test-time compute reasoning |
| 2024 | GraphRAG (MS) | Knowledge graph + RAG |
| 2024 | FLUX | Rectified flow image gen |
| 2025 | DeepSeek-R1 | Open reasoning model |
| 2025 | LLaMA 4 Scout | 10M context window |
| 2026 | Spring Sprint | GPT-5.5, Claude 4.8, Gemini 3.x |

---

## Appendix B: Glossary of Key Terms

| Term | Definition |
|---|---|
| Attention | Mechanism weighting relevance of different inputs |
| Autoregressive | Generating tokens one-by-one conditioned on all previous |
| Backpropagation | Algorithm to compute gradients via chain rule |
| Batching | Processing multiple examples simultaneously |
| Chain-of-thought | Generating intermediate reasoning steps |
| Context window | Maximum tokens a model can process at once |
| DPO | Direct Preference Optimization — alignment without RL |
| Embedding | Dense vector representation of data |
| Fine-tuning | Further training a pre-trained model on specific data |
| Flash Attention | Memory-efficient attention via tiling |
| Foundation model | Large pre-trained model for general use |
| GQA | Grouped Query Attention — efficient multi-head attention |
| Hallucination | Confidently generating factually incorrect content |
| RLHF | Reinforcement Learning from Human Feedback |
| Latent space | Compressed representation space in neural networks |
| LoRA | Low-Rank Adaptation — efficient parameter fine-tuning |
| MoE | Mixture of Experts — sparse conditional computation |
| RAG | Retrieval-Augmented Generation |
| RoPE | Rotary Position Embedding used in modern LLMs |
| Softmax | Function converting logits to probability distribution |
| Tokenization | Process of splitting text into model-digestible units |
| Transfer learning | Apply knowledge learned on one task to another |
| Transformer | Attention-based architecture dominating modern AI |
| Vector database | DB optimized for storing and searching embeddings |
| Zero-shot | Task performance without task-specific training examples |

---

*Last updated: June 2026 | Covers the complete AI/ML landscape through the spring 2026 model sprint*
