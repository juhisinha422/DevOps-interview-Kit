# Crontab_Golden_Questions (Scenario Based) (0–3 Years DevOps Engineer)

These are real Crontab scenarios…

Commonly used in production automation 👇

---

## 1. Cron job is not running. What will you check first?

* Check crontab entry: `crontab -l`
* Verify cron service is running: `systemctl status cron/crond`
* Check logs: `/var/log/syslog` or `/var/log/cron`

---

## 2. Job runs manually but not via cron. Why?

* Different environment variables
* PATH not set
* Permission issues
* Script requires interactive shell

---

## 3. Cron job executed but output not visible. What will you check?

* Output is not logged by default
* Redirect output to file

---

## 4. Script runs but cron job fails. What could be the issue?

* Missing environment variables
* Relative paths used
* Permission issues
* Shell differences

---

## 5. Environment variables not working in cron. Why?

* Cron runs in minimal environment
* Need to define variables explicitly in script or crontab

---

## 6. Cron job running multiple times unexpectedly. What will you verify?

* Duplicate crontab entries
* Multiple users having same job
* Overlapping execution

---

## 7. Job timing is incorrect. How will you debug it?

* Check cron syntax
* Verify system timezone
* Check server time (`date`)

---

## 8. Cron job not running after reboot. What could be wrong?

* Cron service not enabled

```bash id="xq2e9m"
systemctl enable cron
```

---

## 9. Permission denied error in cron job. What will you check?

* Script execute permission

```bash id="m4r9yk"
chmod +x script.sh
```

* File ownership

---

## 10. Cron job is running but not performing expected task. Why?

* Wrong logic in script
* Wrong file paths
* Missing dependencies

---

## 11. Logs not generated for cron execution. What will you do?

* Redirect logs manually

```bash id="l2cmz5"
* * * * * /script.sh >> /var/log/script.log 2>&1
```

---

## 12. Cron job fails intermittently. How will you debug?

* Check logs
* Monitor system load
* Check dependency services
* Add debug logs in script

---

## 13. Script path not working in cron. What is the issue?

* Relative path issue → use absolute path

---

## 14. Multiple cron jobs conflicting. How will you handle it?

* Schedule properly
* Add locking mechanism
* Separate responsibilities

---

## 15. Job needs to run every 5 minutes. How will you configure it?

```bash id="3m4shk"
*/5 * * * * /script.sh
```

---

## 16. You need to schedule job at specific time daily. How?

```bash id="8x3p9l"
0 2 * * * /script.sh
```

---

## 17. Cron job consuming high resources. What will you do?

* Optimize script
* Limit execution frequency
* Use nice/priority control
* Monitor CPU/memory

---

## 18. You want to disable a cron job temporarily. How?

* Comment the line using `#`

---

## 19. You need to monitor cron job execution. How will you do it?

* Logging
* Monitoring tools (Prometheus, alerts)
* Exit status tracking

---

## 20. You want to log cron job output. What will you use?

```bash id="i6k3qw"
>> logfile 2>&1
```

---

## 21. Cron job depends on another service. How will you manage it?

* Check service status before execution
* Add retry logic
* Use systemd timers if needed

---

## 22. Timezone mismatch affecting cron. What will you check?

* System timezone (`timedatectl`)
* Cron timezone settings

---

## 23. Cron job runs but script not updating data. Why?

* Permission issues
* Wrong DB/API endpoint
* Script logic issue

---

## 24. You need to run job on multiple servers. How will you manage?

* Use configuration management (Ansible)
* Central scheduler
* Avoid duplicate execution

---

## 25. Cron syntax confusion. How will you verify schedule?

* Use online cron validators
* Test with short intervals

---

## 26. Job runs but takes too long. How will you optimize?

* Optimize code
* Parallel execution
* Increase resources

---

## 27. Cron job not triggering in container. Why?

* Cron service not running inside container
* Use proper entrypoint or scheduler

---

## 28. You want to notify on job failure. How will you design it?

* Check exit code
* Send alert (email/Slack)

---

## 29. Cron job overlapping executions. How will you prevent it?

```bash id="9xyq7a"
flock -n /tmp/job.lock /script.sh
```

---

## 30. What is your step-by-step approach to debug cron issues?

1. Check cron service
2. Verify crontab entry
3. Check logs
4. Test script manually
5. Use absolute paths
6. Add logging
7. Validate environment variables
8. Monitor execution

---
