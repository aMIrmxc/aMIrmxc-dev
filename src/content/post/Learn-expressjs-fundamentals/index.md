---
title: "آموزش جامع Express.js "
description: "راهنمای کامل Express.js شامل routing، middleware، view engine ها و تکنیک‌های پیشرفته برای ساخت backend های قدرتمند"
publishDate: "17 Sep 2023"
updatedDate: "03 Apr 2025"
tags: ["express", "nodejs", "backend", "javascript", "middleware", "routing"]
---



# آموزش کامل Express.js: از مبانی تا قابلیت‌های پیشرفته

اگه داری با JavaScript backend می‌سازی، احتمالاً باید از Express.js استفاده کنی. تو این راهنمای کامل، همه چیزی که باید در مورد Express بدونی رو یاد می‌گیری، حتی چند تا فیچر پیشرفته‌ای که کم تو آموزش‌های دیگه دیده میشه.

## فهرست مطالب
1. [شروع کار با Express.js](#getting-started)
2. [ساخت اولین سرور](#first-server)
3. [درک کردن Route ها](#routes)
4. [کار با Response Methods](#responses)
5. [استفاده از View Engine با EJS](#view-engines)
6. [سازماندهی کد با Router ها](#routers)
7. [Route های پویا و Parameter ها](#dynamic-routes)
8. [تکنیک‌های پیشرفته Routing](#advanced-routing)
9. [درک کردن Middleware](#middleware)
10. [Middleware های داخلی](#built-in-middleware)
11. [کار با داده‌های Form](#form-data)
12. [Query Parameter ها](#query-parameters)

## شروع کار با Express.js {#getting-started}

### پیش نیازها
قبل از اینکه تو Express.js فرو بری، باید Node.js رو روی سیستمت نصب کنی. اگه هنوز نصبش نکردی، از سایت رسمی Node.js دانلودش کن.

### راه‌اندازی پروژه
اول یه پروژه جدید بساز و dependency های لازم رو نصب کن:

```bash
# یه پروژه npm جدید درست کن
npm init -y

# Express رو نصب کن
npm install express

# nodemon رو برای development نصب کن (خودکار server رو restart می‌کنه)
npm install --save-dev nodemon
```

### تنظیمات package.json
یه script برای development به `package.json`ت اضافه کن:

```json
{
  "scripts": {
    "dev": "nodemon server.js"
  }
}
```

حالا می‌تونی `npm run dev` رو اجرا کنی تا server ات با restart خودکار شروع شه.

## ساخت اولین سرور {#first-server}

یه فایل `server.js` تو root پروژه‌ت بساز:

```javascript
const express = require('express');
const app = express();

// server رو روی port 3000 شروع کن
app.listen(3000, () => {
  console.log('Server running on http://localhost:3000');
});
```

این کد یه server پایه Express می‌سازه که روی port 3000 گوش می‌ده. اما هنوز هیچ route ای نداره.

## درک کردن Route ها {#routes}

Route ها مشخص می‌کنن که app تو چطور به request های مختلف client پاسخ بده. Express از همه HTTP method ها پشتیبانی می‌کنه:

```javascript
// GET route
app.get('/', (req, res) => {
  console.log('Home page accessed');
  res.send('Hello World!');
});

// POST route
app.post('/users', (req, res) => {
  res.send('Create a new user');
});

// PUT route
app.put('/users/:id', (req, res) => {
  res.send('Update user');
});

// DELETE route
app.delete('/users/:id', (req, res) => {
  res.send('Delete user');
});
```

### Route Parameter ها
`req` و `res` parameter ها به ترتیب request object و response object رو نشون می‌دن. بیشتر وقت‌ها parameter سوم (`next`) رو نمی‌خوای که برای middleware استفاده میشه.

## کار با Response Methods {#responses}

Express چندین method برای ارسال response به client ها داره:

### Response Method های پایه

```javascript
app.get('/basic', (req, res) => {
  // ارسال متن ساده
  res.send('Hello World');
});

app.get('/status', (req, res) => {
  // ارسال status code همراه با پیام
  res.status(500).send('Server Error');
});

app.get('/json', (req, res) => {
  // ارسال JSON response
  res.json({ message: 'Hello World', status: 'success' });
});

app.get('/download', (req, res) => {
  // فعال کردن دانلود فایل
  res.download('./server.js');
});
```

### زنجیره کردن Response Method ها
می‌تونی چند response method رو با هم زنجیر کنی:

```javascript
app.get('/error', (req, res) => {
  res.status(500).json({ message: 'Internal Server Error' });
});
```

## استفاده از View Engine ها با EJS {#view-engines}

View engine ها بهت اجازه می‌دن صفحات HTML پویا رندر کنی. EJS (Embedded JavaScript) یکی از محبوب‌ترین گزینه‌هاست چون شبیه HTML هست.

### راه‌اندازی EJS

```bash
npm install ejs
```

Express رو طوری تنظیم کن که از EJS استفاده کنه:

```javascript
const express = require('express');
const app = express();

// EJS رو به عنوان view engine تنظیم کن
app.set('view engine', 'ejs');

app.get('/', (req, res) => {
  res.render('index', { text: 'World' });
});
```

### ساخت پوشه Views
یه پوشه `views` تو root پروژه‌ت بساز و فایل `index.ejs` رو اضافه کن:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Express App</title>
</head>
<body>
    <h1>Hello <%= text %></h1>
    <!-- می‌تونی کد JavaScript هم بنویسی -->
    <p>2 + 2 = <%= 2 + 2 %></p>
    
    <!-- variable های undefined رو ایمن handle کن -->
    <p>Name: <%= locals.name || 'Default Name' %></p>
</body>
</html>
```

### نکات EJS Syntax
- `<%= %>` - مقدار رو تو HTML output می‌کنه
- `<% %>` - کد JavaScript اجرا می‌کنه بدون output
- `locals` - object ای که همیشه تعریف شده و شامل داده‌های پاس شده
- از `locals.variableName` برای دسترسی ایمن به variable های احتمالاً undefined استفاده کن

## سازماندهی کد با Router ها {#routers}

وقتی app تو بزرگ میشه، نگه داشتن همه route ها تو یه فایل سخت میشه. Express router ها کمک می‌کنن کدت رو به route handler های modular تبدیل کنی.

### ساخت فایل Router
یه پوشه `routes` بساز و فایل `users.js` رو اضافه کن:

```javascript
const express = require('express');
const router = express.Router();

// Route ها نسبت به mounted path هستن
router.get('/', (req, res) => {
  res.send('User list');
});

router.get('/new', (req, res) => {
  res.send('New user form');
});

router.post('/', (req, res) => {
  res.send('Create user');
});

// router رو export کن
module.exports = router;
```

### استفاده از Router تو Main Server
تو `server.js`ت:

```javascript
const express = require('express');
const userRouter = require('./routes/users');
const app = express();

// router رو روی /users mount کن
app.use('/users', userRouter);

app.listen(3000);
```

این کار route هایی مثل اینا می‌سازه:
- GET `/users/` → User list
- GET `/users/new` → New user form  
- POST `/users/` → Create user

## Route های پویا و Parameter ها {#dynamic-routes}

Dynamic route ها بهت اجازه می‌دن قسمت‌های متغیر URL رو capture کنی:

```javascript
// Dynamic parameter با دونقطه
router.get('/:id', (req, res) => {
  const userId = req.params.id;
  res.send(`Get user with ID: ${userId}`);
});

// چند parameter
router.get('/:id/posts/:postId', (req, res) => {
  const { id, postId } = req.params;
  res.send(`User ${id}, Post ${postId}`);
});
```

### مهم: ترتیب Route ها مهمه
Route های static باید قبل از dynamic route ها بیان:

```javascript
// ✅ ترتیب درست
router.get('/new', (req, res) => {
  res.send('New user form');
});

router.get('/:id', (req, res) => {
  res.send(`User ${req.params.id}`);
});

// ❌ ترتیب اشتباه - /:id میتونه /new رو بگیره
```

## تکنیک‌های پیشرفته Routing {#advanced-routing}

### Route Chaining
وقتی چند HTTP method مسیر یکسانی دارن، از route chaining استفاده کن:

```javascript
router.route('/:id')
  .get((req, res) => {
    res.send(`Get user ${req.params.id}`);
  })
  .put((req, res) => {
    res.send(`Update user ${req.params.id}`);
  })
  .delete((req, res) => {
    res.send(`Delete user ${req.params.id}`);
  });
```

### Router Parameters Middleware
Method `router.param()` middleware رو برای parameter های خاص اجرا می‌کنه:

```javascript
const users = [
  { name: 'Kyle' },
  { name: 'Sally' }
];

// هروقت parameter :id پیدا شد اجرا میشه
router.param('id', (req, res, next, id) => {
  console.log(`Looking for user with ID: ${id}`);
  
  // user رو از database/array بگیر
  req.user = users[id];
  
  // به middleware/route بعدی برو
  next();
});

router.get('/:id', (req, res) => {
  // req.user حالا در دسترسه
  console.log(req.user);
  res.json(req.user);
});
```

این middleware قبل از هر route ای که parameter `:id` داره اجرا میشه و بهت اجازه می‌ده:
- Parameter ها رو validate کنی
- داده رو از database بگیری
- داده رو به request object اضافه کنی
- منطق مشترک parameter ها رو در یه جا handle کنی

## درک کردن Middleware {#middleware}

Function های Middleware در طول چرخه request-response اجرا میشن. اونا به request object (`req`)، response object (`res`)، و function middleware بعدی (`next`) دسترسی دارن.

### ساخت Custom Middleware

```javascript
// function middleware برای log کردن
function logger(req, res, next) {
  console.log(`${req.method} ${req.originalUrl}`);
  next(); // برای ادامه به middleware بعدی next رو صدا کن
}

// استفاده global از middleware
app.use(logger);

// استفاده از middleware روی route های خاص
app.get('/', logger, (req, res) => {
  res.send('Home page');
});

// چند function middleware
app.get('/protected', auth, logger, (req, res) => {
  res.send('Protected route');
});
```

### Router-Level Middleware
middleware رو روی router های خاص اعمال کن:

```javascript
// تو routes/users.js
function userLogger(req, res, next) {
  console.log('User route accessed');
  next();
}

// روی همه route های این router اعمال کن
router.use(userLogger);
```

### ترتیب اجرای Middleware
Middleware ها به ترتیب تعریف شدنشون اجرا میشن:

```javascript
app.use(middleware1); // اول اجرا میشه
app.use(middleware2); // دوم اجرا میشه
app.get('/', middleware3, handler); // middleware3 سوم، بعدش handler
```

## Middleware های داخلی {#built-in-middleware}

Express چندین function middleware داخلی داره:

### سرو کردن Static File ها
فایل‌های static (HTML، CSS، JavaScript، عکس) رو از یه پوشه سرو کن:

```javascript
// فایل‌ها رو از پوشه 'public' سرو کن
app.use(express.static('public'));
```

یه پوشه `public` بساز و فایل‌ها رو اضافه کن:
```
public/
  ├── index.html
  ├── style.css
  ├── script.js
  └── images/
      └── logo.png
```

فایل‌ها اینجا در دسترس هستن:
- `http://localhost:3000/index.html`
- `http://localhost:3000/style.css`
- `http://localhost:3000/images/logo.png`

### Parse کردن Request Body ها
داده‌های form و JSON رو handle کن:

```javascript
// Parse کردن داده‌های URL-encoded form
app.use(express.urlencoded({ extended: true }));

// Parse کردن داده‌های JSON
app.use(express.json());
```

## کار با داده‌های Form {#form-data}

### ساخت Form
فایل `views/users/new.ejs` رو بساز:

```html
<!DOCTYPE html>
<html>
<head>
    <title>New User</title>
</head>
<body>
    <form action="/users" method="POST">
        <input type="text" name="firstName" placeholder="First Name" 
               value="<%= locals.firstName || '' %>" required>
        <button type="submit">Create User</button>
    </form>
</body>
</html>
```

### پردازش داده‌های Form
submit شدن form رو handle کن:

```javascript
const users = [];

router.get('/new', (req, res) => {
  res.render('users/new');
});

router.post('/', (req, res) => {
  const isValid = true; // منطق validation تو اینجا
  
  if (isValid) {
    // user رو بساز
    const newUser = {
      firstName: req.body.firstName
    };
    users.push(newUser);
    
    // به صفحه user redirect کن
    const userId = users.length - 1;
    res.redirect(`/users/${userId}`);
  } else {
    // form رو دوباره با error و حفظ input نمایش بده
    res.render('users/new', {
      firstName: req.body.firstName,
      error: 'Invalid input'
    });
  }
});
```

### مفاهیم کلیدی Form Handling
1. **حفظ Input در صورت Error**: داده‌های form رو برگردون تا input کاربر حفظ بشه
2. **Validation**: همیشه server-side validate کن، صرف نظر از client-side validation
3. **Redirect بعد از POST**: از الگوی Post-Redirect-Get برای جلوگیری از submit های تکراری استفاده کن
4. **Error Handling**: feedback واضح به کاربرها بده

## Query Parameter ها {#query-parameters}

Query parameter ها بخشی از URL بعد از علامت `?` هستن:
`http://localhost:3000/users?name=Kyle&age=25`

### دسترسی به Query Parameter ها

```javascript
router.get('/', (req, res) => {
  const name = req.query.name;
  const age = req.query.age;
  
  // یا با استفاده از destructuring
  const { name, age } = req.query;
  
  console.log(`Name: ${name}, Age: ${age}`);
  
  res.send(`Hello ${name || 'Anonymous'}`);
});
```

### موارد استفاده Query Parameter ها
- **فیلتر کردن**: `GET /products?category=electronics&price_max=100`
- **Pagination**: `GET /users?page=2&limit=10`
- **جستجو**: `GET /search?q=express.js`
- **مرتب سازی**: `GET /posts?sort=date&order=desc`

## مثال کامل: سیستم مدیریت کاربر

اینجا یه مثال کامل که همه چیز رو با هم ترکیب می‌کنه:

### server.js
```javascript
const express = require('express');
const userRouter = require('./routes/users');
const app = express();

// View engine
app.set('view engine', 'ejs');

// Middleware
app.use(express.static('public'));
app.use(express.urlencoded({ extended: true }));
app.use(express.json());

// Routes
app.get('/', (req, res) => {
  res.render('index');
});

app.use('/users', userRouter);

app.listen(3000, () => {
  console.log('Server running on http://localhost:3000');
});
```

### routes/users.js
```javascript
const express = require('express');
const router = express.Router();

const users = [
  { firstName: 'Kyle' },
  { firstName: 'Sally' }
];

// Middleware برای user parameter
router.param('id', (req, res, next, id) => {
  req.user = users[id];
  next();
});

// Routes
router.get('/', (req, res) => {
  res.render('users/index', { users });
});

router.get('/new', (req, res) => {
  res.render('users/new');
});

router.post('/', (req, res) => {
  const isValid = req.body.firstName && req.body.firstName.trim() !== '';
  
  if (isValid) {
    users.push({ firstName: req.body.firstName });
    res.redirect(`/users/${users.length - 1}`);
  } else {
    res.render('users/new', {
      firstName: req.body.firstName,
      error: 'First name is required'
    });
  }
});

router.route('/:id')
  .get((req, res) => {
    res.render('users/show', { user: req.user });
  })
  .put((req, res) => {
    req.user.firstName = req.body.firstName;
    res.redirect(`/users/${req.params.id}`);
  })
  .delete((req, res) => {
    users.splice(req.params.id, 1);
    res.redirect('/users');
  });

module.exports = router;
```

## بهترین روش‌ها و نکات

### 1. Error Handling
همیشه error ها رو به درستی handle کن:

```javascript
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});
```

### 2. Environment Variables
برای configuration از environment variable ها استفاده کن:

```javascript
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

### 3. امنیت
Security middleware ها رو نصب و پیکربندی کن:

```bash
npm install helmet cors
```

```javascript
const helmet = require('helmet');
const cors = require('cors');

app.use(helmet());
app.use(cors());
```

### 4. Validation
برای validation محکم input ها از کتابخونه‌هایی مثل Joi یا express-validator استفاده کن.

### 5. یکپارچگی Database
در حالی که این آموزش از array استفاده می‌کنه، تو production باید با database هایی مثل MongoDB، PostgreSQL، یا MySQL یکپارچه بشی.

## نتیجه‌گیری

Express.js یه framework قوی و انعطاف‌پذیره که ساخت web server ها با Node.js رو ساده و لذت‌بخش می‌کنه. مفاهیم کلیدی که تو این آموزش پوشش دادیم شامل:

- راه‌اندازی Express application ها و server ها
- ساخت و سازماندهی route ها با router ها
- درک middleware و جریان اجراش
- Handle کردن انواع مختلف داده (JSON، form ها، static file ها)
- کار با route های پویا و parameter ها
- استفاده از view engine ها برای server-side rendering

با این مبانی، برای ساخت web application ها و API های محکم آماده‌ای. همونطور که به یادگیری ادامه می‌دی، موضوعاتی مثل authentication، یکپارچگی database، testing، و deployment رو بررسی کن تا application های آماده production بسازی.

یادت باشه که Express.js از طریق اکوسیستم middleware اش خیلی قابل گسترشه. npm registry هزاران package middleware داره که می‌تونن قابلیت‌هایی مثل authentication، logging، rate limiting، و خیلی چیزهای دیگه رو به application تو اضافه کنن. 