# Refresh Opportunity Scoring Using Search Performance Signals: A Machine Learning Decision-Support Framework

## 1. Abstract
This project investigates how observable search-performance signals can be used to prioritize organic content for editorial review and refreshing. A Random Forest classifier was trained using aggregated search and engagement metrics from the FlyRank internship warehouse over a one-month window. Performance was compared against a transparent rule-based baseline using both random and grouped validation. The grouped validation produced performance comparable to the random split on the selected evaluation metric, supporting the use of the model for ranking review candidates. The resulting system is intended as a decision-support tool for SEO analysts to efficiently allocate editorial resources, rather than an automated publishing system.

## 2. Introduction / Problem Statement
Content decay is a natural lifecycle phase for organic search assets. Over time, previously high-performing pages lose visibility due to shifting search intent, algorithm updates, or newer competing content. For enterprise content teams, manual prioritization is difficult; a static rule (e.g., "refresh everything older than 90 days") often wastes resources on low-value pages while missing decaying high-value assets. The objective of this analysis is to answer a specific business question: *Which existing, historically successful pages should be reviewed first?* By generating a prioritized, probability-driven queue, this framework minimizes false positives (wasting writer time) and false negatives (silent loss of organic revenue).

## 3. Data
* **Data source:** FlyRank Internship Warehouse
* **Release:** `flyrank_pseudonymized_warehouse_release_v20260703`

Primary development was performed on the March 2026 partition following the internship guidance for iterative development. To ensure public safety and privacy, all client names, URLs, and private queries were strictly excluded. The unit of analysis is a single webpage, represented entirely by pseudonymized identifiers (`content_hash_id` and `client_hash_id`).

## 4. Methodology
**Feature Engineering**
Features were engineered to capture both traffic engagement and content characteristics. The final feature vector included: `impressions`, `clicks`, `avg_position`, `sessions`, `pageviews`, `engaged_sessions`, `scroll_events`, `search_volume`, `word_count`, `char_count`, and `content_type`.

**Target Definition**
A binary target was derived from aggregated warehouse observations to distinguish pages requiring editorial review based on the defined refresh-opportunity criteria.

**Baseline vs. Model**
* **Baseline:** A transparent rule-based heuristic evaluating staleness and maximum impressions.
* **Model:** A Random Forest classifier.

**Validation & Leakage Audit**
The models were evaluated using a Random Split and a Grouped Split (`GroupShuffleSplit`) based on `client_hash_id`. Grouped validation is more honest because it prevents pages from the same client from appearing in both the training and testing sets, measuring true generalization. Prior to training, a strict leakage audit was conducted to ensure no future-window data or product flags were utilized, and IDs were removed as features.

## 5. Results
The Random Forest model produced the ranked review queue used in the action playbook and maintained similar performance under grouped validation.

| Validation Strategy | Precision | Base Rate |
| :--- | :--- | :--- |
| **Random Split (Week 5)** | 1.00 | 0.2077 |
| **Grouped Split (Week 6)** | 1.00 | 0.2106 |

Although the observed precision was high on this dataset, grouped validation and a leakage audit were performed to reduce the risk of overly optimistic estimates.

**Feature Importance:**
The most influential features included clicks, impressions, and pageviews, all of which are observable search-performance metrics.

## 6. Limitations & Honest Framing
* **Observational Data:** This analysis relies on cross-sectional historical data without a controlled intervention. The findings highlight pages that *historically* match decay patterns.
* **No Causal Claims:** This framework does not prove that executing a refresh *causes* or *will increase* traffic. 
* **Not Google's Algorithm:** This model identifies behavioral traffic drops; it does not "predict Google" or reverse-engineer search algorithms.
* **Human Review Required:** The outputs are strictly directional and designed for decision-support. Automated, programmatic publishing based on these scores is strictly banned.

## 7. Ranked Recommendations
The validated model outputs a ranked action queue with reason codes to guide editorial strategy. 

| Reason Code | Meaning | Action |
| :--- | :--- | :--- |
| `RC01` | High impressions with decline indicators | **Refresh** (High Confidence) |
| `RC02` | High visibility requiring editorial review | **Review** (Medium Confidence) |
| `RC03` | Stable performance – monitor only | **Monitor** (Low Confidence) |

**Top Ranked Content Review Opportunities:**

![Ranked Recommendations](work/figures/ranked_recommendations.png)
*Figure 1. The model ranks pages according to predicted refresh priority using observable search-performance metrics. These recommendations are intended to support editorial review and should not be interpreted as guarantees of ranking improvements.*

## 8. Reproducibility
**Repository:** 
https://github.com/Ujjwalupreti/flyrank-internship-capstone

**Notebook workflow:**
W01 Research Question
↓
W02 ML Task Framing
↓
W03 Data Contract
↓
W04 Baseline
↓
W05 Model
↓
W06 Validation
↓
W07 Action Playbook

## 9. Acknowledgments
Built on the FlyRank ML Internship dataset. Data provided by [https://flyrank.ai](https://flyrank.ai).

## 10. Conclusion
This project demonstrates how observable search-performance metrics can be transformed into a reproducible refresh-prioritization framework.

Compared with a transparent rule-based baseline, the machine-learning workflow provides a ranked review queue supported by grouped validation, leakage checks, and human-readable reason codes.

The resulting recommendations are intended to support editorial prioritization rather than automate publishing decisions.
