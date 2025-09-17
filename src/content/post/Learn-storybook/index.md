---
title: "آموزش  React Storybook "
description: "راهنمای جامع  برای راه‌اندازی، ساخت story و بهترین‌‌های کار با Storybook در پروژه‌های React"
publishDate: "17 Sep 2023"
updatedDate: "03 May 2025"
tags: ["react", "storybook", "component", "testing"]
---



# آموزش کامل React Storybook: ساخت component های بهتر با documentation و testing

وقتی صحبت از develop کردن، test کردن و documentation کردن component ها و design system ها میشه، Storybook واقعاً ابزار فوق العاده‌ایه. با تقریباً هر front-end library که فکرشو بکنی کار می‌کنه، و تو این راهنمای کامل قراره یاد بگیریم چطور از Storybook مخصوص React استفاده کنیم.

## Storybook چیه؟

Storybook یه open-source tool هست که بهتون اجازه میده component های UI رو به صورت جدا develop کنید. یه محیط تمیز و interactive برامون فراهم می‌کنه که میتونیم:

- Component ها رو مستقل از اپلیکیشن اصلی develop کنیم
- حالت‌های مختلف component ها رو test کنیم
- Documentation خودکار تولید کنیم  
- محیط interactive برای کار با property های component بسازیم
- Component ها رو با designer ها و stakeholder ها به اشتراک بذاریم

## راه‌اندازی پروژه

قبل از اینکه وارد Storybook بشیم، بیاید با یه React app ساده شروع کنیم. برای این آموزش، با دو تا component ساده کار می‌کنیم:

### Button Component

```jsx
// components/Button.js
import React from 'react';
import PropTypes from 'prop-types';

const Button = ({ size = 'medium', backgroundColor = 'blue', label, onClick }) => {
  const mode = backgroundColor;
  return (
    <button
      type="button"
      className={`button ${size}`}
      style={{ backgroundColor }}
      onClick={onClick}
    >
      {label}
    </button>
  );
};

Button.propTypes = {
  size: PropTypes.oneOf(['small', 'medium', 'large']),
  backgroundColor: PropTypes.string,
  label: PropTypes.string.isRequired,
  onClick: PropTypes.func,
};

export default Button;
```

### Stack Component

```jsx
// components/Stack.js
import React from 'react';
import PropTypes from 'prop-types';

const Stack = ({ children, spacing = 1, direction = 'row', wrap = false }) => {
  const styles = {
    display: 'flex',
    flexDirection: direction,
    flexWrap: wrap ? 'wrap' : 'nowrap',
    gap: `${spacing * 8}px`, // 8px base unit
  };

  return <div style={styles}>{children}</div>;
};

Stack.propTypes = {
  children: PropTypes.node,
  spacing: PropTypes.number,
  direction: PropTypes.oneOf(['row', 'column']),
  wrap: PropTypes.bool,
};

export default Stack;
```

## نصب و راه‌اندازی Storybook

برای شروع کار با Storybook، باید بعد از ساختن React app تون، اون رو initialize کنید:

```bash
npx sb init
```

**نکته مهم:** حتماً Storybook initializer رو بعد از ساختن اپلیکیشن‌تون اجرا کنید، نه قبلش. Storybook باید project type شما رو تشخیص بده تا درست configure بشه.

این دستور کارهای زیر رو انجام میده:
- تمام dependency های لازم Storybook رو دانلود می‌کنه
- script های Storybook رو به `package.json` اضافه می‌کنه
- پوشه `.storybook` رو با فایل‌های config می‌سازه
- پوشه `stories` رو با story های نمونه تولید می‌کنه

### ساختار پروژه بعد از نصب

بعد از initialization، این موارد جدید رو خواهید دید:

```
your-project/
├── .storybook/
│   ├── main.js      # تنظیمات اصلی
│   └── preview.js   # تنظیمات global و decorator ها
├── stories/         # تمام story هاتون اینجا میان
│   ├── Button.stories.js
│   ├── Header.stories.js
│   ├── Page.stories.js
│   └── Introduction.stories.mdx
└── package.json     # حالا شامل script های storybook میشه
```

## اجرای Storybook

Storybook رو با این دستور اجرا کنید:

```bash
npm run storybook
```

این کار Storybook رو تو مرورگر باز می‌کنه، معمولاً تو آدرس `http://localhost:6006`، که میتونید با component های نمونه که فراهم شده تعامل کنید.

## ساختن اولین Story

بیاید story های نمونه رو پاک کنیم و خودمون story بسازیم. همه چیز تو پوشه `stories` رو پاک کنید و یه فایل جدید بسازید:

### Story ساده برای Button

```javascript
// stories/Button.stories.js
import React from 'react';
import Button from '../components/Button';

// Default export تنظیمات story رو تعریف می‌کنه
export default {
  title: 'Components/Button',
  component: Button,
};

// Template function برای استفاده مجدد
const Template = (args) => <Button {...args} />;

// Story های جداگانه
export const Red = Template.bind({});
Red.args = {
  backgroundColor: 'red',
  label: 'Press me',
  size: 'medium',
};

export const Green = Template.bind({});
Green.args = {
  backgroundColor: 'green',
  label: 'Press me',
  size: 'medium',
};

export const Small = Template.bind({});
Small.args = {
  backgroundColor: 'red',
  label: 'Press me',
  size: 'small',
};

export const Large = Template.bind({});
Large.args = {
  backgroundColor: 'red',
  label: 'Press me',
  size: 'large',
};

export const LongLabel = Template.bind({});
LongLabel.args = {
  backgroundColor: 'red',
  label: 'این یه لیبل خیلی طولانی برای دکمه است که نشون میده متن چطور wrap میشه',
  size: 'medium',
};
```

## فهمیدن ساختار Story

### Default Export
Default export metadata رو برای تمام story های فایل تعریف می‌کنه:
- `title`: Story ها رو تو sidebar سازماندهی می‌کنه (از `/` برای پوشه استفاده کنید)
- `component`: Component React که داره document میشه

### Template Pattern
Template pattern بهتون اجازه میده ساختار یکسان component رو تو story های مختلف استفاده کنید:

```javascript
const Template = (args) => <ComponentName {...args} />;
```

### Story های جداگانه
هر named export یه story می‌سازه. از method `bind({})` برای کپی کردن template استفاده کنید و `args` مخصوص هر variation رو تعریف کنید.

## Documentation خودکار

Storybook به طور خودکار documentation تولید می‌کنه براساس:

1. **PropTypes**: اگه PropTypes تعریف کنید، Storybook control های interactive می‌سازه
2. **Component props**: property های موجود و type هاشون رو نشون میده
3. **Default values**: تو control panel نمایش داده میشن

به همین دلیله که تعریف PropTypes برای تجربه خوب Storybook خیلی مهمه.

## کار با Action ها و Event ها

### تشخیص خودکار Action

Storybook به طور خودکار prop هایی مثل `onClick`, `onSubmit` و غیره رو تشخیص میده و توی Actions panel لاگ می‌کنه.

### Action Handler های سفارشی

برای prop هایی با نام های متفاوت، تویه `argTypes` تعریفشون کنید:

```javascript
export default {
  title: 'Components/Button',
  component: Button,
  argTypes: {
    handleClick: { action: 'handle click' },
  },
};
```

## Story پیشرفته: Stack Component

Stack component نشون میده چطور story های پیچیده‌تر بسازیم:

```javascript
// stories/Stack.stories.js
import React from 'react';
import Stack from '../components/Stack';

export default {
  title: 'Components/Stack',
  component: Stack,
  argTypes: {
    numberOfChildren: {
      type: 'number',
      defaultValue: 4,
    },
  },
};

const Template = ({ numberOfChildren, ...args }) => (
  <Stack {...args}>
    {[...Array(numberOfChildren).keys()].map((n) => (
      <div
        key={n}
        style={{
          width: '50px',
          height: '50px',
          backgroundColor: 'red',
          display: 'flex',
          justifyContent: 'center',
          alignItems: 'center',
          color: 'white',
          fontWeight: 'bold',
        }}
      >
        {n + 1}
      </div>
    ))}
  </Stack>
);

export const Horizontal = Template.bind({});
Horizontal.args = {
  direction: 'row',
  spacing: 2,
  wrap: false,
};

export const Vertical = Template.bind({});
Vertical.args = {
  direction: 'column',
  spacing: 2,
  wrap: false,
};

export const NoSpacing = Template.bind({});
NoSpacing.args = {
  direction: 'row',
  spacing: 0,
  wrap: false,
};

export const WrapOverflow = Template.bind({});
WrapOverflow.args = {
  direction: 'row',
  spacing: 2,
  wrap: true,
  numberOfChildren: 40,
};

export const Empty = Template.bind({});
Empty.args = {
  direction: 'row',
  spacing: 2,
  wrap: false,
  numberOfChildren: 0,
};
```

## توضیح مفاهیم کلیدی

### Argument های سفارشی با argTypes

argument `numberOfChildren` یه prop از Stack component نیست—این یه control مخصوص Storybook هست. تویه `argTypes` تعریفش می‌کنیم و بعد تو template استخراجش می‌کنیم:

```javascript
const Template = ({ numberOfChildren, ...args }) => {
  // از numberOfChildren برای ساختن child element ها استفاده می‌کنیم
  // بقیه arg ها رو به component اصلی پاس میدیم
  return <Stack {...args}>{/* generated children */}</Stack>;
};
```

### تولید Dynamic Children

ساختن child های dynamic برای test:

```javascript
{[...Array(numberOfChildren).keys()].map((n) => (
  <ChildComponent key={n} />
))}
```

این pattern یه array با طول مشخص می‌سازه و روی index ها map می‌کنه.

## Best Practice ها

### ۱. سازماندهی با پوشه‌ها
از forward slash تو title ها برای ساختن ساختار پوشه استفاده کنید:

```javascript
export default {
  title: 'Components/Forms/Button', // پوشه‌های تو در تو می‌سازه
  component: Button,
};
```

### ۲. از PropTypes یا TypeScript استفاده کنید
prop type های خوب تعریف شده، تولید خودکار control ها و documentation بهتر رو ممکن می‌کنن.

### ۳. variation های مختلف بسازید
state ها، سایزها و تنظیمات مختلف رو نشون بدید:
- حالت پیش‌فرض
- حالت loading
- حالت خطا
- سایزهای مختلف
- theme های مختلف

### ۴. Documentation اضافه کنید
از فایل‌های MDX برای documentation غنی استفاده کنید:

```mdx
<!-- Button.stories.mdx -->
import { Meta, Story, Canvas } from '@storybook/addon-docs';
import Button from '../components/Button';

<Meta title="Components/Button" component={Button} />

# Button Component

Button component برای تعاملات کاربر استفاده میشه.

## نحوه استفاده

<Canvas>
  <Story name="Default">
    <Button label="Click me" />
  </Story>
</Canvas>
```

### ۵. Edge Case ها رو test کنید
Story بسازید برای:
- حالت‌های خالی
- محتوای طولانی
- سناریوهای overflow
- شرایط خطا

## تنظیمات و سفارشی‌سازی

### Style های Global

Style های global رو تو `.storybook/preview.js` اضافه کنید:

```javascript
// .storybook/preview.js
import '../src/index.css'; // Style های global شما

export const parameters = {
  backgrounds: {
    default: 'light',
    values: [
      { name: 'light', value: '#ffffff' },
      { name: 'dark', value: '#333333' },
    ],
  },
};
```

### تنظیمات سفارشی Webpack

`.storybook/main.js` رو برای تنظیمات webpack سفارشی ویرایش کنید:

```javascript
// .storybook/main.js
module.exports = {
  stories: ['../src/**/*.stories.@(js|jsx|ts|tsx|mdx)'],
  addons: [
    '@storybook/addon-docs',
    '@storybook/addon-controls',
    '@storybook/addon-actions',
  ],
  webpackFinal: async (config) => {
    // تغییرات سفارشی webpack
    return config;
  },
};
```

## مزایای استفاده از Storybook

### برای Developer ها
- **Isolated Development**: Component ها رو بدون اجرای کل اپلیکیشن بسازید
- **Visual Testing**: regression های UI رو راحت تشخیص بدید
- **Component Reusability**: طراحی modular رو پیاده می‌کنه
- **Documentation**: خودکار تولید میشه و همیشه به‌روز هست

### برای تیم‌ها
- **Design System**: مخزن مرکزی برای تمام UI component ها
- **Collaboration**: designer ها و developer ها میتونن با هم component ها رو بررسی کنن
- **Quality Assurance**: state های مختلف component رو راحت test کنید
- **Onboarding**: اعضای جدید تیم میتونن component ها رو سریع درک کنن

### برای Stakeholder ها
- **Visual Review**: component ها رو بدون راه‌اندازی فنی ببینن
- **Interactive Demo**: رفتار component ها رو test کنن
- **Documentation**: قابلیت‌ها و محدودیت‌های component ها رو بفهمن

## Deploy و اشتراک‌گذاری

یه Storybook static برای deploy بسازید:

```bash
npm run build-storybook
```

این کار یه پوشه `storybook-static` می‌سازه که میتونید روی هر static hosting service مثل Netlify، Vercel یا GitHub Pages قرارش بدید.

## نتیجه‌گیری

Storybook طرز develop، test و document کردن component های React رو کاملاً تغییر میده. با فراهم کردن یه محیط جدا برای component development، طراحی بهتر component ها رو تشویق می‌کنه، همکاری تیم رو بهبود میده و documentation زنده‌ای می‌سازه که همیشه با codebase شما sync هست.

کلید موفقیت با Storybook اینه که ساده شروع کنید و به تدریج story های پیچیده‌تر اضافه کنید همونطور که component library تون رشد می‌کنه. با variation های ساده component شروع کنید، بعد edge case ها، state های مختلف و interaction های پیچیده رو پوشش بدید.

چه یه اپلیکیشن کوچک بسازید یا یه design system بزرگ، Storybook ابزارهایی رو فراهم می‌کنه که برای ساختن UI component های قوی نیاز دارید.
