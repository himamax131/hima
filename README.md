# منصة العلوم - منصة كورسات خاصة بالاشتراك

منصة متكاملة لتقديم كورسات فيديو لمادة العلوم بنظام اشتراكات أعضاء.

## المميزات

- ✅ تسجيل دخول آمن بـ Firebase Auth
- ✅ لوحة تحكم أدمن كاملة (إدارة أعضاء + كورسات)
- ✅ تحديد مدة اشتراك لكل عضو (أسبوع / شهر / سنة...)
- ✅ منع الوصول بعد انتهاء الاشتراك
- ✅ دعم فيديوهات YouTube / Vimeo / Bunny Stream
- ✅ واجهة عربية كاملة (RTL)
- ✅ تصميم حديث وجميل

## التثبيت والتشغيل

### 1. تثبيت الحزم

```bash
cd science-platform
npm install
```

### 2. إعداد Firebase

1. اذهب إلى [Firebase Console](https://console.firebase.google.com)
2. أنشئ مشروع جديد
3. فعّل **Authentication** → Sign-in method → Email/Password
4. أنشئ **Firestore Database** (في وضع production أو test)
5. من Project Settings → Your apps → Web app → انسخ الـ Config

### 3. إعداد المتغيرات

```bash
cp .env.local.example .env.local
```

افتح `.env.local` وضع بيانات Firebase الخاصة بك.

### 4. قواعد Firestore (مهم جداً)

في Firebase Console → Firestore → Rules ضع التالي:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read: if request.auth != null && (request.auth.uid == userId || get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin');
      allow write: if request.auth != null && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }
    match /courses/{courseId} {
      allow read: if request.auth != null;
      allow write: if request.auth != null && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }
    match /lessons/{lessonId} {
      allow read: if request.auth != null;
      allow write: if request.auth != null && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }
  }
}
```

### 5. إنشاء حساب الأدمن الأول

1. من Firebase Console → Authentication → Users → Add user
   - أدخل بريدك وكلمة مرور
2. من Firestore → ابدأ collection اسمها `users`
3. أنشئ document بـ ID = الـ UID الخاص بالمستخدم اللي عملته
4. ضع الحقول التالية:

```json
{
  "uid": "الـ UID بتاع المستخدم",
  "email": "your@email.com",
  "name": "اسمك",
  "role": "admin",
  "expiresAt": null,
  "createdAt": "2026-01-01T00:00:00.000Z",
  "isActive": true
}
```

### 6. تشغيل المشروع

```bash
npm run dev
```

افتح http://localhost:3000

## كيفية استخدام الفيديوهات

### YouTube (موصى به للبداية - مجاني)
1. ارفع الفيديو على YouTube واختر **Unlisted**
2. انسخ الرابط وضعه في الدرس
3. المنصة هتحوله لـ embed تلقائياً

### Bunny Stream (الأفضل للأمان والسعر)
1. سجل على bunny.net
2. ارفع الفيديو
3. انسخ رابط الـ embed وضعه في الدرس

### Vimeo
نفس الطريقة، ضع رابط الفيديو.

## رفع على الإنترنت (Deploy)

### Vercel (الأسهل)
1. ارفع المشروع على GitHub
2. اربطه بـ Vercel
3. أضف متغيرات البيئة في Vercel
4. Deploy

### الدومين
بعد الشراء، اربطه بـ Vercel أو أي استضافة Next.js.

## ملاحظات مهمة

- عند إضافة عضو جديد من لوحة التحكم، Firebase يسجل دخوله تلقائياً. قد تحتاج تسجل دخول تاني كأدمن.
- لحذف المستخدم من Authentication نهائياً تحتاج Firebase Admin SDK (يمكن إضافته لاحقاً).
- المنصة جاهزة للإنتاج بعد ضبط Firebase.

## الدعم

لو احتجت تعديلات أو إضافات، قول لي.
