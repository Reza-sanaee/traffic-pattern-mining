# Large-Scale Traffic Pattern Mining and Detection-Consistency Analysis

A PySpark batch pipeline processes anonymized plate observations, reorganizes storage, constructs time-window baskets, mines frequent camera itemsets, estimates detection inconsistencies, and finds similar vehicle route sets with MinHash. The archived notebook contains 32,769,490 input rows; inferred missing observations are hypotheses rather than verified hardware faults.

## Technical highlights

- Built PySpark batch analytics for over 32 million archived traffic observations using Parquet, FP-Growth, and MinHash route-set similarity.
- Derived camera-consistency indicators from frequent co-observation patterns and compared CSV/Parquet query timings.

## Explore the project

- [Source and execution guide](code/RUNNING.md)
- [Technical report (PDF)](report/Report_EN.pdf)
- [Technical report (Markdown)](report/Report_EN.md)
- [Assignment/specification (PDF)](assignment/Assignment_EN.pdf)

## Reproduction and attribution

Read the execution guide and report before running the code. Dependencies, input data, licensed tools, and hardware requirements vary by project. Recorded results are distinguished from rerun results in the accompanying documentation. Team and third-party source attribution in the reports and source headers is retained. This portfolio does not claim independent authorship of supplied frameworks or dependencies.
