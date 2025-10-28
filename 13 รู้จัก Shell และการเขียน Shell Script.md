# Shell, Process Management และการเขียน Shell Script เบื้องต้น

## บทที่ 1: ความเข้าใจพื้นฐานเกี่ยวกับ Shell

### 1.1 Shell คืออะไร

Shell เป็นโปรแกรมที่ทำหน้าที่เป็นตัวกลางระหว่างผู้ใช้และระบบปฏิบัติการ (Operating System Kernel) ทำหน้าที่แปลคำสั่งที่ผู้ใช้พิมพ์เป็นภาษาที่เคอร์เนลเข้าใจ และส่งผลลัพธ์กลับมาแสดงให้ผู้ใช้เห็น

**ประเภทของ Shell ที่พบบ่อย:**
- **Bourne Shell (sh)** - Shell ดั้งเดิมของ Unix
- **Bash (Bourne Again Shell)** - พัฒนาต่อจาก sh, เป็นมาตรฐานใน Linux
- **Zsh (Z Shell)** - มีความสามารถขั้นสูง, ใช้เป็น default ใน macOS
- **Fish (Friendly Interactive Shell)** - เน้นความง่ายในการใช้งาน

### 1.2 การทำงานของ Shell

Shell ทำงานแบบวนรอบ (Loop) ดังนี้:

```
1. แสดง Prompt ($, #, หรืออื่นๆ)
2. รอรับคำสั่งจากผู้ใช้
3. แปลความหมายคำสั่ง (Parse)
4. ดำเนินการคำสั่ง (Execute)
5. แสดงผลลัพธ์
6. กลับไปขั้นตอนที่ 1
```

**ตัวอย่างการทำงาน:**
```bash
$ echo "Hello World"    # ผู้ใช้พิมพ์คำสั่ง
Hello World            # Shell แสดงผลลัพธ์
$ pwd                  # คำสั่งแสดงตำแหน่งปัจจุบัน
/home/username         # ผลลัพธ์
```

### 1.3 Shell Variables และ Environment

Shell เก็บข้อมูลในรูปแบบตัวแปร 2 ประเภท:

**1. Local Variables** - ใช้ได้เฉพาะใน Shell session นั้น
```bash
$ name="Student"
$ echo $name
Student
```

**2. Environment Variables** - ส่งต่อไปยัง process ลูก
```bash
$ export PATH=/usr/local/bin:$PATH
$ echo $HOME
/home/username
$ echo $USER
username
```

---

## บทที่ 2: Process และการจัดการ Process

### 2.1 Process คืออะไร

Process คือโปรแกรมที่กำลังถูกประมวลผล (executing program) ในระบบ โดยแต่ละ process จะมี:
- **PID (Process ID)** - หมายเลขประจำตัวที่ไม่ซ้ำกัน
- **PPID (Parent Process ID)** - หมายเลข PID ของ process แม่
- **Memory Space** - พื้นที่หน่วยความจำที่ใช้
- **CPU Time** - เวลาที่ใช้ประมวลผล
- **State** - สถานะการทำงาน (Running, Sleeping, Stopped, Zombie)

### 2.2 การดู Process ด้วย ps

คำสั่ง `ps` แสดงสถานะ process:

```bash
$ ps           # แสดงเฉพาะ process ใน terminal ปัจจุบัน
  PID TTY          TIME CMD
 2345 pts/0    00:00:00 bash
 2567 pts/0    00:00:00 ps

$ ps aux       # แสดงทุก process ในระบบ
USER  PID %CPU %MEM    VSZ   RSS TTY  STAT START TIME COMMAND
root    1  0.0  0.4 225768  9456 ?    Ss   09:00 0:01 /sbin/init
```

**ความหมายของคอลัมน์:**
- `%CPU` - เปอร์เซ็นต์การใช้ CPU
- `%MEM` - เปอร์เซ็นต์การใช้หน่วยความจำ
- `STAT` - สถานะ (S=Sleep, R=Running, Z=Zombie)

### 2.3 การใช้ top และ htop

#### คำสั่ง top
`top` แสดงข้อมูล process แบบ real-time:

```bash
$ top
```

**ส่วนสำคัญของหน้าจอ top:**

```
top - 10:30:45 up 5 days, 3:22, 2 users, load average: 0.15, 0.20, 0.18
Tasks: 256 total,   1 running, 255 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.5 us,  1.0 sy,  0.0 ni, 96.5 id,  0.0 wa,  0.0 hi,  0.0 si
MiB Mem :  16384.0 total,   5120.0 free,   8192.0 used,   3072.0 buff/cache
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used,   7168.0 avail Mem
```

**การอ่านค่า Load Average:**
- ค่า 3 ตัว = ค่าเฉลี่ย 1, 5, และ 15 นาที
- ถ้ามี CPU 4 cores, load average ไม่ควรเกิน 4.0

**คำสั่งควบคุมใน top:**
- `k` - kill process (ต้องใส่ PID)
- `r` - renice (เปลี่ยนลำดับความสำคัญ)
- `q` - ออกจาก top
- `M` - เรียงตาม Memory usage
- `P` - เรียงตาม CPU usage

#### คำสั่ง htop
`htop` เป็นเวอร์ชันที่พัฒนาขึ้นมา มี interface ที่ใช้งานง่ายกว่า:

```bash
$ htop
```

**ข้อดีของ htop:**
- แสดงผลเป็นสี ง่ายต่อการอ่าน
- ใช้ mouse เลือก process ได้
- แสดง CPU และ Memory เป็นแท่งกราฟ
- สามารถ scroll ดู process ทั้งหมดได้

### 2.4 การจัดการ Process

**การส่ง Signal ให้ Process:**
```bash
$ kill PID          # ส่ง SIGTERM (ขอให้ปิดตัวเอง)
$ kill -9 PID       # ส่ง SIGKILL (บังคับปิดทันที)
$ killall firefox   # ปิดทุก process ชื่อ firefox
```

**การรัน Process แบบ Background:**
```bash
$ long_command &    # รันเป็น background
$ jobs              # ดู background jobs
$ fg %1             # นำ job 1 มา foreground
$ bg %1             # ส่ง job 1 ไป background
```

---

## บทที่ 3: การเขียน Shell Script เบื้องต้น

### 3.1 โครงสร้างพื้นฐานของ Shell Script

Shell Script คือไฟล์ text ที่บรรจุคำสั่ง shell หลายคำสั่ง เพื่อให้ทำงานอัตโนมัติตามลำดับ

**โครงสร้างพื้นฐาน:**
```bash
#!/bin/bash
# นี่คือ comment - อธิบายการทำงานของ script

# ประกาศตัวแปร
VARIABLE="value"

# คำสั่งต่างๆ
echo "Hello from script"
```

### 3.2 การสร้างและรัน Script

**ขั้นตอนการสร้าง:**
```bash
$ nano my_script.sh     # สร้างไฟล์
$ chmod +x my_script.sh # ให้สิทธิ์ execute
$ ./my_script.sh        # รัน script
```

### 3.3 ตัวแปรและการรับค่า

```bash
#!/bin/bash

# ตัวแปรปกติ
NAME="Linux"
echo "System: $NAME"

# รับค่าจาก command line arguments
echo "Script name: $0"
echo "First argument: $1"
echo "All arguments: $@"
echo "Number of arguments: $#"

# รับ input จากผู้ใช้
read -p "Enter your name: " USER_NAME
echo "Hello, $USER_NAME"
```

### 3.4 เงื่อนไขและการวนซ้ำ

**If-else statement:**
```bash
#!/bin/bash

if [ -f "/etc/passwd" ]; then
    echo "File exists"
else
    echo "File not found"
fi

# เปรียบเทียบตัวเลข
NUMBER=10
if [ $NUMBER -gt 5 ]; then
    echo "Greater than 5"
fi
```

**For loop:**
```bash
#!/bin/bash

# Loop through numbers
for i in 1 2 3 4 5; do
    echo "Number: $i"
done

# Loop through files
for file in *.txt; do
    echo "Processing $file"
done
```

**While loop:**
```bash
#!/bin/bash

COUNTER=0
while [ $COUNTER -lt 5 ]; do
    echo "Counter: $COUNTER"
    COUNTER=$((COUNTER + 1))
done
```

### 3.5 Functions

```bash
#!/bin/bash

# ประกาศ function
check_disk_usage() {
    echo "Checking disk usage..."
    df -h | grep "/$"
}

# เรียกใช้ function
check_disk_usage
```

---

## บทที่ 4: ตัวอย่าง Script ที่ใช้งานจริง

### 4.1 System Monitoring Script

```bash
#!/bin/bash
# system_monitor.sh - ตรวจสอบทรัพยากรระบบ

echo "=== System Resource Monitor ==="
echo "Date: $(date)"
echo "----------------------------"

# CPU Usage
echo "CPU Load Average:"
uptime | awk -F'load average:' '{print $2}'

# Memory Usage
echo -e "\nMemory Usage:"
free -h | grep "^Mem:" | awk '{print "Total: "$2", Used: "$3", Free: "$4}'

# Disk Usage
echo -e "\nDisk Usage:"
df -h / | tail -1 | awk '{print "Used: "$3" of "$2" ("$5")"}'

# Top 5 CPU consuming processes
echo -e "\nTop 5 CPU Processes:"
ps aux --sort=-%cpu | head -6 | awk '{print $11, $3"%"}'
```

### 4.2 Backup Script

```bash
#!/bin/bash
# backup.sh - สำรองข้อมูลอัตโนมัติ

SOURCE_DIR="$HOME/Documents"
BACKUP_DIR="$HOME/Backups"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="backup_$DATE.tar.gz"

# สร้าง directory สำหรับ backup
mkdir -p "$BACKUP_DIR"

# ทำการ backup
echo "Starting backup..."
tar -czf "$BACKUP_DIR/$BACKUP_FILE" "$SOURCE_DIR" 2>/dev/null

if [ $? -eq 0 ]; then
    echo "Backup completed: $BACKUP_FILE"
    # ลบ backup เก่าที่เกิน 7 วัน
    find "$BACKUP_DIR" -name "backup_*.tar.gz" -mtime +7 -delete
else
    echo "Backup failed!"
    exit 1
fi
```

### 4.3 Process Management Script

```bash
#!/bin/bash
# process_check.sh - ตรวจสอบและจัดการ process

PROCESS_NAME="firefox"
MAX_CPU=50  # Maximum CPU percentage

# ตรวจสอบว่า process ทำงานอยู่หรือไม่
if pgrep -x "$PROCESS_NAME" > /dev/null; then
    echo "$PROCESS_NAME is running"
    
    # ตรวจสอบการใช้ CPU
    CPU_USAGE=$(ps aux | grep -E "^.*$PROCESS_NAME" | awk '{print $3}' | head -1)
    
    if (( $(echo "$CPU_USAGE > $MAX_CPU" | bc -l) )); then
        echo "Warning: High CPU usage ($CPU_USAGE%)"
        read -p "Kill process? (y/n): " ANSWER
        if [ "$ANSWER" = "y" ]; then
            pkill "$PROCESS_NAME"
            echo "Process terminated"
        fi
    fi
else
    echo "$PROCESS_NAME is not running"
fi
```

---

## สรุปและแนวทางการศึกษาเพิ่มเติม

### ประโยชน์ของ Shell Script
1. **Automation** - ลดการทำงานซ้ำๆ
2. **System Administration** - จัดการระบบได้ง่ายขึ้น
3. **Batch Processing** - ประมวลผลข้อมูลจำนวนมาก
4. **Task Scheduling** - ใช้ร่วมกับ cron สำหรับงานตามเวลา

### Best Practices
1. ใส่ comment อธิบายการทำงาน
2. ตรวจสอบ error handling
3. ใช้ชื่อตัวแปรที่สื่อความหมาย
4. ทดสอบ script ในสภาพแวดล้อมที่ปลอดภัย
5. ทำ backup ก่อนรัน script ที่แก้ไขระบบ

### แหล่งเรียนรู้เพิ่มเติม
- `man bash` - คู่มือ Bash shell
- `man top` - คู่มือคำสั่ง top
- GNU Bash Manual
- Advanced Bash Scripting Guide
