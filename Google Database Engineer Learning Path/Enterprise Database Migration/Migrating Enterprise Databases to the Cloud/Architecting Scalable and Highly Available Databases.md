## Scalable systems:
Systems that work as no. of users and amount of data grow.
Database are split into shards, then use multiple nodes/servers to process different shards.
Replicas of the database can handle high volumes of reads - the read replicas handle analytics and reporting use cases, while the main handles the writes and synchronizes data with the replicas.
Customers with global users can create read replicas in multiple regions, and requests from users are routed to the region geographically closest to them. Google's global load balancers automate this

## Highly available databases:
Fault-tolerant, works even when some nodes fail
Achieved by deploying to multiple regions/zones 
Load-balances are Google-provided regional/global resources that connects to back-end instances. They monitor health of these instances and only send traffic to healthy ones.
Databases are deployed to different zones. All databases are data-synced, should the main fail, the other databases takes requests until the main is recomissioned.