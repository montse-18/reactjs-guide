# فصل ۱: مروری بر جاوااسکریپت

## مقدمه
در این فصل، به مرور مفاهیم کلیدی جاوااسکریپت می‌پردازیم که برای یادگیری ری‌اکت ضروری هستند. اگر با جاوااسکریپت آشنا هستید، این فصل به شما کمک می‌کند تا دانش خود را تثبیت کنید. اگر تازه‌کار هستید، نگران نباشید؛ مفاهیم را به‌صورت ساده و با مثال‌های عملی توضیح می‌دهیم. هدف این فصل، ایجاد یک پایه محکم برای درک بهتر ری‌اکت و تایپ‌اسکریپت است.

## مفاهیم پایه جاوااسکریپت
### متغیرها
متغیرها برای ذخیره داده‌ها استفاده می‌شوند. در جاوااسکریپت، از `let`، `const` و `var` برای تعریف متغیرها استفاده می‌کنیم:
- `let`: برای متغیرهایی که مقدارشان ممکن است تغییر کند.
- `const`: برای متغیرهایی که مقدار ثابت دارند.
- `var`: روش قدیمی‌تر تعریف متغیر (کمتر توصیه می‌شود).

```javascript
let name = "علی"; // قابل تغییر
const age = 25; // غیرقابل تغییر
var score = 100; // روش قدیمی
name = "سارا"; // مجاز
// age = 26; // خطا: نمی‌توان مقدار const را تغییر داد
```

### توابع
توابع بلوک‌های کدی هستند که می‌توانند بارها استفاده شوند. می‌توان آن‌ها را به‌صورت معمولی یا به‌صورت Arrow Function تعریف کرد:
```javascript
// تابع معمولی
function greet(name) {
  return `سلام، ${name}!`;
}

// Arrow Function
const add = (a, b) => a + b;

console.log(greet("علی")); // خروجی: سلام، علی!
console.log(add(2, 3)); // خروجی: 5
```

### آرایه‌ها
آرایه‌ها برای ذخیره مجموعه‌ای از داده‌ها استفاده می‌شوند. می‌توانید از متدهایی مثل `map`، `filter` و `forEach` برای کار با آرایه‌ها استفاده کنید:
```javascript
const numbers = [1, 2, 3, 4];
const doubled = numbers.map(num => num * 2); // [2, 4, 6, 8]
const evens = numbers.filter(num => num % 2 === 0); // [2, 4]
numbers.forEach(num => console.log(num)); // چاپ 1, 2, 3, 4
```

### اشیاء
اشیاء برای ذخیره داده‌های ساختارمند استفاده می‌شوند:
```javascript
const person = {
  name: "علی",
  age: 25,
  greet: function() {
    return `سلام، من ${this.name} هستم!`;
  }
};
console.log(person.name); // خروجی: علی
console.log(person.greet()); // خروجی: سلام، من علی هستم!
```

## کار با DOM و رویدادها
جاوااسکریپت برای تعامل با صفحات وب از **DOM** (Document Object Model) استفاده می‌کند. می‌توانید عناصر HTML را انتخاب کرده و تغییر دهید:
```javascript
// انتخاب عنصر
const button = document.querySelector("#myButton");

// افزودن رویداد
button.addEventListener("click", () => {
  alert("دکمه کلیک شد!");
});
```

مثال: تغییر متن یک پاراگراف با کلیک روی دکمه:
```html
<button id="myButton">کلیک کن</button>
<p id="myParagraph">متن اولیه</p>

<script>
  const button = document.querySelector("#myButton");
  const paragraph = document.querySelector("#myParagraph");
  button.addEventListener("click", () => {
    paragraph.textContent = "متن تغییر کرد!";
  });
</script>
```

## مفاهیم پیشرفته‌تر
### کلوژرها (Closures)
کلوژرها توابعی هستند که به متغیرهای محیط بیرونی خود دسترسی دارند:
```javascript
function counter() {
  let count = 0;
  return function() {
    count++;
    return count;
  };
}
const myCounter = counter();
console.log(myCounter()); // 1
console.log(myCounter()); // 2
```

### Promise و Async/Await
برای مدیریت عملیات غیرهمزمان (مثل درخواست‌های API) از Promise و Async/Await استفاده می‌کنیم:
```javascript
// Promise
const fetchData = new Promise((resolve, reject) => {
  setTimeout(() => resolve("داده دریافت شد!"), 1000);
});

fetchData.then(data => console.log(data)); // خروجی بعد از 1 ثانیه: داده دریافت شد!

// Async/Await
async function getData() {
  const data = await fetchData;
  console.log(data);
}
getData();
```

## تمرین‌های عملی
1. **تمرین ۱**: یک آرایه از اعداد بسازید و با استفاده از `map`، تمام اعداد را به توان ۲ برسانید.
2. **تمرین ۲**: یک تابع بنویسید که نام و سن یک شخص را به‌عنوان ورودی بگیرد و یک شیء با این اطلاعات برگرداند.
3. **تمرین ۳**: یک دکمه و یک پاراگراف در HTML بسازید. با کلیک روی دکمه، رنگ متن پاراگراف تغییر کند.
4. **تمرین ۴**: یک تابع بنویسید که با استفاده از Promise، بعد از ۲ ثانیه یک پیام نمایش دهد.

## نتیجه‌گیری
در این فصل، مفاهیم پایه و پیشرفته جاوااسکریپت را مرور کردیم. این دانش برای درک ری‌اکت و کار با کامپوننت‌ها ضروری است. در فصل بعدی، به سراغ **Node.js** می‌رویم تا با محیط اجرایی جاوااسکریپت و ابزارهای توسعه آشنا شویم.