to be filled

Create a free-text file named backup_sla.md in the root of your Ansible repository.

In that file describe the backup approach for:

    Database servers
    InfluxDB
    Ansible repository

This document should contain the information about:

    Backup coverage -- what is backed up and what is not
    RPO (recovery point objective)
    Versioning and retention -- how many backup versions are stored and for how long
    Usability checks -- how is backup usability verified
    Restoration criteria -- when should backup be restored
    RTO (recovery time objective)

Note that you don't need to backup each and every service. In case of disaster recovery it makes more sense to re-create some service from scratch than restore it from the backup (Nginx, AGAMA, Grafana, Bind). But for other services backups are must (MySQL and InfluxDB contain data that cannot be restored with Ansible).

If you are missing some information to complete backup documentation (list of responsible IT staff, amount of data, value of information, acceptable data loss) -- make it up! Be creative, but try to keep it adequate and somewhat related to real life.