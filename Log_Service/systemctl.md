برای مشاهده سرویس‌های فعال (Active) در سیستم عامل دبیان با استفاده از `systemctl`، می‌توانی از دستور زیر استفاده کنی:

```bash
systemctl list-units --type=service --state=active
```

🔎 **توضیحات:**  
- `list-units` → لیست تمام یونیت‌ها (واحدها)  
- `--type=service` → فقط سرویس‌ها را نمایش می‌دهد  
- `--state=active` → فقط سرویس‌های فعال را نمایش می‌دهد  

### ✅ **ذخیره خروجی در فایل**  
اگر بخواهی خروجی دستور را در یک فایل ذخیره کنی (مثلاً `active_services.txt`)، از این دستور استفاده کن:

```bash
systemctl list-units --type=service --state=active > ~/active_services.txt
```

🔎 **توضیحات:**  
- `>` → خروجی را در فایل مشخص‌شده ذخیره می‌کند (اگر فایل موجود باشد، آن را بازنویسی می‌کند)  
- `>>` → خروجی را به انتهای فایل اضافه می‌کند (بدون حذف محتوای قبلی)  

### 💡 **نمونه:**  
اگر بخواهی خروجی به فایل اضافه شود (بدون پاک کردن محتوای قبلی)، از `>>` استفاده کن:

```bash
systemctl list-units --type=service --state=active >> ~/active_services.txt
```





---

## 🚀 **سرویس‌های حیاتی سیستم**
1. **systemd-journald.service** → لاگ‌های سیستم (System Logs) رو جمع‌آوری و مدیریت می‌کنه.  
2. **systemd-logind.service** → مدیریت نشست‌های کاربر (Login) رو انجام می‌ده.  
3. **systemd-timesyncd.service** → ساعت سیستم رو با سرور NTP همگام می‌کنه.  
4. **systemd-udevd.service** → مدیریت دستگاه‌ها (Devices) رو برعهده داره.  
5. **systemd-fsck@*** → بررسی سیستم فایل‌ها هنگام بوت شدن سیستم.  

---

## 🌐 **سرویس‌های شبکه**
1. **NetworkManager.service** → مدیریت اتصالات شبکه (Wi-Fi، Ethernet)  
2. **dnsmasq.service** → سرویس سبک DNS و DHCP (برای کش کردن DNS و تخصیص IP)  
3. **inetd.service** → اینترنت سوپرسرور (برای مدیریت سرویس‌های شبکه)  
4. **hostapd.service** → ایجاد نقطه دسترسی (Access Point) برای Wi-Fi  
5. **ssh.service** → سرور OpenSSH برای دسترسی ریموت  
6. **wpa_supplicant.service** → مدیریت ارتباط Wi-Fi و پروتکل WPA  

---

## 🖥️ **سرویس‌های مرتبط با سخت‌افزار و هسته**
1. **apparmor.service** → امنیت AppArmor (MAC)  
2. **kmod-static-nodes.service** → بارگذاری ماژول‌های هسته (Kernel Modules)  
3. **systemd-modules-load.service** → بارگذاری ماژول‌های هسته در زمان بوت  
4. **systemd-random-seed.service** → مدیریت اعداد تصادفی (برای رمزنگاری)  

---

## 📁 **سرویس‌های ذخیره‌سازی و فایل سیستم**
1. **nfs-server.service** → سرور NFS (برای اشتراک‌گذاری فایل)  
2. **nfs-mountd.service** → مدیریت درخواست‌های Mount در NFS  
3. **rpcbind.service** → نگاشت پورت‌ها برای NFS و سایر سرویس‌های RPC  
4. **smbd.service** → سرور SMB (برای اشتراک‌گذاری فایل با ویندوز)  
5. **nmbd.service** → مدیریت نام‌های SMB در شبکه  

---

## 🛡️ **سرویس‌های امنیتی**
1. **polkit.service** → مدیریت سطوح دسترسی  
2. **ModemManager.service** → مدیریت مودم‌ها  
3. **apparmor.service** → مدیریت پروفایل‌های امنیتی AppArmor  

---

## 📡 **سرویس‌های NFS**
1. **nfs-idmapd.service** → نگاشت ID کاربران و گروه‌ها در NFS  
2. **nfsdcld.service** → مدیریت کلاینت‌های NFSv4  

---

## 📌 **سرویس‌های مدیریت کاربران**
1. **user-runtime-dir@0.service** → ایجاد دایرکتوری کاربر  
2. **user@0.service** → مدیریت فرآیندهای مربوط به UID 0 (Root)  

---

## 🌍 **سرویس‌های مدیریت سیستم**
1. **systemd-remount-fs.service** → Remount کردن سیستم فایل هنگام بوت  
2. **systemd-sysctl.service** → اعمال تنظیمات مربوط به کرنل  

---
