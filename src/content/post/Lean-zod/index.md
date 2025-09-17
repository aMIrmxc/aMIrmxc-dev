---
title: "آموزش کامل Zod: راهنمای جامع"
description: "یاد بگیرید چطور با Zod validation قدرتمند و type-safe برای TypeScript بسازید؛ از مقدماتی تا پیشرفته با مثال‌های عملی"
publishDate: "14 Sep 2024"
updatedDate: "03 Mar 2025"
tags: ["typescript", "zod", "validation", "schema", "type-safety"]
---




# آموزش کامل Zod : راهنمای جامع TypeScript Schema Validation

Zod داره به سرعت تبدیل به اول انتخاب توسعه‌دهنده‌های TypeScript میشه، و دلیلش هم مشخصه. این TypeScript-first schema validation library قدرت runtime validation رو با compile-time type safety ترکیب میکنه و یه ابزار فوق‌العاده ارزشمند برای web development مدرن محسوب میشه.

## Zod چیه؟

Zod یه TypeScript-first schema validation library هست که هم runtime validation و هم compile-time type safety رو در اختیارتون قرار میده. برخلاف validation library های سنتی که مجبورتون میکنن type ها رو جداگانه تعریف کنید، Zod به شما اجازه میده یه بار schema تون رو تعریف کنید و به صورت خودکار TypeScript type هاتون رو از روی اون استنتاج کنه.

### مزایای کلیدی Zod

- **Zero dependencies** - سبک وزنه و اپلیکیشنتون رو سنگین نمیکنه
- **TypeScript-first** - integration فوق‌العاده با TypeScript و automatic type inference
- **Immutable** - با functional programming patterns به کمال کار میکنه
- **Universal** - توی Node.js، browser ها و سایر JavaScript environment ها کار میکنه
- **Runtime safety** - داده هاتون رو در زمان اجرا validate میکنه، نه فقط compile time

## شروع کار با Zod

### نصب

اول Zod رو توی پروژه‌تون نصب کنید:

```bash
npm install zod
```

### تنظیمات اولیه

```typescript
import { z } from 'zod';
```

**مهم:** مطمئن بشید که توی `tsconfig.json` تون `strict: true` توی compiler options فعال باشه.

```json
{
  "compilerOptions": {
    "strict": true
  }
}
```

## اولین Schema تون با Zod

بیاید با یه مثال ساده شروع کنیم تا ببینیم Zod چجوری کار میکنه:

```typescript
import { z } from 'zod';

// Schema رو تعریف میکنیم
const userSchema = z.object({
  username: z.string()
});

// یه user object میسازیم
const user = { username: "WDS" };

// داده رو validate میکنیم
const result = userSchema.parse(user);
console.log(result); // { username: "WDS" }
```

اگه validation ناموفق باشه، Zod یه error پرتاب میکنه:

```typescript
const invalidUser = { username: 123 };
userSchema.parse(invalidUser); // ZodError رو throw میکنه
```

### Type Inference - جادوی Zod

یکی از قوی‌ترین قابلیت‌های Zod همین automatic type inference هست:

```typescript
// به جای اینکه type ها رو جداگانه تعریف کنیم...
type User = {
  username: string;
}

// میتونیم type رو از schema مون استنتاج کنیم
type User = z.infer<typeof userSchema>;
```

این کار نیاز به نگهداری جداگانه TypeScript type ها و validation schema ها رو حذف میکنه!

### Safe Parsing

برای مواقعی که میخواید validation error ها رو به جای throw کردن exception، به آرامی handle کنید، از `safeParse` استفاده کنید:

```typescript
const result = userSchema.safeParse(user);

if (result.success) {
  console.log(result.data); // داده‌های validate شده
} else {
  console.log(result.error); // خطاهای validation
}
```

## Primitive Type ها

Zod از تمام JavaScript primitive type ها پشتیبانی میکنه:

```typescript
const schema = z.object({
  username: z.string(),
  age: z.number(),
  birthday: z.date(),
  isProgrammer: z.boolean(),
  bigNumber: z.bigint()
});
```

### Type های خاص

```typescript
const specialTypes = z.object({
  nothing: z.undefined(),
  empty: z.null(),
  anything: z.any(),
  unknown: z.unknown(),
  never: z.never() // این فیلد هرگز نباید وجود داشته باشه
});
```

## اختیاری کردن Field ها

به طور پیش‌فرض، تمام field های Zod اجباری هستن. برای اختیاری کردنشون از `.optional()` استفاده کنید:

```typescript
const userSchema = z.object({
  username: z.string(),
  age: z.number().optional(),
  birthday: z.date().optional(),
  isProgrammer: z.boolean().optional()
});

// حالا این validation رو پاس میکنه
const user = { username: "WDS" };
```

## Validation پیشرفته

### String Validation ها

Zod گزینه‌های validation گسترده‌ای برای string ها ارائه میده:

```typescript
const stringSchema = z.string()
  .min(3, "حداقل باید ۳ کاراکتر باشه")
  .max(10, "حداکثر میتونه ۱۰ کاراکتر باشه")
  .email("باید یه email معتبر باشه")
  .url("باید یه URL معتبر باشه");
```

### Number Validation ها

```typescript
const ageSchema = z.number()
  .positive("سن باید مثبت باشه")
  .int("سن باید عدد صحیح باشه")
  .min(0, "سن نمیتونه منفی باشه")
  .max(150, "این سن عجیبه!");
```

### Nullable و Nullish

```typescript
const schema = z.object({
  name: z.string().nullable(), // میتونه string یا null باشه
  age: z.number().nullish()    // میتونه number، null یا undefined باشه
});
```

### مقادیر پیش‌فرض

برای field های اختیاری مقدار پیش‌فرض تنظیم کنید:

```typescript
const userSchema = z.object({
  username: z.string(),
  isProgrammer: z.boolean().default(true),
  age: z.number().default(() => Math.floor(Math.random() * 100))
});
```

### Literal Values

با استفاده از literal ها مقدار دقیقی رو اجبار کنید:

```typescript
const statusSchema = z.object({
  status: z.literal("active") // باید دقیقاً "active" باشه
});
```

## Enum ها

### Zod Enum ها (توصیه شده)

```typescript
const hobbySchema = z.object({
  hobby: z.enum(["programming", "weightlifting", "guitar"])
});

type User = z.infer<typeof hobbySchema>;
// hobby: "programming" | "weightlifting" | "guitar"
```

### استفاده از Array برای Enum ها

```typescript
const hobbies = ["programming", "weightlifting", "guitar"] as const;

const schema = z.object({
  hobby: z.enum(hobbies)
});
```

**نکته:** `as const` برای TypeScript خیلی مهمه تا literal type ها رو درست استنتاج کنه.

### Native TypeScript Enum ها

```typescript
enum Hobbies {
  Programming = "programming",
  Weightlifting = "weightlifting",
  Guitar = "guitar"
}

const schema = z.object({
  hobby: z.nativeEnum(Hobbies)
});
```

## دستکاری Object ها

Zod متدهای قدرتمندی برای دستکاری object schema ها ارائه میده:

### Partial

همه field ها رو اختیاری کنید:

```typescript
const partialUserSchema = userSchema.partial();
// حالا همه field ها اختیاری هستن
```

### Pick و Omit

field های خاصی رو انتخاب یا حذف کنید:

```typescript
const usernameOnlySchema = userSchema.pick({ username: true });
const withoutAgeSchema = userSchema.omit({ age: true });
```

### گسترش Object ها

field های جدید به schema های موجود اضافه کنید:

```typescript
const extendedSchema = userSchema.extend({
  email: z.string().email()
});
```

### ترکیب Schema ها

چند schema رو با هم ترکیب کنید:

```typescript
const personalSchema = z.object({ name: z.string() });
const contactSchema = z.object({ email: z.string() });

const combinedSchema = personalSchema.merge(contactSchema);
```

### مدیریت Key های ناشناخته

به طور پیش‌فرض، Zod key های ناشناخته رو حذف میکنه:

```typescript
// رفتار پیش‌فرض - key های ناشناخته حذف میشن
const result = userSchema.parse({
  username: "WDS",
  unknownField: "این حذف میشه"
});

// key های ناشناخته رو پاس کن
const passthroughSchema = userSchema.passthrough();

// حالت سخت‌گیرانه - برای key های ناشناخته error بده
const strictSchema = userSchema.strict();
```

## Array ها و Collection ها

### Array های پایه

```typescript
const friendsSchema = z.object({
  friends: z.array(z.string())
});

const user = {
  friends: ["Kyle", "Julie"]
};
```

### Array Validation ها

```typescript
const schema = z.array(z.string())
  .nonempty("Array نمیتونه خالی باشه")
  .min(2, "حداقل باید ۲ آیتم داشته باشه")
  .max(5, "نمیتونه بیشتر از ۵ آیتم داشته باشه");
```

### دسترسی به Array Element Schema

```typescript
const friendsSchema = z.array(z.string());
const elementSchema = friendsSchema.element; // z.string()
```

## Type های پیشرفته

### Tuple ها

برای array هایی با طول ثابت و type های مشخص:

```typescript
const coordinatesSchema = z.tuple([
  z.number(), // x
  z.number(), // y
  z.number()  // z
]);

const coords = [1, 2, 3]; // معتبره
```

### Tuple ها با Rest Element ها

```typescript
const schema = z.tuple([
  z.string(),    // عنصر اول باید string باشه
  z.date()       // عنصر دوم باید date باشه
]).rest(z.number()); // بقیه عناصر باید number باشن

const valid = ["hello", new Date(), 1, 2, 3, 4];
```

### Union Type ها

چندین type رو مجاز کنید:

```typescript
const idSchema = z.union([z.string(), z.number()]);
// یا از shorthand استفاده کنید
const idSchema = z.string().or(z.number());

const id1 = "user-123"; // معتبره
const id2 = 42;         // معتبره
```

### Discriminated Union ها

برای بهتر شدن performance در union هایی که field مشترک دارن:

```typescript
const responseSchema = z.discriminatedUnion("status", [
  z.object({
    status: z.literal("success"),
    data: z.string()
  }),
  z.object({
    status: z.literal("error"),
    error: z.string()
  })
]);
```

### Record ها و Map ها

برای object هایی با key های پویا:

```typescript
// Record - object با string key ها و value type مشخص
const userMapSchema = z.record(z.string()); // { [key: string]: string }

// با key و value type های مشخص
const userMapSchema = z.record(z.string(), z.number());

// Map ها
const userMapSchema = z.map(
  z.string(), // key type
  z.object({  // value type
    name: z.string()
  })
);
```

### Set ها

برای array های منحصر به فرد:

```typescript
const uniqueNumbersSchema = z.set(z.number())
  .min(2, "حداقل باید ۲ عدد منحصر به فرد داشته باشه");

const numbers = new Set([1, 2, 3]); // معتبره
```

### Promise ها

return type های promise رو validate کنید:

```typescript
const promiseSchema = z.promise(z.string());

const p = Promise.resolve("Hello World");
const result = promiseSchema.parse(p); // Promise<string>
```

## Validation سفارشی

### استفاده از Refine

منطق validation سفارشی بسازید:

```typescript
const emailSchema = z.string()
  .email()
  .refine(
    (email) => email.endsWith("@webdevsimplified.com"),
    {
      message: "Email باید از domain webdevsimplified.com باشه"
    }
  );
```

### چندین Refinement

```typescript
const passwordSchema = z.string()
  .min(8, "Password حداقل باید ۸ کاراکتر باشه")
  .refine(
    (password) => /[A-Z]/.test(password),
    "Password باید حداقل یه حرف بزرگ داشته باشه"
  )
  .refine(
    (password) => /[0-9]/.test(password),
    "Password باید حداقل یه عدد داشته باشه"
  );
```

### Super Refine

برای validation سفارشی پیشرفته با کنترل بیشتر:

```typescript
const schema = z.string().superRefine((val, ctx) => {
  if (val.length < 3) {
    ctx.addIssue({
      code: z.ZodIssueCode.too_small,
      minimum: 3,
      type: "string",
      inclusive: true,
      message: "پیام خطای سفارشی"
    });
  }
});
```

## مدیریت خطاها

### درک کردن Zod Error ها

Zod error ها شامل اطلاعات دقیق هستن اما ممکنه پرحرف باشن:

```typescript
const result = schema.safeParse(invalidData);

if (!result.success) {
  console.log(result.error.errors); // Array از error object ها
}
```

### پیام‌های خطای سفارشی

پیام‌های سفارشی به validation هاتون اضافه کنید:

```typescript
const schema = z.string({
  required_error: "Username الزامیه",
  invalid_type_error: "Username باید string باشه"
}).min(3, "Username حداقل باید ۳ کاراکتر باشه");
```

### استفاده از zod-validation-error Library

برای پیام‌های خطای تمیزتر، helper library نصب کنید:

```bash
npm install zod-validation-error
```

```typescript
import { fromZodError } from 'zod-validation-error';

const result = schema.safeParse(data);

if (!result.success) {
  const validationError = fromZodError(result.error);
  console.log(validationError.message);
  // "Validation error: Username حداقل باید ۳ کاراکتر باشه at username"
}
```

## بهترین روش‌ها

### ۱. از Type Inference استفاده کنید

همیشه از `z.infer` استفاده کنید به جای تعریف دستی type ها:

```typescript
// ✅ خوبه
const userSchema = z.object({ name: z.string() });
type User = z.infer<typeof userSchema>;

// ❌ اینکار رو نکنید
type User = { name: string };
const userSchema = z.object({ name: z.string() });
```

### ۲. Zod Enum ها رو ترجیح بدید

از `z.enum()` استفاده کنید به جای native TypeScript enum ها برای بهتر شدن performance:

```typescript
// ✅ بهتره
const statusSchema = z.enum(["pending", "completed", "failed"]);

// ✅ این هم خوبه با const assertion
const statuses = ["pending", "completed", "failed"] as const;
const statusSchema = z.enum(statuses);
```

### ۳. برای User Input از Safe Parse استفاده کنید

برای validation هایی که به کاربر نشون داده میشه، از `safeParse()` استفاده کنید تا error نپرتابه:

```typescript
function validateUserInput(input: unknown) {
  const result = userSchema.safeParse(input);
  
  if (result.success) {
    return { data: result.data };
  } else {
    return { error: "اطلاعات کاربر نامعتبره" };
  }
}
```

### ۴. Schema های قابل استفاده مجدد بسازید

Schema های پیچیده رو از تکه‌های کوچکتر و قابل استفاده مجدد بسازید:

```typescript
const emailSchema = z.string().email();
const phoneSchema = z.string().regex(/^\d{10}$/);

const contactSchema = z.object({
  email: emailSchema,
  phone: phoneSchema.optional()
});

const userSchema = z.object({
  name: z.string(),
  contact: contactSchema
});
```

## موارد استفاده رایج

### Validation فرم

```typescript
const signupSchema = z.object({
  email: z.string().email("لطفاً یه email معتبر وارد کنید"),
  password: z.string()
    .min(8, "Password حداقل باید ۸ کاراکتر باشه")
    .regex(/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)/, 
           "Password باید شامل حروف بزرگ، کوچک و عدد باشه"),
  confirmPassword: z.string()
}).refine((data) => data.password === data.confirmPassword, {
  message: "Password ها مطابقت ندارن",
  path: ["confirmPassword"]
});
```

### Validation پاسخ API

```typescript
const apiResponseSchema = z.object({
  success: z.boolean(),
  data: z.array(z.object({
    id: z.number(),
    name: z.string(),
    email: z.string().email()
  })).optional(),
  error: z.string().optional()
});

// با fetch استفاده کنید
async function fetchUsers() {
  const response = await fetch('/api/users');
  const rawData = await response.json();
  
  const result = apiResponseSchema.safeParse(rawData);
  
  if (result.success) {
    return result.data;
  } else {
    throw new Error('پاسخ API نامعتبره');
  }
}
```

### Validation متغیرهای محیطی

```typescript
const envSchema = z.object({
  NODE_ENV: z.enum(["development", "production", "test"]),
  DATABASE_URL: z.string().url(),
  PORT: z.string().regex(/^\d+$/).transform(Number),
  API_KEY: z.string().min(1)
});

const env = envSchema.parse(process.env);
// حالا env کاملاً typed و validate شدس!
```

## نتیجه‌گیری

Zod یه ابزار قدرتمنده که شکاف بین runtime validation و compile-time type safety رو در TypeScript پر میکنه. با تعریف schema هاتون یه بار با Zod، هم validation و هم type ها رو دریافت میکنید و نیازی به نگهداری جداگانه منطق validation و تعاریف type نیست.

نکات کلیدی:

1. **ساده شروع کنید** - با schema های پایه شروع کنید و به تدریج پیچیدگی اضافه کنید
2. **از type inference استفاده کنید** - بذارید Zod TypeScript type هاتون رو تولید کنه
3. **از safe parsing استفاده کنید** - در کد هایی که به کاربر نمایش داده میشه، validation error ها رو به آرامی handle کنید
4. **schema ها رو compose کنید** - validation پیچیده رو از قطعات کوچکتر و قابل استفاده مجدد بسازید
5. **validation سفارشی** - از `refine()` برای validation منطق کسب‌وکار استفاده کنید

چه داشته باشید form input ها، API response ها یا environment variable ها رو validate کنید، Zod ابزارهایی که نیاز دارید رو در اختیارتون قرار میده تا مطمئن بشید داده‌هاتون هم valid و هم type-safe هستن. همین امروز شروع به استفاده از Zod در پروژه‌های TypeScript تون کنید و اعتماد به نفسی که از runtime-validated، type-safe code به دست میاد رو تجربه کنید.

## منابع بیشتر

- [مستندات رسمی Zod](https://zod.dev/)
- [GitHub Repository Zod](https://github.com/colinhacks/zod)
- [zod-validation-error Library](https://www.npmjs.com/package/zod-validation-error)

موفق باشید با Zod! 🚀