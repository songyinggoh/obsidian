>Extract, Transform, Load (ETL) jobs are data processing jobs which takes data from a source, alters it , and saves it to a sink/target allowed by Data Processing Pipelines

# Cloud Data Fusion (CDF)
>A fully managed, cloud-native enterprise data integration service for quickly building and managing data pipelines

- Cleanse, match, de-dupe, blend, transform, partition, transfer, and standardize data
- Visual interface for drag-and-drop building of pipelines
- Test, debug, deploy pipelines using small subset of data
- Run ETL jobs at scale on Google Cloud

CDF pipelines provide an interface for building ETL jobs: connect to source, transform source data, write to sink
Wranglers provide visual interface for transforming data (drag-and-drop objects to manipulate data)

## Pipelines

![[Screenshot 2025-10-07 011258.png]]
>A series of stages arranged in a Directed Acyclic Graph (DAG)