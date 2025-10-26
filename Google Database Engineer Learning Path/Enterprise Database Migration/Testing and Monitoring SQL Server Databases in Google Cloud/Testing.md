# To verify:
- [ ] Database schema migration
- [ ] Data migration
- [ ] User logins
- [ ] Can users connect to application and only access database data they are permitted to 


# Complete testing
Ensure that:
- [ ] New server environment can meet application demand
- [ ] All applications continue to work with new database
- [ ] Migration tools and scripts work correctly


# Database migration testing
## Structural testing
> Validates successful migration of all components 

1. Automate structural tests to ensure that all tables have been migrated
2. Fields, data types, links, constraints have been migrated
3. All indexes created
4. Primary-foreign keys matching were created and matched correctly in related tables
5. Stored procedures and triggers have been migrated 
6. User logins were migrated successfully


## Functional testing
> Ensuring that all data was migrated and database functions work as expected
> All views and stored procedures work well and users can access permitted data

1. Ensure all data has been migrated correctly
2. Stored procedures and triggers work as expected
3. User logins work as expected
4. Users can only execute permitted operations and access permitted data
5. Queries and views continue to return expected results

## Non-functional testing
> Assert that new database system performs well and can withstand high usage

1. Load and stress testing to determine resource allocation