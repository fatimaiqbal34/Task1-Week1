# FlyRank ML Internship
## Final Retrospective

### Content Opportunity Scoring for Search Performance Review

**Fatima Iqbal**  
**AI Software Engineer | Data Science | Machine Learning**  
**FlyRank ML Internship — Final Checkpoint (FL-10)**

## Looking Back from Week 1

When I started the FlyRank ML Internship, my main goal was to improve my practical skills in Artificial Intelligence, Machine Learning, data analysis, and building projects that could be explained and used by other people. I wanted to move beyond only writing code and learn how to take a real data problem from exploration to modelling, evaluation, documentation, and a final deliverable.

My capstone project focused on Content Opportunity Scoring for Search Performance Review. The goal was to help content teams identify which content pages should receive human review first based on observable search-performance signals. Instead of trying to predict Google's ranking algorithm, I treated the project as a decision-support system that could help reviewers decide where to investigate first.

## What Changed During the Project

One of the biggest changes in how I work is that I now pay much more attention to problem definition and evaluation design before focusing on the model.

Initially, it is easy to look at a dataset and immediately think about training a machine-learning model. During this project, I learned that understanding what the model is actually supposed to support is more important. I defined the decision first: which pages should be prioritized for review?

I also learned the importance of data leakage checks. Because the target was based on trend_direction, I had to make sure that target-derived or closely related fields were not accidentally being used as predictive features. I excluded fields such as trend_direction, trend_pct, recent and previous 30-day outcome fields, and identifiers such as client_id and content_id.

Another important change was my understanding of validation. The original random split produced 70.13% accuracy, which initially appeared strong. However, observations from the same client could appear in both training and testing data. I therefore used a client-grouped validation approach with GroupShuffleSplit. The result dropped to 57.24%, compared with a 54.21% majority-class baseline.

This was an important lesson for me: a higher model score does not automatically mean a better or more trustworthy model. The way data is split can significantly affect the evaluation. The client-grouped result gave me a more conservative view of how the model might perform on unseen clients.

## What I Would Build Next

If I continued this project, I would improve the system in several ways.

First, I would collect more historical observations and test the model across multiple time periods and client groups. This would provide a stronger understanding of how well the approach generalizes.

Second, I would improve the opportunity scoring layer so that reviewers receive not only a priority score but also clear reason codes explaining why a page was ranked highly. This would make the system easier for a content or SEO team to understand and use.

Third, I would build a simple interactive dashboard where a content reviewer could select a page and see its search visibility, traffic, engagement, content age, and recommended review priority in one place.

I would also investigate additional evaluation metrics beyond accuracy, such as precision, recall, F1-score, and ranking-based metrics, because the practical goal of the project is prioritization rather than simply classifying observations correctly.

## Three Transferable Things I Learned

### 1. Define the Decision Before Building the Model

Machine learning should solve a clearly defined problem. A model is more useful when its output connects directly to a real decision. Defining the review-prioritization question first helped me understand what the model needed to provide and prevented the project from becoming only a modelling exercise.

### 2. Validate Honestly

I learned that validation design matters. The original random split produced 70.13% accuracy, while client-grouped validation produced 57.24%. Comparing these results showed me that the way data is divided can have a major effect on reported performance. I learned to prefer the more conservative evaluation when it better represents the real-world use case.

### 3. Communicate Limitations Clearly

A strong technical project is not only about achieving a high score. It is also about explaining what the model can and cannot claim. My final system is therefore presented as directional decision support, not as a prediction of Google's ranking algorithm and not as proof that refreshing content will improve performance.

## Final Reflection

The most important outcome of this internship is that I became more comfortable treating machine learning as an end-to-end engineering and decision-support process. I learned to move from a business question to data inspection, feature construction, leakage auditing, modelling, validation, interpretation, documentation, and communication.

Compared with Week 1, I now approach ML projects with a stronger focus on reproducibility, honest evaluation, and practical usefulness. I also learned that building a useful machine-learning project requires more than selecting an algorithm. It requires understanding the data, questioning assumptions, checking for leakage, choosing an appropriate validation strategy, and communicating results responsibly.

The capstone is not a perfect prediction system, and I do not present it as one. Its value is in providing a repeatable workflow for reducing the search space and helping content teams decide where to investigate first.

This internship has given me a stronger foundation for future work in AI, Machine Learning, and Data Science. Going forward, I want to build systems that are not only technically functional but also reproducible, explainable, carefully evaluated, and useful for real-world decisions.
