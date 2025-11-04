# คู่มือปฏิบัติการ: Linux System Admin บน AWS Academy
## 🎯 Individual Lab Activity - 3 ชั่วโมง

---

## 📋 ข้อมูลทั่วไป

**วัตถุประสงค์:** ฝึกทักษะ System Administrator แบบ hands-on ผ่าน AWS EC2  
**รูปแบบ:** ปฏิบัติเดี่ยว (Individual)  
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

## ⏰ Timeline การทำ Lab

| เวลา | กิจกรรม | รายละเอียด |
|------|---------|------------|
| 0:00-0:30 | Setup EC2 & Connect | สร้าง Instance และเชื่อมต่อ |
| 0:30-1:00 | System Health & Users | ตรวจสอบระบบและจัดการผู้ใช้ |
| 1:00-1:30 | Logs & Monitoring | วิเคราะห์ logs และ monitoring |
| 1:30-2:00 | Services & Automation | จัดการ services และ cron |
| 2:00-2:30 | Backup & Security | สำรองข้อมูลและความปลอดภัย |
| 2:30-3:00 | Challenges & Submit | ทำ challenges และส่งงาน |

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
| **Name** | `std[รหัสนักศึกษา]-ubuntu` <br>เช่น `std6401001-ubuntu` |
| **OS Image** | คลิก **Ubuntu** → เลือก **Ubuntu Server 24.04 LTS** |
| **Instance type** | `t3.micro` (Free tier eligible) |
| **Key pair** | คลิก **Create new key pair** <br>- Name: `std[รหัส]-key` <br>- Type: **RSA** <br>- Format: **.pem** (Mac/Linux) หรือ **.ppk** (Windows) <br>→ **ดาวน์โหลดและเก็บไว้ให้ดี!** |

### 2.3 Network Settings และ Configure Storage

**Network Settings:**
```
✅ ติ๊ก "Allow SSH traffic from" → เลือก "Anywhere"
✅ ติ๊ก "Allow HTTP traffic from the internet"
✅ ติ๊ก "Allow HTTPS traffic from the internet"
```

**Configure Storage:**
```
เปลี่ยนจาก 8 เป็น 30 GiB
(GiB = Gibibyte = 2^30 bytes)
```

💡 **เหตุผลที่ใช้ 30 GB:**
- Docker images ใช้พื้นที่มาก (~2-5 GB)
- Log files สะสมเร็ว
- Backup files ต้องการพื้นที่
- Multiple users และ home directories
- ฝึกทำ Lab ได้สะดวกไม่ต้องกังวลพื้นที่เต็ม

### 2.4 Launch และรอ

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
chmod 400 std6401001-key.pem

# เชื่อมต่อ
ssh -i std6401001-key.pem ubuntu@[Public-IP]
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

sudo apt-get upgrade


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
sudo hostnamectl set-hostname student-admin-server

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

**📝 บันทึกผลลัพธ์:**

| ข้อมูล | ค่าที่ได้ |
|--------|----------|
| Hostname | _________________ |
| OS Version | _________________ |
| Uptime | _________________ |
| Disk Usage | _________________ |
| Memory Usage | _________________ |
| Services Running | _________________ |

---

## 👥 Task 2: User Management (25 นาที)

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

**📝 บันทึกตาราง permissions:**

| User | Groups | Can Access | Cannot Access |
|------|--------|------------|---------------|
| alice | _________________ | _________________ | _________________ |
| bob | _________________ | _________________ | _________________ |
| charlie | _________________ | _________________ | _________________ |

---

## 🔍 Task 3: Log Analysis & Monitoring (30 นาที)

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
timeout 10 sudo /opt/scripts/monitor.sh
```

---

## 🔧 Task 4: Service Management (20 นาที)

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
    <title>Admin Server - Student [รหัสนักศึกษา]</title>
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
            max-width: 600px;
            margin: 0 auto;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🐧 Linux Admin Training Server</h1>
        <h2>Student ID: [รหัสนักศึกษา]</h2>
        <p>Name: [ชื่อ-นามสกุล]</p>
        <p>Server Time: <span id="time"></span></p>
        <hr>
        <h3>System Information</h3>
        <p>Ubuntu 24.04 LTS | nginx Web Server</p>
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
sleep 300
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
grep -Po '^sudo:.*:\K.* /etc/group

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

## 🏆 Individual Challenges

### Challenge 1: Performance Troubleshooting (10 นาที)
```bash
# Install stress tool
sudo apt-get install -y stress

# สร้าง high CPU load
stress --cpu 2 --timeout 30s &

# ใช้ tools หา process ที่ใช้ CPU สูง
top
htop
ps aux --sort=-%cpu | head

# Kill the process
killall stress
```

### Challenge 2: Disk Space Management (10 นาที)
```bash
# สร้างไฟล์ใหญ่
dd if=/dev/zero of=/tmp/bigfile bs=100M count=5

# หา files ที่ใหญ่ที่สุด
du -sh /tmp/* | sort -hr | head -5
find /tmp -type f -size +100M

# Cleanup
rm /tmp/bigfile
```

### Challenge 3: Automated Daily Report (10 นาที)
สร้าง script ที่รวมทุก health checks:

```bash
sudo nano /opt/scripts/daily_report.sh
```

```bash
#!/bin/bash
REPORT_FILE="/var/log/custom/daily_report_$(date +%Y%m%d).txt"

echo "=== Daily System Report ===" > $REPORT_FILE
echo "Generated: $(date)" >> $REPORT_FILE
echo "" >> $REPORT_FILE

echo "=== Health Check ===" >> $REPORT_FILE
/opt/scripts/health_check.sh >> $REPORT_FILE
echo "" >> $REPORT_FILE

echo "=== Security Audit ===" >> $REPORT_FILE
/opt/scripts/security_audit.sh >> $REPORT_FILE
echo "" >> $REPORT_FILE

echo "=== Backup Status ===" >> $REPORT_FILE
ls -lah /var/backups/daily/backup_*.tar.gz | tail -5 >> $REPORT_FILE

echo "Report saved to: $REPORT_FILE"
cat $REPORT_FILE
```

```bash
sudo chmod +x /opt/scripts/daily_report.sh
sudo /opt/scripts/daily_report.sh
```

---

# 📝 การส่งงาน

## สิ่งที่ต้องส่ง:

### 1. Screenshots (10 รูป)
- [ ] EC2 Instance running
- [ ] SSH connection successful  
- [ ] Health check script output
- [ ] User management (ls -la /shared/)
- [ ] Log analyzer output
- [ ] Web page ที่แก้ไขแล้ว (จาก browser)
- [ ] Monitoring dashboard
- [ ] Firewall status (ufw status)
- [ ] Backup files list
- [ ] Daily report output

### 2. Scripts (upload ไฟล์ .sh)
รวมไฟล์ scripts ทั้งหมดในไฟล์ zip:
```bash
# สร้าง zip file
cd ~
zip -r scripts_[รหัสนักศึกษา].zip /opt/scripts/*.sh

# ตรวจสอบไฟล์ใน zip
unzip -l scripts_[รหัสนักศึกษา].zip

# Download ผ่าน MobaXterm หรือ scp
scp -i std[รหัส]-key.pem ubuntu@[IP]:~/scripts_*.zip .
```

**Scripts ที่ต้องมี:**
- [ ] `health_check.sh`
- [ ] `log_analyzer.sh`
- [ ] `monitor.sh`
- [ ] `backup.sh`
- [ ] `security_audit.sh`
- [ ] `daily_report.sh`

### 3. รายงาน (1-2 หน้า)
สร้างไฟล์ **report_[รหัสนักศึกษา].md** ตามรูปแบบนี้:

```markdown
# Lab Report - Linux System Administration

## ข้อมูลนักศึกษา
- ชื่อ-นามสกุล: _____________________
- รหัสนักศึกษา: _____________________
- วันที่ทำ Lab: _____________________

## Instance Information
- Instance ID: i-xxxxxxxxxx
- Public IP: xx.xx.xx.xx
- Instance Type: t3.micro
- AMI: Ubuntu Server 24.04 LTS
- Storage: 30 GiB

## สรุปงานที่ทำ
### Task 1: System Health Check
- [อธิบายสิ่งที่ทำและผลลัพธ์]

### Task 2: User Management
- [อธิบายการจัดการ users และ permissions]

### Task 3: Log Analysis
- [อธิบายการวิเคราะห์ logs]

### Task 4: Service Management
- [อธิบายการจัดการ services]

### Task 5: Backup & Recovery
- [อธิบายระบบ backup]

### Task 6: Security
- [อธิบายการตั้งค่าความปลอดภัย]

## ปัญหาที่พบและวิธีแก้ไข
1. ปัญหา: _____________________
   วิธีแก้: _____________________

2. ปัญหา: _____________________
   วิธีแก้: _____________________

## สิ่งที่ได้เรียนรู้
1. _____________________
2. _____________________
3. _____________________

## การประยุกต์ใช้ในอนาคต
[อธิบายว่าจะนำความรู้ไปใช้อย่างไร]
```

---

## 🧹 Cleanup (สำคัญ!)

### Backup ข้อมูลก่อน Cleanup:

```bash
# 1. Backup all scripts
tar -czf ~/lab_backup_$(date +%Y%m%d).tar.gz \
    /opt/scripts/ \
    /var/www/html/index.html \
    /var/backups/daily/

# 2. Download backup file
# ผ่าน MobaXterm: ลาก file จาก panel ซ้าย
# หรือใช้ scp:
scp -i std[รหัส]-key.pem ubuntu@[IP]:~/lab_backup_*.tar.gz .
```

### Stop หรือ Terminate Instance:

**ใน AWS Console:**
1. EC2 → Instances
2. เลือก instance ของคุณ
3. Actions → Instance State → **Stop** (หยุดชั่วคราว)
   หรือ
4. Actions → Instance State → **Terminate** (ลบถาวร)

⚠️ **คำเตือน:**
- **Stop** = หยุดชั่วคราว ยังเก็บข้อมูล สามารถ Start ใหม่ได้
- **Terminate** = ลบถาวร ข้อมูลหายทั้งหมด ใช้เมื่อส่งงานเสร็จแล้ว

---

## 📊 เกณฑ์การประเมิน

| หัวข้อ | คะแนน |
|--------|--------|
| Screenshots ครบถ้วน | 20% |
| Scripts ทำงานได้ถูกต้อง | 30% |
| รายงานละเอียดครบถ้วน | 20% |
| Security Setup | 15% |
| Backup & Recovery | 15% |
| **รวม** | **100%** |

---

## 📚 Resources เพิ่มเติม

### Official Documentation
- [Ubuntu Server Guide](https://ubuntu.com/server/docs)
- [AWS EC2 Documentation](https://docs.aws.amazon.com/ec2/)
- [Nginx Documentation](https://nginx.org/en/docs/)

### Command References
- [Linux Command Cheat Sheet](https://ss64.com/bash/)
- [Cron Expression Generator](https://crontab.guru/)
- [Vim Cheat Sheet](https://vim.rtorr.com/)

### Learning Paths
- [Linux Foundation Training](https://training.linuxfoundation.org/)
- [AWS Training and Certification](https://aws.amazon.com/training/)
- [Linux Journey](https://linuxjourney.com/)

---

## ⚡ Quick Command Reference

| Task | Command |
|------|---------|
| **System Info** |  |
| Check system info | `uname -a` |
| View distribution | `lsb_release -a` |
| Check uptime | `uptime` |
| **Process Management** |  |
| View processes | `htop` or `top` |
| Kill process | `kill [PID]` or `killall [name]` |
| Background job | `command &` |
| **File Management** |  |
| Find files | `find / -name "*.log"` |
| Disk usage | `df -h` |
| Directory size | `du -sh /path/` |
| **User Management** |  |
| Add user | `sudo useradd -m username` |
| Change password | `sudo passwd username` |
| Add to group | `sudo usermod -aG group user` |
| **Network** |  |
| Check ports | `ss -tulpn` |
| Test connectivity | `ping -c 3 google.com` |
| View IP | `ip a` |
| **Services** |  |
| Start service | `sudo systemctl start service` |
| Enable on boot | `sudo systemctl enable service` |
| Check status | `systemctl status service` |
| **Logs** |  |
| View system logs | `journalctl -xe` |
| Follow log file | `tail -f /var/log/syslog` |
| Search in logs | `grep "error" /var/log/*.log` |

---

## 🎯 Checklist ก่อนส่งงาน

- [ ] Scripts ทั้ง 6 ไฟล์ทำงานได้
- [ ] Web page แสดงข้อมูลนักศึกษา
- [ ] Firewall และ fail2ban ทำงาน
- [ ] Cron job ถูกตั้งค่าแล้ว
- [ ] มี backup files อย่างน้อย 1 ไฟล์
- [ ] Screenshots ครบ 10 รูป
- [ ] รายงานเขียนครบทุกหัวข้อ
- [ ] Download backup ข้อมูลแล้ว
- [ ] Instance ถูก Stop/Terminate แล้ว
- [ ] ส่งงานใน LMS แล้ว

---

**🚀 Good luck with your Lab!**
**💪 You've got this!**
