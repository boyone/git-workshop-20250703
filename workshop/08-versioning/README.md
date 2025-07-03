# 8. การจัดการ Version ของ Application

## 1 วิธีคิดและวัตถุประสงค์ของการใช้งานแบบ Sematic Versioning

Semantic Versioning คือระบบการกำหนดหมายเลข version ที่มีความหมายชัดเจน เพื่อสื่อสารการเปลี่ยนแปลงของ software

### โครงสร้างพื้นฐาน

#### รูปแบบ: `MAJOR.MINOR.PATCH`

```txt
v2.1.5
│ │ │
│ │ └── PATCH (Bug fixes)
│ └──── MINOR (New features, backward compatible)
└────── MAJOR (Breaking changes)
```

### วิธีคิดหลัก (Core Concept)

#### 🔢 **MAJOR Version (X.0.0)**

เปลี่ยนเมื่อมี breaking changes

```javascript
// v1.x.x
function getUser(id) {
  return database.users.find(id);
}

// v2.0.0 - Breaking change
function getUser(options) {
  // เปลี่ยน parameter
  return database.users.find(options.id);
}
```

#### ➕ **MINOR Version (1.X.0)**

เปลี่ยนเมื่อเพิ่ม features ใหม่ (backward compatible)

```javascript
// v1.1.0 - เพิ่ม feature ใหม่
function getUser(id) {
  return database.users.find(id);
}

function getUserByEmail(email) {
  // ฟังก์ชันใหม่
  return database.users.findByEmail(email);
}
```

#### 🐛 **PATCH Version (1.1.X)**

เปลี่ยนเมื่อแก้ไข bugs

```javascript
// v1.1.1 - แก้ไข bug
function getUser(id) {
  if (!id) return null; // เพิ่ม validation
  return database.users.find(id);
}
```

### วัตถุประสงค์หลัก

#### 1. **สื่อสารการเปลี่ยนแปลง**

```json
{
  "v1.0.0": "Initial release",
  "v1.0.1": "Bug fix - null pointer exception",
  "v1.1.0": "New feature - user authentication",
  "v2.0.0": "Breaking - API restructure"
}
```

#### 2. **จัดการ Dependencies**

```json
// package.json
{
  "dependencies": {
    "library-a": "^1.2.0", // ยอมรับ 1.2.x และ 1.x.x
    "library-b": "~2.1.0", // ยอมรับ 2.1.x เท่านั้น
    "library-c": "3.0.0" // ต้องเป็น 3.0.0 เท่านั้น
  }
}
```

#### 3. **ควบคุมความเสี่ยงในการ Update**

```bash
# Safe updates
npm update library-name  # อัปเดต PATCH และ MINOR

# Careful updates
npm install library-name@2.0.0  # MAJOR version ต้องระวัง
```

### Pre-release และ Build Metadata

#### Pre-release Versions

```txt
v1.0.0-alpha.1    # Alpha release
v1.0.0-beta.2     # Beta release
v1.0.0-rc.1       # Release candidate
```

#### Build Metadata

```txt
v1.0.0+20240703   # Build จากวันที่
v1.0.0+abc123     # Build จาก commit hash
```

### การใช้งานในทีม

#### 🏷️ **Git Tags**

```bash
# สร้าง release tag
git tag -a v1.2.0 -m "Release version 1.2.0"
git push origin v1.2.0

# ดู version ล่าสุด
git describe --tags --abbrev=0
```

#### 📦 **Package Management**

```json
// package.json
{
  "name": "my-app",
  "version": "1.2.0",
  "scripts": {
    "version:patch": "npm version patch",
    "version:minor": "npm version minor",
    "version:major": "npm version major"
  }
}
```

#### 🚀 **CI/CD Pipeline**

```yaml
# GitHub Actions
name: Release
on:
  push:
    tags: ['v*']

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - name: Get version
        run: echo "VERSION=${GITHUB_REF#refs/tags/v}" >> $GITHUB_ENV
      - name: Create release
        run: gh release create v$VERSION
```

### ตัวอย่างการใช้งานจริง

#### 📱 **Mobile App**

```
v1.0.0 - เปิดตัวแอป
v1.0.1 - แก้ไข crash bug
v1.1.0 - เพิ่ม dark mode
v1.2.0 - เพิ่ม push notifications
v2.0.0 - เปลี่ยน UI ใหม่ทั้งหมด
```

#### 🌐 **Web API**

```
v1.0.0 - API เวอร์ชันแรก
v1.0.1 - แก้ไข response format
v1.1.0 - เพิ่ม endpoint ใหม่
v1.2.0 - เพิ่ม authentication
v2.0.0 - เปลี่ยน URL structure
```

#### 📚 **Library/Framework**

```
v0.1.0 - ระยะ development
v1.0.0 - stable release
v1.0.1 - bug fixes
v1.1.0 - new features
v2.0.0 - breaking changes
```

### Best Practices

#### ✅ **ควรทำ**

- เริ่มจาก `v1.0.0` เมื่อ public release
- ใช้ `v0.x.x` สำหรับ development
- อัปเดต CHANGELOG.md ทุกครั้ง
- ทำ regression testing ก่อน MAJOR release

#### ❌ **ไม่ควรทำ**

- ข้าม version numbers
- เปลี่ยน version ที่ release แล้ว
- ใช้ date-based versioning กับ SemVer ปนกัน
- MAJOR bump เพื่อ marketing

### Version Ranges

#### NPM Style

```json
{
  "^1.2.0": ">=1.2.0 <2.0.0", // Compatible changes
  "~1.2.0": ">=1.2.0 <1.3.0", // Patch releases only
  "1.2.x": ">=1.2.0 <1.3.0", // Same as ~
  ">=1.2.0": "Any version >= 1.2.0"
}
```

#### เลือกใช้ Range อย่างไร

```javascript
// Conservative (production)
"dependencies": {
  "express": "~4.18.0"  // แค่ patch updates
}

// Moderate (development)
"dependencies": {
  "lodash": "^4.17.0"   // minor และ patch updates
}

// Aggressive (internal tools)
"dependencies": {
  "webpack": ">=5.0.0"  // รับ breaking changes ได้
}
```

### เครื่องมือช่วย

#### 🤖 **Automated Versioning**

```bash
# ใช้ conventional commits
npm install -g standard-version

# Auto bump version
standard-version  # อ่าน commit messages และ bump version
```

#### 🏷️ **Release Notes Generation**

```bash
# Auto generate changelog
npx conventional-changelog -p angular -i CHANGELOG.md -s
```

---

## 2 วิธีคิดและวัตถุประสงค์ของการใช้งานแบบ Calendar Versioning or CalVer

Calendar Versioning คือระบบการกำหนดหมายเลข version ตามวันที่/เวลาที่ release แทนการใช้ semantic meaning

### โครงสร้างพื้นฐาน

#### รูปแบบหลัก: `YYYY.MM.DD` หรือ `YY.MM.MICRO`

```
2024.07.03    # ปี.เดือน.วัน
24.07.1       # ปี.เดือน.รหัสย่อย
2024.27       # ปี.สัปดาห์ที่
24Q3          # ปี.ไตรมาส
```

### วิธีคิดหลัก (Core Concept)

#### 📅 **เน้นเวลาการ Release**

```
v2024.07.03   - Release วันที่ 3 ก.ค. 2024
v2024.07.15   - Release วันที่ 15 ก.ค. 2024
v2024.08.01   - Release วันที่ 1 ส.ค. 2024
```

#### 🔄 **Release ตาม Schedule**

```
v24.07.1      - Release แรกของเดือน ก.ค.
v24.07.2      - Release ที่สองของเดือน ก.ค.
v24.08.1      - Release แรกของเดือน ส.ค.
```

### วัตถุประสงค์หลัก

#### 1. **ความชัดเจนเรื่องเวลา**

```bash
# รู้ทันทีว่า release เมื่อไร
Ubuntu 22.04 LTS    # เมษายน 2022
Ubuntu 24.04 LTS    # เมษายน 2024

Windows 11 22H2     # Half 2 ของปี 2022
Windows 11 23H2     # Half 2 ของปี 2023
```

#### 2. **เหมาะกับ Time-based Release**

```
Chrome 124 (2024 สัปดาห์ที่ 24)
Firefox 127.0 (2024 เดือนที่ 7)
```

#### 3. **ง่ายต่อการวางแผน**

```javascript
// Release schedule
const releaseSchedule = {
  'v2024.01': 'January 2024 - New features',
  'v2024.02': 'February 2024 - Bug fixes',
  'v2024.03': 'March 2024 - Performance improvements',
};
```

### รูปแบบยอดนิยม

#### 🗓️ **Date-based**

```
YYYY.MM.DD    # 2024.07.03
YY.MM.DD      # 24.07.03
YYYY.MM       # 2024.07
YY.MM         # 24.07
```

#### 📊 **Period-based**

```
YYYY.WW       # 2024.27 (สัปดาห์ที่ 27)
YYYYQN        # 2024Q3 (ไตรมาส 3)
YYYY.MM.MICRO # 2024.07.1 (เดือน + รุ่นย่อย)
```

### ตัวอย่างการใช้งานจริง

#### 🖥️ **Operating Systems**

```
Ubuntu 20.04 LTS    # เมษายน 2020
Ubuntu 22.04 LTS    # เมษายน 2022
Ubuntu 24.04 LTS    # เมษายน 2024

# มีความหมาย: YY.MM format
```

#### 🌐 **Web Browsers**

```javascript
// Chrome versioning
const chromeVersions = {
  '124.0.6367.60': 'Release ในสัปดาห์ที่ 24 ของ 2024',
  '125.0.6422.60': 'Release ในสัปดาห์ที่ 25 ของ 2024',
};
```

#### 📱 **Mobile Apps**

```json
{
  "app_version": "2024.07.1",
  "release_date": "2024-07-03",
  "features": ["New dashboard", "Bug fixes"]
}
```

#### 🏢 **Enterprise Software**

```
SAP S/4HANA 2023    # Release ปี 2023
Salesforce 24.2     # Release ครั้งที่ 2 ของปี 2024
```

### การใช้งานในทีม

#### 🏷️ **Git Tagging**

```bash
# สร้าง CalVer tags
git tag -a v2024.07.03 -m "Release July 3, 2024"
git tag -a v24.07.1 -m "First release of July 2024"

# ดู release ล่าสุด
git tag -l "v2024.*" | sort -V | tail -1
```

#### 📦 **Package.json**

```json
{
  "name": "my-app",
  "version": "2024.7.1",
  "scripts": {
    "version:calver": "node scripts/bump-calver.js"
  }
}
```

#### 🤖 **Automated CalVer**

```javascript
// scripts/bump-calver.js
const now = new Date();
const year = now.getFullYear();
const month = now.getMonth() + 1;
const version = `${year}.${month}.1`;

console.log(`New version: ${version}`);
```

### เมื่อไรควรใช้ CalVer

#### ✅ **เหมาะกับ:**

##### 📅 **Regular Release Schedule**

```
- Weekly releases (Chrome, Firefox)
- Monthly releases (Ubuntu interim)
- Quarterly releases (Enterprise software)
- Annual releases (Ubuntu LTS, Windows)
```

##### 🔄 **Continuous Deployment**

```javascript
// สำหรับ CD pipeline
const buildVersion = () => {
  const now = new Date();
  const year = now.getFullYear();
  const month = String(now.getMonth() + 1).padStart(2, '0');
  const day = String(now.getDate()).padStart(2, '0');
  return `${year}.${month}.${day}`;
};
```

##### 📊 **Time-sensitive Products**

```
- News applications
- Financial software
- Compliance-driven systems
- Security updates
```

#### ❌ **ไม่เหมาะกับ:**

##### 🔧 **API Libraries**

```
- เน้น compatibility มากกว่าเวลา
- ต้องการ semantic meaning
- Breaking changes ไม่ตาม schedule
```

##### 🚀 **Feature-driven Development**

```
- Release เมื่อ feature พร้อม
- ไม่มี fixed schedule
- ขึ้นกับ development velocity
```

### Hybrid Approaches

#### 🔀 **CalVer + SemVer**

```
2024.07.1.0    # Year.Month.Minor.Patch
24.Q3.2.1      # Year.Quarter.Minor.Patch
```

#### 🏷️ **CalVer + Marketing Names**

```javascript
const versions = {
  technical: '2024.07.1',
  marketing: 'Summer Release 2024',
  codename: 'Phoenix',
};
```

### การเปรียบเทียบ

#### CalVer vs SemVer

| CalVer            | SemVer                |
| ----------------- | --------------------- |
| เน้นเวลา          | เน้นการเปลี่ยนแปลง    |
| ทำนายได้          | ยืดหยุ่น              |
| เหมาะกับ schedule | เหมาะกับ feature      |
| ง่ายต่อ planning  | ง่ายต่อ compatibility |

### Best Practices

#### ✅ **ควรทำ**

```bash
# กำหนด format ให้ชัด
YYYY.MM.MICRO  # 2024.07.1

# ใช้ leading zeros
2024.07.01     # แทน 2024.7.1

# มี fallback plan
2024.07.1.hotfix1  # สำหรับ emergency fixes
```

#### ❌ **ไม่ควรทำ**

```bash
# อย่าผสมรูปแบบ
2024.07.1      # CalVer
1.2.3          # SemVer ในโปรเจกต์เดียวกัน

# อย่าข้าม dates
2024.07.1 → 2024.09.1  # ข้าม August

# อย่าใช้กับ libraries ที่เน้น compatibility
```

### เครื่องมือช่วย

#### 🛠️ **CalVer Tools**

```bash
# Python package
pip install calver
calver --format="{YYYY}.{MM}.{DD}"

# Node.js
npm install calver
```

#### 🤖 **CI/CD Integration**

```yaml
# GitHub Actions
name: CalVer Release
on:
  schedule:
    - cron: '0 0 1 * *' # Monthly release

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - name: Generate CalVer
        run: |
          VERSION=$(date +"%Y.%m.1")
          echo "VERSION=$VERSION" >> $GITHUB_ENV
      - name: Create release
        run: gh release create v$VERSION
```
