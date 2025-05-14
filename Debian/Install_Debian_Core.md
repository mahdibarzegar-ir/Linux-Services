### **مستند نصب و راه‌اندازی Debian Core با sudo و بسته‌های ضروری**

این راهنما مراحل نصب و راه‌اندازی **Debian Core** را همراه با تنظیمات `sudo` و نصب بسته‌های ضروری شامل `ssh` را پوشش می‌دهد.

---

## **۱. نصب Debian Core**
ابتدا **Debian Core** را روی سیستم خود نصب کنید. در این مرحله فرض می‌کنیم که شما مراحل استاندارد نصب را طی کرده‌اید.

---

## **۲. نصب `sudo`**
بعد از نصب، برای فعال‌سازی `sudo` در سیستم، مراحل زیر را انجام دهید:

1. ابتدا با کاربر `root` وارد شوید:
   ```shell
   su
   ```
2. سپس بسته `sudo` را نصب کنید:
   ```shell
   apt install sudo
   ```

---

## **۳. افزودن کاربر به `sudoers`**
برای اینکه کاربر عادی مجوز `sudo` داشته باشد، باید او را به گروه `sudo` اضافه کنیم:

1. کاربر موردنظر را به گروه `sudo` اضافه کنید:
   ```shell
   su
   /sbin/usermod -aG sudo username
   ```
   (به جای `username` نام کاربر خود را جایگزین کنید.)

2. سیستم را برای اعمال تغییرات راه‌اندازی مجدد کنید:
   ```shell
   /sbin/shutdown -r now
   ```

---

## **۴. نصب بسته‌های ضروری**
برای بهبود کارایی و امکانات خط فرمان، بسته‌های زیر را نصب کنید:

```shell
sudo apt install bash-completion bash-builtins vim htop iproute2
```
- `bash-completion`: قابلیت تکمیل خودکار دستورات در Bash
- `bash-builtins`: ابزارهای داخلی برای بهینه‌سازی Bash
- `vim`: ویرایشگر متن پیشرفته
- `htop`: نمایش فرایندهای در حال اجرا با رابط گرافیکی متنی
- `iproute2`: ابزارهای مدیریت شبکه در لینوکس (جایگزین `net-tools`)

---

## **۵. نصب و راه‌اندازی `SSH Server`**
برای دسترسی از راه دور، باید **SSH Server** را نصب و پیکربندی کنیم:

1. **نصب OpenSSH Server**:
   ```shell
   sudo apt install openssh-server
   ```

2. **فعال‌سازی و راه‌اندازی سرویس `SSH`**:
   ```shell
   sudo systemctl enable ssh
   sudo systemctl start ssh
   ```

3. **بررسی وضعیت سرویس `SSH`**:
   ```shell
   sudo systemctl status ssh
   ```

---

## **۶. امنیت `SSH` (پیشنهاد شده)**
برای افزایش امنیت، می‌توانید برخی تغییرات را در پیکربندی `SSH` اعمال کنید:

1. **ویرایش فایل تنظیمات SSH**:
   ```shell
   sudo vim /etc/ssh/sshd_config
   ```
   - مقدار **پورت SSH** را تغییر دهید تا از حملات رایج جلوگیری شود:
     ```shell
     Port 2222
     ```
   - ورود `root` را غیرفعال کنید:
     ```shell
     PermitRootLogin no
     ```
   - فقط کاربران خاص را مجاز کنید:
     ```shell
     AllowUsers username
     ```
     (به جای `username` نام کاربر خود را وارد کنید.)

2. **اعمال تغییرات با راه‌اندازی مجدد `SSH`**:
   ```shell
   sudo systemctl restart ssh
   ```

---
---