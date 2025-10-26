# Compute Engine
Can run Linux and Windows VMs
Supports up to 96 cores and 100s GB storage and 100s TB storage 
Pre-configured VM images with SQL Server already installed
VM creation may be automated with scripts or templates

## To configure VMs to run databases:
1. Select region and zone closest to users
2. Choose boot disk (a persistent disk image that is configured with OS already installed)
3. Specify disk space required for VM

## SQL server
SQL Server 2014-latest may be chosen
Express, Web, Standard, and Enterprise editions are supported
Microsoft License Mobility for Google Cloud program allows for bring-your-own-license, otherwise, the license fee is already included in the hourly rate
Google's VM security features ensures that images have not been tempered with, and tempered images do not boot

## Automating the installation of VM components
Include start-up script that can be added to the machine config directly or can refer to external script in Cloud Storage
Linux machines use Bash shell scripts
Windows machines use Batch, Command, or Powershell scripts
Google Cloud SDK can script creation of CE VM and other infrastructure resources (e.g. networks, firewall rules, instance templates and instance groups)
Google's web console can configure resources and click cmd line link at bottom of window