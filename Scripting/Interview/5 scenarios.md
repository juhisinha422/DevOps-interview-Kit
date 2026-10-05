# 🚀 Linux / DevOps Scripting – Interview Answers (4 Years Experience)

---

## 1 - if CPU usage is crossing between 82-87%, your team needs to get an alert email.

```bash
#!/bin/bash

THRESHOLD_LOW=82
THRESHOLD_HIGH=87

CPU_USAGE=$(top -bn1 | grep "Cpu(s)" | awk '{print 100 - $8}' | cut -d. -f1)

if [ "$CPU_USAGE" -ge "$THRESHOLD_LOW" ] && [ "$CPU_USAGE" -le "$THRESHOLD_HIGH" ]; then
  echo "CPU usage is at ${CPU_USAGE}% on $(hostname)" | mail -s "CPU Alert" team@example.com
fi
```

---

## 2 - if /var FS is reaching above 85%, send alert email.

```bash
#!/bin/bash

THRESHOLD=85

USAGE=$(df -h /var | awk 'NR==2 {print $5}' | sed 's/%//')

if [ "$USAGE" -ge "$THRESHOLD" ]; then
  echo "/var filesystem usage is ${USAGE}% on $(hostname)" | mail -s "/var FS Alert" team@example.com
fi
```

---

## 3 - how to create a user/ service account by using the shell script

```bash
#!/bin/bash

USERNAME=$1

if id "$USERNAME" &>/dev/null; then
  echo "User already exists"
else
  useradd -m -s /bin/bash "$USERNAME"
  echo "User $USERNAME created"
fi
```

---

## 4 - write scripting to take pre-checks before patching / OS update

```bash
#!/bin/bash

echo "===== Pre-check Report ====="

echo "Hostname: $(hostname)"
echo "Date: $(date)"

echo "---- Disk Usage ----"
df -h

echo "---- Memory Usage ----"
free -m

echo "---- CPU Load ----"
uptime

echo "---- Running Services ----"
systemctl list-units --type=service --state=running

echo "---- Kernel Version ----"
uname -r

echo "---- Pending Updates ----"
if command -v yum &>/dev/null; then
  yum check-update
elif command -v apt &>/dev/null; then
  apt list --upgradable
fi
```

---

## 5 - increasing any FS using LVM (shell script)

```bash
#!/bin/bash

LV_PATH=$1      # e.g. /dev/vg_root/lv_root
SIZE=$2         # e.g. +5G

echo "Extending logical volume..."

lvextend -L $SIZE $LV_PATH

if [[ "$LV_PATH" == *"xfs"* ]]; then
  xfs_growfs $LV_PATH
else
  resize2fs $LV_PATH
fi

echo "Filesystem extended successfully"
```

---

## ✅ Notes (Important for Interview)

* Always mention **cron jobs** for automation
* Ensure **mail service (mailx/postfix)** is configured
* Scripts should have proper **permissions and logging**
* Validate before executing destructive commands

---
