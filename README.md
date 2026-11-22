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






