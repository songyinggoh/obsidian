# CloudSQL
Easy to set up either in console or with CLI cmd
Up to 96 cores and 30TB disk space
May be configured to autocreate a failover database in another zone
Firewall allows access to database only from project where database is created
Supports MySQL, PostgreSQL, SQLServer

## Scale vertically to increase capacity
Up to 128vCPU, 864GB RAM, 64TB storage

# Spanner
Completely managed patches, administration, backups, etc.
Simple setup
Cross-region deployment

## Scale horizontally to increase capacity
Add nodes, which each node can accommodate ~2TB data

### Scaling by processing units or nodes
1000 processing units == 1 node
For instances smaller than 1 node or 1000 processing units, Spanner allocates 409.6GB of data/100 processing units
For instances larger than 1 node or 1000 processing units, Spanner allocates 4TB of data/node