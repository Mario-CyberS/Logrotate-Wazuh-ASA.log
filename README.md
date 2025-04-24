# Wazuh ASA Log Rotation Configuration  
This project explains how to configure **log rotation** for Cisco ASA firewall logs ingested by Wazuh using `logrotate` on a RHEL-based system. This ensures efficient log management and prevents disk space issues by rotating and compressing the `siteasa.log` file daily.

---

## 🎯 Objective  
To automatically rotate the `/var/log/siteasa.log` file created for Cisco ASA logs using `logrotate` and schedule the rotation via `cron` to run daily at 2 AM.

---

## 🔍 Why Log Rotation?  
When logs are continuously written (especially from firewalls like Cisco ASA), the log files can grow rapidly and consume disk space. **Log rotation** helps by:

- Automatically rotating logs on a schedule  
- Creating backups with date-based filenames  
- Compressing old logs to save space  
- Enforcing correct permissions on new log files  

This is crucial for long-term log management and system stability.

---

## 📚 Skills Learned  
- Creating custom logrotate configuration files  
- Automating log rotation with `cron`  
- Managing file permissions and compression policies  
- Ensuring service restarts post-rotation (e.g., `rsyslog`)  
- Working with system-level scheduled jobs

---

## 🛠️ Tools Used  
<div>
  <img src="https://img.shields.io/badge/-crontab-6A5ACD?&style=for-the-badge&logo=cron&logoColor=white" />
  <img src="https://img.shields.io/badge/-logrotate-333333?&style=for-the-badge&logo=Linux&logoColor=white" />
</div>

---

## 📝 Deployment Steps

### 1. Create a Logrotate Configuration File  
This file will tell `logrotate` how to handle the `siteasa.log`.
```bash
sudo nano /etc/logrotate.d/asarotate
```
Paste the following configuration:
```bash
/var/log/siteasa.log {
    daily
    rotate 1
    create 0600 root root
    compress
    missingok
    notifempty
    dateext
    dateformat -%m-%d-%Y-%H:%M:%S
    postrotate
        /usr/bin/systemctl restart rsyslog.service > /dev/null 2>/dev/null || true
    endscript
}
```

### 2. Schedule the Rotation with Crontab
Open the crontab editor:
```bash
crontab -e
```
Add the following line to run the custom rotation daily at 2 AM:
```bash
0 2 * * * /usr/sbin/logrotate /etc/logrotate.d/asarotate
```

### 3. Confirm the Scheduled Job
Check your crontab to ensure the job is listed:
```bash
crontab -l
```
You should see the logrotate command listed for 2:00 AM.

---

### Expected Result
Your siteasa.log file will rotate daily, with the previous version saved using a date-based name, compressed, and recreated with the correct permissions. The rsyslog service will restart automatically to avoid logging issues.

---

### Notes
Make sure the original log file (/var/log/siteasa.log) exists before enabling rotation.
The rotate 1 setting keeps only one compressed backup — increase if needed.
Test manually with:
```bash
sudo logrotate -f /etc/logrotate.d/asarotate
```

---

### 👨‍💻 Author
Mario Tagaras | Florida State University Alum





























