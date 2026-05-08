# Contribution Record

This document records the specific role-based team contributions for the current project state. The responsibilities have been distributed among the team members according to the core phases of the project, from theoretical definition and data engineering to statistical modeling and machine learning deployment.

## ZiRui Zhang

**Primary responsibilities: Project Vision, Theoretical Framework & Conclusion**
* Defined the project title, overall vision, and project scope.
* Formulated the problem definition and provided the theoretical background for the research.
* Authored the project summary and conclusion, and coordinated overall team collaboration and workflow structure.

## JiaYu Chen

**Primary responsibilities: Data Engineering & Pipeline Construction**
* Designed and implemented the end-to-end data engineering pipeline.
* Managed data acquisition, external data-source setup, and data cleaning processes (including the S&P 500 company universe cache, price cache, and EDGAR cache).
* Matched company identifiers and assembled the final firm-year panel by joining company metadata, price features, and ESG signals.

## QiuYu Wang

**Primary responsibilities: Exploratory Data Analysis (EDA) & Risk Visualization**
* Led the data exploration phase, focusing on data distribution analysis and risk visualization.
* Designed the data-usability analysis layer for the merged panel.
* Produced coverage summaries, missingness diagnostics, sector comparisons, and visual interpretations of the dataset.

## LingHe Zhang

**Primary responsibilities: Statistical Modeling, Inference & Real-World Testing**
* Conducted statistical modeling and causal inference analysis.
* Implemented the calculation and analysis of the NCSKEW (negative coefficient of skewness) metric as a core indicator.
* Co-led the real-world testing phase, evaluating model performance and generating the Top 20 crash-risk company rankings (in collaboration with JingYi Guo).

## JingYi Guo

**Primary responsibilities: Machine Learning System & Real-World Testing**
* Built the machine learning architecture, developing a recall-optimized early warning system for crash risk.
* Implemented model training, threshold tuning, and external validation tasks for the prediction datasets.
* Co-led the real-world testing phase, evaluating model performance and generating the Top 20 crash-risk company rankings (in collaboration with LingHe Zhang).