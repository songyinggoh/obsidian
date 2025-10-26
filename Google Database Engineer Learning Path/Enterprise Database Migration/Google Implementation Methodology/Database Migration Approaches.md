# Scheduled maintenance
> Use when some downtime can be tolerated
- [ ] Set time window when database and applications will be unavailable
- [ ] Migrate data to new database
- [ ] Reroute client connections to server hosting the new database
- [ ] Execute validation tests to ensure:
	- [ ] all data has been successfully transferred 
	- [ ] that the new database is functioning correctly
- [ ] Lastly turn applications and database online
	- [ ] Take old database offline

When database is very large, execute migration of old data. Then, migrate new data added to new database.

# Continuous replication*
- [ ] Uses current database capabilities to synchronize with new database
- [ ] When sync is complete, switch clients to the new database 
- [ ] Retire old database
# Split reading and writing*
>Requires code change on client 

>Only done when migrating to different types of databases(e.g. migrating from Oracle to Spanner)

>==Best fit for migrating on-premises Oracle database to Oracle on Google Cloud Bare Metal Solution==
>==Impractical if there are large amounts of clients==

Clients read and write to ==both== old and new databases

# Data access microservice
>Makes split reading and writing seamless to clients

>Most complex, but best when:
>	Many clients to migrate
>	Can tolerate little to no downtime

- [ ] Data access is encapsulated / hidden behind service
- [ ] First, migrate client connections to service
- [ ] Then, service handles migration from old to new database

