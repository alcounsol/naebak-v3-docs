# بنية الخدمات المصغرة (Microservices) لمشروع "نائبك.كوم" - الإصدار v3.0.0

## 1. المبادئ الأساسية

تعتمد البنية الجديدة على تفكيك المشروع إلى مجموعة من الخدمات الصغيرة والمستقلة (Microservices)، حيث تكون كل خدمة مسؤولة عن جزء محدد من وظائف النظام. هذا التصميم يهدف إلى تحقيق:

- **قابلية التطوير (Scalability):** يمكن تطوير وتحديث كل خدمة بشكل مستقل.
- **المرونة (Flexibility):** يمكن استخدام أفضل التقنيات لكل خدمة على حدة (مع الالتزام بـ Django للخلفية).
- **الموثوقية (Reliability):** فشل خدمة واحدة لا يؤثر على باقي النظام.
- **سهولة الصيانة (Maintainability):** يسهل فهم وإدارة كل خدمة بشكل منفصل.

## 2. مخطط البنية العامة

```mermaid
graph TD
    subgraph "العميل (Client)"
        A[Next.js Frontend]
    end

    subgraph "البنية التحتية (Infrastructure)"
        B[API Gateway (Django)]
    end

    subgraph "الخدمات المصغرة (Microservices)"
        C[Auth Service (Django)]
        D[Core Content Service (Django)]
        E[Messaging Service (Django)]
        F[Problems Service (Django)]
        G[Ratings Service (Django)]
    end

    subgraph "قواعد البيانات (Databases)"
        DB1[Auth DB (PostgreSQL)]
        DB2[Content DB (PostgreSQL)]
        DB3[Messaging DB (PostgreSQL)]
        DB4[Problems DB (PostgreSQL)]
        DB5[Ratings DB (PostgreSQL)]
    end

    A --> B
    B --> C
    B --> D
    B --> E
    B --> F
    B --> G

    C --- DB1
    D --- DB2
    E --- DB3
    F --- DB4
    G --- DB5
```

## 3. تفاصيل الخدمات

| اسم الخدمة | الوظيفة | نقاط النهاية (Endpoints) المقترحة |
| :--- | :--- | :--- |
| **1. Auth & Permissions Service** | إدارة المستخدمين والصلاحيات | `/api/auth/register/`<br>`/api/auth/login/`<br>`/api/auth/logout/`<br>`/api/auth/profile/` |
| **2. Core Content Service** | إدارة المحتوى الأساسي | `/api/core/candidates/`<br>`/api/core/members/`<br>`/api/core/parties/`<br>`/api/core/governorates/` |
| **3. Messaging Service** | إدارة الرسائل والمحادثات | `/api/messaging/conversations/`<br>`/api/messaging/messages/` |
| **4. Problems/Issues Service** | إدارة الشكاوى والمشاكل | `/api/problems/issues/`<br>`/api/problems/comments/` |
| **5. Ratings & Feedback Service** | إدارة التقييمات | `/api/ratings/rate/`<br>`/api/ratings/stats/` |
| **6. API Gateway** | توجيه الطلبات | `/` (نقطة الدخول الوحيدة) |

## 4. تدفق البيانات (Data Flow)

1.  **طلب تسجيل مستخدم جديد:**
    - الواجهة الأمامية (Next.js) ترسل طلب `POST` إلى `API Gateway`.
    - `API Gateway` يوجه الطلب إلى `Auth Service`.
    - `Auth Service` يقوم بإنشاء المستخدم الجديد في قاعدة بياناته (`Auth DB`) ويعيد token للمصادقة.

2.  **طلب عرض قائمة المرشحين:**
    - الواجهة الأمامية (Next.js) ترسل طلب `GET` إلى `API Gateway`.
    - `API Gateway` يوجه الطلب إلى `Core Content Service`.
    - `Core Content Service` يجلب بيانات المرشحين من قاعدة بياناته (`Content DB`) ويعيدها.

هذه البنية ستكون الأساس المتين للمشروع في إصداره الثالث، وستحل جميع المشاكل الحالية المتعلقة بالتداخل والاعتمادية.
