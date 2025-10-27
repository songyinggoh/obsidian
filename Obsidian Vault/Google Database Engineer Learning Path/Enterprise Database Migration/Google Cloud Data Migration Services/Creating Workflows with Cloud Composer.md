> For coordinating larger workflows (ETL pipeline only as part of a multi-part process) that are less suited for Cloud Data Fusion

## Diagram
![[Screenshot 2025-10-07 011647 1.png]]

# Airflow in Google Cloud Composer:

![[Screenshot 2025-10-07 012034.png]]
Each Airflow environment hosts separate web server to:
	access Airflow UI
	access folder created in Cloud Storage for pipeline Directed Acyclic Graph (DAGs) where .py code files for pipelines are placed

# DAG folder 

>A Cloud Storage bucket to load pipeline code 

To run workflow: 
1. save code file into Cloud Storage bucket
2. Airflow detects new file and runs on specified schedule
![[Screenshot 2025-10-07 012522 1.png]]


# .py workflow files

Example:
![[Screenshot 2025-10-07 013312.png]]