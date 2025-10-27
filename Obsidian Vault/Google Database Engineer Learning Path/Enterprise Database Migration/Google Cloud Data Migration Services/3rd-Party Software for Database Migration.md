# migVisor
>Automated assessment tool

Assists in assessing and planning as part of Google's implementation methodology
Finds dependencies and dependents and makes recommendations for which Google databases and APIs to pick as targets
Analyses source database configs, attributes, schema objects and proprietary features, which helps to build initial plan for a successful migration
# striim
>Deployment

Allows to do online database migrations and handles transferring and synchronisation of databases
Streams data between a target and a source
Data transformations within data pipelines using SQL

1. Initial data transfer done to move most of historical data
2. Striim captures data changes on source in real time and synchronizes with new target database
	1. During sync period, migrate clients to new database
3. Eventually all old databases will have no clients and can be retired

Use striim to transfer data between database types