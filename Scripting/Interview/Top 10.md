# Top 10 Bash Scripting Questions Asked in DevOps Interviews

---

# 1. Write a script to monitor CPU, Memory, and Disk usage.

```bash
#!/bin/bash

echo "===== System Resource Usage ====="
echo

echo "CPU Usage:"
top -bn1 | grep "Cpu(s)"

echo
echo "Memory Usage:"
free -h

echo
echo "Disk Usage:"
df -h
```

---

# 2. Write a script to restart a service if it goes down.

```bash
#!/bin/bash

SERVICE="nginx"

if ! systemctl is-active --quiet $SERVICE
then
    echo "$SERVICE is down. Restarting..."
    systemctl restart $SERVICE
    echo "$SERVICE restarted successfully."
else
    echo "$SERVICE is running."
fi
```

---

# 3. Write a script to delete log files older than 30 days.

```bash
#!/bin/bash

LOG_DIR="/var/log/myapp"

find $LOG_DIR -type f -name "*.log" -mtime +30 -exec rm -f {} \;

echo "Old log files deleted successfully."
```

---

# 4. Write a script to take backups and compress them.

```bash
#!/bin/bash

SOURCE="/home/app/data"
BACKUP_DIR="/backup"

DATE=$(date +%F)

mkdir -p $BACKUP_DIR

tar -czf $BACKUP_DIR/backup-$DATE.tar.gz $SOURCE

echo "Backup completed successfully."
```

---

# 5. Write a script to monitor a website and send an alert if it's down.

```bash
#!/bin/bash

URL="https://example.com"
EMAIL="admin@example.com"

STATUS=$(curl -o /dev/null -s -w "%{http_code}" $URL)

if [ "$STATUS" != "200" ]
then
    echo "Website is DOWN" | mail -s "Website Alert" $EMAIL
fi
```

---

# 6. Write a script to check whether a process is running.

```bash
#!/bin/bash

PROCESS="nginx"

if pgrep $PROCESS > /dev/null
then
    echo "$PROCESS is running."
else
    echo "$PROCESS is NOT running."
fi
```

---

# 7. Write a script to monitor SSL certificate expiry.

```bash
#!/bin/bash

DOMAIN="google.com"

echo | openssl s_client -connect $DOMAIN:443 -servername $DOMAIN 2>/dev/null \
| openssl x509 -noout -dates
```

---

# 8. Write a script to create multiple Linux users from a file.

**users.txt**

```text
john
alice
bob
david
```

**Script**

```bash
#!/bin/bash

while read USER
do
    useradd $USER
    echo "Created user: $USER"
done < users.txt
```

---

# 9. Write a script to archive logs every day using cron.

**Script**

```bash
#!/bin/bash

LOG_DIR="/var/log/myapp"
BACKUP="/backup"

DATE=$(date +%F)

tar -czf $BACKUP/logs-$DATE.tar.gz $LOG_DIR
```

**Cron Job**

```cron
0 0 * * * /home/user/archive_logs.sh
```

This runs the script every day at **12:00 AM**.

---

# 10. Write a script to check disk usage and send an email when it exceeds a threshold.

```bash
#!/bin/bash

THRESHOLD=80
EMAIL="admin@example.com"

df -h | awk 'NR>1 {print $5,$6}' | while read OUTPUT
do
    USAGE=$(echo $OUTPUT | awk '{print $1}' | sed 's/%//')
    PARTITION=$(echo $OUTPUT | awk '{print $2}')

    if [ $USAGE -ge $THRESHOLD ]
    then
        echo "Disk usage on $PARTITION is ${USAGE}%." \
        | mail -s "Disk Usage Alert" $EMAIL
    fi
done
```
