
<div dir="rtl">

# سند مدل همکاری چند-Agent (Multi-Agent Collaboration Model)

**نام پلتفرم:** Enterprise AI Context & Automation Platform

**نسخه:** 1.2

**وضعیت:** پیش‌نویس بازبینی‌شده

**سند مرجع نام‌گذاری:** `Naming_And_Terminology_Glossary`

---

# ۱. هدف و محدوده سند

این سند نحوه همکاری چند Agent هوشمند برای انجام Taskهای پیچیده را تعریف می‌کند. این همکاری در دل **Agent Orchestration Engine** اتفاق می‌افتد؛ Agentهای معرفی‌شده در این سند نقش‌های اجرایی داخل همان Engine هستند، نه Componentهای معماری جدید.

---

# ۲. اصول (Principles)

| اصل | توضیح |
| :--- | :--- |
| **Coordinator-driven Orchestration** | هماهنگی بین Agentها توسط یک Agent مرکزی (Orchestrator) مدیریت می‌شود، نه به‌صورت غیرمتمرکز |
| **Loose Coupling** | Agentها مستقیماً به یکدیگر وابسته نیستند و از طریق پیام و Context مشترک تعامل می‌کنند |
| **Tool-first Execution** | هر Agent برای اثرگذاری بر دنیای بیرون، صرفاً از طریق Action & Tool Engine عمل می‌کند، نه با دسترسی مستقیم |
| **Shared Context** | تمام Agentهای درگیر در یک Task به یک نسخه مشترک از Context دسترسی دارند (بخش ۷) |
| **Secure Communication** | پیام بین Agentها تحت کنترل Governance & Platform Services است و تمام ارتباطات رمزنگاری و احراز هویت می‌شوند (بخش ۵) |
| **Human-in-the-Loop when Required** | برای تصمیم‌ها یا عملیات پرریسک، تأیید انسانی الزامی است |
| **Observability** | تمام تعاملات، تصمیم‌ها و پیام‌های بین Agentها برای تحلیل و عیب‌یابی قابل ردیابی هستند (بخش ۱۰) |

---

# ۳. سلسله‌مراتب Agentها (Agent Hierarchy)

| نقش Agent | مسئولیت | Component پشتیبان |
| :--- | :--- | :--- |
| **Orchestrator Agent** | دریافت Task، مدیریت کل چرخه همکاری و تصمیم‌گیری نهایی | Agent Orchestration Engine |
| **Planner Agent** | شکستن Task پیچیده به مراحل کوچک‌تر (Task Decomposition) | Agent Orchestration Engine |
| **Knowledge Agent** | درخواست بازیابی دانش و Context موردنیاز | Knowledge & Context Engine |
| **Tool Agent** | شناسایی و انتخاب Tool مناسب برای هر مرحله از Catalog | Action & Tool Engine |
| **Execution Agent** | اجرای واقعی Tool یا Workflow انتخاب‌شده | Action & Tool Engine |
| **Reviewer Agent** | اعتبارسنجی نتیجه اجرا پیش از تحویل نهایی یا مرحله بعد | Agent Orchestration Engine |
| **Memory Agent** | ثبت و بازیابی تجربیات، تصمیم‌ها و نتایج | Memory Management Engine |

**مدیریت چرخه حیات Agentها (Agent Lifecycle Management):**

| مرحله | توضیح |
| :--- | :--- |
| **Creation** | Agent بر اساس نیاز Task توسط Orchestrator ایجاد می‌شود. Agentهای عمومی (مانند Knowledge Agent) به‌صورت پایدار و Agentهای تخصصی به‌صورت موقت ایجاد می‌شوند. |
| **Activation** | Agent پس از دریافت وظیفه، فعال شده و شروع به پردازش می‌کند. |
| **Execution** | Agent وظیفه محول‌شده را با استفاده از Context مشترک و ابزارهای در دسترس اجرا می‌کند. |
| **Deactivation** | پس از اتمام وظیفه، Agent غیرفعال شده و منابع آن آزاد می‌شود. Agentهای پایدار در حالت انتظار باقی می‌مانند. |
| **Destruction** | Agentهای موقت پس از اتمام همکاری یا در شرایط خطای بحرانی، به‌کلی حذف می‌شوند. |

> **یادداشت هم‌راستاسازی معماری:** این هفت نقش، نقش‌های منطقی (Logical Roles) در سطح اجرای Agent هستند و نباید با هفت Component اصلی پلتفرم اشتباه گرفته شوند. Knowledge Agent، Tool/Execution Agent و Memory Agent صرفاً واسط فراخوانی Agent Orchestration Engine به‌ترتیب به Knowledge & Context Engine، Action & Tool Engine و Memory Management Engine هستند؛ منطق واقعی بازیابی دانش، اجرای عملیات و مدیریت حافظه همچنان در همان Engineهای تخصصی پیاده‌سازی می‌شود.

---

# ۴. چرخه حیات همکاری (Collaboration Lifecycle)

```text
User Request → Planning → Agent Assignment → Parallel Execution → Result Aggregation → Review → Memory Update
```

| مرحله | توضیح |
| :--- | :--- |
| **User Request** | ورود درخواست از طریق Interface & Integration Layer |
| **Planning** | Planner Agent، Task را به زیرمراحل قابل اجرا تجزیه می‌کند |
| **Agent Assignment** | Orchestrator Agent هر زیرمرحله را به Agent مناسب واگذار می‌کند |
| **Parallel Execution** | اجرای هم‌زمان زیرمراحل مستقل توسط Agentهای متناظر (طبق الگوهای بخش ۶). Agentهایی که به داده‌های یکدیگر وابسته‌اند، از مکانیزم همگام‌سازی (Synchronization) برای تبادل وضعیت و جلوگیری از شرایط رقابتی استفاده می‌کنند. |
| **Result Aggregation** | ترکیب نتایج زیرمراحل توسط Orchestrator Agent |
| **Review** | اعتبارسنجی نتیجه نهایی توسط Reviewer Agent پیش از پاسخ به کاربر |
| **Memory Update** | ثبت نتیجه و تجربه توسط Memory Agent در Memory Management Engine |

---

# ۵. پروتکل ارتباطی (Communication Protocol)

هر پیام بین Agentها شامل فیلدهای زیر است:

| فیلد | توضیح |
| :--- | :--- |
| **Correlation ID** | شناسه یکتای کل زنجیره همکاری، برای پیوند دادن تمام پیام‌های یک Task |
| **Task ID** | شناسه زیرمرحله مشخصی که پیام به آن مربوط است |
| **Sender / Receiver** | Agent فرستنده و گیرنده پیام |
| **Context Reference** | ارجاع به Context مشترک (بخش ۷)، نه کپی کامل آن، برای جلوگیری از تکرار داده |
| **Payload** | محتوای اصلی پیام (درخواست، نتیجه، خطا) |
| **Priority** | اولویت پردازش پیام |
| **Status** | وضعیت جاری (Pending، In Progress، Completed، Failed) |
| **Timestamp** | زمان ارسال پیام؛ ورودی Audit & Traceability (بخش ۱۰) |

**امنیت ارتباطی (Communication Security):**

- تمام پیام‌ها در حال انتقال (In Transit) با TLS 1.3 رمزنگاری می‌شوند.
- هر پیام حاوی امضای دیجیتال (Digital Signature) Agent فرستنده برای تأیید اصالت است.
- احراز هویت متقابل (Mutual Authentication) بین Agentها از طریق توکن‌های کوتاه‌مدت (Short-lived Tokens) انجام می‌شود.
- تمام پیام‌ها از مسیر Governance & Platform Services عبور می‌کنند و در Audit Log ثبت می‌شوند.

---

# ۶. الگوهای هماهنگی (Coordination Patterns)

| الگو | کاربرد |
| :--- | :--- |
| **Sequential** | زیرمراحل به ترتیب و با وابستگی مستقیم به نتیجه مرحله قبل اجرا می‌شوند |
| **Parallel** | زیرمراحل مستقل به‌صورت هم‌زمان اجرا می‌شوند تا زمان کل کاهش یابد. برای همگام‌سازی بین Agentهای موازی، از Barrier Synchronization در نقاط وابستگی استفاده می‌شود. |
| **Hierarchical** | یک Agent مادر، اجرای چند Agent فرزند را مدیریت می‌کند |
| **Event-Driven** | Agentها بر اساس وقوع Event فعال می‌شوند، نه فراخوانی مستقیم |
| **Consensus** | چند Agent مستقل روی یک تصمیم واحد به توافق می‌رسند (برای تصمیم‌های حساس یا نامطمئن) |

---

# ۷. Context مشترک (Shared Context)

Agentها به منابع زیر دسترسی دارند:

- **Conversation Context:** تاریخچه تعامل جاری (معادل Session Memory)
- **Knowledge Context:** دانش بازیابی‌شده مرتبط با Task
- **Memory:** تجربیات و تصمیم‌های گذشته مرتبط
- **Policies:** محدودیت‌های Governance قابل اعمال روی Task جاری
- **Tool Catalog:** فهرست Toolهای در دسترس برای اجرای زیرمراحل

**همگام‌سازی و انتشار Context (Context Synchronization & Propagation):**

- Context مشترک در یک حافظه مرکزی موقت (Transient Context Store) نگهداری می‌شود.
- هر Agent در لحظه شروع پردازش، یک snapshot از Context دریافت می‌کند.
- تغییرات اعمال‌شده توسط هر Agent، از طریق رویدادهای Context Update به سایر Agentهای مرتبط منتشر می‌شود.
- برای حفظ یکپارچگی، نسخه‌گذاری Context (Context Versioning) انجام می‌شود تا از بازنویسی ناخواسته جلوگیری شود.

---

# ۸. حل تعارض (Conflict Resolution)

هنگام تعارض بین تصمیم‌های چند Agent، اولویت زیر (از بالا به پایین) اعمال می‌شود:

1. **Security Policy** — محدودیت‌های امنیتی همیشه بر هر تصمیم دیگر اولویت دارند. در صورت تعارض بین Security Policy و Human Decision، Security Policy بر Human Decision ارجحیت دارد.
2. **Human Decision** — تصمیم انسانی ثبت‌شده (در صورت وجود Human-in-the-Loop)
3. **Business Rules** — قوانین کسب‌وکاری تعریف‌شده در Policy Management Service
4. **Confidence Score** — تصمیم Agent با بالاترین امتیاز اطمینان
5. **Majority Consensus** — رأی اکثریت در الگوی Consensus
6. **Retry / Escalation** — در صورت عدم حل تعارض، تلاش مجدد یا ارجاع به سطح بالاتر

---

# ۹. مدیریت خطا (Failure Handling)

- **Retry:** تلاش مجدد خودکار برای خطای قابل تکرار با سیاست افزایشی (Exponential Backoff)
- **Timeout:** لغو اجرای Agentی که از زمان تعیین‌شده عبور کرده
- **Agent Replacement:** جایگزینی Agent ناسالم با یک نمونه جدید از همان نقش
- **Fallback Model:** استفاده از مدل جایگزین در صورت خرابی مدل اصلی
- **Human Escalation:** ارجاع به تصمیم‌گیرنده انسانی در صورت شکست مکرر یا ریسک بالا
- **State Rollback:** در صورت شکست در مراحل میانی، بازگشت به آخرین وضعیت پایدار (Stable State) با استفاده از داده‌های ذخیره‌شده در Memory Management Engine

---

# ۱۰. حاکمیت (Governance)

هماهنگی چند-Agent تحت همان چارچوب Governance & Platform Services قرار دارد:

- **RBAC:** کنترل دسترسی هر Agent بر اساس نقش تعریف‌شده
- **Audit Log:** ثبت تصمیم و پیام هر Agent
- **Traceability:** ردیابی کامل مسیر یک Task از طریق Correlation ID (بخش ۵)
- **Cost Limits / Token Budget:** محدودیت مصرف منابع برای جلوگیری از اجرای کنترل‌نشده چند-Agent
- **Execution Quota:** محدودیت تعداد درخواست‌های همزمان هر Agent برای جلوگیری از اشباع منابع

---

# ۱۱. اهداف کارایی همکاری (Collaboration SLA)

این اهداف، مکمل جدول کارایی عمومی در سند `Non_Functional_Requirements_And_SLA` هستند و مختص عملیات داخلی هماهنگی چند-Agent‌اند:

| نیازمندی | هدف |
| :--- | :--- |
| **Agent Dispatch** | کمتر از ۱۰۰ میلی‌ثانیه |
| **Internal Messaging** | کمتر از ۵۰ میلی‌ثانیه |
| **Planner** | کمتر از ۱ ثانیه |
| **Result Aggregation** | کمتر از ۵۰۰ میلی‌ثانیه |
| **Context Synchronization** | کمتر از ۲۰۰ میلی‌ثانیه (زمان انتشار تغییرات Context بین Agentها) |

---

# ۱۲. تکامل و مقیاس‌پذیری (Evolution & Scalability)

- **افزایش تعداد Agentها:** با رشد پیچیدگی Taskها، تعداد Agentهای همکار افزایش می‌یابد. Orchestrator Agent باید توانایی مدیریت حداقل ۲۰ Agent همزمان در یک Task واحد را داشته باشد.
- **افزایش هم‌زمانی (Concurrency):** پلتفرم باید بتواند حداقل ۱۰۰ Task هم‌زمان با ساختار چند-Agent را مدیریت کند.
- **مقیاس‌پذیری افقی Agentها:** Agentهای پرمصرف (مانند Knowledge Agent و Execution Agent) باید قابلیت مقیاس‌پذیری افقی مستقل داشته باشند تا از ایجاد گلوگاه در هماهنگی جلوگیری شود.

---

# ۱۳. ارتباط با سایر اسناد (References)

| سند | ارتباط |
| :--- | :--- |
| `Architecture_Overview_Enterprise_AI_Platform` | مرجع اصول معماری (Event-Driven، Zero Trust) که الگوهای همکاری این سند بر پایه آن‌هاست |
| `Component_Map_And_Responsibilities` | مرجع Agent Orchestration Engine؛ نقش‌های بخش ۳ همین سند زیرمجموعه همان Engine هستند |
| `Data_Model_And_Knowledge_Schema_Overview` | مرجع کامل Context Model که Shared Context (بخش ۷) بر اساس آن ساخته می‌شود |
| `Integration_Boundaries_And_Tooling_Framework` | مرجع کنترل دسترسی (بخش ۸) و Human-in-the-Loop اشاره‌شده در بخش‌های ۲ و ۸ این سند |
| `Non_Functional_Requirements_And_SLA` | مرجع اهداف کارایی و در دسترس‌پذیری عمومی که SLA بخش ۱۱ مکمل آن است |
| `Governance_And_Platform_Services_Design` | مرجع پیاده‌سازی حاکمیت، Audit و کنترل هزینه اشاره‌شده در بخش ۱۰ این سند |

---

# ۱۴. نتیجه‌گیری

این سند چارچوب همکاری چند-Agent را به‌صورت جامع تعریف می‌کند. با استفاده از نقش‌های مشخص، الگوهای هماهنگی استاندارد و پروتکل ارتباطی امن، پلتفرم قادر خواهد بود Taskهای پیچیده را به‌صورت مؤثر بین Agentهای تخصصی توزیع کرده و نتایج قابل اعتمادی تولید کند. رعایت اصول حاکمیت، مشاهده‌پذیری و مدیریت خطا، اجرای همکاری چند-Agent را در محیط‌های سازمانی امن و قابل کنترل می‌سازد.

</div>
