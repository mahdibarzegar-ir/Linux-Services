### **راهنمای کامل نصب و پیکربندی Samba با تنظیمات امنیتی**  

این راهنما مراحل کامل نصب، تنظیم و ایمن‌سازی Samba را روی لینوکس توضیح می‌دهد.

---

## **۱. نصب Samba**
ابتدا بسته‌های مورد نیاز را نصب کنید:
```bash
sudo apt update
sudo apt install samba
```
سپس دایرکتوری اشتراک‌گذاری را ایجاد کنید:
```bash
sudo mkdir -p /srv/sambaserver
sudo vi /etc/samba/smb.conf
```
---

## **۲. پیکربندی دایرکتوری اشتراک‌گذاری**
فایل تنظیمات Samba را ویرایش کنید:
```ini
[ssharea]
comment = Server Share A
browseable = yes
path = /srv/sambaserver
public = no
writable = yes
valid users = smbuser smbadmin
hosts allow = 192.168.1.0/24
hosts deny = 0.0.0.0/0
encrypt passwords = yes
guest ok = no
security = user
passdb backend = tdbsam
```
- `valid users = smbuser smbadmin` → فقط کاربران مشخص شده اجازه دسترسی دارند.
- `hosts allow = 192.168.1.0/24` → فقط IPهای این رنج دسترسی دارند.
- `hosts deny = 0.0.0.0/0` → سایر IPها مسدود می‌شوند.
- `encrypt passwords = yes` → رمز عبورها رمزگذاری شده‌اند.
- `guest ok = no` → دسترسی مهمان غیرفعال است.

---

## **۳. بررسی تنظیمات Samba**
برای اطمینان از صحت پیکربندی، از دستور زیر استفاده کنید:
```bash
sudo testparm
```

---

## **۴. ایجاد کاربر برای Samba و تنظیم دسترسی‌ها**
ابتدا یک کاربر جدید برای Samba ایجاد کنید:
```bash
sudo adduser smbuser
sudo passwd smbuser
sudo smbpasswd -a smbuser
```
اکنون باید مجوزهای مناسب را روی دایرکتوری اشتراک‌گذاری اعمال کنیم:
```bash
sudo chown -R smbuser:smbuser /srv/sambaserver
sudo chmod -R 770 /srv/sambaserver
```
- `chown -R smbuser:smbuser /srv/sambaserver` → مالکیت پوشه را به کاربر `smbuser` تغییر می‌دهد.
- `chmod -R 770 /srv/sambaserver` → فقط صاحب پوشه و گروه آن می‌توانند به آن دسترسی داشته باشند.

برای مشاهده کاربران Samba:
```bash
pdbedit -L
```

---

## **۵. مدیریت سرویس Samba**
پس از پیکربندی، سرویس Samba را راه‌اندازی کنید:
```bash
sudo systemctl start smb
sudo systemctl enable smb
sudo systemctl status smb
```
---

## **۶. نصب و استفاده از Samba Client**
روی کلاینت لینوکسی، برای اتصال به Samba، بسته‌های زیر را نصب کنید:
```bash
sudo apt install samba-client cifs-utils
```
سپس می‌توان منابع اشتراک Samba را لیست کرد:
```bash
smbclient -L //localhost -U smbuser
```
برای اتصال به سرور Samba و مدیریت فایل‌ها:
```bash
smbclient //localhost -U smbuser
```

برای مانت کردن دایرکتوری Samba در کلاینت:
```bash
sudo mount -o username=smbuser,noperm \
> //192.168.xx.xx/sambaserver /mnt/mydir
```

---

## **۷. بررسی و فعال‌سازی ماژول CIFS**
```bash
less /proc/filesystems
modprobe cifs
```
---

## **۸. ذخیره تنظیمات مانت Samba در `fstab`**
برای مانت خودکار Samba در بوت، ابتدا فایل `fstab` را ویرایش کنید:
```bash
sudo cp /etc/fstab /etc/fstab.bak
sudo nano /etc/fstab
```
و خط زیر را اضافه کنید:
```bash
//192.168.xx.xx/sambaserver /mnt/mydir cifs credentials=/etc/samba/smbuser,uid=1001 0 0
```

---

## **۹. ایجاد فایل اعتبارسنجی کاربر Samba**
یک فایل حاوی اطلاعات ورود ایجاد کنید:
```bash
sudo vi /etc/samba/smbuser
```
و اطلاعات زیر را در آن قرار دهید:
```
username=smbuser
password=123
```
سپس سطح دسترسی این فایل را محدود کنید:
```bash
sudo chmod 0400 /etc/samba/smbuser
```

---

## **۱۰. مانت کردن دایرکتوری Samba**
```bash
sudo mount /mnt/mydir
```
یا
```bash
sudo mount -a
```
---

## **۱۱. ابزارهای مدیریتی Samba**
| ابزار | توضیح |
|-------|-------|
| `pdbedit` | مدیریت کاربران Samba |
| `smbcacls` | تغییر لیست کنترل دسترسی اشتراک‌ها |
| `smbcontrol` | مدیریت `smbd` |
| `smbpasswd` | تغییر رمز عبور کاربران Samba |
| `smbstatus` | نمایش اتصالات جاری |
| `smbtar` | پشتیبان‌گیری و بازیابی اشتراک‌ها |
| `testparm` | بررسی صحت تنظیمات Samba |
