# ETL-PROJECT-BIGQUERY-covid19_vaccination_search_insights

# BigQuery to GCS Data Pipeline

This project retrieves data from a public BigQuery dataset, performs data cleaning using Pandas, and exports the cleaned data to a Google Cloud Storage (GCS) bucket in both CSV and JSON formats.

# Table of Contents

•	Querying the BigQuery Dataset

•	Data Cleaning with Pandas 

•	Exporting Data to GCS 

•	How to Run the Code 

•	Project Files 

•	Conclusion 


# Python Environment: 

Set up an environment with the following libraries:

    o	pandas
    o	google-cloud-bigquery
    o	google-cloud-storage



# Install Required Python Libraries

Run the following command to install the necessary packages:

      bash
      pip install pandas google-cloud-bigquery google-cloud-storage
      

# Creating a GCS Bucket

To store files in Google Cloud Storage, you'll need to create a GCS bucket. Follow these steps:

1.	Log into Google Cloud Console:

  	  o	Go to the Google Cloud Console.

3.	Navigate to Cloud Storage:

      o	From the main menu, go to Storage > Browser.

4.	Create a New Bucket:

      o	Click on the Create Bucket button.

5.	Configure Bucket Settings:

      o	Name Your Bucket: Enter a globally unique name (e.g., my-data-pipeline-bucket).

      o	Choose Location Type:

       	Region: Stores data in a specific region.

       	Multi-region: Stores data across multiple regions for higher availability.

      o	Select a Default Storage Class:

       	Choose Standard for frequent access.

       	Nearline or Coldline are cheaper options if you access data infrequently.

      o	Set Access Control:

       	Fine-grained: Allows individual object permissions.

       	Uniform: Manages permissions at the bucket level (recommended for simplicity).

      o	Click Create.

6.	Assign Permissions:

      o	After creating the bucket, navigate to the Permissions tab in your bucket’s settings.

      o	Click Grant Access and add the required roles:
        	Storage Object Admin: For full control over objects within the bucket.
        	If using a service account, add it here by entering its email address.

# Setting Up Authentication for GCS

1.	Service Account Authentication (recommended):

      o	Go to IAM & Admin > Service Accounts in Google Cloud Console.

      o	Click Create Service Account and enter a name and description.

      o	In the Role dropdown, add the following roles:

  	   	BigQuery User: Allows querying BigQuery.

  	   	Storage Object Admin: Provides access to upload data to GCS.

      o	Download the JSON key for this service account and save it to your machine.

2.	Authenticate Locally:

      o	Set the environment variable to point to your JSON key file:

        Bash
        export GOOGLE_APPLICATION_CREDENTIALS="path/to/your-service-account-key.json"

Now, you’re ready to use your new bucket for storing cleaned data.


# Querying the BigQuery Dataset

The following code snippet retrieves data from the BigQuery dataset using SQL. It uses google.cloud.bigquery to establish a connection and query the data:

# python code
    from google.cloud import bigquery
    
    client = bigquery.Client()
    query = """ SELECT * FROM `bigquery-public-data.dataset_name.table_name`
    LIMIT 1000 """
    query_job = client.query(query)           # Execute the query
    data = query_job.to_dataframe()           # Convert the results to a Pandas DataFrame
    print(data.head())                        # Preview the data

# Data Cleaning with Pandas

After retrieving the data, we perform several cleaning steps using Pandas to prepare the data for analysis and storage:

  1.	Remove Duplicates: Eliminate duplicate rows.

  2.	Drop Unnecessary Columns: Exclude columns that aren’t essential to the analysis.
  
  3.	Handle Missing or Null Data: Replace missing values with NaN or fill with default values.

  4.	Standardize Text Case: Convert string fields to lowercase or uppercase for consistency.

  5.	Add Timestamp Column: Add a new column with the current date and time.

  6.	Extract Patterns Using Regex: Use regular expressions to extract specific patterns from fields.

  7.	Convert Data Types: Change field data types as needed for accuracy and efficiency.

Here’s an example of the data cleaning code:

    python code
    import pandas as pd
    import numpy as np

    # Remove duplicates
    data = data.drop_duplicates()

    # Drop unnecessary columns
    data = data.drop(['column_to_drop'], axis=1)

    # Handle missing values (fill with NaN or a default value)
    data = data.fillna(np.nan)

    # Convert fields to lowercase
    data['field_name'] = data['field_name'].str.lower()

    #  Add a new timestamp column
    data['timestamp'] = datetime.now()

    # Extract patterns using regex
    data['extracted_field'] = data['field_name'].str.extract(r'pattern')

    # Convert data types
    data['numeric_field'] = data['numeric_field'].astype(float)

    print(data.info())  # Check data structure

    # Save cleaned data locally as 'Covidcoun.csv'
    data.to_csv('Covidcoun.csv', index=False)

# Exporting Data to GCS

Once the data has been cleaned, it is exported to a Google Cloud Storage (GCS) bucket in both CSV and JSON formats:

      Python code
    from google.cloud import storage

    def upload_to_gcs(bucket_name, file_name, file_content, content_type):
    client = storage.Client()
    bucket = client.bucket(bucket_name)
    blob = bucket.blob(file_name)
    blob.upload_from_string(file_content, content_type=content_type)
    print(f"File {file_name} uploaded to {bucket_name}.")

    # Load cleaned data from 'Covidcoun.csv'
    cleaned_data = pd.read_csv('Covidcoun.csv')

    # Convert DataFrame to CSV and JSON formats
    csv_content = cleaned_data.to_csv(index=False)
    json_content = cleaned_data.to_json(orient='records')

    # Specify your GCS bucket name
    bucket_name = "your-gcs-bucket-name"
    upload_to_gcs(bucket_name, "Covidcoun.csv", csv_content, "text/csv")
    upload_to_gcs(bucket_name, "Covidcoun.json", json_content, "application/json")



# How to Run the Code

1.	Set Up Google Cloud Authentication: Ensure that you’re authenticated with Google Cloud:

        Bash
        gcloud auth application-default login

2.	Run the Python Script: Execute the script from your Python environment. Ensure that the dataset, table names, and GCS bucket names are correctly specified.

3.	Verify the Data in GCS: Check your GCS bucket in the Google Cloud Console to confirm that both CSV and JSON files were successfully uploaded.

# Project Files

•	covid19_vaccination_search_insights.csv: Original dataset queried from BigQuery.

•	Covidcoun.csv: Cleaned data prepared for upload to GCS.

•	covid.ipynb: Jupyter notebook containing the complete code for querying, cleaning, and exporting the data.

# Conclusion

This project demonstrates the process of querying a BigQuery dataset, performing data cleaning using Pandas, and exporting the results to GCS. This pipeline can be extended with additional transformations or automated using Google Cloud services like Cloud Functions or Cloud Run.


