# تقرير الأدلة الرقمية — محاكاة هجوم على ويندوز 

**اسم القضية:** `Case_VMName`  
**موقع حفظ الأدلة:** `C:\CaseFolder`

---

## 1. مقدمة
هذا المستند ملخص تحقيق جنائي رقمي لعملية محاكاة هجوم على نظام Windows. الغرض: توثيق خطوات جمع الأدلة، وصف المخرجات، وتقديم تحليل أولي وتوصيات قابلة للتطبيق.  
جميع الأدلة تم جمعها بطريقة تحفظ سلامتها وتم توثيق المسارات لكل ملف.

---

## 2. منهجية العمل
- تنفيذ سكربت جمع (BAT) يعمل بصلاحيات المدير (Administrator).  
- جمع الأدلة الحية (Processes, Network, Scheduled Tasks, Startup).  
- جمع آثارجلسة: Prefetch, Registry, Event Logs.  
- جمع صور للذاكرة والقرص (RAM, Disk) باستخدام أدوات خارجية (DumpIt, Disk2vhd أو FTK Imager).  
- حساب قيم SHA256 للتأكد من سلامة الأدلة.

---

## 3. المخرجات (Outputs) — ملفات ومواقعها وشرحها

> **المجلد الرئيسي:** `C:\CaseFolder`

### 3.1 العمليات (Processes)
- `C:\CaseFolder\Processes.csv`  
- `C:\CaseFolder\processes.txt`  
**شرح:** قائمة العمليات النشطة وقت جمع الأدلة (PID, ImageName, User, CPU Time).  
**أهمية:** كشف عمليات غير اعتيادية أو برامج خبيثة تعمل في الذاكرة.

### 3.2 الاتصالات الشبكية (Network)
- `C:\CaseFolder\network_connections.txt`  
- `C:\CaseFolder\NetworkConnections.txt`  
- `C:\CaseFolder\NetworkConfig.txt`  
**شرح:** اتصالات TCP/UDP المفتوحة، عناوين IP، والمنافذ.  
**أهمية:** تحديد اتصالات محتملة مع خوادم C2 أو تسريب بيانات.

### 3.3 المستخدمون النشطون
- `C:\CaseFolder\logged_on_users.txt`  
**شرح:** أسماء الجلسات والمستخدمين المتصلين.  
**أهمية:** التحقق من جلسات غير مصرح بها.

### 3.4 المهام المجدولة (Scheduled Tasks)
- `C:\CaseFolder\scheduled_tasks.txt`  
- `C:\CaseFolder\ScheduledTasks.txt`  
**شرح:** جميع المهام المسجلة التي تعمل تلقائياً.  
**أهمية:** مؤشر شائع على Persistence.

### 3.5 عناصر الإقلاع (Startup)
- `C:\CaseFolder\startup_items.txt`  
**شرح:** برامج تعمل عند إقلاع النظام.  
**أهمية:** البحث عن إضافات غير مصرح بها.

### 3.6 الخدمات (Services)
- `C:\CaseFolder\Services.txt`  
**شرح:** قائمة بجميع الخدمات وحالاتها.  
**أهمية:** كشف خدمات خبيثة أو معدلة.

### 3.7 سجلات الأحداث (Event Logs)
- `C:\CaseFolder\Application.evtx`  
- `C:\CaseFolder\Security.evtx`  
- `C:\CaseFolder\System.evtx`  
**شرح:** سجلات ويندوز الأساسية.  
**أهمية:** مصدر أساسي لبناء الـ timeline وتحليل محاولات الدخول وأخطاء النظام.

### 3.8 Prefetch
- `C:\CaseFolder\Prefetch\` (مجلد يحتوي ملفات `.pf`)  
**شرح:** ملفات تشير إلى تشغيل برامج وتواتر التشغيل.  
**أهمية:** إثبات تشغيل برنامج حتى لو حُذف لاحقًا.

### 3.9 Registry
- `C:\CaseFolder\Registry\` (مجلد يحتوي نسخ من مفاتيح هامة)  
**شرح:** نسخ من مفاتيح النظام والبرامج.  
**أهمية:** الكشف عن مفاتيح Autorun أو تغييرات بقاء.

### 3.10 سلامة الأدلة (Hashes)
- `C:\CaseFolder\Case_VMName_RAM_SHA256.txt`  
- `C:\CaseFolder\Case_VMName_Disk_SHA256.txt`  
**شرح:** قيم SHA256 لصور الذاكرة والقرص.  
**أهمية:** إثبات عدم التلاعب بالأدلة بعد جمعها.

---

## 4. ملاحظات على أخطاء النسخ (Errors)
أثناء الجمع ظهرت رسائل مثل:
- `ERROR: The system was unable to find the specified registry key or value.`  
- `File not found - History` / `0 File(s) copied`  

**التفسير:** بعض المسارات أو المفاتيح غير موجودة على هذا الجهاز أو الحساب المستخدم لا يملك صلاحية الوصول، وهذا أمر طبيعي بناءً على إعدادات النظام أو بيئة المحاكاة.

---

## 5. تحليل أولي (Summary & Observations)
- وجود ملفات `POWERSHELL.EXE` و`CMD.EXE` ضمن Prefetch وعمليات PowerShell المدرجة يشير إلى تنفيذ سكربتات (متوقع في محاكاة الهجوم).  
- راجع `network_connections.txt` للبحث عن عناوين IP غريبة أو اتصالات طويلة المدة (C2 indicators).  
- راجع `scheduled_tasks.txt` و `startup_items.txt` لأي مدخلات غير معتادة؛ هذه غالبًا مؤشر استمرار.  
- استخدم Event Logs (`System.evtx`, `Security.evtx`) لبناء **Timeline** زمني بالأحداث الحرجة.

---

## 6. خطوات التحليل المقترحة (Next steps)
1. فتح ملفات العمليات والتصفية حسب PID واسم العملية (ابحث عن أسماء غير معروفة أو مواقع تنفيذ غريبة).  
2. تحليل `MemoryDump.raw` بواسطة Volatility/Redline لاكتشاف عمليات مخفية، حقن DLL، وسلاسل نصية ضارة.  
3. تحميل `DiskImage.vhd` في بيئة تحليل (FTK/Autopsy) لفحص نظام الملفات ومجلدات المستخدم.  
4. مقارنة ملفات التنفيذ المشبوهة مع قواعد بيانات الفيروسات (VirusTotal).  
5. بناء **Timeline** زمني من Event Logs + Prefetch + Processes.

---

## 7. التوصيات (Recommendations)
- عزل الجهاز عن الشبكة حتى اكتمال التحليل.  
- الاحتفاظ بنسخة من `C:\CaseFolder` على وسيط خارجي للمرجعية.  
- تحديث سياسات أمان PowerShell (تضييق ExecutionPolicy) وتفعيل Monitoring لعمليات PowerShell.  
- إجراء مسح كامل للقرص على بيئة منفصلة (sandbox) للملفات المشبوهة.

---

## 8. المراجع والتدقيق
- تم استخدام أوامر Windows القياسية وأدوات جمع معتمدة.  
- تم توثيق كل مسار ونتيجة في هذا المجلد لتسهيل التدقيق اللاحق.

---

## 9. تذييل (Contact)
**Prepared by:** `[moataz yazan nawaf]`  













شكرًا لتوضيحك! إليك كل أمر مع الشرح بالعربي، بالإضافة إلى مثال على **الإدخال (Input)** و**المخرجات (Output)** كما طلبت:

---

### 1️⃣ **أمر PowerShell لجمع العمليات**
**الإدخال (الأمر):**
```powershell
Get-Process | Format-List * > "C:\CaseFolder\powershell_processes.txt"
```
**الوصف:**  
هذا الأمر يجمع **قائمة كاملة بالعمليات النشطة** في النظام مع تفاصيلها (مثل: الاسم، المعرف PID، الذاكرة المستخدمة، وقت البدء، إلخ) ويحفظ النتائج في ملف نصي.

**مثال المخرجات (في الملف `powershell_processes.txt`):**
```
Name           : chrome
Id             : 1234
CPU            : 102.34
WorkingSet     : 304,256 KB
StartTime      : 9/5/2025 10:30:00 AM
Path           : C:\Program Files\Google\Chrome\Application\chrome.exe
```

---

### 2️⃣ **أمر CMD لعرض العمليات (بديل لـ Task Manager)**
**الإدخال (الأمر):**
```cmd
tasklist /v /fo list > "C:\CaseFolder\processes.txt"
```
**الوصف:**  
يعرض **قائمة العمليات النشطة** مع تفاصيل إضافية مثل: المستخدم، عنوان النافذة، وحالة العملية.

**مثال المخرجات (في الملف `processes.txt`):**
```
Image Name:   chrome.exe
PID:          1234
Session Name: Console
Mem Usage:    304,256 K
Status:       Running
User Name:    DESKTOP\User1
Window Title: Google Chrome
```

---

### 3️⃣ **أمر الشبكات النشطة (Netstat)**
**الإدخال (الأمر):**
```cmd
netstat -ano > "C:\CaseFolder\network_connections.txt"
```
**الوصف:**  
يعرض **الاتصالات الشبكية النشطة** (TCP/UDP) مع العناوين المحلية والخارجية وحالة الاتصال ومعرف العملية (PID).

**مثال المخرجات (في الملف `network_connections.txt`):**
```
Proto  Local Address    Foreign Address     State       PID
TCP    192.168.1.10:80  172.217.20.110:443  ESTABLISHED 1234
UDP    0.0.0.0:5353     *:*                 LISTENING   4567
```

---

### 4️⃣ **أمر المهام المجدولة (Scheduled Tasks)**
**الإدخال (الأمر):**
```cmd
schtasks /query /fo list /v > "C:\CaseFolder\scheduled_tasks.txt"
```
**الوصف:**  
يعرض **المهام المجدولة في النظام** مع تفاصيلها (مثل: اسم المهمة، الوقت التالي للتنفيذ، المسار، إلخ).

**مثال المخرجات (في الملف `scheduled_tasks.txt`):**
```
TaskName:   \UpdateChecker
Next Run Time: 9/5/2025 3:00:00 PM
Status:       Ready
Task To Run: C:\Windows\System32\update.exe
```

---

### 5️⃣ **أمر برامج بدء التشغيل (Startup Programs)**
**الإدخال (الأمر):**
```cmd
wmic startup get Caption,Command,Location > "C:\CaseFolder\startup_items.txt"
```
**الوصف:**  
يعرض **البرامج التي تعمل عند بدء تشغيل Windows** مع اسمها ومسار التنفيذ وموقع التسجيل.

**مثال المخرجات (في الملف `startup_items.txt`):**
```
Caption    Command                          Location
OneDrive   "C:\Program Files\OneDrive\OneDrive.exe"  HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```

---

### 6️⃣ **أمر المستخدمون النشطون (Logged-in Users)**
**الإدخال (الأمر):**
```cmd
query user > "C:\CaseFolder\logged_on_users.txt"
```
**الوصف:**  
يعرض **المستخدمون المتصلون حاليًا** بالنظام (محليًا أو عن بُعد).

**مثال المخرجات (في الملف `logged_on_users.txt`):**
```
USERNAME  SESSIONNAME  ID  STATE  IDLE TIME  LOGON TIME
user1     console      1   Active 0:05       9/5/2025 8:00 AM
```

---

### 7️⃣ **أمر نسخ ملفات Prefetch (أثر التطبيقات)**
**الإدخال (الأمر):**
```cmd
xcopy C:\Windows\Prefetch\* "C:\CaseFolder\Prefetch\" /E /I /Y
```
**الوصف:**  
ينسخ **جميع ملفات Prefetch** (التي تخزن معلومات عن التطبيقات التي تم تشغيلها) إلى مجلد الأدلة.

**مثال المخرجات (لا يظهر محتوى الملفات، بل تأكيد النسخ):**
```
C:\Windows\Prefetch\CHROME.EXE-ABC123.pf
C:\Windows\Prefetch\EXPLORER.EXE-DEF456.pf
...
تم نسخ 15 ملفًا
```

---

### 8️⃣ **إنشاء صورة للذاكرة (RAM Image)**
**الوصف:**  
هذا ليس أمرًا مباشرًا، لكنه يشير إلى استخدام أداة مثل **FTK Imager** أو **DumpIt** لإنشاء صورة كاملة للذاكرة.  
**المثال (كيفية التنفيذ):**
```
تم إنشاء الملف: C:\CaseFolder\Case_Win10VM_20250905_RAM.raw
```

---

### 9️⃣ **حساب البصمة الرقمية (Hash) للذاكرة**
**الإدخال (الأمر):**
```powershell
Get-FileHash -Path "C:\CaseFolder\Case_VMName_YYYYMMDD_RAM.raw" -Algorithm SHA256
```
**الوصف:**  
يحسب **البصمة الرقمية (Hash)** لملف صورة الذاكرة للتأكد من سلامته وعدم العبث به.

**مثال المخرجات:**
```
Algorithm       Hash                                                               Path
---------       ----                                                               ----
SHA256          A1B2C3D4E5F6... (64 حرفًا)                                          C:\CaseFolder\Case_VMName_20250905_RAM.raw
```

---

### ✅ **ملاحظة:**  
- استبدل `VMName` و`YYYYMMDD` باسم الجهاز والتاريخ الفعليين.
- بعض الأوامر تتطلب **صلاحيات Administrator** لتشغيلها.


































شكرًا لتوضيحك! إليك كل أمر مع الشرح بالعربي، بالإضافة إلى مثال على **الإدخال (Input)** و**المخرجات (Output)** كما طلبت:

---

### 1️⃣ **أمر PowerShell لجمع العمليات**
**الإدخال (الأمر):**
```powershell
Get-Process | Format-List * > "C:\CaseFolder\powershell_processes.txt"
```
**الوصف:**  
هذا الأمر يجمع **قائمة كاملة بالعمليات النشطة** في النظام مع تفاصيلها (مثل: الاسم، المعرف PID، الذاكرة المستخدمة، وقت البدء، إلخ) ويحفظ النتائج في ملف نصي.

**مثال المخرجات (في الملف `powershell_processes.txt`):**
```
Name           : chrome
Id             : 1234
CPU            : 102.34
WorkingSet     : 304,256 KB
StartTime      : 9/5/2025 10:30:00 AM
Path           : C:\Program Files\Google\Chrome\Application\chrome.exe
```

---

### 2️⃣ **أمر CMD لعرض العمليات (بديل لـ Task Manager)**
**الإدخال (الأمر):**
```cmd
tasklist /v /fo list > "C:\CaseFolder\processes.txt"
```
**الوصف:**  
يعرض **قائمة العمليات النشطة** مع تفاصيل إضافية مثل: المستخدم، عنوان النافذة، وحالة العملية.

**مثال المخرجات (في الملف `processes.txt`):**
```
Image Name:   chrome.exe
PID:          1234
Session Name: Console
Mem Usage:    304,256 K
Status:       Running
User Name:    DESKTOP\User1
Window Title: Google Chrome
```

---

### 3️⃣ **أمر الشبكات النشطة (Netstat)**
**الإدخال (الأمر):**
```cmd
netstat -ano > "C:\CaseFolder\network_connections.txt"
```
**الوصف:**  
يعرض **الاتصالات الشبكية النشطة** (TCP/UDP) مع العناوين المحلية والخارجية وحالة الاتصال ومعرف العملية (PID).

**مثال المخرجات (في الملف `network_connections.txt`):**
```
Proto  Local Address    Foreign Address     State       PID
TCP    192.168.1.10:80  172.217.20.110:443  ESTABLISHED 1234
UDP    0.0.0.0:5353     *:*                 LISTENING   4567
```

---

### 4️⃣ **أمر المهام المجدولة (Scheduled Tasks)**
**الإدخال (الأمر):**
```cmd
schtasks /query /fo list /v > "C:\CaseFolder\scheduled_tasks.txt"
```
**الوصف:**  
يعرض **المهام المجدولة في النظام** مع تفاصيلها (مثل: اسم المهمة، الوقت التالي للتنفيذ، المسار، إلخ).

**مثال المخرجات (في الملف `scheduled_tasks.txt`):**
```
TaskName:   \UpdateChecker
Next Run Time: 9/5/2025 3:00:00 PM
Status:       Ready
Task To Run: C:\Windows\System32\update.exe
```

---

### 5️⃣ **أمر برامج بدء التشغيل (Startup Programs)**
**الإدخال (الأمر):**
```cmd
wmic startup get Caption,Command,Location > "C:\CaseFolder\startup_items.txt"
```
**الوصف:**  
يعرض **البرامج التي تعمل عند بدء تشغيل Windows** مع اسمها ومسار التنفيذ وموقع التسجيل.

**مثال المخرجات (في الملف `startup_items.txt`):**
```
Caption    Command                          Location
OneDrive   "C:\Program Files\OneDrive\OneDrive.exe"  HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```

---

### 6️⃣ **أمر المستخدمون النشطون (Logged-in Users)**
**الإدخال (الأمر):**
```cmd
query user > "C:\CaseFolder\logged_on_users.txt"
```
**الوصف:**  
يعرض **المستخدمون المتصلون حاليًا** بالنظام (محليًا أو عن بُعد).

**مثال المخرجات (في الملف `logged_on_users.txt`):**
```
USERNAME  SESSIONNAME  ID  STATE  IDLE TIME  LOGON TIME
user1     console      1   Active 0:05       9/5/2025 8:00 AM
```

---

### 7️⃣ **أمر نسخ ملفات Prefetch (أثر التطبيقات)**
**الإدخال (الأمر):**
```cmd
xcopy C:\Windows\Prefetch\* "C:\CaseFolder\Prefetch\" /E /I /Y
```
**الوصف:**  
ينسخ **جميع ملفات Prefetch** (التي تخزن معلومات عن التطبيقات التي تم تشغيلها) إلى مجلد الأدلة.

**مثال المخرجات (لا يظهر محتوى الملفات، بل تأكيد النسخ):**
```
C:\Windows\Prefetch\CHROME.EXE-ABC123.pf
C:\Windows\Prefetch\EXPLORER.EXE-DEF456.pf
...
تم نسخ 15 ملفًا
```

---

### 8️⃣ **إنشاء صورة للذاكرة (RAM Image)**
**الوصف:**  
هذا ليس أمرًا مباشرًا، لكنه يشير إلى استخدام أداة مثل **FTK Imager** أو **DumpIt** لإنشاء صورة كاملة للذاكرة.  
**المثال (كيفية التنفيذ):**
```
تم إنشاء الملف: C:\CaseFolder\Case_Win10VM_20250905_RAM.raw
```

---

### 9️⃣ **حساب البصمة الرقمية (Hash) للذاكرة**
**الإدخال (الأمر):**
```powershell
Get-FileHash -Path "C:\CaseFolder\Case_VMName_YYYYMMDD_RAM.raw" -Algorithm SHA256
```
**الوصف:**  
يحسب **البصمة الرقمية (Hash)** لملف صورة الذاكرة للتأكد من سلامته وعدم العبث به.

**مثال المخرجات:**
```
Algorithm       Hash                                                               Path
---------       ----                                                               ----
SHA256          A1B2C3D4E5F6... (64 حرفًا)                                          C:\CaseFolder\Case_VMName_20250905_RAM.raw
```

---

### ✅ **ملاحظة:**  
- استبدل `VMName` و`YYYYMMDD` باسم الجهاز والتاريخ الفعليين.
- بعض الأوامر تتطلب **صلاحيات Administrator** لتشغيلها.


