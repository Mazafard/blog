---
title: "TermForge: نوسازی محیط کاری ترمینال من"
date: 2025-11-27T00:00:00+00:00
draft: false
tags: ["ترمینال", "Ansible", "DevOps", "macOS", "Kubernetes", "Neovim"]
categories: ["ابزارها", "DevOps"]
---

من همیشه پروژه اصلی [jazik/termenv](https://github.com/jazik/termenv) رو دوست داشتم — یه Ansible playbook تمیزه که یه محیط ترمینال عالی رو براتون آماده (bootstrap) می‌کنه. ولی بعد از یه مدت زندگی کردن باهاش، دیدم دلم پشتیبانی بهتر از macOS، نصب خودکار iTerm2 و یه سری ابزارهای مخصوص Kubernetes می‌خواد. تصمیم گرفتم پروژه رو فورک (fork) کنم و اون تیکه‌های اضافی رو بسازم. نتیجه شد **TermForge**، برداشت تازه من از ایده «ترمینال آماده در جعبه».

## چرا فورک به جای PR؟

اولش سعی کردم تغییرات رو کم‌کم به مخزن اصلی (upstream) اضافه کنم. ولی خیلی زود تغییرات مثل گلوله برف بزرگ شد — پلی‌بوک‌های جدید، تغییر نام اختیاری، نقش‌های (roles) مخصوص macOS، بازنویسی مستندات و ابزارهای کمکی کوبرنتیز که چندین فایل رو تغییر می‌دادن. به جای اینکه ساختار پروژه اصلی رو بهم بریزم، فورکش کردم و با حفظ اعتبار کار اصلی، یه هویت جدید به پروژه دادم.

---

## ویژگی‌های برجسته

### ۱. استک شل کراس-پلتفرم (Cross-Platform Shell Stack)
*   **لینوکس**: همچنان از پکیج منیجرهای بومی با `sudo` استفاده می‌کنه.
*   **مک (macOS)**: حالا برای `zsh`، `tmux`، فونت‌ها، Node و iTerm2 به Homebrew/Homebrew Cask تکیه می‌کنه.
*   **تشخیص خودکار zsh**: مسیر `zsh` نصب شده (`/opt/homebrew/bin/zsh` روی Apple Silicon) رو قبل از اینکه اون رو شل پیش‌فرض کنه پیدا می‌کنه و به `/etc/shells` اضافه می‌کنه.
*   **dircolors روی macOS**: ترکیب Homebrew `coreutils` + یه بلوک alias تضمین می‌کنه که `dircolors` کار کنه، با اینکه macOS خودش اون رو نداره.

**امتحانش کنید:**
```bash
ansible-playbook -i hosts --ask-become-pass termforge.yml --tags "zsh,zsh-dircolors-solarized"
```

### ۲. آماده‌سازی iTerm2 (مخصوص macOS)
*   اگه iTerm2 نصب نباشه، اون رو از طریق Homebrew Cask نصب می‌کنه.
*   اگه `/Applications/iTerm.app` وجود داشته باشه، خودکار رد میشه (اگه دستی نصب کردید ارور نمیده).

**فقط اجرای همین نقش:**
```bash
ansible-playbook -i hosts --ask-become-pass termforge.yml --tags iterm2
```

### ۳. فونت‌ها و ظاهر (UI Polish)
*   دانلودر Nerd Fonts به صورت پیش‌فرض Fira Mono (یا هر آرشیوی که بگید) رو نصب می‌کنه.
*   روی macOS، فونت‌ها میرن توی `~/Library/Fonts/<Font>`، پس بدون نیاز به `fc-cache` فوراً ظاهر میشن.
*   دستورالعمل‌های بعد از نصب iTerm2 توی README توضیح میده چطور Nerd Font رو انتخاب کنید، ligatures رو فعال کنید یا iTerm2 رو به پوشه sync وصل کنید.

### ۴. استک Neovim (Lua, Telescope, Treesitter, Copilot Optional)
*   نصب Neovim به همراه ripgrep برای Telescope.
*   کپی کردن یه کانفیگ Lua آماده (keymaps, plugins, LSP).
*   پلی‌بوک اختیاری `neovim-copilot.yml` که Copilot/CopilotChat رو بعداً نصب می‌کنه.
*   نقش CSpell که Node/npm رو چک می‌کنه، `cspell` رو نصب می‌کنه، کانفیگ رو میذاره و کلیدهای میانبر برای عیب‌یابی رو اضافه می‌کنه.

**نصب Copilot در مرحله بعد:**
```bash
ansible-playbook -i hosts neovim-copilot.yml
```

### ۵. ابزارهای کمکی کوبرنتیز (Kubernetes Helpers)
*   نقش جدید `kubernetes-tools` چک می‌کنه که آیا `kubectl` توی `PATH` شما هست یا نه.
*   اگه بود:
    *   **macOS**: ابزارهای `k9s` و `kubectx` رو با Homebrew نصب می‌کنه (kubectx خودش `kubens` رو داره).
    *   **Linux**: فایل باینری k9s رو دانلود و توی `/usr/local/bin` نصب می‌کنه، مخزن kubectx رو کلون می‌کنه و برای `kubectx` و `kubens` لینک نمادین (symlink) می‌سازه.
*   اگه `kubectl` نباشه، کل این نقش بی سروصدا رد میشه — تا وقتی واقعاً با کلاسترها کار نکنید، سیستم شلوغ نمیشه.

**اجباری کردن فقط نقش کمکی:**
```bash
ansible-playbook -i hosts termforge.yml --tags k8s-tools
```

### ۶. بهبودهای کیفیت زندگی (Quality-of-Life Tweaks)
*   نصب `fzf` شامل اتصال پلاگین oh-my-zsh و تنظیمات پیش‌فرض (`--height 40% --layout=reverse --border`) میشه.
*   نقش `tmux` تم رو نصب می‌کنه، `base-index 1` رو تنظیم می‌کنه و کلیدهای ناوبری هوشمند Vim/Tmux رو اضافه می‌کنه.
*   فایل README حالا اینا رو مستند کرده:
    *   پیش‌نیازهای خاص macOS (نصب Homebrew، هشدار مفسر، نحوه تنظیم `ANSIBLE_PYTHON_INTERPRETER`).
    *   استفاده از تگ‌ها و ترکیب نقش‌ها.
    *   تست‌های Docker + Vagrant با دستورالعمل‌های به‌روز شده برای `termforge.yml`.
    *   یه برگه تقلب (cheat sheet) به‌روز شده برای Neovim و اسکرین‌شات‌ها.

---

## مرور نحوه استفاده

### کلون و اجرا (Clone + Run)
```bash
git clone https://github.com/<your-username>/termforge.git
cd termforge
ansible-playbook -i hosts --ask-become-pass termforge.yml
```
*   فایل `termenv.yml` هنوز هست ولی الان فقط `termforge.yml` رو ایمپورت می‌کنه، پس اسکریپت‌های قدیمی کار می‌کنن.

### شخصی‌سازی با تگ‌ها
مثال‌ها:
```bash
ansible-playbook -i hosts termforge.yml --skip-tags iterm2
ansible-playbook -i hosts termforge.yml --tags "neovim,k8s-tools"
```

### نصب فونت‌ها
```bash
ansible-playbook -i hosts nerdfonts.yml -e "font_name=Hack"
```
بعدش پروفایل ترمینال‌تون رو به فونت جدید تغییر بدید (دستورالعمل‌های iTerm2 توی README هست).

---

## جمع‌بندی

فورک کردن بهم اجازه داد سریع پیش برم: تغییر نام پروژه، مرتب‌سازی داکیومنت‌ها و اضافه کردن تیکه‌های خاص macOS/Kubernetes بدون اینکه بخوام تغییرات سلیقه‌ای بزرگ رو به مخزن اصلی تحمیل کنم. اگه دنبال اینایید:

*   یه بوت‌استرپ ترمینال کراس-پلتفرم،
*   نصب خودکار iTerm2،
*   نئوویم (Neovim) آماده برای Lua/LSP/Copilot،
*   تنظیمات پیش‌فرض tmux/fzf/oh-my-zsh،
*   کار کردن Nerd Fonts و dircolors روی macOS،
*   ابزارهای کمکی کوبرنتیز که فقط وقتی `kubectl` دارید ظاهر میشن،

...پس TermForge ارزش امتحان کردن رو داره.

کدها روی گیت‌هاب هست، README تمام جزئیات رو پوشش میده و می‌تونید پلی‌بوک رو تیکه تیکه یا یکجا اجرا کنید. Happy forging!
