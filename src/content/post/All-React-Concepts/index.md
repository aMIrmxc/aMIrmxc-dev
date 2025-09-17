---
title: "راهنمای کامل React: همه مفاهیم به زبان ساده"
description: "این پست تمام مفاهیم اساسی React را از componentها و JSX گرفته تا Context، Portals و Error Boundaries با مثال‌های عملی و توضیحات ساده پوشش می‌دهد"
publishDate: "15 Apr 2024"
updatedDate: "03 Jan 2025"
tags: ["React", "JavaScript", "Frontend", "Tutorial"]
---

# راهنمای کامل React: همه مفاهیم به زبان ساده

React یه کتابخانه قدرتمند JavaScript هست که طریقه ساخت رابط کاربری رو کاملاً عوض کرده. اگرچه اصطلاحات خاص خودش مثل reconciliation، composition و error boundaries داره، اما فهمیدن این مفاهیم کلید تسلط روی React هست. این راهنمای جامع قراره همه مفاهیم اساسی React رو با مثال‌های عملی و توضیحات ساده برات شرح بده.

## فهرست مطالب

- [راهنمای کامل React: همه مفاهیم به زبان ساده](#راهنمای-کامل-react-همه-مفاهیم-به-زبان-ساده)
  - [فهرست مطالب](#فهرست-مطالب)
  - [Component ها: قطعات سازنده](#component-ها-قطعات-سازنده)
  - [JSX: JavaScript با لباس HTML](#jsx-javascript-با-لباس-html)
    - [قوانین کلیدی JSX:](#قوانین-کلیدی-jsx)
    - [React Fragments](#react-fragments)
  - [Props: انتقال داده بین Component ها](#props-انتقال-داده-بین-component-ها)
    - [Props مخصوص Children](#props-مخصوص-children)
    - [Props مخصوص Key](#props-مخصوص-key)
  - [فرآیند Rendering](#فرآیند-rendering)
    - [فرآیند Virtual DOM:](#فرآیند-virtual-dom)
  - [Event Handling](#event-handling)
  - [State Management](#state-management)
    - [Hook مخصوص useState](#hook-مخصوص-usestate)
    - [Controlled Components](#controlled-components)
  - [React Hooks](#react-hooks)
    - [۱. State Hooks](#۱-state-hooks)
    - [۲. Context Hooks](#۲-context-hooks)
    - [۳. Ref Hooks](#۳-ref-hooks)
    - [۴. Effect Hooks](#۴-effect-hooks)
    - [۵. Performance Hooks](#۵-performance-hooks)
  - [خلوص Component ها](#خلوص-component-ها)
    - [Strict Mode](#strict-mode)
  - [Effects و Side Effects](#effects-و-side-effects)
  - [Refs: دسترسی مستقیم به DOM](#refs-دسترسی-مستقیم-به-dom)
  - [Context: انتقال پیشرفته داده](#context-انتقال-پیشرفته-داده)
  - [Portals: رندر خارج از درخت](#portals-رندر-خارج-از-درخت)
  - [Suspense: مدیریت حالت Loading](#suspense-مدیریت-حالت-loading)
  - [Error Boundaries: مدیریت نرم خطاها](#error-boundaries-مدیریت-نرم-خطاها)
  - [نتیجه‌گیری](#نتیجهگیری)

## Component ها: قطعات سازنده

Component ها پایه و اساس همه اپ‌های React هستن. فکرشون کن مثل قطعه‌های لگو - قطعات قابل استفاده مجددی که میشه باهاشون رابط‌های کاربری پیچیده ساخت. Component ها میتونن هر چیزی باشن از دکمه‌های ساده تا صفحات کامل.

```jsx
// مثال ساده از component
function Welcome() {
  return <h1>سلام دنیا!</h1>;
}

// component با پارامتر
function Greeting({ name }) {
  return <h1>سلام، {name}!</h1>;
}

// استفاده از component ها
function App() {
  return (
    <div>
      <Welcome />
      <Greeting name="توسعه‌دهنده React" />
    </div>
  );
}
```

هر component در React در واقع یه تابع JavaScript هست که markup برمی‌گردونه. این رویکرد تابعی باعث میشه component ها قابل پیش‌بینی و آسون برای تست باشن.

## JSX: JavaScript با لباس HTML

JSX (JavaScript XML) یه syntax extension برای React هست که بهت اجازه میده کدهای شبیه HTML رو داخل JavaScript بنویسی. اگرچه اختیاری هست، اما JSX خیلی راحت‌تر از استفاده از `React.createElement()` هست.

```jsx
// syntax جی‌اس‌ایکس
function Button() {
  return <button className="primary">روم کلیک کن</button>;
}

// معادل بدون JSX
function ButtonWithoutJSX() {
  return React.createElement(
    'button',
    { className: 'primary' },
    'روم کلیک کن'
  );
}
```

### قوانین کلیدی JSX:

1. **برای attribute ها از camelCase استفاده کن**: `className` به جای `class`، `onClick` به جای `onclick`
2. **JavaScript رو با آکولاد embed کن**: `{variable}` یا `{expression}`
3. **یه element والد برگردون**: چندین element رو داخل div یا React Fragment قرار بده

```jsx
function UserCard({ user }) {
  const isOnline = user.lastSeen < Date.now() - 300000; // 5 دقیقه
  
  return (
    <div className="user-card">
      <h2>{user.name}</h2>
      <p style={{ color: isOnline ? 'green' : 'red' }}>
        {isOnline ? 'آنلاین' : 'آفلاین'}
      </p>
    </div>
  );
}
```

### React Fragments

وقتی که باید چندین element برگردونی بدون اینکه node اضافی به DOM اضافه کنی:

```jsx
// استفاده از React.Fragment
function UserInfo() {
  return (
    <React.Fragment>
      <h1>پروفایل کاربر</h1>
      <p>خوش اومدی!</p>
    </React.Fragment>
  );
}

// syntax کوتاه
function UserInfoShort() {
  return (
    <>
      <h1>پروفایل کاربر</h1>
      <p>خوش اومدی!</p>
    </>
  );
}
```

## Props: انتقال داده بین Component ها

Props (properties) روشی هست که با اون میتونی داده رو از component والد به component فرزند منتقل کنی. فکرشون کن مثل attribute های سفارشی که میتونی به هر component اضافه کنی تا انعطاف‌پذیر و قابل استفاده مجدد بشن.

```jsx
function BlogPost({ title, content, author, publishDate }) {
  return (
    <article>
      <h1>{title}</h1>
      <div className="meta">
        نویسنده: {author} - تاریخ: {publishDate}
      </div>
      <div className="content">{content}</div>
    </article>
  );
}

// استفاده
function App() {
  return (
    <BlogPost 
      title="یادگیری React"
      content="React برای ساخت UI عالیه..."
      author="علی احمدی"
      publishDate="۱۴۰۳/۰۳/۱۵"
    />
  );
}
```

### Props مخصوص Children

prop خاص `children` بهت اجازه میده component ها رو به عنوان محتوا منتقل کنی:

```jsx
function Card({ children }) {
  return (
    <div className="card">
      <div className="card-content">
        {children}
      </div>
    </div>
  );
}

// استفاده
function App() {
  return (
    <Card>
      <h2>عنوان کارت</h2>
      <p>این محتوا به عنوان children منتقل میشه</p>
      <button>دکمه عمل</button>
    </Card>
  );
}
```

### Props مخصوص Key

وقتی که لیست render میکنی، React به prop `key` نیاز داره تا تغییرات رو به طور مؤثر رصد کنه:

```jsx
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map((todo) => (
        <li key={todo.id}>
          {todo.text}
        </li>
      ))}
    </ul>
  );
}

// مثال داده
const todos = [
  { id: 1, text: "یادگیری React" },
  { id: 2, text: "ساخت اپلیکیشن" },
  { id: 3, text: "انتشار در پروداکشن" }
];
```

## فرآیند Rendering

فهمیدن اینکه React چطور اپلیکیشنت رو render میکنه برای ساخت اپ‌های کارآمد خیلی مهمه. React از Virtual DOM (VDOM) برای بهینه‌سازی آپدیت‌ها استفاده میکنه.

### فرآیند Virtual DOM:

1. **تغییر State**: چیزی تو اپت تغییر میکنه
2. **آپدیت Virtual DOM**: React یه نمایش مجازی جدید میسازه
3. **Diffing**: React Virtual DOM جدید رو با نسخه قبلی مقایسه میکنه
4. **Reconciliation**: React فقط قسمت‌های تغییر کرده رو تو DOM واقعی آپدیت میکنه

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  // وقتی setCount صدا زده میشه:
  // 1. React یه Virtual DOM جدید میسازه
  // 2. با Virtual DOM قبلی مقایسه میکنه
  // 3. فقط محتوای متنی رو تو DOM واقعی آپدیت میکنه
  return (
    <div>
      <p>شمارنده: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        افزایش
      </button>
    </div>
  );
}
```

## Event Handling

React رویدادهای synthetic ارائه میده که رویدادهای DOM بومی رو wrap میکنه و رفتار یکسان رو در همه مرورگرها تضمین میکنه.

```jsx
function ContactForm() {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    message: ''
  });

  const handleInputChange = (event) => {
    const { name, value } = event.target;
    setFormData(prev => ({
      ...prev,
      [name]: value
    }));
  };

  const handleSubmit = (event) => {
    event.preventDefault();
    console.log('فرم ارسال شد:', formData);
    // مدیریت ارسال فرم
  };

  const handleButtonClick = () => {
    alert('دکمه کلیک شد!');
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        name="name"
        value={formData.name}
        onChange={handleInputChange}
        placeholder="نام شما"
      />
      <input
        type="email"
        name="email"
        value={formData.email}
        onChange={handleInputChange}
        placeholder="ایمیل شما"
      />
      <textarea
        name="message"
        value={formData.message}
        onChange={handleInputChange}
        placeholder="پیام شما"
      />
      <button type="submit">ارسال پیام</button>
      <button type="button" onClick={handleButtonClick}>
        کلیک کن
      </button>
    </form>
  );
}
```

## State Management

State نماینده داده‌ای هست که میتونه در طول زمان تو اپلیکیشن React تغییر کنه. برخلاف متغیرهای معمولی JavaScript، تغییرات state باعث re-render شدن میشه.

### Hook مخصوص useState

```jsx
import { useState } from 'react';

function ShoppingCart() {
  const [items, setItems] = useState([]);
  const [total, setTotal] = useState(0);

  const addItem = (item) => {
    setItems(prev => [...prev, item]);
    setTotal(prev => prev + item.price);
  };

  const removeItem = (itemId) => {
    const itemToRemove = items.find(item => item.id === itemId);
    setItems(prev => prev.filter(item => item.id !== itemId));
    setTotal(prev => prev - itemToRemove.price);
  };

  return (
    <div>
      <h2>سبد خرید</h2>
      <p>مجموع: {total} تومان</p>
      <ul>
        {items.map(item => (
          <li key={item.id}>
            {item.name} - {item.price} تومان
            <button onClick={() => removeItem(item.id)}>حذف</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

### Controlled Components

Controlled components از React state برای مدیریت input های فرم استفاده میکنن:

```jsx
function LoginForm() {
  const [credentials, setCredentials] = useState({
    username: '',
    password: ''
  });

  const handleChange = (event) => {
    const { name, value } = event.target;
    setCredentials(prev => ({
      ...prev,
      [name]: value
    }));
  };

  return (
    <form>
      <input
        type="text"
        name="username"
        value={credentials.username}
        onChange={handleChange}
        placeholder="نام کاربری"
      />
      <input
        type="password"
        name="password"
        value={credentials.password}
        onChange={handleChange}
        placeholder="رمز عبور"
      />
    </form>
  );
}
```

## React Hooks

Hook ها بهت اجازه میدن که از قابلیت‌های React داخل function component ها استفاده کنی. پنج دسته اصلی دارن:

### ۱. State Hooks
- `useState`: مدیریت state محلی component
- `useReducer`: مدیریت state پیچیده با الگوی reducer

### ۲. Context Hooks  
- `useContext`: دسترسی به مقادیر React context

### ۳. Ref Hooks
- `useRef`: ارجاع به element های DOM یا ذخیره مقادیر قابل تغییر

### ۴. Effect Hooks
- `useEffect`: انجام side effects

### ۵. Performance Hooks
- `useMemo`: memoize کردن محاسبات گران
- `useCallback`: memoize کردن توابع

```jsx
import { useState, useEffect, useRef, useMemo, useCallback } from 'react';

function OptimizedComponent({ items }) {
  const [filter, setFilter] = useState('');
  const [count, setCount] = useState(0);
  const inputRef = useRef(null);

  // useEffect برای side effects
  useEffect(() => {
    document.title = `شمارنده: ${count}`;
  }, [count]);

  // useMemo برای محاسبات گران
  const filteredItems = useMemo(() => {
    return items.filter(item => 
      item.name.toLowerCase().includes(filter.toLowerCase())
    );
  }, [items, filter]);

  // useCallback برای ارجاعات پایدار تابع
  const handleItemClick = useCallback((item) => {
    console.log('آیتم کلیک شد:', item);
  }, []);

  const focusInput = () => {
    inputRef.current?.focus();
  };

  return (
    <div>
      <input
        ref={inputRef}
        type="text"
        value={filter}
        onChange={(e) => setFilter(e.target.value)}
        placeholder="فیلتر آیتم‌ها..."
      />
      <button onClick={focusInput}>فوکوس روی Input</button>
      
      <p>شمارنده: {count}</p>
      <button onClick={() => setCount(c => c + 1)}>افزایش</button>

      <ul>
        {filteredItems.map(item => (
          <li key={item.id} onClick={() => handleItemClick(item)}>
            {item.name}
          </li>
        ))}
      </ul>
    </div>
  );
}
```

## خلوص Component ها

Component های خالص قابل پیش‌بینی هستن و debug کردنشون آسون‌تره. یه component خالص همیشه برای ورودی یکسان، خروجی یکسان برمی‌گردونه و متغیرهای خارجی رو تغییر نمیده.

```jsx
// ❌ Component ناخالص - متغیر خارجی رو تغییر میده
let globalCount = 0;

function ImpureCounter() {
  globalCount++; // Side effect در حین render
  return <div>شمارنده: {globalCount}</div>;
}

// ✅ Component خالص - ورودی یکسان، خروجی یکسان
function PureCounter({ count }) {
  return <div>شمارنده: {count}</div>;
}

// ✅ Component خالص با state
function PureCounterWithState() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <div>شمارنده: {count}</div>
      <button onClick={() => setCount(c => c + 1)}>
        افزایش
      </button>
    </div>
  );
}
```

### Strict Mode

Strict Mode React کمک میکنه component های ناخالص رو با دوبار صدا زدن توابع خاص شناسایی کنی:

```jsx
import { StrictMode } from 'react';

function App() {
  return (
    <StrictMode>
      <MyApp />
    </StrictMode>
  );
}
```

## Effects و Side Effects

Effect ها بهت اجازه میدن که از React بیرون بری و با سیستم‌های خارجی مثل API ها، دستکاری DOM، یا subscription ها ارتباط برقرار کنی.

```jsx
import { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    async function fetchUser() {
      try {
        setLoading(true);
        const response = await fetch(`/api/users/${userId}`);
        if (!response.ok) throw new Error('دریافت کاربر ناموفق');
        const userData = await response.json();
        setUser(userData);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }

    fetchUser();
  }, [userId]); // Effect وقتی userId تغییر کنه اجرا میشه

  // Cleanup effect
  useEffect(() => {
    const timer = setInterval(() => {
      console.log('چک کردن برای آپدیت...');
    }, 30000);

    return () => {
      clearInterval(timer); // پاک‌سازی در unmount
    };
  }, []);

  if (loading) return <div>در حال بارگذاری...</div>;
  if (error) return <div>خطا: {error}</div>;
  if (!user) return <div>کاربر پیدا نشد</div>;

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}
```

## Refs: دسترسی مستقیم به DOM

Ref ها راهی برای دسترسی مستقیم به element های DOM هستن، مفیدن برای focus کردن input ها، اندازه‌گیری element ها، یا integration با کتابخانه‌های third-party.

```jsx
import { useRef, useEffect } from 'react';

function VideoPlayer({ src }) {
  const videoRef = useRef(null);
  const [isPlaying, setIsPlaying] = useState(false);

  useEffect(() => {
    // فوکوس روی element ویدیو وقتی component mount میشه
    videoRef.current?.focus();
  }, []);

  const togglePlayPause = () => {
    const video = videoRef.current;
    if (!video) return;

    if (isPlaying) {
      video.pause();
    } else {
      video.play();
    }
    setIsPlaying(!isPlaying);
  };

  const handleTimeUpdate = () => {
    const video = videoRef.current;
    if (video) {
      console.log(`زمان فعلی: ${video.currentTime}`);
    }
  };

  return (
    <div>
      <video
        ref={videoRef}
        src={src}
        onTimeUpdate={handleTimeUpdate}
        width="100%"
      />
      <button onClick={togglePlayPause}>
        {isPlaying ? 'توقف' : 'پخش'}
      </button>
    </div>
  );
}
```

## Context: انتقال پیشرفته داده

Context مشکل "prop drilling" رو حل میکنه و بهت اجازه میده داده رو در سراسر درخت component بدون انتقال props از هر سطح به اشتراک بذاری.

```jsx
import { createContext, useContext, useState } from 'react';

// ساخت context
const ThemeContext = createContext();
const UserContext = createContext();

// Provider components
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

function UserProvider({ children }) {
  const [user, setUser] = useState(null);

  const login = (userData) => setUser(userData);
  const logout = () => setUser(null);

  return (
    <UserContext.Provider value={{ user, login, logout }}>
      {children}
    </UserContext.Provider>
  );
}

// Consumer components
function Header() {
  const { theme, toggleTheme } = useContext(ThemeContext);
  const { user, logout } = useContext(UserContext);

  return (
    <header className={`header ${theme}`}>
      <h1>اپلیکیشن من</h1>
      <div>
        <button onClick={toggleTheme}>
          تغییر به حالت {theme === 'light' ? 'تاریک' : 'روشن'}
        </button>
        {user ? (
          <div>
            سلام، {user.name}!
            <button onClick={logout}>خروج</button>
          </div>
        ) : (
          <button>ورود</button>
        )}
      </div>
    </header>
  );
}

// App با provider ها
function App() {
  return (
    <ThemeProvider>
      <UserProvider>
        <Header />
        <main>
          {/* سایر component ها */}
        </main>
      </UserProvider>
    </ThemeProvider>
  );
}
```

## Portals: رندر خارج از درخت

Portal ها بهت اجازه میدن component ها رو خارج از سلسله مراتب DOM والدشون render کنی، مفیدن برای modal ها، tooltip ها، و dropdown ها.

```jsx
import { createPortal } from 'react-dom';
import { useState } from 'react';

function Modal({ isOpen, onClose, children }) {
  if (!isOpen) return null;

  return createPortal(
    <div className="modal-overlay" onClick={onClose}>
      <div className="modal-content" onClick={(e) => e.stopPropagation()}>
        <button className="modal-close" onClick={onClose}>
          ×
        </button>
        {children}
      </div>
    </div>,
    document.body // مستقیماً تو document.body رندر میشه
  );
}

function App() {
  const [isModalOpen, setIsModalOpen] = useState(false);

  return (
    <div className="app">
      <h1>اپلیکیشن من</h1>
      <button onClick={() => setIsModalOpen(true)}>
        باز کردن Modal
      </button>

      <Modal 
        isOpen={isModalOpen} 
        onClose={() => setIsModalOpen(false)}
      >
        <h2>محتوای Modal</h2>
        <p>این modal خارج از درخت DOM اپ رندر شده!</p>
      </Modal>
    </div>
  );
}
```

## Suspense: مدیریت حالت Loading

Suspense یه روش declarative برای مدیریت حالت‌های loading عملیات asynchronous ارائه میده.

```jsx
import { Suspense, lazy } from 'react';

// Component با lazy loading
const LazyComponent = lazy(() => import('./LazyComponent'));

function LoadingSpinner() {
  return (
    <div className="loading-spinner">
      <div className="spinner"></div>
      <p>در حال بارگذاری...</p>
    </div>
  );
}

function App() {
  return (
    <div>
      <h1>اپلیکیشن من</h1>
      
      <Suspense fallback={<LoadingSpinner />}>
        <LazyComponent />
      </Suspense>

      {/* Suspense میتونه data fetching رو هم مدیریت کنه */}
      <Suspense fallback={<div>بارگذاری اطلاعات کاربر...</div>}>
        <UserProfile userId="123" />
      </Suspense>
    </div>
  );
}
```

## Error Boundaries: مدیریت نرم خطاها

Error boundary ها خطاهای JavaScript رو در درخت component ها میگیرن و به جای crash کردن کل اپ، یه UI جایگزین نشان میدن.

```jsx
import { Component } from 'react';

class ErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    console.error('خطا توسط boundary گرفته شد:', error, errorInfo);
    // میتونی خطا رو به یه سرویس error reporting ارسال کنی
  }

  render() {
    if (this.state.hasError) {
      return (
        <div className="error-boundary">
          <h2>مشکلی پیش اومده!</h2>
          <p>متأسفیم از این مزاحمت. لطفاً صفحه رو refresh کنید.</p>
          <button onClick={() => this.setState({ hasError: false, error: null })}>
            دوباره امتحان کن
          </button>
        </div>
      );
    }

    return this.props.children;
  }
}

// Component که ممکنه خطا بده
function ProblematicComponent({ user }) {
  if (!user) {
    throw new Error('اطلاعات کاربر ضروریه');
  }

  return <div>سلام، {user.name}!</div>;
}

// استفاده
function App() {
  return (
    <ErrorBoundary>
      <ProblematicComponent user={null} />
    </ErrorBoundary>
  );
}
```

## نتیجه‌گیری

اکوسیستم مفاهیم React با هم کار میکنن تا اپلیکیشن‌های قدرتمند و قابل نگهداری بسازن. از component های پایه و JSX تا الگوهای پیشرفته مثل context و error boundaries، هر مفهوم هدف خاصی در ساخت رابط‌های کاربری مقاوم داره.

نکات کلیدی:

- **با component ها و props شروع کن** برای ساخت UI پایه
- **از state و effects استفاده کن** برای رفتار پویا  
- **Hook ها رو بکار ببر** برای منطق قابل استفاده مجدد
- **از context استفاده کن** برای state کل اپ
- **Error boundaries پیاده کن** برای مقاومت
- **Component ها رو خالص نگه دار** برای قابل پیش‌بینی بودن

وقتی که تو سفر React ادامه میدی، یادت باشه که تسلط از تمرین میاد. با component های ساده شروع کن و به تدریج الگوهای پیشرفته‌تر رو وقتی که اپلیکیشن‌هات پیچیده‌تر میشن اضافه کن.


