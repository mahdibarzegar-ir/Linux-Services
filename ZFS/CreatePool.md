در ZFS، **پول (Pool)** به مجموعه‌ای از دیسک‌ها گفته می‌شود که برای ذخیره‌سازی داده‌ها استفاده می‌شود. ZFS از **RAID-Z** به‌عنوان روشی برای ایجاد افزونگی و تحمل‌پذیری خطا استفاده می‌کند که معادل RAID 0، 1، 5, 6 و 7 در سیستم‌های سنتی است.

### **1. ایجاد ZFS Pool بدون افزونگی (معادل RAID 0)**
RAID 0 فقط ظرفیت را افزایش می‌دهد و هیچ افزونگی ندارد. اگر یکی از دیسک‌ها خراب شود، تمام داده‌ها از بین می‌روند.

```bash
sudo zpool create mypool /dev/sdX /dev/sdY
```

🔹 **مثال:** اگر دو دیسک `/dev/sdb` و `/dev/sdc` دارید:
```bash
sudo zpool create mypool /dev/sdb /dev/sdc
```
این پول فضای دو دیسک را با هم ترکیب می‌کند اما هیچ محافظتی در برابر خرابی دیسک ندارد.

---

### **2. ایجاد ZFS Mirror (معادل RAID 1)**
RAID 1 اطلاعات را بین دو یا چند دیسک کپی می‌کند و داده‌ها را در همه دیسک‌ها یکسان ذخیره می‌کند.

```bash
sudo zpool create mypool mirror /dev/sdX /dev/sdY
```

🔹 **مثال:** ایجاد یک Mirror با دو دیسک:
```bash
sudo zpool create mypool mirror /dev/sdb /dev/sdc
```
این روش امنیت بالایی دارد، اما ظرفیت کل فقط برابر با یک دیسک خواهد بود.

---

### **3. ایجاد ZFS RAID-Z1 (معادل RAID 5)**
  RAID-Z1 از **یک دیسک برای Parity** استفاده می‌کند و تحمل خرابی **یک دیسک** را دارد.

```bash
sudo zpool create mypool raidz1 /dev/sdX /dev/sdY /dev/sdZ
```

🔹 **مثال:** اگر سه دیسک `/dev/sdb /dev/sdc /dev/sdd` دارید:
```bash
sudo zpool create mypool raidz1 /dev/sdb /dev/sdc /dev/sdd
```
🔹 **نکته:** حداقل **3 دیسک** نیاز است.

---

### **4. ایجاد ZFS RAID-Z2 (معادل RAID 6)**
RAID-Z2 از **دو دیسک برای Parity** استفاده می‌کند و می‌تواند هم‌زمان خرابی **دو دیسک** را تحمل کند.

```bash
sudo zpool create mypool raidz2 /dev/sdX /dev/sdY /dev/sdZ /dev/sdW
```

🔹 **مثال:** اگر چهار دیسک `/dev/sdb /dev/sdc /dev/sdd /dev/sde` دارید:
```bash
sudo zpool create mypool raidz2 /dev/sdb /dev/sdc /dev/sdd /dev/sde
```
🔹 **نکته:** حداقل **4 دیسک** نیاز است.

---




**RAID-Z3** سطح بالاتری از افزونگی در ZFS است که مشابه **RAID 6** اما با **3 دیسک Parity** است. این پیکربندی می‌تواند **هم‌زمان خرابی 3 دیسک** را تحمل کند، که آن را برای سیستم‌هایی با تعداد دیسک‌های زیاد و نیاز به امنیت بالا مناسب می‌کند.

---

### **5. ایجاد ZFS RAID-Z3 (مشابه RAID 7)**
🔹 RAID-Z3 به **حداقل 5 دیسک** نیاز دارد، زیرا 3 دیسک به Parity اختصاص داده می‌شود.

```bash
sudo zpool create mypool raidz3 /dev/sdX /dev/sdY /dev/sdZ /dev/sdW /dev/sdV
```

**🔹 مثال:** اگر پنج دیسک `/dev/sdb /dev/sdc /dev/sdd /dev/sde /dev/sdf` دارید:
```bash
sudo zpool create mypool raidz3 /dev/sdb /dev/sdc /dev/sdd /dev/sde /dev/sdf
```

🔹 **ویژگی‌های RAID-Z3:**
✔ امنیت بالا (تحمل خرابی **3 دیسک**)  
✔ مناسب برای آرایه‌های بزرگ دیسک  
✔ کاهش ظرفیت کلی به دلیل 3 دیسک Parity

---

### **6. بررسی وضعیت پول**
پس از ایجاد هر **pool**، می‌توانید وضعیت آن را بررسی کنید:
```bash
zpool status
```

---