# 5 steps to accomplish database migration:
- [ ] Transfer data from old to new
- [ ] Resolve data integration issues
- [ ] Validate data migration
- [ ] Promote the new database
- [ ] Retire old database


![[Screenshot 2025-09-21 165245.png]]

# Using Cloud Storage as staging area when migrating data 
## gcloud Storage
for relatively small amounts of data
use gcloud cmd CLI 
familiar to Linux file system commands 

## Automated Transfer Services
may be used to transfer large amounts of data over the internet from AWS S3 bucket, Azure storage, or other Cloud Storage buckets


##  Transfer Appliance
a physical storage device to copy data to 
recommended for data size >20TB (would take longer than 1 week)
- [ ] Google sends appliance, and manually connect to network
- [ ] Copy data, encrypt with generated encryption key
- [ ] Send generated encryption key to Google and then copy data into Cloud Storage bucket and use encryption key to decrypt data

---

# Altering database structure / Moving to different database
  
> for automation of altering data before loading to new database

## Google ETL pipeline
![[Screenshot 2025-09-21 174415.png]]

![[Screenshot 2025-09-21 174427.png]]

![[Screenshot 2025-09-21 174442.png]]

## 3rd-party ETL pipeline
Oracle
Windows
## Supplier-specific ETL pipeline

---

# Validating data
> Use automated test framework for lower-level tests
## Unit tests 
> Used to verify individual components (functions, properties, stored procedures, triggers, etc.)

## Integration tests 
> Ensuring working compatibility of different components, so that clients can use services -> databases

## Regression tests 
> Ensuring new platform is consistent with older one (new functionality does not break older code)

---

# Promoting Database using Site Reliability Engineering Techniques
> Ensures that new server works before switching over

## Blue/Green deployment 
Running new and old environments simultaneously
> A strategy designed to minimize downtime and reduce risk when deploying new versions of an application
- [ ] Test Green (new) environment, while Blue currently serves all user traffic 
- [ ] When Green is fully tested, perform traffic switch 
- [ ] Green is now the production environment
	- [ ] Eventually, Blue can be turned off
Should errors arise, Green can be switched back to Blue 


## Canary deployment 
Running new and old environments simultaneously
- [ ] Gradual migration of user traffic to be deployed to new environment 
	- [ ] Monitor for errors
- [ ] Old environment can be turned off after migration is complete
