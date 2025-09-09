# บทเรียน: เริ่มต้นเป็น Linux Developer จากมุมมองนักพัฒนา

## 🎯 เป้าหมายการเรียนรู้
หลังจบคลาสนี้ นักศึกษา จะสามารถ:
- เข้าใจพื้นฐาน Linux และความสำคัญสำหรับ Developer
- ใช้ MobaXterm เชื่อมต่อกับ Linux Server
- ใช้คำสั่ง Linux พื้นฐานสำหรับการพัฒนา
- สร้าง EC2 Instance ของตัวเองได้
- เตรียมพร้อมสำหรับการเป็น Developer ที่ใช้ Linux

---

## 📚 ความรู้เบื้องต้น: ทำไม Developer ต้องรู้จัก Linux?

### Linux คืออะไร? 🐧
- ระบบปฏิบัติการ Open Source ที่ฟรี
- ใช้ในเซิร์ฟเวอร์มากกว่า 90%
- เป็นพื้นฐานของ Android, Docker, Cloud Computing
- บริษัทใหญ่ๆ อย่าง Google, Facebook, Netflix ใช้ Linux

### ทำไม Developer ต้องเรียน Linux?
✅ **เซิร์ฟเวอร์ส่วนใหญ่ใช้ Linux** - เว็บไซต์และแอปที่เราใช้รันบน Linux  
✅ **เครื่องมือ Developer อยู่บน Linux** - Docker, Kubernetes, Git servers  
✅ **Cloud Computing** - AWS, Google Cloud, Azure ล้วนใช้ Linux  
✅ **การทำงานร่วมกัน** - Developer ทีมใช้ Linux เป็นมาตรฐาน  
✅ **ความเร็วและประสิทธิภาพ** - Linux เร็วและใช้ทรัพยากรน้อย

---

## 🕐 ตารางเวลาคลาส (3 ชั่วโมง)

| เวลา | กิจกรรม | รายละเอียด |
|------|---------|------------|
| 0:00-0:20 | **Introduction & Demo** | อาจารย์สาธิต AWS EC2 |
| 0:20-0:30 | **Setup MobaXterm** | ติดตั้งและเชื่อมต่อ |
| 0:30-1:15 | **Linux Basics for Developers** | คำสั่งพื้นฐานและการใช้งาน |
| 1:15-1:30 | **Break** | พักครึ่ง |
| 1:30-2:15 | **Developer Tools & Skills** | Git, Text Editors, Package Management |
| 2:15-2:45 | **Your First Project** | สร้างโปรเจ็กต์แรก |
| 2:45-3:00 | **Assignment: Create Your VM** | แนวทางสร้าง EC2 ของตัวเอง |

---

## ส่วนที่ 1: สาธิตการสร้าง EC2 Instance (20 นาที)

### 🎬 สิ่งที่อาจารย์จะสาธิต:

#### ขั้นตอนการสร้าง Ubuntu Server บน AWS
```
1. เข้า AWS Console → EC2 Dashboard
2. คลิก "Launch Instance"
3. เลือก "Ubuntu Server 22.04 LTS"
4. เลือก Instance Type: t2.micro (ฟรี)
5. สร้าง Key Pair สำหรับการเชื่อมต่อ
6. ตั้งค่า Security Group:
   - SSH (port 22) from anywhere
   - HTTP (port 80) from anywhere
   - HTTPS (port 443) from anywhere
7. Launch Instance
```

#### ขั้นตอนการสร้าง User Accounts
```bash
# อาจารย์จะรันคำสั่งเหล่านี้บนเซิร์ฟเวอร์
sudo useradd -m -s /bin/bash student01
sudo passwd student01
sudo usermod -aG sudo student01

# สร้าง users สำหรับนักเรียนทุกคน
for i in {01..30}; do
    sudo useradd -m -s /bin/bash student$i
    echo "student$i:Password123!" | sudo chpasswd
    sudo usermod -aG sudo student$i
done
```

### 📝 ข้อมูลที่นักศึกษาจะได้รับ:
```
Server IP: 13.250.xxx.xxx
Username: student01, student02, ..., student40
Password: Password123!
```

---

## ส่วนที่ 2: ติดตั้งและใช้ MobaXterm (10 นาที)

### 📥 ดาวน์โหลดและติดตั้ง
1. ไปที่ https://mobaxterm.mobatek.net/download.html
2. ดาวน์โหลด "MobaXterm Home Edition (Portable edition)"
3. แตกไฟล์และเปิดโปรแกรม

### 🔌 การเชื่อมต่อครั้งแรก
```
1. เปิด MobaXterm
2. คลิก "Session" → "SSH"
3. กรอกข้อมูล:
   - Remote host: 13.250.xxx.xxx (IP ที่ได้รับ)
   - Username: student## (หมายเลขที่ได้รับ)
   - Port: 22
4. คลิก "OK"
5. ใส่ Password: Password123!
```

### ✅ ทดสอบการเชื่อมต่อ
```bash
# เมื่อเชื่อมต่อสำเร็จ จะเห็น
student01@ip-172-31-xx-xx:~$ 

# ทดสอบคำสั่งแรก
whoami
pwd
date
```

---

## ส่วนที่ 3: Linux Basics for Developers (45 นาที)

### 3.1 การนำทางระบบไฟล์ (15 นาที)

```bash
# ตรวจสอบตำแหน่งปัจจุบัน
pwd                    # พิมพ์ตำแหน่งปัจจุบัน
ls                     # ดูไฟล์และโฟลเดอร์
ls -la                 # ดูรายละเอียดทั้งหมด รวมไฟล์ซ่อน

# การเปลี่ยนไดเรกทอรี
cd /home              # ไปที่โฟลเดอร์ /home
cd ~                  # กลับ home directory
cd ..                 # ขึ้นไปหนึ่งระดับ
cd -                  # กลับไปโฟลเดอร์ก่อนหน้า

# สร้างโครงสร้างสำหรับ Developer
mkdir -p ~/projects/{web,mobile,api}
mkdir -p ~/scripts
mkdir -p ~/docs
tree ~/projects 2>/dev/null || ls -la ~/projects
```

### 3.2 การจัดการไฟล์ (Developer Style) (15 นาที)

```bash
# สร้างไฟล์โปรเจ็กต์แรก
cd ~/projects/web
touch index.html style.css script.js
echo "Hello World from Linux!" > index.html

# สร้างไฟล์ README
cat << EOF > README.md
# My First Web Project

This is my first project on Linux!

## Files
- index.html - Main page
- style.css - Styling
- script.js - JavaScript code

## Author
Student: $(whoami)
Date: $(date)
EOF

# ดูเนื้อหาไฟล์
cat README.md
cat index.html

# คัดลอกไฟล์
cp index.html index_backup.html
cp -r ~/projects/web ~/projects/web_backup

# ค้นหาไฟล์
find ~/projects -name "*.html"
find ~/projects -name "README*"
```

### 3.3 คำสั่งที่ Developer ใช้บ่อย (15 นาที)

```bash
# ดูข้อมูลระบบ
uname -a              # ข้อมูลระบบ
lsb_release -a        # ข้อมูล Ubuntu
df -h                 # พื้นที่ดิสก์
free -h               # RAM ที่ใช้
top                   # กระบวนการที่กำลังทำงาน (กด q เพื่อออก)

# การจัดการ processes (สำคัญสำหรับ Developer)
ps aux | grep bash    # ดู bash processes
jobs                  # ดูงานที่รันใน background
killall -l            # ดูรายการ signals

# Network commands (สำคัญสำหรับ Web Dev)
curl http://httpbin.org/ip          # ตรวจสอบ IP ของเรา
wget -O test.html http://example.com # ดาวน์โหลดหน้าเว็บ
ping -c 3 google.com               # ทดสอบการเชื่อมต่อ

# ดูเนื้อหาไฟล์แบบ Developer
head -10 /var/log/syslog    # ดู 10 บรรทัดแรกของ log
tail -10 /var/log/syslog    # ดู 10 บรรทัดสุดท้าย
grep "error" /var/log/syslog | head -5  # ค้นหา error ใน log
```

---

## ส่วนที่ 4: Developer Tools & Skills (45 นาที)

### 4.1 Git - เครื่องมือสำคัญของ Developer (15 นาที)

```bash
# ติดตั้งและตั้งค่า Git
sudo apt update
sudo apt install -y git

# ตั้งค่า Git profile
git config --global user.name "Student $(whoami)"
git config --global user.email "$(whoami)@example.com"
git config --list

# สร้าง Git repository แรก
cd ~/projects/web
git init
git add .
git commit -m "Initial commit: My first web project"

# ดูประวัติ
git log --oneline
git status

# สร้างการเปลี่ยนแปลงใหม่
echo "<h1>Hello from Linux Server!</h1>" > index.html
echo "body { background-color: lightblue; }" > style.css
echo "console.log('Hello from Linux!');" > script.js

git add .
git commit -m "Add content to HTML, CSS, and JS files"
git log --oneline
```

### 4.2 Text Editors สำหรับ Developer (15 นาที)

```bash
# ทดลองใช้ nano (เริ่มต้น)
nano hello.py
# พิมพ์โค้ด Python:
print("Hello from Linux!")
print("I am a future developer!")
# กด Ctrl+O เพื่อบันทึก, กด Ctrl+X เพื่อออก

# ทดลองใช้ vim (advanced)
vim hello.sh
# กด i เพื่อเข้าโหมด insert
# พิมพ์:
#!/bin/bash
echo "Welcome to Linux Development!"
echo "Current user: $(whoami)"
echo "Current directory: $(pwd)"
# กด Esc แล้วพิมพ์ :wq เพื่อบันทึกและออก

# ทำให้ script รันได้และทดสอบ
chmod +x hello.sh
./hello.sh
python3 hello.py
```

### 4.3 Package Management (สำคัญสำหรับ Developer) (15 นาที)

```bash
# อัปเดตระบบ
sudo apt update
sudo apt list --upgradable

# ติดตั้งเครื่องมือ Developer
sudo apt install -y tree curl wget htop neofetch

# ติดตั้ง Development tools
sudo apt install -y build-essential python3-pip nodejs npm

# ตรวจสอบเวอร์ชัน
python3 --version
node --version
npm --version
gcc --version

# ติดตั้ง Python packages
pip3 install requests flask
pip3 list | grep -E "(requests|flask)"

# ติดตั้ง Node.js packages globally
sudo npm install -g express-generator create-react-app

# ดูข้อมูลระบบแบบสวย
neofetch
```

---

## ส่วนที่ 5: Your First Project (30 นาที)

### 5.1 สร้าง Web Server ง่ายๆ ด้วย Python (15 นาที)

```bash
# ไปที่โฟลเดอร์โปรเจ็กต์
cd ~/projects/web

# สร้าง Simple Web Server
cat << 'EOF' > server.py
#!/usr/bin/env python3
import http.server
import socketserver
from datetime import datetime

class MyHTTPRequestHandler(http.server.SimpleHTTPRequestHandler):
    def do_GET(self):
        if self.path == '/':
            self.send_response(200)
            self.send_header('Content-type', 'text/html')
            self.end_headers()
            
            html_content = f"""
            <!DOCTYPE html>
            <html>
            <head>
                <title>My Linux Server</title>
                <style>
                    body {{ 
                        font-family: Arial, sans-serif; 
                        text-align: center; 
                        background-color: #f0f8ff;
                        padding: 50px;
                    }}
                    h1 {{ color: #333; }}
                    .info {{ 
                        background: white; 
                        padding: 20px; 
                        border-radius: 10px; 
                        margin: 20px auto; 
                        max-width: 500px;
                        box-shadow: 0 2px 10px rgba(0,0,0,0.1);
                    }}
                </style>
            </head>
            <body>
                <h1>🐧 Welcome to My Linux Server!</h1>
                <div class="info">
                    <h2>Server Information</h2>
                    <p><strong>Current Time:</strong> {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}</p>
                    <p><strong>Running on:</strong> Ubuntu Linux</p>
                    <p><strong>Created by:</strong> Student Developer</p>
                    <p><strong>Status:</strong> ✅ Online and Learning!</p>
                </div>
                <p>🚀 I'm learning to be a Linux Developer!</p>
            </body>
            </html>
            """
            self.wfile.write(html_content.encode())
        else:
            super().do_GET()

PORT = 8000
with socketserver.TCPServer(("", PORT), MyHTTPRequestHandler) as httpd:
    print(f"🌐 Server running at http://localhost:{PORT}")
    print("Press Ctrl+C to stop")
    httpd.serve_forever()
EOF

# ทำให้ไฟล์รันได้
chmod +x server.py

# รันเซิร์ฟเวอร์ใน background
python3 server.py &
SERVER_PID=$!
echo "Server PID: $SERVER_PID"

# ทดสอบเซิร์ฟเวอร์
sleep 2
curl http://localhost:8000
```

### 5.2 สร้าง API ง่ายๆ ด้วย Python Flask (15 นาที)

```bash
# สร้างโฟลเดอร์สำหรับ API
mkdir -p ~/projects/api
cd ~/projects/api

# สร้าง Simple REST API
cat << 'EOF' > api.py
#!/usr/bin/env python3
from flask import Flask, jsonify, request
from datetime import datetime
import os

app = Flask(__name__)

# Sample data
students = [
    {"id": 1, "name": "สมชาย", "skill": "Python"},
    {"id": 2, "name": "สมหญิง", "skill": "JavaScript"},
    {"id": 3, "name": "วีระ", "skill": "Linux Admin"}
]

@app.route('/')
def home():
    return jsonify({
        "message": "🐧 Welcome to My Linux API!",
        "server": "Ubuntu Linux",
        "timestamp": datetime.now().isoformat(),
        "student": os.environ.get('USER', 'unknown')
    })

@app.route('/api/students')
def get_students():
    return jsonify(students)

@app.route('/api/students/<int:student_id>')
def get_student(student_id):
    student = next((s for s in students if s["id"] == student_id), None)
    if student:
        return jsonify(student)
    return jsonify({"error": "Student not found"}), 404

@app.route('/api/system')
def system_info():
    import platform
    import socket
    
    return jsonify({
        "hostname": socket.gethostname(),
        "platform": platform.platform(),
        "python_version": platform.python_version(),
        "current_user": os.environ.get('USER'),
        "working_directory": os.getcwd()
    })

if __name__ == '__main__':
    print("🚀 Starting Flask API Server...")
    app.run(host='0.0.0.0', port=5000, debug=True)
EOF

# ติดตั้ง Flask ถ้ายังไม่มี
pip3 install flask

# รัน API server ใน background
python3 api.py &
API_PID=$!
echo "API Server PID: $API_PID"

# ทดสอบ API
sleep 3
echo "=== Testing API Endpoints ==="
curl http://localhost:5000/
echo -e "\n"
curl http://localhost:5000/api/students
echo -e "\n"
curl http://localhost:5000/api/system
echo -e "\n"

# หยุดเซิร์ฟเวอร์
kill $SERVER_PID $API_PID 2>/dev/null
```

---

## ส่วนที่ 6: Assignment - สร้าง VM ของตัวเอง (15 นาที)

### 🎯 ภารกิจของน้องๆ: สร้าง EC2 Instance เป็นของตัวเอง!

#### ขั้นตอนที่ต้องทำ:

**1. สมัคร AWS Free Tier Account**
```
- ไปที่ https://aws.amazon.com
- คลิก "Create an AWS Account"
- ใช้อีเมลและข้อมูลจริง
- ใส่ข้อมูลบัตรเครดิต (จะไม่ถูกเรียกเก็บเงินใน Free Tier)
- ผ่านการยืนยันตัวตน
```

**2. สร้าง EC2 Instance**
```
Specifications ที่ต้องใช้:
- AMI: Ubuntu Server 22.04 LTS
- Instance Type: t2.micro (Free Tier)
- Storage: 8 GB gp2 (Free Tier)
- Security Group: SSH (22), HTTP (80), HTTPS (443)
- Key Pair: สร้างใหม่และดาวน์โหลด .pem file
```

**3. เชื่อมต่อด้วย MobaXterm**
```
- ใช้ Private Key (.pem file) แทน password
- Username: ubuntu (default สำหรับ Ubuntu AMI)
- Test การเชื่อมต่อ
```

**4. Setup Development Environment**
```bash
# อัปเดตระบบ
sudo apt update && sudo apt upgrade -y

# ติดตั้ง Developer Tools
sudo apt install -y git curl wget tree htop neofetch
sudo apt install -y build-essential python3-pip nodejs npm

# Clone โปรเจ็กต์จากคลาส
mkdir -p ~/projects
cd ~/projects
# สร้างโปรเจ็กต์ตามที่เรียนในคลาส
```

### 📋 Checklist สำหรับการส่งงาน:

```markdown
## ✅ รายการตรวจสอบ

### AWS Setup
- [ ] สร้าง AWS Account สำเร็จ
- [ ] สร้าง EC2 Instance (t2.micro, Ubuntu 22.04)
- [ ] ตั้งค่า Security Group ถูกต้อง
- [ ] ดาวน์โหลด Key Pair (.pem file)

### Connection
- [ ] เชื่อมต่อด้วย MobaXterm สำเร็จ
- [ ] รันคำสั่ง `whoami`, `pwd`, `date` ได้
- [ ] ทำ screenshot หน้าจอ Terminal

### Development Setup
- [ ] อัปเดตระบบ (apt update & upgrade)
- [ ] ติดตั้ง Git, Python, Node.js
- [ ] สร้างโครงสร้างโฟลเดอร์ ~/projects
- [ ] สร้างไฟล์ README.md ในโปรเจ็กต์

### Project Recreation
- [ ] สร้าง Web Server Python ได้รัน
- [ ] สร้าง Flask API ได้รัน
- [ ] ทดสอบ endpoints ด้วย curl
- [ ] Git init และ commit โปรเจ็กต์

### Documentation
- [ ] ถ่าย screenshot ทุกขั้นตอน
- [ ] เขียนรายงานสั้นๆ ว่าเรียนรู้อะไรบ้าง
- [ ] บันทึกปัญหาที่เจอและวิธีแก้
```

### 📤 การส่งงาน:

**สร้างรายงานในรูปแบบ:**
```bash
# สร้างไฟล์รายงาน
cat << EOF > ~/assignment_report.md
# รายงานการสร้าง EC2 Instance

## ข้อมูลพื้นฐาน
- ชื่อ: [ชื่อจริง]
- รหัสนักศึกษา: [รหัส]
- วันที่ทำ: $(date)

## ขั้นตอนที่ทำ
1. สร้าง AWS Account
2. Launch EC2 Instance
3. Connect ด้วย MobaXterm
4. Setup Development Environment
5. Recreate Projects from Class

## ปัญหาที่เจอ
[เขียนปัญหาและวิธีแก้]

## สิ่งที่เรียนรู้
[เขียนสิ่งที่ได้เรียนรู้]

## Screenshots
[แนบ screenshots สำคัญ]

## Instance Information
- Instance ID: [i-xxxxxxxxxxxxx]
- Public IP: [IP Address]
- Instance Type: t2.micro
- AMI: Ubuntu 22.04 LTS
EOF
```

## 🎓 สรุปคลาส

### สิ่งที่เราได้เรียนรู้วันนี้:
✅ **ความเข้าใจพื้นฐาน** - ทำไม Linux สำคัญสำหรับ Developer  
✅ **การใช้งานจริง** - เชื่อมต่อและใช้งาน Linux Server  
✅ **Developer Skills** - Git, Package Management, Text Editors  
✅ **Hands-on Project** - สร้าง Web Server และ API  
✅ **Independence** - สร้าง Environment ของตัวเองได้

### Next Steps สำหรับน้องๆ:
1. 🏠 **ทำการบ้าน** - สร้าง EC2 Instance ของตัวเอง
2. 📚 **ศึกษาเพิ่มเติม** - ตาม roadmap ที่แนะนำ
3. 💬 **เข้าร่วมชุมชน** - หาเพื่อนนักพัฒนาที่ใช้ Linux
4. 🔄 **ฝึกฝนสม่ำเสมอ** - ใช้ Linux เป็นประจำ
5. 🎯 **ตั้งเป้าหมาย** - เป็น Full-Stack Developer ที่เชี่ยวชาญ Linux




🚀 **Ready to be a Linux Developer? Let's code the future!** 🚀
