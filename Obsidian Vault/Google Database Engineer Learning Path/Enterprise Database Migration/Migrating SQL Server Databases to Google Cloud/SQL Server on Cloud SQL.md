# Automating instance creation with CLI
`gcloud sql instances create myinstance \
--database-version=SQLSERVER_2017_STANDARD \
--cpu=2 \
--memory=7680MiB \
--root-password=[INSERT-PASSWORD-HERE]`

# Automating instance creation with Terraform

![[Screenshot 2025-09-30 182601.png]]

# Backups and restores from CLI
`gcloud sql backups create --async
--instance sql-server
--root-password=**********
gcloud sql backups list --instance
sql-server
gcloud sql backups restore
[BACKUP_ID]
--restore-instance=sql-server`