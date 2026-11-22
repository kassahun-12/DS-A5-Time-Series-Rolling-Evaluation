DS-A5: Time-Series Rolling Evaluation Lab

DS-A5: Repeated-Acquisition Fingerprint Image Quality Proxy Estimation and Cross-Database Generalization Using FVC2004
Student: Kassahun Azezew Ayidagn
Course: Data Science
Dataset: FVC2004 DB1_B and DB2_B
GitHub repository: https://github.com/kassahun-12/DS-A5-Time-Series-Rolling-Evaluation
Evaluation alternative: Instructor-approved repeated-acquisition evaluation
1. Introduction and research question
This study investigates whether image-derived characteristics can estimate a fingerprint image's sharpness proxy and whether a machine-learning model generalizes across two FVC2004 databases.
The research question is:
How accurately can image-derived features estimate a fingerprint sharpness proxy, and how well does the model generalize across DB1_B and DB2_B?
The original rolling-origin time-series approach was not directly applicable because the available fingerprint images did not provide verified chronological timestamps. Following instructor approval, the study used repeated-acquisition grouped validation and cross-database evaluation as an alternative.
2. Dataset and feature availability
The experiment used 160 fingerprint images:
•	DB1_B: 80 images.
•	DB2_B: 80 images.
•	10 finger identifiers per database.
•	8 impressions per finger.

Five input features were extracted:
1.	Mean intensity.
2.	Contrast.
3.	Gradient mean.
4.	Gradient standard deviation.
5.	Orientation consistency.

The prediction target was sharpness, calculated as an image-derived proxy. It was not an expert-annotated fingerprint-quality score.
Data validation identified no missing feature values and no duplicate database–finger–impression identifiers.
3. Methodology
3.1 Models
Two regression approaches were evaluated:
•	Baseline: DummyRegressor using the training-set mean.
•	Random Forest: 300 trees, minimum leaf size of 2, and random state 42.

A median imputer was included in the Random Forest pipeline. Preprocessing was fitted within each training fold to avoid fitting the imputer on held-out observations.
3.2 Grouped validation
Five-fold GroupKFold validation was used. The grouping variable combined database and finger identifier, ensuring that impressions belonging to the same finger group were not divided between training and testing.
Each fold contained 128 training images and 32 test images, with 16 training groups and 4 test groups.
3.3 Cross-database evaluation
Two additional experiments assessed database transfer:
•	Train on DB1_B and test on DB2_B.
•	Train on DB2_B and test on DB1_B.

Performance was assessed using mean absolute error (MAE), root mean squared error (RMSE), and coefficient of determination (R²).
4. Experimental results
4.1 Grouped five-fold validation
Model	Mean MAE	Mean RMSE	Mean R²
Baseline	0.000502	0.000603	-0.003108
Random Forest	0.000094	0.000122	0.956353

The Random Forest substantially outperformed the baseline in all five folds. Its average MAE was approximately 81.3% lower than the baseline MAE.
These results demonstrate strong performance for estimating the image-derived sharpness proxy under the grouped validation design.
4.2 Performance by database
Database	Model	MAE	RMSE	Bias
DB1_B	Baseline	0.000454	0.000563	-0.000184
DB1_B	Random Forest	0.000094	0.000117	0.000004
DB2_B	Baseline	0.000551	0.000654	0.000184
DB2_B	Random Forest	0.000093	0.000134	-0.000012

The Random Forest achieved similar MAE values for both databases in the grouped evaluation. Its bias was close to zero in both cases.
4.3 Cross-database generalization
Training database	Test database	Model	MAE	RMSE	R²
DB1_B	DB2_B	Baseline	0.000619	0.000726	-0.346560
DB1_B	DB2_B	Random Forest	0.000471	0.000580	0.141149
DB2_B	DB1_B	Baseline	0.000528	0.000648	-0.477878
DB2_B	DB1_B	Random Forest	0.000502	0.000611	-0.313007

Cross-database performance was substantially weaker than grouped-validation performance. In the DB1_B-to-DB2_B direction, the Random Forest achieved a positive R² of 0.141. In the reverse direction, its R² was negative (-0.313), indicating performance worse than predicting the test-set mean.
These results demonstrate that strong within-dataset performance does not necessarily guarantee cross-database generalization.
5. Distribution-shift analysis
5.1 Input features
The largest absolute standardized mean differences were:
Feature	Absolute SMD
Orientation consistency	6.498
Mean intensity	2.889
Gradient standard deviation	2.048
Gradient mean	1.511
Contrast	0.411

These differences indicate substantial separation between several input-feature distributions.
5.2 Target distribution
Statistic	DB1_B	DB2_B
Number of images	80	80
Mean	0.001685	0.001317
Standard deviation	0.000536	0.000630
Median	0.001688	0.001236
Minimum	0.000580	0.000264
Maximum	0.002917	0.002926

The target standardized mean difference was 0.630, indicating a moderate difference between the database means.
Together, the input-feature and target comparisons provide evidence consistent with dataset shift. Differences in image dimensions and acquisition characteristics may contribute, although the available experiments do not establish the individual causes of the shift.
6. Error analysis
The ten largest absolute Random Forest prediction errors were examined.
The largest absolute error was 0.000482, for DB2_B, finger 105, impression 3.
Six of the ten largest errors came from DB2_B, while four came from DB1_B. Eight of the ten errors were negative, indicating underestimation. Finger 106 in DB2_B appeared three times in the top-ten error list.
Several observations with high actual sharpness-proxy values were underestimated. This suggests that some high-value observations were more difficult for the model to estimate. The results do not establish the underlying causes of these individual errors.
7. Monitoring and fallback strategy
For future fingerprint acquisition batches, the following monitoring strategy is proposed.
Input monitoring: Monitor mean intensity, contrast, gradient mean, gradient standard deviation, and orientation-consistency distributions. Compare new batches against the training-data distributions.
Prediction monitoring: Examine predicted sharpness-proxy distributions and flag unusual values or substantial distribution changes.
Performance monitoring: When reference measurements become available, calculate MAE, RMSE, and bias for each evaluated batch. Compare these results with the model's validation performance.
Fallback strategy: If performance deteriorates or a new database differs substantially from the training data, flag predictions for review. Use a separately validated database-specific model or a simple baseline while investigating the shift.
Retraining strategy: Retrain or recalibrate only when representative new data and appropriate reference measurements are available. Evaluate the updated model on held-out finger groups before deployment.
No numerical monitoring thresholds are claimed because these have not yet been established through additional validation.
8. Limitations
1.	The experiment used only 160 images from two database subsets.
2.	The sharpness target was an engineered image-derived proxy, not an independently annotated fingerprint-quality score.
3.	Input features and target were calculated from the same images, so the regression task measures estimation of a related image-derived quantity rather than prediction of an independent future outcome.
4.	The orientation-consistency feature is a gradient-based proxy and has not been validated as a fingerprint ridge-flow quality measure.
5.	DB1_B and DB2_B differ in image dimensions and potentially other acquisition characteristics. Therefore, cross-database performance differences cannot be attributed exclusively to sensor differences.
6.	The GroupKFold evaluation is not temporal rolling-origin validation. Impression identifiers were not treated as chronological timestamps.
7.	The small number of finger groups limits the strength of generalization claims.
9. Conclusion
The Random Forest substantially outperformed the mean baseline in grouped validation, achieving a mean MAE of 0.000094 and mean R² of 0.956353. However, cross-database experiments showed considerably weaker performance, particularly when training on DB2_B and testing on DB1_B.
Feature and target distribution comparisons revealed differences between the two databases, providing evidence consistent with dataset shift. The results highlight the importance of evaluating models across acquisition databases rather than relying exclusively on within-dataset validation.
The experiment provides a useful initial investigation of image-derived sharpness-proxy estimation and cross-database generalization. Further work should use independently annotated fingerprint-quality labels, more acquisition databases, and larger datasets.
10. Reproducibility and submission artifacts
The GitHub repository should contain:
•	DS_A5_FVC2004.ipynb — complete executed notebook.
•	README.md — project description, methodology, environment, and instructions.
•	requirements.txt — required Python packages.
•	data/ — dataset access instructions; do not redistribute restricted data without permission.
•	results/fold_metrics.csv
•	results/predictions.csv
•	results/modeling_dataset.csv
•	results/sensor_performance.csv
•	results/fold_sensor_performance.csv
•	results/cross_sensor_results.csv
•	results/feature_distribution_comparison.csv
•	results/worst_prediction_errors.csv
•	figures/ — actual-versus-predicted, residual, feature-distribution, and target-distribution plots.
•	report.pdf — final report.
•	AI_USE.md — transparent record of AI assistance and student verification.

Before submission, restart the notebook runtime and run all cells from beginning to end. Verify that the saved results can be regenerated and that the notebook contains no unresolved errors.
