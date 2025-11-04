# คู่มือปฏิบัติการ: Linux System Admin บน AWS Academy
## 👥 กิจกรรมคู่ (Pair Activity) - 3 ชั่วโมง

---

## 📋 ข้อมูลทั่วไป

**วัตถุประสงค์:** ฝึกทักษะ System Administrator แบบ hands-on ผ่าน AWS EC2  
**รูปแบบ:** ทำงานเป็นคู่ (2 คน/กลุ่ม)  
**ระยะเวลา:** 3 ชั่วโมง  
**สิ่งที่ต้องเตรียม:**
- บัญชี AWS Academy Learner Lab
- โปรแกรม MobaXterm หรือ Terminal
- เอกสารนี้

---

## 🎯 Learning Outcomes
หลังจบ Lab นี้ นักศึกษาจะสามารถ:
1. ✅ สร้างและจัดการ EC2 Instance บน AWS
2. ✅ ปฏิบัติงาน System Admin พื้นฐานประจำวัน
3. ✅ จัดการ users, permissions และ services
4. ✅ ตรวจสอบ system health และแก้ปัญหาเบื้องต้น
5. ✅ เขียน automation scripts สำหรับงานประจำ

---

## 📝 การแบ่งบทบาทในคู่

| คู่ที่ 1 (Driver) | คู่ที่ 2 (Navigator) |
|------------------|---------------------|
| ควบคุมหน้าจอ/พิมพ์คำสั่ง | อ่านคู่มือ/ตรวจสอบความถูกต้อง |
| รัน commands | จดบันทึกผลลัพธ์ |
| **สลับบทบาททุก 30 นาที** | **สลับบทบาททุก 30 นาที** |

---

# ส่วนที่ 1: สร้าง EC2 Instance (30 นาที)

## 📍 ขั้นที่ 1: เข้า AWS Academy Learner Lab

1. เปิด browser ไปที่ AWS Academy
2. เข้าสู่ course **DTI 131**
3. คลิก **Modules** → **Learner Lab**
4. คลิก **Start Lab** (รอสถานะเป็น 🟢)
5. คลิก **AWS** เพื่อเปิด Console

## 🚀 ขั้นที่ 2: สร้าง Ubuntu Server

### 2.1 เริ่มสร้าง Instance

```
1. ค้นหา "EC2" ในช่องค้นหา → เปิด EC2 Dashboard
2. คลิกปุ่ม "Launch Instance" (สีส้ม)
```

### 2.2 กรอกข้อมูล Instance

| หัวข้อ | ค่าที่ต้องกรอก |
|--------|--------------|
| **Name** | `pair[เลขกลุ่ม]-ubuntu-admin` <br>เช่น `pair01-ubuntu-admin` |
| **OS Image** | คลิก **Ubuntu** → เลือก **Ubuntu Server 24.04 LTS** |
| **Instance type** | `t3.micro` (Free tier eligible) |
| **Key pair** | คลิก **Create new key pair** <br>- Name: `pair[เลขกลุ่ม]-key` <br>- Type: **RSA** <br>- Format: **.pem** (Mac/Linux) หรือ **.ppk** (Windows) <br>→ **ดาวน์โหลดและเก็บไว้ให้ดี!** |

### 2.3 Network Settings

```
✅ ติ๊ก "Allow SSH traffic from" → เลือก "My IP"
✅ ติ๊ก "Allow HTTP traffic from the internet"
✅ ติ๊ก "Allow HTTPS traffic from the internet"
```

### 2.4 Configure Storage (ไม่ต้องเปลี่ยน)

ปล่อยค่า Storage เป็น default (8 GiB gp3)

### 2.5 Launch และรอ

1. คลิก **Launch instance**
2. คลิก **View all instances**
3. รอสถานะเป็น **Running** (✅ 2/2 checks passed)
4. ✅ **จด Instance ID และ Public IP ไว้**

---

## 🔌 ขั้นที่ 3: เชื่อมต่อกับ Server

### Option A: ใช้ EC2 Instance Connect (ง่ายสุด)
```
1. เลือก Instance → คลิก "Connect"
2. เลือก tab "EC2 Instance Connect"
3. Username: ubuntu (อัตโนมัติ)
4. คลิก "Connect"
```

### Option B: ใช้ MobaXterm (Windows)
```
1. เปิด MobaXterm → Session → SSH
2. Remote host: [Public IP ของคุณ]
3. Username: ubuntu
4. Advanced → Use private key → เลือกไฟล์ .pem
5. คลิก OK
```

### Option C: ใช้ Terminal (Mac/Linux)
```bash
# ตั้งสิทธิ์ key file
chmod 400 pair01-key.pem

# เชื่อมต่อ
ssh -i pair01-key.pem ubuntu@[Public-IP]
```

---

## 🛠️ ขั้นที่ 4: Manual Setup (จำเป็น! - 10 นาที)

**⚠️ หมายเหตุ:** User Data อาจไม่ทำงานใน AWS Academy ต้อง setup manual

### 4.1 ตรวจสอบว่า setup script ทำงานหรือไม่

```bash
# Check if users exist
ls /home/

# ถ้าเห็นแค่ 'ubuntu' แสดงว่า script ไม่ทำงาน
# ถ้าเห็น alice, bob, charlie ข้ามไปทำ Section 2 ได้เลย
```

### 4.2 ถ้า script ไม่ทำงาน - รัน setup manual

**คัดลอกและรันคำสั่งนี้ทั้งหมด:**

```bash
# Update system
sudo apt-get update -y

# Install required packages (อาจใช้เวลา 2-3 นาที)
sudo apt-get install -y \
    docker.io git htop tree curl wget \
    net-tools vim build-essential \
    software-properties-common nginx \
    apache2-utils fail2ban ufw

# Create test users
sudo useradd -m -s /bin/bash alice
sudo useradd -m -s /bin/bash bob
sudo useradd -m -s /bin/bash charlie

# Set passwords
echo "alice:Pass123!" | sudo chpasswd
echo "bob:Pass123!" | sudo chpasswd
echo "charlie:Pass123!" | sudo chpasswd

# Create directories
sudo mkdir -p /var/backups/daily
sudo mkdir -p /var/log/custom
sudo mkdir -p /opt/scripts

# Start services
sudo systemctl enable docker nginx
sudo systemctl start docker nginx
sudo usermod -aG docker ubuntu

# Set hostname
sudo hostnamectl set-hostname pair-admin-server

# Verify setup
echo "=== Setup Complete ==="
echo "Users created:"
ls /home/
echo ""
echo "Services status:"
sudo systemctl is-active nginx docker
```

### 4.3 ตรวจสอบความพร้อม

```bash
# ต้องเห็นผลลัพธ์แบบนี้:
# Users: alice bob charlie ubuntu
# Services: active active

# ถ้าไม่เห็น alice, bob, charlie ให้รันคำสั่งใน 4.2 อีกครั้ง
```

---

# ส่วนที่ 2: Daily Admin Tasks (2 ชั่วโมง)

## 📊 Task 1: System Health Check (20 นาที)

**บทบาท:** Driver รันคำสั่ง, Navigator จดบันทึกผล

### 1.1 ตรวจสอบข้อมูลพื้นฐาน

```bash
# ตรวจสอบระบบ
hostname
uname -a
lsb_release -a
uptime
date

# ตรวจสอบทรัพยากร
df -h                    # disk usage
free -h                  # memory usage
top                      # กด 'q' เพื่อออก
htop                     # กด 'q' เพื่อออก

# ตรวจสอบ network
ip a                     # network interfaces
ss -tulpn               # listening ports
systemctl status nginx   # web server status
```

### 1.2 สร้าง Health Check Script

```bash
# สร้าง script
sudo nano /opt/scripts/health_check.sh
```

วางโค้ดนี้:

```bash
#!/bin/bash
echo "=== System Health Report ==="
echo "Date: $(date)"
echo "Hostname: $(hostname)"
echo "Uptime: $(uptime -p)"
echo ""
echo "=== Resource Usage ==="
echo "Disk: $(df -h / | tail -1 | awk '{print $5}')"
echo "Memory: $(free -h | grep Mem | awk '{print $3 "/" $2}')"
echo "Load: $(uptime | awk -F'load average:' '{print $2}')"
echo ""
echo "=== Service Status ==="
for service in nginx docker ssh; do
    if systemctl is-active --quiet $service; then
        echo "✅ $service: Running"
    else
        echo "❌ $service: Stopped"
    fi
done
```

```bash
# ทำให้รันได้และทดสอบ
sudo chmod +x /opt/scripts/health_check.sh
sudo /opt/scripts/health_check.sh
```

**📝 Navigator:** บันทึกผลลัพธ์ในตาราง

| ข้อมูล | ค่าที่ได้ |
|--------|----------|
| Hostname | |
| OS Version | |
| Uptime | |
| Disk Usage | |
| Memory Usage | |
| Services Running | |

---

## 👥 Task 2: User Management (25 นาที)

**🔄 สลับบทบาท:** Navigator เป็น Driver

### 2.1 ตรวจสอบ users ที่มีอยู่

```bash
# ดู users ทั้งหมด
cat /etc/passwd | grep -E "(alice|bob|charlie|ubuntu)"

# ดูกลุ่ม
groups alice
groups bob
id charlie

# ดู last login
last -5
```

### 2.2 จัดการ users และ groups

```bash
# สร้าง groups
sudo groupadd developers
sudo groupadd admins
sudo groupadd interns

# จัด users เข้า groups
sudo usermod -aG developers alice
sudo usermod -aG admins bob
sudo usermod -aG interns charlie
sudo usermod -aG admins ubuntu

# ตรวจสอบ
groups alice bob charlie

# สร้าง shared directories
sudo mkdir -p /shared/{developers,admins,interns}
sudo chown :developers /shared/developers
sudo chown :admins /shared/admins
sudo chown :interns /shared/interns

# ตั้ง permissions
sudo chmod 770 /shared/developers
sudo chmod 770 /shared/admins
sudo chmod 750 /shared/interns

# ตรวจสอบ
ls -la /shared/
```

### 2.3 ทดสอบ permissions

```bash
# ลอง login เป็น alice
sudo su - alice

# ทดสอบเขียนไฟล์
echo "Alice's file" > /shared/developers/alice.txt
ls -la /shared/developers/

# ลองเข้า admin folder (ควร permission denied)
ls /shared/admins/

# กลับเป็น ubuntu
exit

# ทำเหมือนกันกับ bob
sudo su - bob
echo "Bob's admin file" > /shared/admins/bob.txt
ls -la /shared/admins/
exit
```

**📝 Navigator:** บันทึกตาราง permissions

| User | Groups | Can Access | Cannot Access |
|------|--------|------------|---------------|
| alice | | | |
| bob | | | |
| charlie | | | |

---

## 🔍 Task 3: Log Analysis & Monitoring (25 นาที)

**บทบาท:** ทำงานพร้อมกัน

### 3.1 ตรวจสอบ System Logs

```bash
# ดู system logs
sudo journalctl -n 20                    # 20 บรรทัดล่าสุด
sudo journalctl -p err -n 10            # errors only
sudo journalctl -u nginx                 # nginx logs
sudo journalctl -u ssh                   # ssh logs

# ดู auth logs
sudo tail -20 /var/log/auth.log         # authentication logs
sudo grep "Failed" /var/log/auth.log    # failed logins

# ดู nginx logs
sudo tail -f /var/log/nginx/access.log &  # background
# กด Enter หลายครั้ง
jobs                                     # ดู background jobs
kill %1                                  # หยุด job

# สร้าง log analyzer script
sudo nano /opt/scripts/log_analyzer.sh
```

วางโค้ดนี้:

```bash
#!/bin/bash
LOG_FILE="/var/log/auth.log"
REPORT_FILE="/tmp/security_report_$(date +%Y%m%d).txt"

echo "=== Security Report $(date) ===" > $REPORT_FILE
echo "" >> $REPORT_FILE

echo "Failed Login Attempts:" >> $REPORT_FILE
grep "Failed password" $LOG_FILE | tail -10 >> $REPORT_FILE

echo "" >> $REPORT_FILE
echo "Successful Logins:" >> $REPORT_FILE
grep "Accepted password\|Accepted publickey" $LOG_FILE | tail -10 >> $REPORT_FILE

echo "" >> $REPORT_FILE
echo "Sudo Commands:" >> $REPORT_FILE
grep "sudo" $LOG_FILE | tail -10 >> $REPORT_FILE

cat $REPORT_FILE
```

```bash
# รันและดูผล
sudo chmod +x /opt/scripts/log_analyzer.sh
sudo /opt/scripts/log_analyzer.sh
```

### 3.2 Real-time Monitoring

```bash
# Monitor processes
htop                      # กด F10 เพื่อออก

# Monitor disk I/O (ถ้ามี)
sudo apt install -y iotop
sudo iotop -o            # Ctrl+C เพื่อออก

# Monitor network
sudo apt install -y nethogs
sudo nethogs             # Ctrl+C เพื่อออก

# Create monitoring dashboard script
sudo nano /opt/scripts/monitor.sh
```

```bash
#!/bin/bash
while true; do
    clear
    echo "=== System Monitor Dashboard ==="
    echo "Time: $(date)"
    echo ""
    echo "=== CPU & Memory ==="
    top -bn1 | head -5
    echo ""
    echo "=== Disk Usage ==="
    df -h | grep -E "^/dev/"
    echo ""
    echo "=== Network Connections ==="
    ss -tu | head -10
    echo ""
    echo "Press Ctrl+C to exit"
    sleep 5
done
```

```bash
sudo chmod +x /opt/scripts/monitor.sh
# รันสักครู่แล้ว Ctrl+C
sudo /opt/scripts/monitor.sh
```

---

## 🔧 Task 4: Service Management (20 นาที)

**🔄 สลับบทบาทอีกครั้ง**

### 4.1 จัดการ Web Server

```bash
# ตรวจสอบ nginx
systemctl status nginx
sudo nginx -t                    # test configuration

# แก้ไข default page
sudo nano /var/www/html/index.html
```

ใส่ HTML:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Admin Server - Pair [NUMBER]</title>
    <style>
        body { 
            font-family: Arial; 
            text-align: center; 
            padding: 50px;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
        }
        .container {
            background: rgba(255,255,255,0.1);
            padding: 30px;
            border-radius: 15px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🐧 Linux Admin Training Server</h1>
        <h2>Pair: [YOUR_NUMBER]</h2>
        <p>Server Time: <span id="time"></span></p>
        <hr>
        <h3>System Info</h3>
        <p>Hostname: <?php echo gethostname(); ?></p>
    </div>
    <script>
        setInterval(function() {
            document.getElementById('time').innerHTML = new Date().toLocaleString();
        }, 1000);
    </script>
</body>
</html>
```

```bash
# Restart nginx
sudo systemctl restart nginx

# ทดสอบใน browser
curl http://localhost
```

### 4.2 Setup Cron Jobs

```bash
# ดู cron jobs ปัจจุบัน
crontab -l
sudo crontab -l

# สร้าง cron job
sudo crontab -e
# เลือก nano (1)
```

เพิ่มบรรทัดนี้:

```cron
# Run health check every 5 minutes
*/5 * * * * /opt/scripts/health_check.sh > /var/log/custom/health_$(date +\%Y\%m\%d_\%H\%M).log 2>&1

# Clean old logs daily at 2 AM
0 2 * * * find /var/log/custom -name "*.log" -mtime +7 -delete
```

```bash
# Save และออก (Ctrl+X, Y, Enter)

# รอ 5 นาที แล้วตรวจสอบ
ls -la /var/log/custom/
```

---

## 💾 Task 5: Backup & Recovery (20 นาที)

### 5.1 สร้าง Backup Script

```bash
sudo nano /opt/scripts/backup.sh
```

```bash
#!/bin/bash
BACKUP_DIR="/var/backups/daily"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="$BACKUP_DIR/backup_$DATE.tar.gz"

echo "Starting backup at $(date)"

# Backup important directories
tar -czf $BACKUP_FILE \
    /etc/passwd \
    /etc/group \
    /etc/nginx/ \
    /var/www/html/ \
    /opt/scripts/ \
    2>/dev/null

if [ $? -eq 0 ]; then
    echo "✅ Backup successful: $BACKUP_FILE"
    ls -lh $BACKUP_FILE
    
    # Keep only last 5 backups
    cd $BACKUP_DIR
    ls -t backup_*.tar.gz | tail -n +6 | xargs rm -f 2>/dev/null
else
    echo "❌ Backup failed!"
fi

echo "Backup completed at $(date)"
```

```bash
# ทำให้รันได้และทดสอบ
sudo chmod +x /opt/scripts/backup.sh
sudo /opt/scripts/backup.sh

# ดูไฟล์ backup
ls -la /var/backups/daily/
```

### 5.2 ทดสอบ Recovery

```bash
# สร้างสถานการณ์: ลบไฟล์
sudo rm /var/www/html/index.html
ls /var/www/html/

# Restore จาก backup
cd /tmp
sudo tar -xzf /var/backups/daily/backup_*.tar.gz
sudo cp -r var/www/html/index.html /var/www/html/
ls /var/www/html/

# ตรวจสอบ
curl http://localhost | head -20
```

---

## 🛡️ Task 6: Security Hardening (20 นาที)

### 6.1 Setup Firewall

```bash
# ตรวจสอบ firewall
sudo ufw status

# ตั้งค่า basic rules
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https

# Enable firewall
sudo ufw --force enable
sudo ufw status verbose
```

### 6.2 Setup Fail2ban

```bash
# ตรวจสอบ fail2ban
sudo systemctl status fail2ban

# ดู jail configuration
sudo fail2ban-client status
sudo fail2ban-client status sshd

# สร้าง custom jail
sudo nano /etc/fail2ban/jail.local
```

ใส่:

```ini
[DEFAULT]
bantime = 3600
findtime = 600
maxretry = 3

[sshd]
enabled = true
port = ssh
logpath = /var/log/auth.log

[nginx-http-auth]
enabled = true
port = http,https
logpath = /var/log/nginx/error.log
```

```bash
# Restart fail2ban
sudo systemctl restart fail2ban
sudo fail2ban-client status
```

### 6.3 Security Audit Script

```bash
sudo nano /opt/scripts/security_audit.sh
```

```bash
#!/bin/bash
echo "=== Security Audit Report ==="
echo "Date: $(date)"
echo ""

echo "=== Open Ports ==="
sudo ss -tulpn | grep LISTEN

echo ""
echo "=== Users with sudo access ==="
grep -Po '^sudo:.*:\K.*$' /etc/group

echo ""
echo "=== Failed login attempts (last 24h) ==="
sudo grep "Failed password" /var/log/auth.log | tail -5

echo ""
echo "=== Firewall Status ==="
sudo ufw status

echo ""
echo "=== Running Services ==="
systemctl list-units --type=service --state=running | grep running
```

```bash
sudo chmod +x /opt/scripts/security_audit.sh
sudo /opt/scripts/security_audit.sh
```

---

# ส่วนที่ 3: Challenge Tasks (30 นาที)

## 🏆 Mini Challenges (ทำเป็นคู่)

### Challenge 1: Performance Troubleshooting
```bash
# สร้าง high CPU load
stress --cpu 2 --timeout 30s &

# ใช้ tools หา process ที่ใช้ CPU สูง
top
htop
ps aux --sort=-%cpu | head

# Kill the process
killall stress
```

### Challenge 2: Disk Space Management
```bash
# สร้างไฟล์ใหญ่
dd if=/dev/zero of=/tmp/bigfile bs=100M count=5

# หา files ที่ใหญ่ที่สุด
du -sh /tmp/* | sort -hr | head -5
find /tmp -type f -size +100M

# Cleanup
rm /tmp/bigfile
```

### Challenge 3: Automated Report
สร้าง script ที่รวมทุก health checks:

```bash
sudo nano /opt/scripts/daily_report.sh
# รวม health_check, log_analyzer, security_audit
# ส่งผลไปที่ /var/log/custom/daily_report.txt
```

---

# 📝 การส่งงาน

## สิ่งที่ต้องส่ง:

### 1. Screenshots (ทั้งคู่)
- [ ] EC2 Instance running
- [ ] SSH connection successful
- [ ] Health check script output
- [ ] User management (ls -la /shared/)
- [ ] Web page ที่แก้ไขแล้ว
- [ ] Firewall status
- [ ] Backup files list

### 2. Scripts (upload ไฟล์)
- [ ] `/opt/scripts/health_check.sh`
- [ ] `/opt/scripts/log_analyzer.sh`
- [ ] `/opt/scripts/monitor.sh`
- [ ] `/opt/scripts/backup.sh`
- [ ] `/opt/scripts/security_audit.sh`

### 3. รายงาน (1-2 หน้า)
```markdown
# Lab Report - Pair [NUMBER]

## สมาชิกกลุ่ม
1. ชื่อ: _____ รหัส: _____
2. ชื่อ: _____ รหัส: _____

## Instance Information
- Instance ID: i-xxxxxxxxxx
- Public IP: xx.xx.xx.xx
- Instance Type: t3.micro

## ปัญหาที่พบและวิธีแก้
1. ปัญหา: _____
   แก้ไข: _____

## สิ่งที่ได้เรียนรู้
1. _____
2. _____
3. _____

## Feedback การทำงานคู่
- สิ่งที่ทำได้ดี: _____
- สิ่งที่ควรปรับปรุง: _____
```

---

## 🧹 Cleanup (สำคัญ!)

เมื่อเสร็จ Lab:

```bash
# 1. Backup your scripts
tar -czf ~/scripts_backup.tar.gz /opt/scripts/

# 2. Download via MobaXterm หรือ scp
scp -i pair01-key.pem ubuntu@[IP]:~/scripts_backup.tar.gz .
```

**ใน AWS Console:**
1. EC2 → Instances
2. เลือก instance → Actions → Instance State → **Stop**
3. หรือ **Terminate** ถ้าไม่ใช้แล้ว

---

## 📚 Resources เพิ่มเติม

- [Ubuntu Server Guide](https://ubuntu.com/server/docs)
- [Linux Command Reference](https://ss64.com/bash/)
- [AWS EC2 Documentation](https://docs.aws.amazon.com/ec2/)

---

## ⚡ Quick Reference Commands

| Task | Command |
|------|---------|
| Check system info | `uname -a` |
| View running processes | `htop` or `top` |
| Check disk usage | `df -h` |
| View logs | `journalctl -xe` |
| List services | `systemctl list-units` |
| Check network | `ss -tulpn` |
| Find files | `find / -name "*.log"` |
| Monitor in real-time | `tail -f /var/log/syslog` |

---

**🎯 Good luck with your Lab! Happy Learning!**
