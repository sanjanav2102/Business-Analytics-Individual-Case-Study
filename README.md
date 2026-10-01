# Predicting Customer Insurance Product Preference and Channel-Affinity Using Demographic and Life-Stage Profiling: A Mismatch-Aware Recommendation Approach

**Course:** Business Analytics – Individual Case Study
**Student:** V Sanjana | **Register No.:** CB.SC.U4CSE23751 | **Class/Section:** CSE – H
**Domain:** Banking / Insurance (BFSI)

---

## 1. Problem Statement

Insurance companies lose revenue and incur higher customer acquisition costs when they pitch the wrong product through the wrong channel. A 25-year-old urban professional and a 55-year-old semi-urban retiree differ not only in the products they want (car/health vs. retirement/life) but also in the sales touchpoint they respond to: online platforms, agents, or bank-led (bancassurance) channels.

Most existing approaches stop at static demographic segmentation. They ignore a useful signal: the **gap between the channel a customer prefers and the channel through which they were actually sold their policies**. This mismatch can lead to poor cross-sell response, wasted agent effort, and quiet customer disengagement.

This study builds a **Channel-Affinity and Mismatch-Aware Recommendation Framework** that:
1. Predicts a customer's preferred purchase channel from demographic, behavioural and life-stage features.
2. Introduces a **Channel-Preference Gap Score** that measures the distance between stated preference and actual purchase channel for each customer.

## 2. Objectives

- **O1:** Identify the demographic, behavioural and life-stage drivers of insurance product ownership and channel preference.
- **O2:** Construct and quantify a Channel-Preference Gap Score to detect customers served through a non-preferred channel.
- **O3:** Build and evaluate a classification model that predicts preferred channel (Agent / Neutral / Online), and translate the results into a segment-level targeting strategy.

## 3. Data Collection

| Item | Detail |
|---|---|
| Method | Google Forms questionnaire (20 questions, 5 sections) |
| Form link | https://docs.google.com/forms/d/e/1FAIpQLSc4qHaUTDaJY0PeJu3mtZzpNVLam0u4g_PVxhlGJ4bDPlroMg/viewform |
| Distribution | WhatsApp groups, LinkedIn, community groups |
| Primary responses | 97 genuine survey responses |
| Final working dataset | 10,000 records × 30 attributes |
| Anonymity | No personally identifying information collected |

**Questionnaire sections:** (i) demographics, (ii) recent and upcoming life events, (iii) insurance ownership and channel used per product, (iv) channel preference and reasons, (v) future purchase intent, digital comfort and financial literacy. An attention-check question is included as a quality control.

> **Note on the extended sample:** 97 records are genuine primary responses. Because this is too small to train and evaluate a three-class classifier, the primary data was extended with a simulated sample that preserves the conditional structure of the survey (age → occupation → income, age → digital comfort, etc.). Every row carries a `Source` flag (`real_survey` / `synthetic`) so the two can be separated. Results should be read as a methodological demonstration, not an industry benchmark.

## 4. Data Preparation

| Issue | Extent | Treatment |
|---|---|---|
| Exact duplicates | 16 rows | Removed |
| Failed attention check | 1,206 rows | Removed |
| Missing values | 7 fields (up to 9.0% in Income) | "Not specified" category for income; age-group median for Digital Comfort and Financial Literacy; mode for the rest |
| Out-of-range values | 73 Digital Comfort entries (0, 6, 9 on a 1–5 scale) | Set to missing, then imputed |
| Inconsistent text | City casing/whitespace, malformed age labels | Standardised |

**Result:** 10,000 raw → 9,984 after de-duplication → **8,778 analysis-ready records**, 30 → 47 features.

**Engineered features:** five product-ownership flags, life-event flags, `Preferred_Channel_Label`, `Actual_Channel_Bucket`, and the novel **`Channel_Gap_Score`** (No / Moderate / High Mismatch).

## 5. Analytics Methods

- **Exploratory analysis:** product ownership by age, channel preference by income, actual channel by area, gap-score distribution, correlation heatmap, digital comfort by age, segment-level mismatch heatmap.
- **Model:** Random Forest classifier (`n_estimators=300`, `max_depth=10`, `class_weight="balanced"`).
- **Target:** `Preferred_Channel_Label` (Agent / Neutral / Online).
- **Split:** 80/20 stratified (7,022 train / 1,756 test), `random_state=42`.
- **Metrics:** accuracy, macro precision/recall/F1, confusion matrix, per-class report, feature importance.

## 6. Key Results

**Model performance**

| Metric | Score |
|---|---|
| Accuracy | 0.629 |
| Macro Precision | 0.592 |
| Macro Recall | 0.614 |
| Macro F1 | 0.598 |

Baselines: 33.3% (random), 49.8% (majority class). The Neutral class is the weakest (F1 = 0.39); the Agent and Online classes are separated well.

**Key findings**

1. **Digital comfort drives channel preference.** It carries 44.7% of feature importance, roughly 4× age and 9× income.
2. **52.8% of customers are served through a non-preferred channel** (37.1% moderate mismatch, 15.8% high mismatch, 1,386 customers).
3. **Mismatch peaks in the 26–45 age band**, highest at 23.3% for 26–35 semi-urban customers; the 60+ group is the best matched (9.6%).
4. **Income is not a valid proxy for digital readiness.** Online preference varies by under 11 points across income bands and is highest in the lowest band.
5. **The life-stage hypothesis was not supported.** Life-event flags show |r| ≤ 0.01 with all variables and about 2% model importance. This is reported as a negative result.

**Recommendations (summary)**

- Capture a one-item digital-comfort score at onboarding and use it for channel routing.
- Prioritise the high-mismatch customers for channel re-assignment, ranked by Gap Score.
- Pilot digital migration in the 26–45 cohort; keep assisted agent servicing for 60+.
- Stop income-tiered digital targeting; treat the Neutral segment as a test-and-learn cohort.
- Validate any life-event-triggered campaigns against transaction data before launch.

## 7. Repository Structure

```
├── README.md
├── analysis.ipynb                       # Full pipeline: load → clean → EDA → model → insights
├── Case_Study_Report.pdf                # Final report (prescribed format)
└── data/
    ├── Insurance_Survey_Fused_Dataset.xlsx   # Collected dataset (10,000 × 30)
    └── Insurance_Survey_Cleaned.csv          # Cleaned dataset used for analysis (8,778 × 47)
```

## 8. How to Run

```bash
pip install pandas numpy matplotlib seaborn scikit-learn openpyxl jupyter
jupyter notebook analysis.ipynb
```

The notebook loads `Insurance_Survey_Fused_Dataset.xlsx` with a relative path. If the file sits in `data/`, change the load line to `pd.read_excel('data/Insurance_Survey_Fused_Dataset.xlsx')` before running.

## 9. Limitations

- **Sample composition:** the realised sample is 79.2% urban and 35.0% salaried; rural respondents (5.6%) are under-represented.
- **Extended sample:** only 97 records are primary; the rest are simulated, so reported accuracy is a methodological demonstration.
- **Self-report bias:** preference, digital comfort and intent are all self-reported.
- **Neutral-class separability:** predictions for undecided customers carry low confidence.
- **Cross-sectional design:** no causal inference or tracking of channel migration over time.

## 10. References

1. Ansari, A. and Riasi, A. (2016). Modelling and evaluating customer loyalty using neural networks: Evidence from startup insurance companies. *Future Business Journal*, 2(1), 15–30.
2. Fang, K., Jiang, Y. and Song, M. (2016). Customer profitability forecasting using Big Data analytics: A case study of the insurance industry. *International Journal of Operations & Production Management*, 36(9), 1128–1152.
3. Neslin, S.A. et al. (2006). Challenges and opportunities in multichannel customer management. *Journal of Service Research*, 9(2), 95–112.
4. Kumar, V. and Venkatesan, R. (2005). Who are the multichannel shoppers and how do they perform? *Journal of Interactive Marketing*, 19(2), 44–62.
5. Breiman, L. (2001). Random Forests. *Machine Learning*, 45(1), 5–32.
6. Pedregosa, F. et al. (2011). Scikit-learn: Machine Learning in Python. *Journal of Machine Learning Research*, 12, 2825–2830.