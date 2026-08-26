# Content Opportunity Scoring for Search Performance Review

## FlyRank ML Internship Capstone

**Fatima Iqbal · AI Software Engineer · Data Science · Machine Learning**

A decision-support framework for prioritizing content observations that may deserve earlier human review.

---

## 1. What This Project Does

Content teams may have many pages to review while search visibility, traffic, engagement, search demand, content age, and trend signals change at different rates.

This project builds a repeatable content-opportunity scoring workflow to help answer:

> Which content observations should be reviewed first?

The system combines observable search-performance and content signals and evaluates a Random Forest classifier for identifying declining observations.

The output is intended as a **review-prioritization tool**, not an automatic content decision system.

It does not predict Google's ranking algorithm and does not claim that refreshing a page will cause better performance.

---

## 2. Who Is It For?

This project is designed for:

- SEO and content teams
- Content strategists
- Search-performance analysts
- Data scientists working with content analytics
- Teams managing large content portfolios

The main user is a reviewer who needs to decide **where to investigate first**.

---

## 3. Research Question

Which observable search-performance signals can be combined into a repeatable score to identify content pages that should be prioritized for refresh or human review?

### Decision Supported

The analysis helps prioritize which content observations should be reviewed first based on observed search-performance patterns.

The resulting score is a decision-support ranking. It is not a claim about Google's ranking algorithm and does not establish a causal effect of refreshing content.

---

## 4. Data

The analysis uses the FlyRank ML Internship warehouse release, centered on:

`fact_content_daily_performance`

The dataset contains daily content-page observations with search and analytics measurements.

### Primary Signal Families

- Search impressions
- Search clicks
- Average search position
- Sessions and page traffic
- Engagement
- Search volume
- Competition
- CPC
- Content age
- Trend-related signals

### Public Safety

The public research artifact excludes:

- Client names
- Identifying information
- Domains
- URLs
- Private queries
- Credentials
- Raw warehouse exports

Client and content identifiers are also excluded from the predictive feature set.

Sparse AI-referral fields were not used as primary opportunity signals where their density was insufficient.

### Data Availability

Data availability flags are used to distinguish observations where GSC or GA4 measurements are present.

Missing or unavailable measurements are not treated as evidence of poor content performance.

### Data Limitations

The warehouse contains observed search and analytics measurements but does not establish why performance changed.

It does not by itself identify causal effects of:

- Content refreshes
- Google algorithm changes
- Competitor activity
- Seasonality
- Marketing campaigns

Therefore, the resulting opportunity score is framed as **directional decision support** rather than a causal or algorithmic claim.

---

## 5. Methodology

The project uses a repeatable content-opportunity scoring approach.

The workflow is:

1. Define the content-review decision.
2. Inspect the available signals.
3. Build observable features.
4. Define declining vs non-declining observations.
5. Establish a majority-class baseline.
6. Train a Random Forest classifier.
7. Compare random and client-grouped validation.
8. Audit for target leakage.
9. Translate the evidence into ranked review priorities.

### Features

The analysis considers:

- `search_volume`
- `competition`
- `cpc`
- `word_count`
- `impressions_90d`
- `clicks_90d`
- `sessions_90d`
- `content_age_days`
- `ctr`
- `avg_position`
- `engagement_rate`
- `scroll_rate`
- `ai_traffic_pct`
- `trend_pct`

These features represent search demand, competition, content characteristics, visibility, traffic, engagement, and recent movement.

### Target

The predictive target distinguishes:

- Declining observations
- Non-declining observations

The label is based on the available trend-direction field.

It is treated as an observed performance outcome, not as proof that a page needs a refresh.

### Signal Audit

The distributions of the main numeric fields were inspected before building recommendations.

Several features showed strong right-skew and heavy tails.

Three signal relationships were tested:

1. Trend direction versus recent sessions
2. Engagement rate versus recent sessions
3. Search volume versus impressions

These relationships were classified as **MIXED** rather than universal rules.

### Baseline

The classification baseline uses the majority-class prediction on the same client-grouped test set.

The dataset contained:

- 16,262 declining observations
- 13,738 non-declining observations
- 30,000 observations in total

The majority class represents approximately **54.21%** of observations.

### Proposed Model

A Random Forest classifier was evaluated as a stronger modelling approach for identifying declining observations.

The model uses content, search, traffic, engagement, age, and categorical features while excluding identifiers and target-derived trend fields.

---

## 6. Leakage Checks

The target label was created from `trend_direction`.

The following target-derived or closely related fields were excluded:

- `trend_direction`
- `trend_pct`
- `impressions_last_30d`
- `clicks_last_30d`
- `sessions_last_30d`
- `impressions_prev_30d`
- `clicks_prev_30d`
- `sessions_prev_30d`

The following identifiers were also excluded:

- `client_id`
- `content_id`

These fields were excluded because they either directly define the target, closely encode the target outcome, or act as identifiers rather than useful predictive signals.

The leakage audit reduces the risk of the model directly learning the target definition.

---

## 7. Validation

The original random split produced:

**70.13% accuracy**

Because observations from the same client may be related, a more conservative client-grouped validation was also performed using `GroupShuffleSplit`.

The client-grouped evaluation kept records from the same client together rather than allowing the same client to appear in both training and testing data.

This produced:

**57.24% accuracy**

The grouped result is treated as the more conservative estimate of generalization to unseen clients.

---

## 8. V2 Evaluation Results

| Approach | Validation | Accuracy |
|---|---|---:|
| Majority-class baseline | Client-grouped | 54.21% |
| Random Forest | Client-grouped | 57.24% |
| Random Forest | Original random split | 70.13% |

### Main Result

The Random Forest achieved **57.24% accuracy** under client-grouped validation compared with **54.21%** for the majority-class baseline.

This is an improvement of approximately:

**+3.03 percentage points**

The original random split produced 70.13%, which is substantially higher.

The grouped result is therefore treated as the more conservative estimate of performance on unseen clients.

### Interpretation

The result provides modest evidence that the selected observable signals contain useful information for prioritizing content review.

However, the improvement is not large enough to justify automatic content decisions.

The model should therefore be used to help decide **where to investigate first**, with the final decision remaining with a human reviewer.

---

## 9. Random Split vs Client-Grouped Split

| Validation Design | Accuracy |
|---|---:|
| Original random split | 70.13% |
| Client-grouped split | 57.24% |
| Majority-class baseline | 54.21% |

The decrease from 70.13% to 57.24% demonstrates why validation design matters.

When observations from the same client are allowed to appear in both training and testing data, performance can appear stronger than it is on unseen clients.

Client-grouped validation provides a more conservative evaluation.

---

## 10. Architecture

```text
                    FlyRank Warehouse
                           |
                           v
           fact_content_daily_performance
                           |
                           v
                   Data Preparation
                           |
                           v
                  Feature Construction
                           |
              +------------+------------+
              |                         |
              v                         v
        Signal Analysis           Leakage Audit
              |                         |
              +------------+------------+
                           |
                           v
                    Random Forest
                           |
              +------------+------------+
              |                         |
              v                         v
        Random Split          Client-Grouped Split
              |                         |
              v                         v
           70.13%                    57.24%
              |                         |
              +------------+------------+
                           |
                           v
                  Review Priorities
                           |
                           v
                    Human Reviewer