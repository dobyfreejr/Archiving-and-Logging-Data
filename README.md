# Archiving and Logging Data




### *Step 1: Create, Extract, Compress, and Manage tar Backup Archives*

#### **1.Command to extract the TarDocs.tar archive to the current directory**:

    tar -xvf TarDocs.tar


### **2.Commrand to create the Javaless_Doc.tar archive from the TarDocs/ directory, while excluding the TarDocs/Documents/Java directory** :

    Tar -cvf Javaless_Docs.tar –exclude-tag-under=Java ~/Projects/TarDocs


### **3.Command to ensure Java/ is not in the new Javaless_Docs.tar archive**:

    Tar -tf Javaless_Docs.tar | grep -rw Java *


## *Bonus*
 
### **4.Command to create an incremental archive called logs_backup_tar.gz with only changed files to snapshot.file for the /var/log directory**:

    Sudo tar –listed-incremental=snapshot.file -cvzf logs_backup.tar.gz /var/log


### *Critical Analysis Question*

### **5.Why wouldn't you use the options -x and -c at the same time with tar?**
    -c Creates a new archive and -x extracts files from an archive . You can not do both actions at the same time. 


## *Step 2: Create, Manage, and Automate Cron Jobs*

#### **1.Cron job for backing up the /var/log/auth.log file**:
    0 6 ** 3 tar -czf /auth_backup.tgz /var/log/auth.log

## *Step 3: Write Basic Bash Scripts*

### **1.Brace expansion command to create the four subdirectories**:

    ySudo mkdir -p ~/backups/{Freedisk,Freeman,Diskuse,Openlist}


### 2.**Paste your system.sh script edits**:

    #!/bin/bash
    [ -mh > awk 'NR==2{printf "Memory Usage: %s/%smb (%.2f%%0\n", $3,$2,$3*100/$2 }' > ~/backups/Freeman/free_mem.txt

    df -h | awk '$NF=="/"{printf "Disk 
    Usage: %d/%dGB (%s)\n", $3,$2,$5}' > 
    ~/backups/Diskuse/Disk_usage.txt

    lsof > ~/backups/Openlist/open_list.txt

     df -h >> ~/backups/Freedisk/Free_disk.txt
]


### 3.**Command to make the system.sh script executable**:

    Sudo chmod +x system.sh

### *Optional*

### **4.Commands to test the script and confirm its execution**:

    Sudo ./system.sh


### *Bonus*

### 5.**Command to copy system to system-wide cron directory**:

    Sudo cp system.sh /etc/cron.weekly



## *Step 4. Manage Log File Sizes*
 
### 1.**Run sudo nano /etc/logrotate.conf to edit the logrotate configuration file**. 

- #### *Configure a log rotation scheme that backs up authentication messages to the /var/log/auth.log*. 

- #### *Add your config file edits*:

        /var/log/auth.log {
            weekly
            rotate 7
            Notifempty
            Delaycompress
            Missingok
            endscript
      }






## *Bonus: Check for Policy and File Violations*

### 1.**Command to verify `auditd` is active**:

    Sudo systemctl status auditd


### 2.**Command to set number of retained logs and maximum log file size**:

    Sudo nano /etc/audit/auditd.conf


### 3.**Add the edits made to the configuration file** 
    Max_logfile = 35
    Num_log = 7


### 4.**Command using auditd to set rules for /etc/shadow, /etc/passwd, and /var/log/auth.log**:

    Sudo nano /etc/audit/rules.d/audit.rules

 
### 5.**Add the edits made to the rules file below**:

    -w /etc/shadow -p wra -k hashpass_audit
    -w /etc/passwd -p wra -k userpass_audit
    -w /var/log/auth.log -p wra -k auth_audit


### 6.**Command to restart auditd**:

    Sudo systemctl restart auditd


### 7.**Command to list all auditd rules**:

    Sudo auditctl -l


### 8.**Command to produce an audit report**:

    Sudo aureport -au


### 9.**Create a user with sudo useradd attacker and produce an audit report that lists account modifications**:

    Sudo aureport -m


### 10.**Command to use auditd to watch /var/log/cron**:

    Sudo auditctl -w /var/log/cron


### 11.**Command to verify auditd rules**:

    Sudo auditctl -l


### *Bonus (Research Activity): Perform Various Log Filtering Techniques*

### 1.**Command to return journalctl messages with priorities from emergency to error**:

    Sudo journalctl -b -p emerg..err


### 2.**Command to check the disk usage of the system journal unit since the most recent boot**:

    Sudo journalctl -b -u systemd-journald | less


### 3.**Command to remove all archived journal files except the most recent two**:

    Sudo journalctl –vacuum-files=2


### 4.**Command to filter all log messages with priority levels between zero and two, and save output to /home/sysadmin/Priority_High.txt**:

    Journalctl -p 0..2 > /home/sysadmin/Priority_High.txt


### 5.**Command to automate the last command in a daily cron job. Add the edits made to the crontab file below**:

    @daily journalctl -p 0..2 > /home/student/Priority_High.txt