### **مدیریت Snapshot، Import، و Restore در ZFS + خطاهای رایج**  

ZFS قابلیت‌های قدرتمندی برای گرفتن اسنپ‌شات (Snapshot)، ایمپورت و بازگردانی داده‌ها دارد که برای بازیابی اطلاعات در مواقع ضروری استفاده می‌شود. در ادامه این قابلیت‌ها و خطاهای رایج توضیح داده شده‌اند.

---

## **1. ایجاد Snapshot (ذخیره لحظه‌ای داده‌ها)**  
اسنپ‌شات در ZFS یک نسخه فقط‌خواندنی از وضعیت فعلی فایل‌سیستم است که می‌توان آن را در آینده بازیابی کرد.

🔹 **ایجاد یک Snapshot ساده:**  
```bash
sudo zfs snapshot mypool/mydataset@snap1
```
این دستور یک اسنپ‌شات به نام `snap1` از مجموعه داده `mypool/mydataset` ایجاد می‌کند.

🔹 **بررسی اسنپ‌شات‌های موجود:**  
```bash
zfs list -t snapshot
```

🔹 **حذف یک Snapshot خاص:**  
```bash
sudo zfs destroy mypool/mydataset@snap1
```

🔹 **حذف تمام Snapshotهای یک پول خاص:**  
```bash
sudo zfs destroy -r mypool/mydataset@snap1
```

---

## **2. بازگردانی از Snapshot (Revert to Snapshot)**  
اگر داده‌ها را از دست داده‌اید یا تغییرات نامطلوبی رخ داده، می‌توانید به یک اسنپ‌شات قبلی بازگردید.

🔹 **برگرداندن یک اسنپ‌شات (همه تغییرات جدید حذف می‌شود!):**  
```bash
sudo zfs rollback mypool/mydataset@snap1
```
⛔ **هشدار:** این کار **تمام تغییرات پس از snapshot** را حذف می‌کند.

🔹 **اگر بعد از snapshot، اسنپ‌شات‌های دیگری هم ایجاد شده‌اند، باید از `-r` استفاده کنید:**  
```bash
sudo zfs rollback -r mypool/mydataset@snap1
```

---

## **3. Export و Import کردن یک Pool (انتقال پول بین سیستم‌ها)**  
اگر بخواهید یک پول ZFS را به سیستم دیگری منتقل کنید، باید از `export` و `import` استفاده کنید.

🔹 **Export کردن یک Pool (قطع ارتباط از سیستم):**  
```bash
sudo zpool export mypool
```
بعد از این کار، سیستم دیگر این پول را نمی‌شناسد و می‌توان آن را روی سیستم دیگری متصل کرد.

🔹 **Import کردن یک Pool در سیستم جدید:**  
```bash
sudo zpool import mypool
```
اگر ZFS پول را به‌طور خودکار پیدا نکند، می‌توانید از گزینه `-d` برای مشخص کردن مسیر دیسک‌ها استفاده کنید:
```bash
sudo zpool import -d /dev/disk/by-id mypool
```

🔹 **مشاهده لیست پول‌های Export شده (بدون Import کردن):**  
```bash
sudo zpool import
```

🔹 **Import یک پول با نام جدید (برای جلوگیری از تداخل با یک پول همنام):**  
```bash
sudo zpool import mypool newpool
```

🔹 **اگر خطای "Pool is already in use" دریافت کردید:**  
این مشکل زمانی رخ می‌دهد که ZFS فکر می‌کند پول در حال استفاده است. می‌توان با اجبار آن را ایمپورت کرد:
```bash
sudo zpool import -f mypool
```

---

## **4. ارسال و دریافت Snapshotها (Backup & Restore روی سیستم دیگر)**  
اگر می‌خواهید یک اسنپ‌شات را به سیستم دیگری منتقل کنید، از `zfs send` و `zfs receive` استفاده کنید.

🔹 **ارسال یک Snapshot به فایل (برای بکاپ‌گیری):**  
```bash
sudo zfs send mypool/mydataset@snap1 > /mnt/backup/snap1.img
```

🔹 **دریافت و بازگردانی یک Snapshot (روی یک سیستم جدید):**  
```bash
sudo zfs receive mypool/newdataset < /mnt/backup/snap1.img
```

🔹 **ارسال Snapshot به یک سیستم دیگر از طریق SSH:**  
```bash
sudo zfs send mypool/mydataset@snap1 | ssh user@remote-host sudo zfs receive remote_pool/newdataset
```

---

## **5. خطاهای رایج در ZFS و راه‌حل‌ها**

### **1. خطای "pool is already in use" هنگام Import**
📌 **علت:** این مشکل زمانی رخ می‌دهد که سیستم فکر می‌کند پول در حال استفاده است.  
✅ **راه‌حل:**  
```bash
sudo zpool import -f mypool
```

---

### **2. خطای "device is busy" هنگام Export کردن یک Pool**
📌 **علت:** یکی از دیسک‌های پول هنوز در حال استفاده است.  
✅ **راه‌حل:**  
ابتدا مطمئن شوید که هیچ فرایندی از این پول استفاده نمی‌کند:
```bash
sudo lsof | grep mypool
```
سپس، پول را با اجبار Export کنید:
```bash
sudo zpool export -f mypool
```

---

### **3. خطای "no pools available to import" هنگام Import**
📌 **علت:** سیستم نمی‌تواند دیسک‌های مربوط به پول را پیدا کند.  
✅ **راه‌حل:** مسیر دیسک‌ها را مشخص کنید:
```bash
sudo zpool import -d /dev/disk/by-id
```

---

### **4. خطای "cannot mount dataset" هنگام استفاده از Snapshot**
📌 **علت:** سیستم نمی‌تواند مجموعه داده (dataset) را پس از بازگردانی اسنپ‌شات، متصل (mount) کند.  
✅ **راه‌حل:** ابتدا mount را غیرفعال کنید و دوباره فعال کنید:
```bash
sudo zfs set mountpoint=legacy mypool/mydataset
sudo mount -t zfs mypool/mydataset /mnt
```

---

### **5. خطای "checksum errors detected" هنگام اجرای `zpool status`**
📌 **علت:** یکی از دیسک‌های پول دارای مشکلات داده‌ای است.  
✅ **راه‌حل:** بررسی وضعیت دیسک:
```bash
sudo zpool scrub mypool
```
اگر مشکل حل نشد، ممکن است نیاز به **تعویض دیسک خراب** داشته باشید:
```bash
sudo zpool replace mypool /dev/sdb
```

---

### **جمع‌بندی**
- برای گرفتن اسنپ‌شات: `zfs snapshot mypool/mydataset@snap1`
- برای بازگردانی از اسنپ‌شات: `zfs rollback mypool/mydataset@snap1`
- برای انتقال اسنپ‌شات به فایل: `zfs send`
- برای بازگردانی از فایل بکاپ: `zfs receive`
- برای انتقال پول بین سیستم‌ها: `zpool export` و `zpool import`
- برای بررسی مشکلات دیسک: `zpool scrub`
