
# ShopSense

## System Overview

ShopSense takes user profiles, product metadata, and browsing/purchase history to build user and item representations for personalized recommendations. It combines nearest-neighbor similarity, decision tree preference prediction, and logistic regression probability modeling, then applies Search to select Top-N products and 1–3 bundles. Outputs are ranked product lists and bundle recommendations (`recommendations.json`), evaluated with precision@K, recall@K, and click-through rate.

## Modules

### Module 1: Data Ingestion and Feature Preparation

**Topics:** Data preprocessing / feature engineering (supporting)

**Input:** `users.csv`, `events.csv` (click/cart/purchase with timestamps), `products.csv` (price, category, brand), `ratings.csv`

**Output:** `user_features.csv`, `item_features.csv`, `user_item_matrix.csv` (implicit feedback), `session_events.csv`

**Integration:** Produces standardized features and interaction data consumed by Modules 2–5 and provides held-out events for Module 6.

**Prerequisites:** None

---

### Module 2: User Similarity via Nearest Neighbors

**Topics:** Unsupervised learning (similarity)

**Input:** `user_features.csv`

**Output:** `user_neighbors.csv` (top-K similar users per user with similarity scores)

**Integration:** Similarity signals inform ranking and bundle selection in Module 5.

**Prerequisites:** Module 1

---

### Module 3: Preference Prediction with Decision Trees

**Topics:** Supervised learning (decision trees / classifiers)

**Input:** `user_features.csv`, `item_features.csv`, labels from `ratings.csv`

**Output:** `predicted_scores.csv` (per user–item preference scores)

**Integration:** Provides base relevance scores for Search-based ranking in Module 5.

**Prerequisites:** Module 1

---

### Module 4: Purchase Probability with Logistic Regression

**Topics:** Supervised learning (logistic regression)

**Input:** `user_features.csv`, `item_features.csv`, evidence from `ratings.csv` / `events.csv`

**Output:** `purchase_probs.csv` (per user–item purchase probabilities)

**Integration:** Adds uncertainty-aware signals used alongside Module 3 in Module 5.

**Prerequisites:** Module 1

---

### Module 5: Search-Based Ranking and Bundle Selection

**Topics:** Search (e.g., A* / beam search for ranking and bundle selection)

**Input:** `predicted_scores.csv`, `purchase_probs.csv`, `user_neighbors.csv`, `item_features.csv`

**Output:** `recommendations.json` (Top-N product IDs with scores + 1–3 bundles, each bundle a list of product IDs)

**Integration:** Produces the system’s final recommendations consumed by Module 6.

**Prerequisites:** Modules 2–4

---

### Module 6: Evaluation and Feedback

**Topics:** Evaluation / metrics (supporting)

**Input:** `recommendations.json`, held-out `events.csv`

**Output:** `evaluation_report.json` (precision@K, recall@K, click-through rate)

**Integration:** Validates recommendation quality and feeds insights back for iteration.

**Prerequisites:** Module 5

---

## Feasibility Study

_Mapped to the published course topics order and project checkpoints. Each module is scheduled after its topic is covered._ 

| Module | Required Topic(s) | Topic Covered By | Checkpoint Due |
| ------ | ----------------- | ---------------- | -------------- |
| 1      | Data preprocessing | Early weeks (prior to ML units) | Checkpoint 1 — Wed, Feb 11 |
| 2      | Unsupervised learning (similarity) | After intro ML topics begin | Checkpoint 2 — Thu, Feb 26 |
| 4      | Supervised learning (logistic regression) | Supervised learning unit | Checkpoint 3 — Thu, Mar 19 |
| 3      | Supervised learning (decision trees) | Supervised learning unit | Checkpoint 4 — Thu, Apr 2 |
| 5      | Search (A* / beam) | Search units (Weeks 1.5–3) | Checkpoint 5 — Thu, Apr 16 |
| 6      | Evaluation / metrics | Evaluation metrics unit | Checkpoint 5 — Thu, Apr 16 |

## Coverage Rationale

Search, similarity modeling, decision trees, and logistic regression were chosen because they reflect core techniques used in real-world recommender systems and align naturally with the ShopSense theme. Similarity modeling supports collaborative filtering by identifying users with shared preferences, while decision trees provide an interpretable way to predict user–item relevance from features. Logistic regression adds probability-calibrated purchase modeling rather than relying only on point estimates. Search combines these heterogeneous signals into a coherent Top-N ranking and small product bundles. Trade-offs include accuracy versus interpretability (decision trees explainability vs. more compact logistic models) and personalization versus diversity (similarity-driven recommendations can narrow user exposure without diversification).
