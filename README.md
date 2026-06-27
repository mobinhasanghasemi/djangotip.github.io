<div align="center">

<img src="https://raw.githubusercontent.com/PKief/vscode-material-icon-theme/main/icons/django.svg" width="120" height="120" alt="Django Logo">

# 🚀 Django Optimization Bible

### کتاب مقدس بهینه‌سازی جنگو — از مبتدی تا حرفه‌ای

<br>

[![Django](https://img.shields.io/badge/Django-5.0-092E20?style=for-the-badge&logo=django&logoColor=white)](https://www.djangoproject.com/)
[![Python](https://img.shields.io/badge/Python-3.12-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)
[![Stars](https://img.shields.io/github/stars/your-repo/django-optimization?style=for-the-badge&color=yellow)](https://github.com/your-repo/django-optimization/stargazers)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen?style=for-the-badge)](http://makeapullrequest.com)

<br>

<img src="https://readme-typing-svg.demolab.com?font=Vazirmatn&weight=800&size=28&duration=3000&pause=1000&color=00E676&center=true&vCenter=true&multiline=true&repeat=true&width=600&height=100&lines=%D8%A7%D8%B2+%D8%B3%D8%B7%D8%AD+%DB%8C%DA%A9+%D8%AA%D8%A7+%D8%AD%D8%B1%D9%81%D9%87%E2%80%8C%D8%A7%DB%8C%3B%D8%A8%D9%87%DB%8C%D9%86%D9%87%E2%80%8C%D8%B3%D8%A7%D8%B2%DB%8C+%D8%AC%D9%86%DA%AF%D9%88+%D8%A8%D8%A7+%DB%8C%DA%A9+%DA%A9%D9%84%DB%8C%DA%A9" alt="Typing SVG" />

<br>

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0a0e17,50:667eea,100:00e676&height=200&section=header&text=&fontSize=0&animation=fadeIn" width="100%"/>

</div>

---

## 📖 فهرست مطالب

<table>
<tr>
<td width="50%">

### 🟢 بخش‌های مبتدی
- [⚡ Lazy Evaluation](#-1-lazy-evaluation-در-queryset)
- [🔗 select_related](#-2-select_related--حل-n1)
- [🔄 prefetch_related](#-3-prefetch_related--روابط-many)

### 🟡 بخش‌های متوسط
- [📊 only / defer](#-4-only-و-defer--بارگذاری-انتخابی)
- [🧮 annotate / aggregate](#-5-annotate-و-aggregate--محاسبه-در-db)
- [📇 Index‌ها](#-6-indexها--سرعت-جستجو-را-100x-کن)

</td>
<td width="50%">

### 🔴 بخش‌های پیشرفته
- [🛠️ Raw SQL](#-7-raw-sql--وقتی-orm-کافی-نیست)
- [🧠 Caching](#-8-caching--حافظه-پنهان-چندسطحی)
- [⚡ Async Views](#-9-async-views--ویوهای-ناهمگام)

### 🟣 بخش‌های حرفه‌ای
- [🔧 Database Tuning](#-10-database-tuning)
- [📦 Connection Pool](#-11-connection-pool)
- [🚀 Bulk Operations](#-12-bulk-operations)
- [📡 Signals](#-13-signals)
- [🎯 Serializer](#-14-serializer)
- [🛡️ Middleware](#-15-middleware)
- [⏰ Celery](#-16-celery)
- [📊 Monitoring](#-17-monitoring)
- [🔒 Security](#-18-security)

</td>
</tr>
</table>

---

## 🎯 نمودار سطوح

```mermaid
graph LR
    A[🟢 مبتدی] --> B[🟡 متوسط]
    B --> C[🔴 پیشرفته]
    C --> D[🟣 حرفه‌ای]
    
    A --- A1[Lazy QuerySet]
    A --- A2[select_related]
    A --- A3[prefetch_related]
    
    B --- B1[only/defer]
    B --- B2[annotate]
    B --- B3[Index]
    
    C --- C1[Raw SQL]
    C --- C2[Caching]
    C --- C3[Async Views]
    
    D --- D1[DB Tuning]
    D --- D2[Celery]
    D --- D3[Monitoring]
    
    style A fill:#00e676,stroke:#333,color:#000
    style B fill:#ffd740,stroke:#333,color:#000
    style C fill:#ff5252,stroke:#333,color:#fff
    style D fill:#b388ff,stroke:#333,color:#000
```

---

## ⚡ 1. Lazy Evaluation در QuerySet

> **اولین قانون:** جنگو تا زمانی که واقعاً به داده نیاز نداشته باشی، کوئری نمی‌زنه!

<div align="center">

```mermaid
sequenceDiagram
    participant U as 👨‍💻 Developer
    participant QS as 📋 QuerySet
    participant DB as 🗄️ Database
    
    U->>QS: User.objects.filter(is_active=True)
    Note over QS: ⏳ هنوز کوئری ارسال نشده
    U->>QS: .filter(age__gt=18)
    Note over QS: ⏳ باز هم کوئری ارسال نشده
    U->>QS: list(qs) یا for loop
    QS->>DB: SELECT * FROM user WHERE is_active AND age > 18
    DB-->>QS: 📦 Results
    QS-->>U: 📊 Data
```

</div>

<details>
<summary>🔍 <b>مثال کد — کلیک کنید</b></summary>

<br>

<table>
<tr>
<th width="50%">❌ اشتباه</th>
<th width="50%">✅ درست</th>
</tr>
<tr>
<td>

```python
# ❌ تبدیل زودهنگام به list
users = list(User.objects.all())
for u in users:
    if u.age > 18:
        print(u.name)
```

</td>
<td>

```python
# ✓ فیلتر در سطح دیتابیس
users = User.objects.filter(
    is_active=True,
    age__gt=18
)
for u in users:
    print(u.name)
```

</td>
</tr>
</table>

</details>

<div align="center">

| 📊 قبل | 📊 بعد | 📈 بهبود |
|:---:|:---:|:---:|
| بارگذاری کل جدول | فیلتر در DB | `⚡ سریع‌تر` |

</div>

---

## 🔗 2. select_related — حل N+1

> **معروف‌ترین مشکل عملکردی جنگو:** N+1 Query Problem

<div align="center">

```mermaid
graph LR
    subgraph "❌ بدون select_related"
        B1[📚 Book 1] -->|1 query| A1[👤 Author]
        B2[📚 Book 2] -->|1 query| A2[👤 Author]
        B3[📚 Book 3] -->|1 query| A3[👤 Author]
        B4[📚 ...] -->|N queries| A4[👤 ...]
    end
    
    subgraph "✓ با select_related"
        BB1[📚 Books] -->|1 JOIN query| AA1[👤 Authors]
    end
    
    style B1 fill:#ff5252,stroke:#333,color:#fff
    style B2 fill:#ff5252,stroke:#333,color:#fff
    style B3 fill:#ff5252,stroke:#333,color:#fff
    style B4 fill:#ff5252,stroke:#333,color:#fff
    style BB1 fill:#00e676,stroke:#333,color:#000
    style AA1 fill:#00e676,stroke:#333,color:#000
```

</div>

<details>
<summary>🔍 <b>مثال کد — کلیک کنید</b></summary>

<br>

<table>
<tr>
<th width="50%">❌ N+1 Problem</th>
<th width="50%">✅ select_related</th>
</tr>
<tr>
<td>

```python
# ❌ 101 کوئری!
books = Book.objects.all()[:100]
for book in books:
    print(book.author.name)
    # هر iteration = 1 کوئری اضافه
```

</td>
<td>

```python
# ✓ فقط 1 کوئری با JOIN
books = Book.objects.select_related(
    'author'
).all()[:100]

for book in books:
    print(book.author.name)
```

</td>
</tr>
</table>

</details>

<div align="center">

```mermaid
graph TD
    A[101 کوئری] -->|select_related| B[1 کوئری]
    B --> C[99% کاهش 🎉]
    
    style A fill:#ff5252,stroke:#333,color:#fff
    style B fill:#00e676,stroke:#333,color:#000
    style C fill:#667eea,stroke:#333,color:#fff
```

</div>

---

## 🔄 3. prefetch_related — روابط Many

> برای **ManyToMany** و **reverse ForeignKey** استفاده میشه

<div align="center">

```mermaid
sequenceDiagram
    participant U as 👨‍💻 Code
    participant DB as 🗄️ Database
    
    rect rgb(10, 14, 23)
    Note over U,DB: ✅ با prefetch_related
    U->>DB: Query 1: SELECT * FROM article
    U->>DB: Query 2: SELECT * FROM tag JOIN article_tags
    DB-->>U: 📦 All data cached in memory
    U->>U: لود از کش — بدون کوئری اضافه ⚡
    end
```

</div>

---

## 📊 4. only و defer — بارگذاری انتخابی

<div align="center">

| متد | کاربرد | مثال |
|:---:|:---:|:---:|
| `only()` | فقط فیلدهای مورد نیاز | `User.objects.only('name', 'email')` |
| `defer()` | حذف فیلدهای سنگین | `Article.objects.defer('content', 'raw_html')` |

</div>

---

## 🧮 5. annotate و aggregate — محاسبه در DB

<div align="center">

```mermaid
graph LR
    subgraph "❌ محاسبه در Python"
        P1[🔄 Loop] --> P2[📊 N+1 Queries]
        P2 --> P3[🐢 کند]
    end
    
    subgraph "✅ محاسبه در DB"
        D1[📊 1 Query] --> D2[⚡ GROUP BY]
        D2 --> D3[🚀 سریع]
    end
    
    style P1 fill:#ff5252,stroke:#333,color:#fff
    style P2 fill:#ff5252,stroke:#333,color:#fff
    style P3 fill:#ff5252,stroke:#333,color:#fff
    style D1 fill:#00e676,stroke:#333,color:#000
    style D2 fill:#00e676,stroke:#333,color:#000
    style D3 fill:#00e676,stroke:#333,color:#000
```

</div>

<details>
<summary>🔍 <b>مثال annotate — کلیک کنید</b></summary>

```python
from django.db.models import Count, Avg, F, Case, When, Value

products = Product.objects.annotate(
    review_count=Count('reviews'),
    avg_rating=Avg('reviews__rating'),
    profit=F('price') - F('cost'),
    status=Case(
        When(stock__gt=0, then=Value('موجود')),
        default=Value('ناموجود'),
    ),
).filter(avg_rating__gte=4.0)
```

</details>

---

## 📇 6. Index‌ها — سرعت جستجو را 100x کن

<div align="center">

```mermaid
graph TD
    A[🔍 جستجو بدون Index] -->|O(n)| B[🐌 Full Table Scan]
    C[🔍 جستجو با Index] -->|O log n| D[⚡ Index Lookup]
    
    B --> E[⏱️ 1000ms]
    D --> F[⏱️ 10ms]
    
    style A fill:#ff5252,stroke:#333,color:#fff
    style B fill:#ff5252,stroke:#333,color:#fff
    style C fill:#00e676,stroke:#333,color:#000
    style D fill:#00e676,stroke:#333,color:#000
    style E fill:#ff5252,stroke:#333,color:#fff
    style F fill:#00e676,stroke:#333,color:#000
```

</div>

<div align="center">

| نوع Index | کاربرد | مثال |
|:---:|:---:|:---:|
| 📄 تک‌فیلدی | فیلدهای ساده | `Index(fields=['price'])` |
| 📑 ترکیبی | کوئری‌های چندفیلدی | `Index(fields=['category', 'price'])` |
| 🔖 شرطی | فقط ردیف‌های مرتبط | `Index(condition=Q(is_active=True))` |
| 🔍 Gin | جستجوی متنی | `GinIndex(fields=['search_vector'])` |

</div>

---

## 🛠️ 7. Raw SQL — وقتی ORM کافی نیست

<div align="center">

```python
# 🛡️ همیشه از پارامتر استفاده کن!
cursor.execute(
    "SELECT * FROM user WHERE id = %s", 
    [user_id]  # ✅ Safe
)
# ❌ NEVER: f"SELECT * FROM user WHERE id = {user_id}"
```

</div>

---

## 🧠 8. Caching — حافظه پنهان چندسطحی

<div align="center">

```mermaid
graph TB
    subgraph "سطوح کش"
        L1[🌐 View Cache]
        L2[📄 Template Cache]
        L3[🗄️ Low-level Cache]
        L4[💾 Redis/Memcached]
    end
    
    Client[👤 Client] --> L1
    L1 --> L2
    L2 --> L3
    L3 --> L4
    L4 --> DB[(🗄️ Database)]
    
    style L1 fill:#667eea,stroke:#333,color:#fff
    style L2 fill:#764ba2,stroke:#333,color:#fff
    style L3 fill:#00e676,stroke:#333,color:#000
    style L4 fill:#ff9800,stroke:#333,color:#000
    style DB fill:#333,stroke:#555,color:#fff
```

</div>

---

## ⚡ 9. Async Views — ویوهای ناهمگام

<div align="center">

```mermaid
sequenceDiagram
    participant C as 👤 Client
    participant S as 🖥️ Server
    
    rect rgb(10, 14, 23)
    Note over C,S: ❌ Sync — 6 ثانیه
    C->>S: Request
    S->>S: API 1 (2s) ⏳
    S->>S: API 2 (2s) ⏳
    S->>S: API 3 (2s) ⏳
    S-->>C: Response (6s total)
    end
    
    rect rgb(0, 30, 0)
    Note over C,S: ✅ Async — 2 ثانیه
    C->>S: Request
    par parallel requests
        S->>S: API 1 (2s) ⚡
    and
        S->>S: API 2 (2s) ⚡
    and
        S->>S: API 3 (2s) ⚡
    end
    S-->>C: Response (2s total) 🚀
    end
```

</div>

---

## 📊 مقایسه عملکرد

<div align="center">

```mermaid
gantt
    title زمان اجرای کوئری‌ها
    dateFormat X
    axisFormat %s ms
    
    section ❌ بدون بهینه‌سازی
    N+1 Queries     :0, 1000
    Full Table Scan :0, 800
    No Caching      :0, 600
    
    section ✅ با بهینه‌سازی
    select_related  :0, 50
    Index Lookup    :0, 10
    Redis Cache     :0, 5
```

</div>

---

## 🏗️ ساختار پروژه

```
django-optimization-bible/
├── 📄 index.html          # نسخه وب کتاب
├── 📖 README.md           # این فایل
├── 📁 examples/
│   ├── 🐍 models.py       # مدل‌های نمونه
│   ├── 👁️ views.py        # ویوهای بهینه
│   ├── 🔧 optimize.py     # تکنیک‌های بهینه‌سازی
│   └── 📊 benchmarks.py   # بنچمارک‌ها
├── 📁 docs/
│   ├── 📝 querysets.md
│   ├── 🗄️ database.md
│   └── ⚡ performance.md
└── 📜 LICENSE
```

---

## 🚀 شروع سریع

<div align="center">

```bash
# 1️⃣ کلون کردن
git clone https://github.com/your-repo/django-optimization.git

# 2️⃣ نصب وابستگی‌ها
cd django-optimization
pip install -r requirements.txt

# 3️⃣ اجرای پروژه
python manage.py runserver

# 4️⃣ مشاهده بنچمارک‌ها
python manage.py benchmark
```

</div>

---

## 📈 آمار بهینه‌سازی

<div align="center">

<table>
<tr>
<td align="center">
<img src="https://img.shields.io/badge/Queries-101→1-green?style=for-the-badge&logo=postgresql"/>
<br>
<sub>select_related</sub>
</td>
<td align="center">
<img src="https://img.shields.io/badge/Speed-99%25-faster?style=for-the-badge&logo=rocket"/>
<br>
<sub>بهبود سرعت</sub>
</td>
<td align="center">
<img src="https://img.shields.io/badge/Caching-3_layer-blue?style=for-the-badge&logo=redis"/>
<br>
<sub>سطوح کش</sub>
</td>
<td align="center">
<img src="https://img.shields.io/badge/Async-6x-faster?style=for-the-badge&logo=python"/>
<br>
<sub>ناهمگام</sub>
</td>
</tr>
</table>

</div>

---

## 💡 نکات طلایی

<div align="center">

| # | نکته | توضیح |
|:---:|:---|:---|
| 1 | 🎯 **Lazy Evaluation** | هیچوقت QuerySet رو بدون نیاز به `list()` تبدیل نکن |
| 2 | 🔗 **select_related** | برای FK و OneToOne از JOIN استفاده کن |
| 3 | 🔄 **prefetch_related** | برای M2M از کوئری جدا استفاده کن |
| 4 | 📊 **only/defer** | فقط فیلدهای مورد نیاز رو بارگذاری کن |
| 5 | 📇 **Index** | فیلدهای filter و order_by رو ایندکس کن |
| 6 | 🧠 **Cache** | از کش چندسطحی استفاده کن |
| 7 | ⚡ **Async** | برای I/O-bound از async استفاده کن |
| 8 | 🛡️ **SQL Injection** | هیچوقت رشته مستقیم در SQL قرار نده |

</div>

---

## 🤝 مشارکت

<div align="center">

[![GitHub Issues](https://img.shields.io/github/issues/your-repo/django-optimization?style=for-the-badge)](https://github.com/your-repo/django-optimization/issues)
[![GitHub PRs](https://img.shields.io/github/issues-pr/your-repo/django-optimization?style=for-the-badge)](https://github.com/your-repo/django-optimization/pulls)

<br>

**مشارکت شما خوش‌آمدید!** 🎉

<br>

<a href="https://github.com/your-repo/django-optimization/issues/new">
  <img src="https://img.shields.io/badge/🐛_Report_Bug-red?style=for-the-badge" alt="Report Bug"/>
</a>
<a href="https://github.com/your-repo/django-optimization/issues/new">
  <img src="https://img.shields.io/badge/💡_Request_Feature-blue?style=for-the-badge" alt="Request Feature"/>
</a>
<a href="https://github.com/your-repo/django-optimization/pulls">
  <img src="https://img.shields.io/badge/🔧_Submit_PR-green?style=for-the-badge" alt="Submit PR"/>
</a>

</div>

---

## 📜 مجوز

<div align="center">

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)

این پروژه تحت مجوز MIT منتشر شده است.

</div>

---

## ⭐ ستاره‌ها

<div align="center">

[![Star History Chart](https://api.star-history.com/svg?repos=your-repo/django-optimization&type=Timeline)](https://star-history.com/#your-repo/django-optimization&Timeline)

</div>

---

<div align="center">

### 🌟 اگر این پروژه مفید بود، لطفاً ستاره بدهید! 🌟

<br>

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0a0e17,50:667eea,100:00e676&height=150&section=footer&text=&fontSize=0" width="100%"/>

<br>

**ساخته شده با ❤️ برای جامعه جنگو ایران**

[![Django Iran](https://img.shields.io/badge/Django_IRAN-Community-092E20?style=for-the-badge&logo=django&logoColor=white)](https://t.me/django_iran)

</div>
