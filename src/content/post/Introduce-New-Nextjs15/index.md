---
title: "Next.js 15 برسی "
description: "هر آنچه برای ارتقاء به Next.js 15 نیاز دارید؛ از Codemod خودکار تا breaking changeهای caching و async APIها"
publishDate: "14 Aug 2024"
updatedDate: "03 Mar 2025"
tags: ["Nextjs", "Migration", "React19"]
---



# Next.js 15: هر چیزی که باید بدونی

Next.js 15 بالاخره منتشر شد و یه سری تغییرات فوق‌العاده مهم و breaking change داره که واقعاً ارزش upgrade کردن رو داره. تو این راهنمای کامل قراره همه چیز رو بگم - از نحوه upgrade گرفته تا feature های جدید و تغییراتی که روی workflow شما تاثیر میذاره.

## Migration آسون با Codemod

قبل از اینکه برم سراغ تغییرات، خوشبختانه Next.js یه ابزار خودکار داره که خودش بیشتر breaking change ها رو برای شما حل میکنه:

```bash
npx @next/codemod@canary upgrade latest
```

این command:
- Version های Next.js و React رو update میکنه
- کدهای شما رو طوری تغییر میده که با breaking change های جدید سازگار باشه
- ESLint و dependency های دیگه رو هم update میکنه
- تنظیمات لازم رو اعمال میکنه

## Breaking Change های اصلی

### 1. Async Request APIs

**تغییر چیه؟**: Headers، cookies، params و searchParams حالا asynchronous شدن و promise برمیگردونن.

**قبلاً (Next.js 14):**
```javascript
import { headers } from 'next/headers';

export default function Page() {
  const headerValues = headers();
  return <div>...</div>;
}
```

**حالا (Next.js 15):**
```javascript
import { headers } from 'next/headers';

export default async function Page() {
  const headerValues = await headers();
  return <div>...</div>;
}
```

**مثال Dynamic Route:**
```javascript
// app/blog/[id]/page.js
export default async function BlogPost({ params }) {
  const { id } = await params;

  return (
    <div>
      <h1>Blog Post ID: {id}</h1>
    </div>
  );
}
```

**چرا این تغییر؟**
این تغییر باعث میشه static rendering بهتر کار کنه. صفحاتی که از این API های dynamic استفاده نمیکنن، میتونن راحت‌تر static render بشن.

### 2. تغییرات Caching - بزرگترین پیروزی!

Next.js 15 بدترین قسمت Next.js 14 رو درست کرده: اون caching مزاحمی که همه ازش شاکی بودن.

#### Fetch Request ها دیگه به صورت پیش‌فرض Cache نمیشن

**قبلاً (Next.js 14):**
```javascript
// این به صورت پیش‌فرض cache میشد
const data = await fetch('https://api.example.com/data');
```

**حالا (Next.js 15):**
```javascript
// دیگه به صورت پیش‌فرض cache نمیشه
const data = await fetch('https://api.example.com/data');

// برای cache کردن، باید صراحتاً بگی:
const cachedData = await fetch('https://api.example.com/data', {
  cache: 'force-cache'
});
```

#### Route Handler ها دیگه Cache نمیشن

**قبلاً (Next.js 14):**
```javascript
// app/api/users/route.js
export async function GET() {
  // این به صورت پیش‌فرض cache میشد (مشکل‌ساز!)
  return Response.json({ users: await getUsers() });
}
```

**حالا (Next.js 15):**
```javascript
// app/api/users/route.js
export async function GET() {
  // دیگه به صورت پیش‌فرض cache نمیشه (خیلی بهتره!)
  return Response.json({ users: await getUsers() });
}

// برای cache کردن، باید صراحتاً بگی:
export async function GET() {
  return Response.json(
    { users: await getUsers() },
    {
      headers: {
        'Cache-Control': 'max-age=60'
      }
    }
  );
}
```

#### Client Router Cache کم شد

Client-side router cache حالا برای dynamic route ها به جای 30 ثانیه، 0 ثانیه پیش‌فرض شده.

**تنظیمات (اگه رفتار قدیمی رو میخوای):**
```javascript
// next.config.js
module.exports = {
  experimental: {
    staleTimes: {
      dynamic: 30 // seconds
    }
  }
}
```

## Feature های جدید و بهبودها

### 1. پشتیبانی از React 19

Next.js 15 با React 19 Release Candidate میاد و feature های جدیدش رو داری:

```javascript
// React 19 features حالا در دسترسه
import { use } from 'react';

function UserProfile({ userPromise }) {
  // hook جدید 'use' برای promise ها
  const user = use(userPromise);
  return <div>{user.name}</div>;
}
```

### 2. Turbopack (حالت Dev)

برای build های سریع‌تر تو development از Turbopack استفاده کن:

```bash
# Turbopack رو برای development فعال کن
npm run dev -- --turbo

# یا تو package.json اضافه کن
{
  "scripts": {
    "dev": "next dev --turbo"
  }
}
```

**بهبود performance:**
- تا 10 برابر سریع‌تر startup
- تا 99.8% سریع‌تر code update ها با Fast Refresh

### 3. Static Route Indicator

یه ابزار جدید تو development که بهت میگه کدوم صفحه static هست کدوم dynamic:

```javascript
// این indicator تو پایین صفحاتت تو development ظاهر میشه
// نشون میده صفحه فعلی static هست یا dynamic
```

میتونی تو config تنظیمش کنی:

```javascript
// next.config.js
module.exports = {
  devIndicators: {
    buildActivity: true,
    buildActivityPosition: 'bottom-right',
  }
}
```

### 4. API جدید `after()` (تجربی)

کد رو بعد از اینکه response به client فرستاده شد اجرا کن:

```javascript
// app/actions.js
import { unstable_after as after } from 'next/server';

export async function saveUser(userData) {
  // این فوری اجرا میشه و response رو block میکنه
  const user = await createUser(userData);

  // این بعد از فرستادن response اجرا میشه (non-blocking)
  after(async () => {
    await sendWelcomeEmail(user.email);
    await logUserCreation(user.id);
    await updateAnalytics(user);
  });

  return user;
}
```

**تو next.config.js فعالش کن:**
```javascript
module.exports = {
  experimental: {
    after: true
  }
}
```

### 5. کامپوننت `<Form>` بهبود یافته

یه Form component جدید برای client-side navigation بین صفحات:

```javascript
// app/search-form.js
import Form from 'next/form';

export default function SearchForm() {
  return (
    <Form action="/search">
      <input name="query" placeholder="جست‌وجو..." />
      <button type="submit">جست‌وجو</button>
    </Form>
  );
}
```

این form:
- Client-side navigation میکنه به `/search?query=...`
- صفحه مقصد رو prefetch میکنه برای navigation سریع‌تر
- بدون JavaScript هم کار میکنه (progressive enhancement)

**کی ازش استفاده کن:**
- وقتی میخوای از یه صفحه به صفحه دیگه بری
- وقتی میخوای form data رو به عنوان search parameter بفرستی
- وقتی performance benefit های client-side routing رو میخوای

**کی استفاده نکن:**
- Form هایی که به API route ها submit میکنن
- Validation پیچیده
- آپلود فایل

### 6. پشتیبانی از TypeScript Configuration

حالا config فایل Next.js از TypeScript پشتیبانی میکنه:

```typescript
// next.config.ts
import type { NextConfig } from 'next';

const config: NextConfig = {
  experimental: {
    turbo: {
      rules: {
        '*.svg': {
          loaders: ['@svgr/webpack'],
          as: '*.js',
        },
      },
    },
  },
};

export default config;
```

### 7. امنیت بهتر برای Server Actions

Server Action ها حالا امنیت بهتری دارن:

```javascript
// app/actions/user.js
'use server';

export async function createUser(formData) {
  // این یه action ID منحصربفرد و متغیر برای امنیت میگیره
  const name = formData.get('name');
  const email = formData.get('email');

  // همیشه input ها رو validate و sanitize کن!
  // Server action ها هنوز هم public API هستن
  if (!name || !email) {
    throw new Error('Missing required fields');
  }

  // منطق امن شما اینجا
  return await saveUser({ name, email });
}
```

**بهبودهای امنیتی شامل:**
- Server action های استفاده‌نشده خودکار از bundle حذف میشن
- Action ID ها به صورت دوره‌ای rotate میشن
- جداسازی بهتر action ها

**یادت باشه:** Server Action ها هنوز هم public API هستن. همیشه input ها رو validate کن و اقدامات امنیتی مناسب رو پیاده کن.

### 8. پیام های خطای بهتر برای Hydration

بهبود تجربه development برای debug کردن hydration mismatch ها:

```javascript
// قبلاً: خطای کلی hydration
// حالا: مکان دقیق source code و جزئیات mismatch

function ProblematicComponent() {
  const [mounted, setMounted] = useState(false);

  useEffect(() => {
    setMounted(true);
  }, []);

  // حالا پیام های خطای واضح‌تری نشون میده
  return <div>{mounted ? 'Client' : 'Server'}</div>;
}
```

## بهبودهای دیگر

### پشتیبانی از ESLint 9

```json
{
  "devDependencies": {
    "eslint": "^9.0.0"
  }
}
```

### امنیت فایل های Environment

پروژه های جدید خودکار `.env*` فایل ها رو به `.gitignore` اضافه میکنن:

```
# Environment variables
.env
.env.local
.env.development.local
.env.test.local
.env.production.local
```

### بهبودهای Self-hosting

پشتیبانی بهتر برای self-hosting با:
- پشتیبانی بهتر از Docker
- بهتر شدن standalone build ها
- سازگاری بهتر با custom server

```dockerfile
# بهبودهای Dockerfile برای Next.js 15
FROM node:18-alpine AS base
FROM base AS deps
RUN apk add --no-cache libc6-compat
WORKDIR /app
COPY package.json yarn.lock* package-lock.json* pnpm-lock.yaml* ./
RUN \
  if [ -f yarn.lock ]; then yarn --frozen-lockfile; \
  elif [ -f package-lock.json ]; then npm ci; \
  elif [ -f pnpm-lock.yaml ]; then yarn global add pnpm && pnpm i --frozen-lockfile; \
  else echo "Lockfile not found." && exit 1; \
  fi

FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM base AS runner
WORKDIR /app
ENV NODE_ENV production
RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs
COPY --from=builder /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static
USER nextjs
EXPOSE 3000
ENV PORT 3000
CMD ["node", "server.js"]
```

## چک‌لیست Migration

1. **Codemod رو اجرا کن:**
   ```bash
   npx @next/codemod@canary upgrade latest
   ```

2. **Async request API ها رو update کن:**
   - به `headers()`، `cookies()`، `params`، `searchParams` اضافه کن `await`
   - Component هایی که لازمه رو `async` کن

3. **رفتار caching رو بررسی کن:**
   - چک کن ببین کدوم fetch request ها نیاز به caching صریح دارن
   - مطمئن شو API route ها بدون caching درست کار میکنن
   - رفتار dynamic page ها رو تست کن

4. **Dependency ها رو update کن:**
   - سازگاری با React 19 RC رو مطمئن شو
   - ESLint رو update کن اگه از version 9 استفاده میکنی
   - سازگاری package های third-party رو چک کن

5. **کامل تست کن:**
   - همه dynamic route ها رو تست کن
   - Form submission ها و server action ها رو چک کن
   - فرایند build و deployment رو تست کن

## توصیه های Performance

### Turbopack رو برای Development فعال کن
```json
{
  "scripts": {
    "dev": "next dev --turbo"
  }
}
```

### استراتژی Caching رو بهینه کن
```javascript
// صراحتاً بگو چی باید cache بشه
const staticData = await fetch('/api/config', {
  cache: 'force-cache',
  next: { revalidate: 3600 }
});

const dynamicData = await fetch('/api/user-specific-data', {
  cache: 'no-store'
});
```

### از Form component جدید برای navigation داخلی استفاده کن
```javascript
import Form from 'next/form';

// خوب: Navigation داخلی صفحات
<Form action="/search">
  <input name="q" />
  <button>جست‌وجو</button>
</Form>

// بد: API call ها (از form معمولی استفاده کن)
<form action="/api/contact">
  <input name="email" />
  <button>ارسال</button>
</form>
```

## نتیجه‌گیری

Next.js 15 یه بهبود قابل توجه نسبت به version 14 محسوب میشه، بخصوص تو حل کردن مشکلات caching که خیلی از developer ها رو اذیت میکرد. این که caching حالا opt-in شده به جای opt-out، async request API ها و پشتیبانی از React 19، همگی باعث میشه این upgrade جذاب باشه.

**نکات کلیدی:**
- Caching حالا opt-in هست به جای opt-out
- Request API ها async شدن برای static rendering بهتر
- تجربه development به طور قابل توجهی بهبود یافته
- Migration بیشترش خودکار شده با codemod ها

مسیر upgrade خوب پشتیبانی میشه، و فواید - بخصوص بهبود رفتار caching - باعث میشه Next.js 15 برای بیشتر application ها ارزش upgrade رو داشته باشه.

همین امروز شروع کن migration رو با codemod خودکار، و از تجربه Next.js قابل پیش‌بینی‌تر و developer-friendly تر لذت ببر!
