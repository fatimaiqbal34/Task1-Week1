# Content Opportunity Scoring for Search Performance Review

## FlyRank ML Internship Capstone

**Fatima Iqbal · AI Software Engineer · Data Science · Machine Learning**

A decision-support framework for prioritizing content observations that may deserve earlier human review.

## 1. What This Project Does

Content teams may have many pages to review while search visibility, traffic, engagement, search demand, content age, and trend signals change at different rates.

This project builds a repeatable content-opportunity scoring workflow to help answer:

> Which content observations should be reviewed first?

The system combines observable search-performance and content signals and evaluates a Random Forest classifier for identifying declining observations.

The output is intended as a **review-prioritization tool**, not an automatic content decision system.

It does not predict Google's ranking algorithm and does not claim that refreshing a page will cause better performance.

## 2. Who Is It For?

This project is designed for:

- SEO and content teams
- Content strategists
- Search-performance analysts
- Data scientists working with content analytics
- Teams managing large content portfolios

The main user is a reviewer who needs to decide **where to investigate first**.

## 3. Research Question

Which observable search-performance signals can be combined into a repeatable score to identify content pages that should be prioritized for refresh or human review?

## 4. Data

The analysis uses the FlyRank ML Internship warehouse release, centered on:

`fact_content_daily_performance`

The dataset contains daily content-page observations with search and analytics measurements.

Primary signal families include:

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

### Public-Safety

The public research artifact excludes:

- Client names
- Identifying information
- Domains
- URLs
- Private queries
- Credentials
- Raw warehouse exports
- Client and content identifiers as predictive features

Sparse AI-referral fields were not used as primary opportunity signals where their density was insufficient.
## 5. Methodology

The workflow is:

1. Define the content-review decision.
2. Inspect the available signals.
3. Build observable features.
4. Define declining vs non-declining observations.
5. Establish a majority-class baseline.
6. Train a Random Forest classifier.
7. Compare random and client-grouped validation.
8. Audit for target leakage.
9. Translate evidence into ranked review priorities.

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

### Target

The predictive target distinguishes:

- Declining observations
- Non-declining observations

The label is based on the available trend-direction field.

It is treated as an observed performance outcome, not as proof that a page needs a refresh.

### Leakage Checks

The following target-derived or closely related fields were excluded:

- `trend_direction`
- `trend_pct`
- `impressions_last_30d`
- `clicks_last_30d`
- `sessions_last_30d`
- `impressions_prev_30d`
- `clicks_prev_30d`
- `sessions_prev_30d`

Identifiers such as:

- `client_id`
- `content_id`

were also excluded.

## 6. Validation

The original random split produced:

**70.13% accuracy**

Because observations from the same client may be related, a more conservative client-grouped validation was also performed using `GroupShuffleSplit`.

The client-grouped evaluation kept records from the same client together.

This produced:

**57.24% accuracy**

## 7. V2 Evaluation Results

| Approach | Validation | Accuracy |
|---|---|---:|
| Majority-class baseline | Client-grouped | 54.21% |
| Random Forest | Client-grouped | 57.24% |
| Random Forest | Original random split | 70.13% |

### Main Result

The Random Forest achieved **57.24% accuracy** under client-grouped validation compared with **54.21%** for the majority-class baseline.

That is an improvement of approximately:

**+3.03 percentage points**

The original random split produced 70.13%, which is substantially higher.

The grouped result is therefore treated as the more conservative estimate of performance on unseen clients.
## 8. Architecture

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
             +----------+----------+
             |                     |
             v                     v
       Signal Analysis       Leakage Audit
             |                     |
             +----------+----------+
                        |
                        v
                Random Forest
                        |
             +----------+----------+
             |                     |
             v                     v
       Random Split        Client-Grouped Split
             |                     |
             v                     v
          70.13%                57.24%
                        |
                        v
               Review Priorities
                        |
                        v
                Human Reviewer
