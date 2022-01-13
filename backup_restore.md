In order to restore backups, one use terminal Terminal.
Open Terminal from desktop or press CTRL + ALT + T.


=== RESTORE APPLICATION AND SERVICES ===

<STEP 1>
Go to ~/ica0002/ directory:

1.    cd ~/ica0002

<STEP 2>
Deploy infrastructure:

1.    ansible-playbook infra.yaml

This process will take some time. Continue work after it shows PLAY RECAP (should be 0 failed)

=== RESTORE BACKUP FOR MYSQL ===

<STEP 3>
Make sure you are located in /ica0002 directory. Connect to main database server.
Paste commands in sequence:

1.    MASTER=$(cat group_vars/all.yaml |  grep -oP '(?<=mysql_master: ).*'); PORTNUM=$(cat hosts | grep -oP '(?<='$MASTER' ansible_port=)\K\w+') 

2.    ssh ubuntu@193.40.156.67 -p$PORTNUM

You will be connected to master MySQL database server.

<STEP 4>
Restore MySQL data from the backup server.
Copy in sequence:

1.    sudo -u backup duplicity --no-encryption restore rsync://sekapictures@backup.gleppy.io//home/sekapictures/mysql/ /home/backup/restore/
2.    sudo su
3.    mysql agama < /home/backup/restore/agama.sql

IF BACKUP SERVER IS DOWN or THERE ARE ERRORS:

1.    sudo su
2.    mysql agama < /home/backup/restore/agama.sql
After this MySQL data is restored.

BACKUP USING HARD DRIVES:

1.    Get the hard drive from safe department. It is located on the second shelf to the right from the entrance
2.    Insert the drive to the port on the PC
3.    Use commands to find drive name:
        - lsblk
        - sudo blkid
        - sudo fdisk -l
        - sudo  mkdir /media/usb
        - sudo mount /dev/{NAME} /media/usb
4.    mysql agama < /media/usb/agama_restore/agama.sql

CHECK MySQL BACKUP:

1.    sudo mysql -e 'SELECT * FROM agama.item ORDER BY id ASC LIMIT 50';
2.    sudo mysql -e 'SELECT * FROM agama.item ORDER BY id DESC LIMIT 50';

Now you can disconnect from the server:

1.    exit

May need to write it twice, to exit sudo mode and disconnect.

Command should run without errors and output user content.

=== RESTORE BACKUP FOR InfluxDB ===

Restore InfluxDB data from the backup.
Make sure you are located in /ica0002 directory. Connect to InfluxDB server.

1.    INFLUX=$(cat group_vars/all.yaml |  grep -oP '(?<=influx_host: ).*'); PORTNUM2=$(cat hosts | grep -oP '(?<='$INFLUX' ansible_port=)\K\w+')

2.    ssh ubuntu@193.40.156.67 -p$PORTNUM2

Restore InfluxDB data from the backup server.

1.    sudo -u backup duplicity --no-encryption restore rsync://sekapictures@backup.gleppy.io//home/sekapictures/influxdb/ /home/backup/restore/

2.    sudo service telegraf stop

3.    sudo influx -execute 'DROP DATABASE telegraf'

4.    sudo -u backup influxd restore -portable -database telegraf /home/backup/restore/

5.    sudo service telegraf start

IF BACKUP SERVER IS DOWN or THERE ARE ERRORS:

1.    sudo service telegraf stop

2.    sudo influx -execute 'DROP DATABASE telegraf'

3.    sudo influxd restore -portable -database telegraf /home/backup/influxdb

4.    sudo service telegraf start

BACKUP USING HARD DRIVES:

1.    Get the hard drive from safe department. It is located on the second shelf to the right from the entrance
2.    Insert the drive to the port on the PC
3.    Use commands to find drive name:
        - lsblk
        - sudo blkid
        - sudo fdisk -l
        - sudo  mkdir /media/usb
        - sudo mount /dev/{NAME} /media/usb
4.    sudo influxd restore -portable -database telegraf /media/usb/influxdb_restore

Now you can disconnect from the server:

1.    exit

May need to write it twice, to exit sudo mode and disconnect.

=== RESTORE BACKUP FOR Ansible repository ===

Check if ansible if installed:

1.    ~/ansible-venv/bin/ansible --version

If command gives ansible version output, skip installation step.
If command throws an error, proceed with installation step.

Ansible installation:

1.    python3 -m venv ~/ansible-venv
2.    ~/ansible-venv/bin/pip install ansible==4.4.0
3.    PATH="$HOME/ansible-venv/bin:$PATH"

Restore repository from local backup:

1.    cd ~
2.    cp -a ansible_backup/. ica0002/

Restore from github:

1.    cd ~
2.    rm -r ica0002
3.    mkdir ica0002 
4.    cd ica0002 
5.    git clone git@github.com:sekapictures/ica0002.git

Restore from hard drive:

1.    Get the hard drive from safe department. It is located on the second shelf to the right from the entrance
2.    Insert the drive to the port on the PC
3.    Use commands to find drive name:
        - lsblk
        - sudo blkid
        - sudo fdisk -l
        - sudo  mkdir /media/usb
        - sudo mount /dev/{NAME} /media/usb
4.    cp -a /media/usb/ansible_restore/. ica0002/ 