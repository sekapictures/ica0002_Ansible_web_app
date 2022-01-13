<h2>SLA for Backup of the services</h2>

This document covers topics regarding the backup and all corresponding procedures of the infrastructure. 
Some aspects are a subject to change as infrastructure may be updated in the future. 


<h2>Backup coverage</h2>
-	Database servers (MySQL and InfluxDB) 
-	Ansible repository (on PC which deploys infrastructure)

Main reasoning for provisioning backup strictly for these services is that database servers run MySQL and InfluxDB.

MySQL databases contain AGAMA user generated data. User generated data cannot be recreated artificially and must be saved as backup. 

InfluxDB provides logging for the infrastructure. Logs are very significant in case of resolving errors and investigating possible causes. 

Ansible repository contains all files needed to deploy the infrastructure on working machines. Deploying the rest of applications with Ansible is more suitable way than creating backups of each one of them. These applications bear no user generated data.


<h2>Backup RPO (Recovery Point Objective)</h2>

MySQL – 7 days (1 week)

InfluxDB – 7 days (1 week)

Ansible repository – 7 days (1 week)

Technology of incremental and full backups is utilized by the infrastructure.

Each day of the week from Monday through Saturday an incremental backup is made and uploaded to the offsite backup server. 
Full backup is made on Sunday.
Incremental-full backup rotation allows to use less storage space. 
Loss of the links between incremental backups is considered under calculated acceptable data loss.


MySQL backups:

Latest data dump is created at 22:10 (UTC) or 0:10 (EET)

Incremental – Mon-Sat at 22:20 (UTC) or 0:20 (EET)

Full – Sunday at 22:20 (UTC) or 0:20 (EET)


InfluxDB backups:

Latest data dump is created at 22:15 (UTC) or 0:15 (EET)

Incremental – Mon-Sat at 22:30 (UTC) or 0:30 (EET)

Full – Sunday at 22:30 (UTC) or 0:30 (EET)

Ansible repository:

Full - Sunday at 22:00 (UTC) or 0:00 (EET)


<h2>Versioning and retention</h2>

MySQL and InfluxDB backups are stored for 28 days (4 weeks) on the offsite backup server. 
It is possible to store 2 backup versions at the same time. 
Each 28 days the oldest backup version is deleted. 
Having 2 version (7 days difference) version offsite is beneficial as random error may occur and one of the version may be not be sufficient. 
Ansible repository is accessible from github.

On-site backups are stored using different media types. 
Main backup copies are stored on the machines directly for the quick access.
Another copies are stored on the hard drives. 
The data is being uploaded to the drives accordingly to the RPO. 
The process is being automated by scripts, but hard drives are checked for failures each 28 days by real personal.


<h2>Usability check</h2>

In order to provide maximum availability to our clients, all backup usability checks are performed using virtual environment. 
The real production infrastructure is simulated on virtual machines. 
Every Thursday of each week rotation of 2 infrastructure team members check if all backup systems are operable. 
In case any issues are discovered, immediate actions will be taken in order to resolve them as quick as possible. 


<h2>Restoration criteria</h2>

Infrastructure team must confirm the occurrence of data corruption, unauthorized modifications, deletion or data steal by doing constant monitoring of the system. 
Hard evidence as logs, photos, screenshots, video must be provided in order to make a final decision.


<h2>Backup RTO</h2>

Restoration of the whole infrastructure takes 2 hours. Possible random errors are taken into account.
