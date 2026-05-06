# 🇮🇷 Iranian Cities Database with Postal Codes & National ID Prefixes

یک مجموعه داده جامع از **شهرهای ایران** به همراه **محدوده کدپستی ۵ رقمی** و **پیشوند سه‌رقمی کد ملی (شماره شناسنامه)**. این پروژه برای استفاده در فرم‌های آدرس، اعتبارسنجی کد پستی، تکمیل خودکار (auto-complete)، و سیستم‌های ثبت‌نام طراحی شده است.

📌 **تعداد رکوردها:** بیش از ۱۵۰۰ شهر از تمام استان‌ها  
📌 **فرمت:** JSON  
📌 **پوشش:** ۳۱ استان، شامل شهرهای اصلی و روستا-شهرها  

---

## 📋 ساختار داده

هر رکورد شامل فیلدهای زیر است:

| فیلد | نوع | توضیح |
|------|------|-------|
| `province` | string | نام استان (مانند "آذربایجان شرقی") |
| `city` | string | نام شهر (مانند "تبریز") |
| `postal_code_start` | string/null | ابتدای محدوده کدپستی ۵ رقمی (در صورت موجود) |
| `postal_code_end` | string/null | انتهای محدوده کدپستی ۵ رقمی |
| `national_code_prefix` | string/null | سه رقم اول کد ملی (مرکز صدور شناسنامه) |

### نمونه رکورد

```json
{
  "province": "آذربایجان شرقی",
  "city": "تبریز",
  "postal_code_start": "51331",
  "postal_code_end": "51999",
  "national_code_prefix": "136"
}


📥 نحوه استفاده
۱. دریافت فایل JSON
می‌توانید فایل iran_cities.json را از همین مخزن دانلود کرده یا با استفاده از لینک زیر دریافت کنید:

bash
wget https://raw.githubusercontent.com/your-username/iran-cities-database/main/iran_cities.json
۲. استفاده در جاوااسکریپت (مثلاً با Select2)
javascript
fetch('iran_cities.json')
  .then(response => response.json())
  .then(data => {
    // استخراج لیست یکتای استان‌ها
    const provinces = [...new Map(data.map(item => [item.province, item.province])).values()];
    
    // فیلتر شهرها بر اساس استان انتخاب شده
    function getCitiesByProvince(provinceName) {
      return data.filter(item => item.province === provinceName);
    }
    
    // پر کردن دراپ‌داون استان
    const provinceSelect = document.getElementById('province');
    provinces.forEach(prov => {
      const option = document.createElement('option');
      option.value = prov;
      option.textContent = prov;
      provinceSelect.appendChild(option);
    });
    
    // پر کردن دراپ‌داون شهر هنگام تغییر استان
    provinceSelect.addEventListener('change', (e) => {
      const selectedProvince = e.target.value;
      const cities = getCitiesByProvince(selectedProvince);
      const citySelect = document.getElementById('city');
      citySelect.innerHTML = '';
      cities.forEach(city => {
        const option = document.createElement('option');
        option.value = city.city;
        option.textContent = city.city;
        citySelect.appendChild(option);
      });
    });
  });
۳. اعتبارسنجی کد پستی
با داشتن postal_code_start و postal_code_end می‌توانید بررسی کنید آیا کد پستی وارد شده در محدوده معتبر یک شهر قرار دارد:

javascript
function isValidPostalCode(cityData, postalCode) {
  if (!cityData.postal_code_start || !cityData.postal_code_end) return false;
  const start = parseInt(cityData.postal_code_start);
  const end = parseInt(cityData.postal_code_end);
  const code = parseInt(postalCode);
  return (code >= start && code <= end);
}
۴. دریافت خودکار استان و شهر از کد پستی
اگر کاربر ۵ رقم اول کد پستی را وارد کند، می‌توانید شهر و استان مربوطه را پیدا کنید:

javascript
function findCityByPostalCode(data, postalCode) {
  const code = postalCode.toString().slice(0,5);
  return data.find(item => 
    item.postal_code_start && 
    parseInt(code) >= parseInt(item.postal_code_start) && 
    parseInt(code) <= parseInt(item.postal_code_end)
  );
}
۵. استفاده در پایتون
python
import json

with open('iran_cities.json', encoding='utf-8') as f:
    data = json.load(f)

# دریافت استان‌های یکتا
provinces = set(item['province'] for item in data)

# فیلتر شهرهای یک استان
def get_cities(province):
    return [item['city'] for item in data if item['province'] == province]
📊 آمار
استان	تعداد شهرها
آذربایجان شرقی	۶۴
آذربایجان غربی	۴۵
اصفهان	۸۹
اردبیل	۲۳
البرز	۱۷
ایلام	۲۷
بوشهر	۳۲
تهران	۴۲
چهارمحال و بختیاری	۳۸
خراسان جنوبی	۲۹
خراسان رضوی	۷۵
خراسان شمالی	۲۳
خوزستان	۶۴
زنجان	۲۶
سمنان	۲۲
سیستان و بلوچستان	۴۳
فارس	۸۴
قزوین	۲۷
قم	۶
کردستان	۳۳
کرمان	۴۸
کرمانشاه	۳۴
کهگیلویه و بویراحمد	۱۸
گلستان	۳۲
گیلان	۴۹
لرستان	۳۱
مازندران	۵۶
مرکزی	۳۴
هرمزگان	۳۵
همدان	۲۹
یزد	۲۲
مجموع	۱۵۳۲
جدول کامل به‌روزرسانی شده در فایل statistics.md موجود است.

🗂 منابع داده
لیست شهرها: استخراج شده از پایگاه داده رسمی تقسیمات کشوری ایران (۱۳۹۹-۱۴۰۲)

کدپستی‌ها: برگرفته از جداول پستی شرکت ملی پست جمهوری اسلامی ایران

پیشوند کد ملی: برگرفته از جداول سازمان ثبت احوال کشور (مراکز صدور شناسنامه)

اطلاعات با دقت دستی و نیمه‌خودکار تجمیع شده و در حد امکان به‌روز است. در صورت مشاهده هرگونه نقص یا خطا، لطفاً از طریق Issues اطلاع دهید.

🤝 مشارکت
این پروژه به کمک جامعه تکمیل می‌شود. اگر:

شهر یا کدپستی جدیدی یافتید که در فایل نیست

اطلاعاتی نادرست مشاهده کردید

می‌خواهید داده‌های استان خاصی را بهبود دهید

پیشنهادی برای بهبود ساختار یا مستندات دارید

لطفاً یک Issue باز کنید یا Pull Request ارسال نمایید.

نکات مشارکت:
فایل iran_cities.json را مستقیماً ویرایش نکنید؛ بهتر است اسکریپت تولید (در صورت وجود) را اصلاح کنید. در غیر این صورت، تغییرات را به صورت دستی و با دقت وارد کنید.

در صورت افزودن شهر جدید، حتماً استان، شهر و (در صورت وجود) کدپستی و کدملی را ذکر کنید.

از فرمت UTF-8 استفاده کنید و کاراکترهای فارسی را حفظ نمایید.

برای اطمینان از صحت کدپستی، محدوده شروع و پایان را بر اساس مستندات پست ایران وارد کنید.

پیشوند کد ملی باید سه رقم باشد (می‌تواند با صفر شروع شود، مانند 037 برای قم).

در Pull Request، حتماً توضیح دهید که بر چه اساس داده‌ها را اضافه یا اصلاح کرده‌اید.

📜 مجوز
این پروژه تحت مجوز MIT منتشر شده است. استفاده تجاری و غیرتجاری آزاد است؛ فقط کافی است ارجاعی به این مخزن دهید.

text
MIT License

Copyright (c) 2026 [Your Name]

Permission is hereby granted, free of charge, to any person obtaining a copy
...
⭐ قدردانی
اگر این پروژه برای شما مفید بوده، یک ⭐ فراموش نکنید.
همچنین از تمام افرادی که با ارسال داده‌های صحیح به بهبود این منبع کمک کردند، سپاسگزاریم.

