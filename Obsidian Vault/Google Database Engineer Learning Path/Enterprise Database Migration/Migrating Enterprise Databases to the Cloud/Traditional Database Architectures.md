## Client-server databases 
have widespread use and they provide business logic (constraints and relationships, procedures, triggers)

The database server manages transactions and enforcing rules where the logics are centrally located and shared by clients

Fast and secure, they are the preferred architecture for many DataBase Architectures


## 3/n-tier database 
Most of the business logic is in the application code
Fewer dependents because clients connect to the application server, which connect to the application database


## Service-oriented database
the easiest to move to the cloud as the database and other details are hidden behind a service layer, which can be used to synchronize the source andtarget databases during migration