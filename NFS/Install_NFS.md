## **راهنمای کامل راه‌اندازی NFS Server و Client در دبیان (با امنیت بالا)**  

**🔹 سناریو:**  
- **IP سرور:** `192.168.10.91`  
- **دایرکتوری اشتراکی:** `/mnt/sda/NFS_SHARE`  
- **فعال‌سازی `secure` برای محدود کردن کلاینت‌ها به پورت‌های امن**  

---

## **۱. نصب NFS Server و Client**
### **🔹 روی سرور (NFS Server)**
```bash
sudo apt update
sudo apt install nfs-kernel-server -y
```
### **🔹 روی کلاینت (NFS Client)**
```bash
sudo apt update
sudo apt install nfs-common -y
```

---

## **۲. تنظیمات NFS Server**  
### **🔹 ایجاد دایرکتوری اشتراکی**
```bash
sudo mkdir -p /mnt/sda/NFS_SHARE
```
### **🔹 تنظیم مجوزهای دسترسی**
```bash
sudo chown nobody:nogroup /mnt/sda/NFS_SHARE
sudo chmod 777 /mnt/sda/NFS_SHARE
```

### **🔹 ویرایش فایل `/etc/exports`**
```bash
sudo nano /etc/exports
```
افزودن مسیر اشتراک و دسترسی برای کلاینت‌ها:
```
/mnt/sda/NFS_SHARE 192.168.10.0/24(rw,sync,no_subtree_check,root_squash,secure)
```
**توضیحات:**  
- **`rw`** → مجوز خواندن و نوشتن  
- **`sync`** → نوشتن داده‌ها همزمان با ارسال درخواست  
- **`no_subtree_check`** → بهبود عملکرد و کاهش تأخیر  
- **`root_squash`** → جلوگیری از دسترسی **root کلاینت** به root سرور  
- **`secure`** → فقط کلاینت‌هایی که از **پورت‌های کمتر از 1024** استفاده می‌کنند، متصل شوند  

### **🔹 اعمال تغییرات و راه‌اندازی سرویس**
```bash
sudo exportfs -ra
sudo systemctl restart nfs-kernel-server
sudo systemctl enable nfs-kernel-server
```

---

## **۳. تنظیمات امنیتی NFS Server**
### **🔹 محدود کردن نسخه NFS (فقط NFSv4)**
در **`/etc/default/nfs-kernel-server`** مقدار زیر را اضافه کنید:
```
RPCMOUNTDOPTS="--manage-gids --no-nfs-version 3"
```
سپس:
```bash
sudo systemctl restart nfs-kernel-server
```

### **🔹 تنظیمات فایروال (UFW)**
```bash
sudo ufw allow from 192.168.10.0/24 to any port nfs
sudo ufw reload
```

---

## **۴. پیکربندی کلاینت NFS**
### **🔹 ایجاد دایرکتوری برای اتصال**
```bash
sudo mkdir -p /mnt/nfs_client_share
```
### **🔹 اتصال دستی برای تست**
```bash
sudo mount 192.168.10.91:/mnt/sda/NFS_SHARE /mnt/nfs_client_share
```
### **🔹 اتصال دائمی (افزودن به `fstab`)**
فایل `/etc/fstab` را ویرایش کنید:
```
192.168.10.91:/mnt/sda/NFS_SHARE  /mnt/nfs_client_share  nfs  defaults  0  0
```
سپس:
```bash
sudo mount -a
```

---

## **۵. بررسی و تست**
### **🔹 بررسی از کلاینت**
```bash
touch /mnt/nfs_client_share/testfile
ls -l /mnt/nfs_client_share/
```
### **🔹 نمایش لیست اشتراک‌ها روی سرور**
```bash
showmount -e 192.168.10.91
```
### **🔹 بررسی وضعیت سرویس NFS روی سرور**
```bash
sudo systemctl status nfs-kernel-server
```

