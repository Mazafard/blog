---
title: "معرفی Django AI Validator: وقتی Regex کافی نیست"
date: 2025-12-01T00:00:00+00:00
draft: false
tags: ["Django", "هوش مصنوعی", "اعتبارسنجی", "LLM"]
weight: -9
categories: ["برنامه‌نویسی"]
---

Regex برای ایمیل، کدپستی و رشته‌های مرتب عالیه، اما وقتی می‌شنوید «لطفاً مطمئن شو بیو حرفه‌ایه» یا «بررسی کن واقعاً دارد یک ماشین رو توصیف می‌کنه»، کل ماجرا فرو می‌ریزه. هرچقدر هم الگوی بیشتر بچسبونید، هنوز دارید نحو رو قضاوت می‌کنید و نه معنا رو.

**Django AI Validator** پاسخم به این شکاف معناییه؛ یک پکیج تازه PyPI که به فیلدهای Django اجازه می‌ده به LLMهای مدرن (OpenAI، Anthropic، Gemini یا حتی Ollama روی سیستم خودتون) وصل بشن و همون لحظه اعتبارسنجی و تمیزکاری انجام بدهند.

## چرا یک اعتبارسنجِ دیگه؟

چون کیفیت محتوا دیگه فقط «کاراکتر مجاز» نیست. باید لحن، نیت و نقض سیاست‌ها رو بدون ساختن سیستم تعدیل پیچیده تشخیص بدیم. به جای کنار هم چیدن فیلترهای کلمه کلیدی و هک‌های شکننده، می‌گید «خوب» یعنی چی و بقیه‌اش رو خود اعتبارسنج انجام می‌ده.

## این پکیج چی میاره؟

- **اعتبارسنجی معنایی:** `AISemanticValidator` پرامپت شما (مثلاً «آیا این توضیح محترمانه و مرتبط است؟») رو می‌خونه و متن رو ارزیابی می‌کنه.
- **تمیزکاری خودکار:** `AICleanedField` ورودی رو قبل از ذخیره بازنویسی می‌کنه یا حتی می‌ره سراغ حذف PII، اصلاح گرامر و نرمال‌سازی و لحن و والخ...
- **امکانات UX در ادمین:** داده‌های مشکوک داخل Django Admin علامت می‌خورند و اکشن‌های پاکسازی گروهی در دسترسه تا تیم محتوا در جریان بمونه.
- **پشتیبانی از پردازش غیرهمزمان:** فراخوانی‌های طولانی LLM رو می‌تونید به Celery بسپارید تا درخواست اصلی سریع تمام شه.

مستندات: [mazafard.github.io/Django-AI-Validator](https://mazafard.github.io/Django-AI-Validator/)

سورس کد: [github.com/Mazafard/Django-AI-Validator](https://github.com/Mazafard/Django-AI-Validator)

## تور معماری (بخش عاشقان جزئیات)

برای اینکه کتابخونه انعطاف‌پذیر و Provider-agnostic بمونه از الگوهای طراحی کلاسیک کمک گرفتم:

1. **Adapter Pattern:** اینترفیس `LLMAdapter` متدهای `validate` و `clean` رو تعریف می‌کنه و آداپترهای `OpenAIAdapter`، `AnthropicAdapter`، `GeminiAdapter` و `OllamaAdapter` درخواست‌ها رو مطابق API هر سرویس ترجمه می‌کنند.
2. **Abstract Factory:** وقتی به کلاس  `AIProviderFactory`  می‌گید «openai» یا «ollama»، بسته کامل آداپتر و تنظیمات صحیح رو می‌ده؛ خبری از `if provider == ...` های پخش و پلا نیست.
3. **Singleton Cache:** کلاس `LLMCacheManager` کش اشتراکی برای جفت پرامپت+متن نگه می‌دارد تا اعتبارسنجی تکراری هزینه توکن اضافه نداشته باشه.
4. **Proxy Pattern:** کلاس `CachingLLMProxy` قبل از تماس با آداپتر واقعی، کش رو چک می‌کنه، روی miss درخواست رو می‌فرسته و نتیجه رو ذخیره می‌کنه، بی‌آنکه شما تغییری بدید.
5. **Facade Pattern:** کلاس `AICleaningFacade` همه این پیچیدگی‌ها رو می‌پوشاند؛ Validator فقط `facade.validate()` یا `facade.clean()` رو صدا می‌زنه.
6. **Template Method:** کلاس `AISemanticValidator` روند ثابت `prepare_input → call_llm → parse_response → raise_validation_error` رو تعریف می‌کنه؛ می‌توانید گام‌های لازم رو override کنید و اسکلت رو دست‌نخورده نگه دارید.

## استفاده داخل مدل

```python
from django.db import models
from django_ai_validator.validators import AISemanticValidator
from django_ai_validator.fields import AICleanedField

class Product(models.Model):
    name = models.CharField(
        max_length=100,
        validators=[
            AISemanticValidator(
                prompt_template="Check if this name is catchy and marketing-friendly. Return VALID if yes."
            )
        ],
    )

    description = AICleanedField(
        cleaning_prompt="Fix grammar, remove profanity, and keep it professional.",
        use_async=True,
    )
```

پشت صحنه، Facade آداپتر مناسب رو انتخاب می‌کنه Proxy کش رو چک می‌کنه و Validator یا `ValidationError` یا خروجی تمیز شده رو برمی‌گردونه.

## شروع سریع

1. `pip install django-ai-validator`
2. کلیدهای OpenAI/Anthropic/Gemini/Ollama رو در تنظیمات بذارید
3. `AISemanticValidator` یا `AICleanedField` رو به فیلدهای مورد نظرتون اضافه کنین 

همین. به جای نوشتن صد خط Regex بروی حدس زدن محترمانه بودن یک متن، رفتار مطلوب رو توضیح بدهید و بذارید LLM حکم نهایی رو صادر کند. مشتاقم ببینم شما چه‌چیزهایی رو اعتبارسنجی می‌کنید!
