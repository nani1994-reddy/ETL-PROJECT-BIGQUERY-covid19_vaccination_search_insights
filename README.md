# COVID-19 Vaccination Insights ETL Project
   This project involves extracting COVID-19 vaccination search insights from Google BigQuery, transforming the data for analysis, and loading it to Google Cloud Storage (GCS).

# Project Overview

The goal of this project is to:

   1 Extract COVID-19 vaccination search insights data from a public dataset in Google BigQuery.

   2 Perform data cleaning and transformations using pandas.

   3 Upload the transformed dataset to a specified Google Cloud Storage bucket.

 # Prerequisites

  Required Libraries

   Install the following libraries using pip:

	bash
		pip install google-cloud-bigquery pandas db_dtypes numpy google-cloud-storage

# Setup Google Cloud Credentials

-   Service Account: Make sure you have a service account with access to BigQuery and GCS.
	
-   Credentials JSON file: Download the service account key JSON file and update the path in the code.

# Code Overview

1 Setup Environment: Set up your Google Cloud credentials.

2 Data Extraction from BigQuery:

  -  Initialize the BigQuery client.
  - Query COVID-19 vaccination insights data and load it into a DataFrame.
	
3 Data Transformation:

  - Fill missing values for certain columns with 'unknown'.
	- Drop unneeded columns (sub_region_2_code, sub_region_3, sub_region_3_code).
	- Rename columns for consistency.
	-  Convert data types for analysis.
	- Replace "unknown" values with NaN for specific columns and change them to float where applicable.

4 Upload to Google Cloud Storage:
	- Specify the GCS bucket and upload the transformed file.

# Code

Set up Google Cloud Credentials
		
		python

			import os
			import warnings
			from google.cloud import bigquery
			import pandas as pd
			import numpy as np

			warnings.filterwarnings("ignore")

			# Set the path to your service account key
			service_account_key_path = r'C:/Users/Personal/Miracle/ETL/project1-439615-3847e8ef9c6a.json'
			os.environ['GOOGLE_APPLICATION_CREDENTIALS'] = service_account_key_path

# BigQuery Data Extraction

		python

			# Initialize BigQuery Client
			client = bigquery.Client()

			# Query data from BigQuery
			query = """
			SELECT * FROM `bigquery-public-data.covid19_vaccination_search_insights.covid19_vaccination_search_insights` LIMIT 1000
			"""

			# Convert query results to a pandas DataFrame
			df = client.query(query)
			data = df.to_dataframe()

# Data Transformation
		
		python

			# Save DataFrame to CSV
			data.to_csv('covid19_vaccination_search_insights.csv', index=False)
			data = pd.read_csv('covid19_vaccination_search_insights.csv')

			# Fill missing values with 'unknown' for certain columns
			data['sub_region_1'].fillna('unknown', inplace=True)
			data['sub_region_1_code'].fillna('unknown', inplace=True)
			data['sub_region_2'].fillna('unknown', inplace=True)
			data['sub_region_2_code'].fillna('unknown', inplace=True)
			data['sub_region_3'].fillna('unknown', inplace=True)
			data['sub_region_3_code'].fillna('unknown', inplace=True)
			data['sni_vaccination_intent'].fillna('unknown', inplace=True)
			data['sni_safety_side_effects'].fillna('unknown', inplace=True)

			# Drop unnecessary columns
			drop_columns = ["sub_region_2_code", "sub_region_3", "sub_region_3_code"]
			data = data.drop(columns=drop_columns)

			# Rename columns for clarity
			data.rename(columns={
    			"country_region_code": "Country_region_code",
    			"date": "Date",
    			"sub_region_1": "State",
    			"sub_region_1_code": "StateCode",
   			"sub_region_2": "County",
    			"country_region": "Country"
			}, inplace=True)

			# Convert 'Date' column to datetime
			data['Date'] = pd.to_datetime(data['Date'])

			# Convert specific columns to string type
			data['Country'] = data['Country'].astype(str)
			data['Country_region_code'] = data['Country_region_code'].astype(str)
			data['State'] = data['State'].astype(str)
			data['StateCode'] = data['StateCode'].astype(str)
			data['County'] = data['County'].astype(str)
			data['place_id'] = data['place_id'].astype(str)

			# Replace 'unknown' with NaN and convert to float where necessary
			data['sni_vaccination_intent'] = data['sni_vaccination_intent'].replace('unknown', np.nan).astype(float)
			data['sni_safety_side_effects'] = data['sni_safety_side_effects'].replace('unknown', np.nan).astype(float)


#  Upload to Google Cloud Storage

		python

			from google.cloud import storage

			# Initialize Google Cloud Storage client
			client = storage.Client()

			# Specify the bucket and file details
			bucket_name = 'covid24'
			source_file_name = 'Covidcoun.csv'
			destination_blob_name = 'smallcovid.csv'

			# Upload to GCS
			bucket = client.get_bucket(bucket_name)
			blob = bucket.blob(destination_blob_name)
			blob.upload_from_filename(source_file_name)

			print(f'File {source_file_name} uploaded to {bucket_name}/{destination_blob_name}.')

# Additional Notes

Update the bucket_name, source_file_name, and destination_blob_name variables as per your project configuration.

Ensure Google Cloud SDK is installed and authenticated if required.
