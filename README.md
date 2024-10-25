# ETL-PROJECT-BIGQUERY-covid19_vaccination_search_insights

BigQuery to GCS Data Pipeline

This project retrieves data from a public BigQuery dataset, performs data cleaning using Pandas, and exports the cleaned data to a Google Cloud Storage (GCS) bucket in both CSV and JSON formats.

Table of Contents

•	Setup and Prerequisites 
•	Querying the BigQuery Dataset
•	Data Cleaning with Pandas 
•	Exporting Data to GCS 
•	How to Run the Code 
•	Project Files 
•	Conclusion 

Setup and Prerequisites
Before running the code, ensure the following tools and configurations are in place:
1.	Google Cloud SDK: Installed and authenticated on your system.
2.	Google Cloud APIs Enabled: Ensure that both BigQuery and Cloud Storage APIs are enabled on your Google Cloud project.
3.	GCS Bucket: Create a Google Cloud Storage bucket to store the final output files.
4.	Python Environment: Set up an environment with the following libraries:
o	pandas
o	google-cloud-bigquery
o	google-cloud-storage
