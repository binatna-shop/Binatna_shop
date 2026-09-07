# Binatna Shop — طريقة النشر

## 1) الملفات

- `index.html` — واجهة المتجر العامة.
- `admin-panel.html` — لوحة الإدارة المنفصلة. لا يوجد رابط لها داخل المتجر.
- `supabase-config.js` — بيانات اتصال Supabase العامة (URL + anon key فقط).
- `supabase/schema.sql` — الجداول وRLS ودالة استقبال الطلبات.
- `robots.txt` — إعداد محركات البحث ومنع فهرسة لوحة الإدارة.
- `netlify.toml` — إعداد النشر على Netlify.

## 2) إعداد Supabase

1. أنشئ مشروعاً في Supabase.
2. افتح SQL Editor.
3. الصق محتوى `supabase/schema.sql` وشغّله بالكامل.
4. من Authentication > Users أنشئ حساب بريد إلكتروني وكلمة مرور للإدارة.
5. انسخ UUID الخاص بحساب الإدارة.
6. نفّذ:

```sql
insert into public.admin_users(user_id)
values ('ضع-UUID-حساب-الإدارة-هنا');
```

> لا تضع `service_role` key داخل الموقع. استعمل `anon` key فقط في `supabase-config.js`.

## 3) إعداد supabase-config.js

استبدل:

```js
window.BINATNA_SUPABASE_URL = 'https://YOUR_PROJECT_REF.supabase.co';
window.BINATNA_SUPABASE_ANON_KEY = 'YOUR_SUPABASE_ANON_KEY';
```

بـ URL و anon public key الخاصين بمشروعك من Supabase > Project Settings > API.

## 4) ملاحظة مهمة حول الطلبات

الواجهة العامة تستعمل دالة `place_order` الموجودة في SQL لإنشاء الطلب وتخفيض المخزون بشكل ذري داخل قاعدة البيانات. لوحة الإدارة تقرأ الطلبات وتستطيع تغيير حالتها، بينما عمليات إضافة/تعديل/حذف المنتجات محمية بواسطة `is_admin()` وRLS.

إذا كانت نسخة `index.html` الحالية تحتوي على اسم حقول مختلف في جدول `products` أو منطق مختلف للطلبات، راجع الحقول قبل الإنتاج. هذا المشروع مبني على الحقول الموجودة في النسخة الحالية: `name`, `category`, `price`, `old_price`, `sizes`, `colors`, `description`, `rating`, `reviews`, `stock`, `image`, `images`.

## 5) النشر على Netlify

ارفع **مجلد المشروع كاملاً** إلى GitHub ثم اربطه بـ Netlify، أو ارفع المجلد مباشرة إذا كان أسلوب النشر المستخدم يسمح بذلك.

Build command: اتركه فارغاً.
Publish directory: `.`

بعد النشر:

- المتجر: `https://اسم-الموقع.netlify.app/`
- الإدارة: `https://اسم-الموقع.netlify.app/admin-panel.html`

## 6) الأمان

`admin-panel.html` منفصلة عن الصفحة العامة، لكن وجود ملف HTML في رابط معروف ليس حماية بحد ذاته. الحماية الحقيقية هنا تأتي من Supabase Authentication + RLS + جدول `admin_users`. لذلك حتى لو عرف شخص رابط لوحة الإدارة فلن يستطيع تنفيذ عمليات الإدارة بدون حساب مصرح له.

لا تشارك كلمة مرور حساب الإدارة ولا `service_role` key.
