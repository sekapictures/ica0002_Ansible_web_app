In order to restore backups, one use terminal Terminal.
Open Terminal from desktop or press CTRL + ALT + T.

=== RESTORE BACKUP FOR MYSQL ===

<STEP 1>
Go to ~/ica0002/ directory:

1.    cd ~/ica0002

<STEP 2>
Deploy infrastructure:

1.    ansible-playbook infra.yaml

This process will take some time. Continue work after it shows PLAY RECAP (should be 0 failed)

<STEP 3>
Connect to main database server.
Paste commands in sequence:

1.    PORTNUM=$(cat hosts | grep -oP '(?<=sekapictures-2 ansible_port=)\K\w+')
2.    ssh ubuntu@193.40.156.86 -p$PORTNUM

You will be connected to database server.

<STEP 4>
Restore MySQL data from the backup.
Copy in sequence:

1.    sudo -u backup duplicity --no-encryption restore rsync://sekapictures@backup.gleppy.io//home/sekapictures/mysql/ /home/backup/restore/
2.    sudo su
3.    mysql agama < /home/backup/restore/agama.sql

IF BACKUP SERVER IS DOWN or THERE ARE ERRORS:

1.    sudo su
2.    mysql agama < /home/backup/restore/agama.sql
After this MySQL data is restored.

Restore InfluxDB data from the backup.

1.    sudo -u backup duplicity --no-encryption restore rsync://sekapictures@backup.gleppy.io//home/sekapictures/influxdb/ /home/backup/restore/
2.    sudo -u backup influxd restore -portable -database telegraf /home/backup/restore/

<STEP 5>
Check that backup is restored.

