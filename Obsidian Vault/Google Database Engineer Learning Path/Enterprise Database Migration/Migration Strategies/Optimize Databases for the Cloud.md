# Redesigning applications using microservice architecture
More appropriate for each microservice and less costly

![[Screenshot 2025-09-23 181954.png]]
Large monolithic applications are broken down into smaller independent services, where each of them should be programmed, deployed, and versioned separately
Large databases shall be broken down into smaller databases for each microservice

## Determining microservice boundaries with monolithic database analysis
Finding loosely-related data to assist in splitting functionality without negatively impacting applications

| Existing database data                   | Potential Google Cloud database solution | Considerations                                                                                                                                                         |
| ---------------------------------------- | ---------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Binary data stored in tables             | Cloud Storage                            | Significant cost savings                                                                                                                                               |
| Web application and session data         | Firestore                                | Relational database required, cheaper, and easier to program for web developers                                                                                        |
| OnLine Analytical Processing (OLAP) data | BigQuery                                 | User-friendly, massively scalable, plentiful and inexpensive storage                                                                                                   |
| Transactional data                       | Cloud SQL/Spanner                        | If using Oracle on Bare Metal Solution/SQL server on Compute Engine to refactor application to use managed database services. Lower administrative and licensing costs |


![[Screenshot 2025-09-23 183707.png]]