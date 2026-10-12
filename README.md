# Robot Framework (Intro & Examples)

โปรเจกต์ตัวอย่างการทดสอบเว็บแอปพลิเคชันอัตโนมัติ (Automated Web Testing) ด้วย **Robot Framework** ประกอบด้วยตัวอย่างชุดทดสอบ 2 รูปแบบ:

1. **SeleniumLibrary**: การทดสอบเว็บผ่าน Selenium WebDriver พร้อมการจัดการความสัมพันธ์ระหว่าง Test Case ด้วย `DependencyLibrary` และ custom Python utility (`utill.py`)
2. **PuppeteerLibrary (POC)**: การทดสอบเว็บแนวคิด Proof of Concept โดยใช้ `PuppeteerLibrary` และ Playwright

---

## โครงสร้างโปรเจกต์ (Project Structure)

```text
intro-robot-framework/
├── .gitignore                   # กำหนดไฟล์ที่ไม่ต้องการติดตามใน Git (log, report, xml, screenshots)
├── data.yaml                    # ไฟล์ตัวแปรและข้อมูลคอนฟิกสำหรับการทดสอบ (URL, browser, xunit_file, rerun)
├── requirements.txt             # รายการ Python dependencies หลักสำหรับ SeleniumLibrary suite
├── utill.py                     # Custom Python library สำหรับตรวจสอบสถานะของ Test Case จาก xUnit XML
├── script/                      # ไดเรกทอรีเก็บชุดทดสอบ Robot Framework
│   ├── sample_selenium.robot    # ชุดทดสอบตัวอย่างการใช้งาน SeleniumLibrary และ DependencyLibrary
│   └── sample_puppeteer.robot   # ชุดทดสอบตัวอย่าง (POC) การใช้งาน PuppeteerLibrary
└── README.md                    # เอกสารแนะนำการติดตั้งและใช้งานโปรเจกต์
```

---

## ข้อกำหนดเบื้องต้น (Prerequisites)

- **Python**: แนะนำเวอร์ชัน **Python 3.9+**
- **Google Chrome**: ติดตั้งเบราว์เซอร์ Google Chrome บนเครื่อง
- **ChromeDriver** (สำหรับ Selenium): ตรวจสอบว่าเวอร์ชันตรงกับ Google Chrome ที่ติดตั้งในเครื่องและสามารถเรียกใช้งานผ่าน system PATH ได้
- **Dependencies หลัก** (ระบุเวอร์ชันใน `requirements.txt`):
  - `robotframework==5.0.1`
  - `robotframework-seleniumlibrary==6.0.0`
  - `robotframework-dependencylibrary==3.0.1`
  - `PyYAML==6.0`

---

## การเตรียมสภาพแวดล้อมเสมือน (Virtual Environment Setup)

แนะนำให้สร้างและเปิดใช้งาน Virtual Environment ก่อนติดตั้ง dependencies:

### macOS / Linux

```bash
# สร้าง virtual environment
python3 -m venv .venv

# เปิดใช้งาน virtual environment
source .venv/bin/activate
```

### Windows

**Command Prompt (cmd):**

```cmd
:: สร้าง virtual environment
python -m venv .venv

:: เปิดใช้งาน virtual environment
.venv\Scripts\activate.bat
```

**PowerShell:**

```powershell
# สร้าง virtual environment
python -m venv .venv

# เปิดใช้งาน virtual environment
.venv\Scripts\Activate.ps1
```

---

## การติดตั้ง Dependencies (Installation)

### 1. ติดตั้ง Dependencies ชุดหลัก (สำหรับ SeleniumLibrary)

ติดตั้งแพ็กเกจที่ระบุไว้ใน `requirements.txt`:

```bash
pip install -r requirements.txt
```

หรือติดตั้งรายแพ็กเกจ:

```bash
pip install robotframework==5.0.1 robotframework-seleniumlibrary==6.0.0 robotframework-dependencylibrary==3.0.1 PyYAML==6.0
```

ตรวจสอบการติดตั้ง Robot Framework:

```bash
robot --version
```

### 2. ติดตั้ง Dependencies เสริม (สำหรับ PuppeteerLibrary + Playwright)

สำหรับชุดทดสอบ `script/sample_puppeteer.robot` มีการเรียกใช้งาน `Library PuppeteerLibrary` ซึ่งเป็นชุดทดสอบทางเลือก (POC) และไม่ได้รวมอยู่ใน `requirements.txt` หลัก หากต้องการรันชุดทดสอบนี้ ให้ติดตั้งแพ็กเกจเสริมและ browser binaries เพิ่มเติมดังนี้:

```bash
pip install robotframework-puppeteerlibrary
python -m playwright install
pyppeteer-install
```

---

## คำสั่งรันชุดทดสอบ (Test Execution)

### 1. ชุดทดสอบ Selenium (`script/sample_selenium.robot`)

ชุดทดสอบนี้อ่านค่าคอนฟิกเริ่มต้นจาก `data.yaml` โดยค่าเริ่มต้นของบราวเซอร์คือ `browser: headlesschrome`

#### รันทั้งไฟล์ Test Suite (Selenium)

```bash
robot -d results script/sample_selenium.robot
```

#### กำหนดตัวแปร Browser ผ่าน Command Line

สามารถส่งตัวแปร `-v browser:<browser_name>` เพื่อเปลี่ยนบราวเซอร์ได้ (รองรับเฉพาะใน Selenium suite เนื่องจากมีการเรียกใช้ `${browser}` ร่วมกับ `data.yaml`):

```bash
# รันแบบ Headless Chrome (ค่าเริ่มต้นใน data.yaml)
robot -d results -v browser:headlesschrome script/sample_selenium.robot

# รันแบบเปิดหน้าต่าง Chrome UI
robot -d results -v browser:chrome script/sample_selenium.robot
```

#### กรองการรันด้วย Tag (`-i` / `--include`)

ใน `sample_selenium.robot` มีการกำหนดแท็ก `demo` ไว้ที่ Test Cases:

```bash
robot -d results -i demo script/sample_selenium.robot
```

#### กรองการรันด้วยชื่อ Test Case ใน Selenium (`-t` / `--test`)

```bash
# รันเฉพาะ TC_001
robot -d results -t "TC_001 - Open Google" script/sample_selenium.robot

# รันเฉพาะ TC_002
# หมายเหตุ: TC_002 มีการเรียกใช้ DependencyLibrary เพื่อตรวจสอบว่า TC_001 ต้องผ่านก่อน
robot -d results -t "TC_002 - Search Google" script/sample_selenium.robot
```

#### การรันซ้ำด้วยตัวแปร rerun และ xUnit File

ในกรณีที่ต้องการรันเฉพาะ Test Case ที่ล้มเหลว หรือตรวจสอบผลจาก xUnit XML ก่อนหน้า สามารถส่งตัวแปร `${rerun}` และ `${xunit_file}` ได้ตามที่กำหนดไว้ใน `data.yaml`:

```bash
robot -d results -v rerun:true -v xunit_file:results/result.xml script/sample_selenium.robot
```

---

### 2. ชุดทดสอบ Puppeteer (`script/sample_puppeteer.robot`)

_ข้อสังเกต: ใน `sample_puppeteer.robot` กำหนด `browser=chrome` ไว้โดยตรง จึงไม่รองรับการส่งตัวแปร `${browser}` แต่รองรับการปรับแต่งการแสดงผลผ่านตัวแปร `${HEADLESS}` (ค่าเริ่มต้นคือ `False`)_

#### รันทั้งไฟล์ Test Suite (Puppeteer)

```bash
robot -d results script/sample_puppeteer.robot
```

#### กำหนดตัวแปร HEADLESS

```bash
# รันแบบ Headless (ไม่แสดงหน้าต่างเบราว์เซอร์)
robot -d results -v HEADLESS:True script/sample_puppeteer.robot

# รันแบบแสดงหน้าต่างเบราว์เซอร์ (Default)
robot -d results -v HEADLESS:False script/sample_puppeteer.robot
```

#### กรองการรันด้วยชื่อ Test Case ใน Puppeteer (`-t` / `--test`)

```bash
robot -d results -t "POC_PUPPETEER_001 - POC Puppeteer" script/sample_puppeteer.robot
```

---

## ผลลัพธ์การทดสอบ (Test Results & Output)

เมื่อกำหนดพารามิเตอร์ `-d results` (หรือโฟลเดอร์ผลลัพธ์ที่ต้องการ) Robot Framework จะสร้างไฟล์รายงานดังนี้:

- `report.html`: รายงานผลการทดสอบสรุปภาพรวม (Summary Report) แสดงสถิติ Pass/Fail
- `log.html`: รายละเอียดการทำงานเชิงลึก (Execution Log) แสดงข้อมูลของแต่ละ Keyword และ Screenshot
- `output.xml`: ผลการทดสอบในรูปแบบ XML เหมาะสำหรับการนำไปประมวลผลต่อบนระบบ CI/CD
- `*.png`: ภาพหน้าจอ (Screenshots) ที่บันทึกผ่านคีย์เวิร์ด `Capture Page Screenshot`

สามารถเปิดดูผลลัพธ์ผ่านเว็บเบราว์เซอร์:

- **macOS**: `open results/report.html` หรือ `open results/log.html`
- **Linux**: `xdg-open results/report.html`
- **Windows**: `start results\report.html`

---

## การแก้ไขปัญหาเบื้องต้น (Troubleshooting)

1. **WebDriverException / ChromeDriver not found**:
   - ตรวจสอบว่าได้ติดตั้ง Google Chrome และ ChromeDriver เรียบร้อยแล้ว
   - ตรวจสอบว่าเวอร์ชันของ ChromeDriver ตรงกับ Google Chrome
   - ตรวจสอบว่า path ของ ChromeDriver อยู่ใน system PATH

2. **การรันบนเซิร์ฟเวอร์แบบไม่มี GUI (CI/CD / Linux Server)**:
   - หากรันบน Environment ที่ไม่มีหน้าจอ ให้ระบุตัวเลือกแบบ Headless เสมอ:
     - Selenium: `-v browser:headlesschrome`
     - Puppeteer: `-v HEADLESS:True`

3. **Dependency not met ใน `TC_002` (Selenium)**:
   - เกิดข้อผิดพลาด `Dependency not met: test case 'TC_001 - Open Google'`
   - สาเหตุ: `TC_002` มี Keyword `Require test case  TC_001 - Open Google` ซึ่งตรวจสอบว่า `TC_001` ต้องผ่านการทดสอบในรอบเดียวกัน
   - วิธีแก้ไข: รันทั้ง Suite ร่วมกัน หรือหากต้องการรันเฉพาะ `TC_002` ให้เปิดโหมด rerun พร้อมระบุ xunit file ที่มีผลทดสอบผ่านของ `TC_001`:

     ```bash
     robot -d results -v rerun:true -v xunit_file:results/result.xml -t "TC_002 - Search Google" script/sample_selenium.robot
     ```

4. **PuppeteerLibrary หรือ Playwright ใช้งานไม่ได้**:
   - หากพบข้อผิดพลาดเกี่ยวกับ browser binary หรือ node process ให้ตรวจสอบว่าได้ติดตั้ง browser binaries ด้วยคำสั่ง:

     ```bash
     python -m playwright install
     pyppeteer-install
     ```

---

## เอกสารอ้างอิง (References)

- [Robot Framework Official Website](https://robotframework.org/)
- [Robot Framework GitHub Repository](https://github.com/robotframework/robotframework)
- [Robot Framework SeleniumLibrary](https://github.com/robotframework/SeleniumLibrary)
- [Robot Framework DependencyLibrary](https://github.com/adiralashiva8/robotframework-dependencylibrary)
- [Robot Framework PuppeteerLibrary (PyPI)](https://pypi.org/project/robotframework-PuppeteerLibrary/)
- [Playwright Python](https://github.com/microsoft/playwright-python)
