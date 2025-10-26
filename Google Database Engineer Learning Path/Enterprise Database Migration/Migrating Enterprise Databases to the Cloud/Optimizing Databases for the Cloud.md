## Microservice architectures
Large apps are broken down into microservices, each of which are responsible for its own data. As a result, when optimizing for the cloud, the databases are broken down into smaller pieces
Designed to minimize dependencies between them
data passed in between them is in the form of xml/html format

### Benefits
Maintenance costs. On the other hand, entire apps have to choose a single relational database, which can be costlier and require more administration
NoSQL databases or data warehousing may be used separately.

### Database considerations for microservices

#### Relational
Used when strong schemas and consistencies are required

---
Google Cloud Platform's solutions:
![[Screenshot 2025-09-15 015453.png]]
Spanner is massively scalable and globally available. Users may also enjoy low latencies.
#### NoSQL (Unstructured)
When flexibility is required or when a schema-less database can be tolerated

---
Google Cloud Platform's solutions:
![[Screenshot 2025-09-15 015459 1.png]]
Firestore is completely managed, highly scalable, and inexpensive
Memorystore provides a managed Redis database solution
BigTable is a managed wide-column database similar to Cassandra

#### Data Warehouse
For big data analytics or object storage

---

Google Cloud Platform's solutions:
![[Screenshot 2025-09-15 015504 1.png]]
Cloud Storage is inexpensive