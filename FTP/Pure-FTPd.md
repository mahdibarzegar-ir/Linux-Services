
### **. نصب و ایمن‌سازی Pure-FTPd**  
Pure-FTPd یک سرویس FTP امن و سبک است که می‌تواند با تنظیمات مناسب، امنیت بیشتری برای انتقال داده‌ها فراهم کند.  

---

### **۱. نصب Pure-FTPd**  
```bash
sudo apt install pure-ftpd
```
🔹 این دستور Pure-FTPd را نصب می‌کند.  

---

### **۲. ایجاد گروه و کاربر FTP**  
```bash
sudo groupadd ftpgroup
sudo useradd -g ftpgroup -d /dev/null -s /etc ftpuser
```
🔹 `ftpgroup`: گروهی برای مدیریت کاربران FTP ایجاد می‌کند.  
🔹 `ftpuser`: کاربری سیستمی است که بدون شل و دایرکتوری خانگی برای افزایش امنیت ایجاد می‌شود.  

---

### **۳. ایجاد کاربر مجازی برای FTP**  
```bash
sudo pure-pw useradd mahdi -u ftpuser -d /ftphome
```
🔹 `mahdi` یک کاربر مجازی است که با `ftpuser` مرتبط شده و مسیر `/ftphome` را به‌عنوان دایرکتوری خانگی خود دارد.  
🔹 پس از اجرای این دستور، سیستم از شما رمز عبور را درخواست می‌کند.  

---

### **۴. به‌روزرسانی پایگاه داده کاربران Pure-FTPd**  
```bash
sudo pure-pw mkdb
```
🔹 این دستور اطلاعات کاربران مجازی را در پایگاه داده Pure-FTPd ذخیره و به‌روزرسانی می‌کند.  

---

### **۵. تنظیم احراز هویت کاربران FTP**  
```bash
cd /etc/pure-ftpd/auth/
ls
sudo ln -s ../conf/PureDB 60pdb
```
🔹 این کار باعث می‌شود سرور از پایگاه داده `PureDB` برای احراز هویت کاربران استفاده کند.  

---

### **۶. ایجاد دایرکتوری FTP و تنظیم مالکیت آن**  
```bash
sudo mkdir -p /ftphome
sudo chown -R ftpuser:ftpgroup /ftphome/
```
🔹 `/ftphome` به‌عنوان مسیر ذخیره‌سازی فایل‌های FTP ایجاد و مالکیت آن به کاربر و گروه `ftpuser:ftpgroup` اختصاص داده می‌شود.  

---

### **۷. راه‌اندازی مجدد سرویس و بررسی وضعیت آن**  
```bash
sudo systemctl restart pure-ftpd
sudo systemctl status pure-ftpd
```
🔹 سرویس Pure-FTPd را راه‌اندازی مجدد کرده و وضعیت آن را بررسی می‌کنیم.  

---

## **راهکارهای امنیتی پیشنهادی**  

 **فعال‌سازی TLS برای رمزنگاری ارتباطات:**  
```bash
sudo apt install openssl
sudo mkdir -p /etc/ssl/private
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/pure-ftpd.pem -out /etc/ssl/private/pure-ftpd.pem
sudo chmod 600 /etc/ssl/private/pure-ftpd.pem
```
🔹 این کار باعث می‌شود ارتباطات FTP از طریق SSL/TLS رمزگذاری شود.  

 **غیرفعال کردن ورود کاربران ناشناس:**  
```bash
echo "NoAnonymous yes" | sudo tee -a /etc/pure-ftpd/conf/NoAnonymous
```
🔹 از ورود کاربران ناشناس جلوگیری می‌کند.  

 **محدود کردن دسترسی کاربران به دایرکتوری‌های خودشان:**  
```bash
echo "ChrootEveryone yes" | sudo tee -a /etc/pure-ftpd/conf/ChrootEveryone
```
🔹 کاربران فقط به دایرکتوری خودشان دسترسی خواهند داشت.  

 **تنظیم تعداد اتصالات هم‌زمان و محدودیت پهنای باند:**  
```bash
echo "MaxClientsNumber 10" | sudo tee -a /etc/pure-ftpd/conf/MaxClientsNumber
echo "LimitRecursion 2000 8" | sudo tee -a /etc/pure-ftpd/conf/LimitRecursion
```
🔹 تعداد اتصالات هم‌زمان و حداکثر سطح پوشه‌های مجاز را محدود می‌کند.  

 **راه‌اندازی مجدد سرویس برای اعمال تغییرات:**  
```bash
sudo systemctl restart pure-ftpd
```
🔹 پس از انجام تغییرات امنیتی، سرویس مجدداً راه‌اندازی می‌شود.  

---

---
---

# یک مورد troubleshooting 

### ۱. بررسی وجود کاربر **mahdi**  
ابتدا بررسی کنید که آیا این کاربر قبلاً ایجاد شده است یا خیر:  
```bash
sudo pure-pw list
```
اگر نام کاربر در لیست وجود دارد، می‌توانید او را حذف کرده و دوباره ایجاد کنید:  
```bash
sudo pure-pw userdel mahdi
```

### ۲. بررسی دسترسی به فایل **pureftpd.passwd.tmp**  
ممکن است برنامه نتواند این فایل را ایجاد یا تغییر دهد. بررسی کنید که مسیر مربوطه وجود داشته و دسترسی لازم را داشته باشید:  
```bash
ls -l /etc/pure-ftpd/
```
اگر فایل یا مسیر وجود ندارد، می‌توانید آن را ایجاد کنید:  
```bash
sudo touch /etc/pure-ftpd/pureftpd.passwd.tmp
sudo chmod 600 /etc/pure-ftpd/pureftpd.passwd.tmp
```

### ۳. اجرای دستور با کاربر **root**  
دستور خود را بدون `sudo` و مستقیماً به‌عنوان کاربر `root` اجرا کنید:  
```bash
pure-pw useradd mahdi -u ftpuser -d /ftphome
```
اگر همچنان مشکل وجود داشت، بررسی کنید که کاربر `root` هستید:  
```bash
whoami
```

### ۴. بررسی سرویس Pure-FTPd  
اطمینان حاصل کنید که سرویس Pure-FTPd در حال اجرا است:  
```bash
sudo systemctl status pure-ftpd
```
اگر غیرفعال است، آن را راه‌اندازی کنید:  
```bash
sudo systemctl start pure-ftpd
```

### ۵. بررسی لاگ‌های Pure-FTPd  
برای جزئیات بیشتر از لاگ‌های سیستم استفاده کنید:  
```bash
sudo journalctl -xe | grep pure-ftpd
```
همچنین می‌توانید لاگ FTP را بررسی کنید:  
```bash
sudo cat /var/log/syslog | grep pure-ftpd
```