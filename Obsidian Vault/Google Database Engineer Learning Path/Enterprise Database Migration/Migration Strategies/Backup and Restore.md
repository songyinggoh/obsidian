> Simplest way to move database

![[Screenshot 2025-09-23 140638.png]]
- [ ] Perform SQL backup on source database
- [ ] Then copy backup files onto Google Cloud
- [ ] Run restore on new target database server

# Using differential backups to minimize downtime
- [ ] Begin with full backup and restore
- [ ] Execute differential backups until backup-restore can be done quickly
- [ ] Schedule maintenance event when database is unavailable
- [ ] Complete final backup and restore
- [ ] Migrate client connections to new database
- [ ] Retire old database