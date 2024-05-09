# Django loyihani linux serverga qo'yish

---

1. Linux serverda loyihani Github dan clone qilib olamiz

```shell
git clone https://github.com/
```

---

2. Virtual muhit yaratib olish

```shell
python -m venv venv
```

---

3. virtual muhitni faollashtirish

```shell
venv\Scripts\activate
```

---

4. Kerakli kutubxonalarni **requirements.txt** faylidan o'qib, muhitga o'rnatish

```shell
pip install -r requirements.txt 
```

---

5. Loyiha to'g'ri ishlashi uchun va admin panelga kirish imkoniyati bo'lishi uchun Django tomonidan tayyorlab qo'yilgan
   migratsiyalarni yaratib olamiz

```shell
python manage.py makemigrations
python manage.py migrate
```

---

6. **superuser** yaratib olamiz (admin panelga kirish imkoniyati bo'lishi uchun)

```shell
python manage.py createsuperuser
```

---

7. Django loyihani ishlatib ko'rish, agar quyidagicha rasm ko'rinsa, unda hammasi joyida bo'ladi

```shell
python manage.py runserver
```

---

8. Serverni **CTRL + C** bilan to'xtatamiz

---

9. **root** yo'nalish bo'yicha o'tib olamiz

```shell
cd /root/
```

---

10. **Nginx** web serverini o'rnatib olamiz

```shell
sudo apt update
sudo apt install python3-venv
sudo apt install nginx -y
```

---

11. Virtual muhitni o'chirib olamiz

```shell
deactivate
```

---

12. **8000** portga ruxsat beramiz (linux server loyihamizni **8000** chi portda ishlatish imkoniga ega bo'lishi uchun)

```shell
sudo ufw allow 8000
```

---

13. **gunicorn** faylni yaratamiz (Django loyihani *run* qilib beradigan asosiy fayl)

```shell
sudo nano /etc/systemd/system/gunicorn.socket
```

---

14. **gunicorn** fayli ichiga quiydagilarni yozamiz

```text
[Unit]
Description=gunicorn socket

[Socket]
ListenStream=/run/gunicorn.sock

[Install]
WantedBy=sockets.target
```

---

15. Yuqorida yaratgan **gunicorn** faylini ishga tushiradigan *service* fayl yaratib olamiz

```shell
sudo nano /etc/systemd/system/gunicorn.service
```

---

16. *service* fayl ichiga quyidagilarni yozamiz

```text
[Unit]
Description=gunicorn daemon
Requires=gunicorn.socket
After=network.target

[Service]
User=<USERNAME>
Group=www-data
WorkingDirectory=/<USERNAME>/<PROJECT_FOLDER>
ExecStart=/<USERNAME>/<PROJECT_FOLDER_NAME>/venv/bin/gunicorn \
          --access-logfile - \
          --workers 3 \
          --bind unix:/run/gunicorn.sock \
          <PROJECT_ROOT_FOLDER>.wsgi:application

[Install]
WantedBy=multi-user.target
```

---

17. **gunicorn** faylini ishlatish uchun buyruq beramiz (bu bizga Django loyihamizni avtomatik ishlatib (*run*) qilib
    beradi)

```shell
sudo systemctl start gunicorn.socket
```

---

18. Linux server har sfar o'chib yonganida yuqoridagi komanda avtomatik ishga tushishi uchun quyidagi komandani teramiz

```shell
sudo systemctl enable gunicorn.socket
```

---

19. Kiritilgan o'zgarishlar ishga tushishi uchun quyidagi komandani teramiz

```shell
sudo systemctl daemon-reload
```

---

20. **gunicorn** faylini ham bir marta *restart* qilib yuboramiz

```shell
sudo systemctl restart gunicorn
```

---

21. Linux serverga kelgan so'rovlar avtomatik Django loyihaga yuborilishi uchun bu uchun **Nginx** fayl yozamiz va qaysi
    manzillardan so'rov kelsa loyihani ishlatib berish kerakligi aytamiz

```shell
sudo nano /etc/nginx/sites-available/PROJECT_FOLDER
```

---

22. Yuqorida yaratib olgan faylimizi ichini quyidagilar bilan to'ldiramiz

```text
server {
    listen 80;
    server_name <SERVER_IP_ADDRESS>;

    location = /favicon.ico { access_log off; log_not_found off; }
    location /static/ {

        <USERNAME> /<PROJECT_FOLDER>/static;
    }

    location / {
        include proxy_params;
        proxy_pass http://unix:/run/gunicorn.sock;
    }
}
```

---

23. Hozirgi yozgan o'zgarishlarimiz ishga tushishi uchun **nginx** fayliga *restart* berib yuboramiz

```shell
sudo systemctl restart nginx
```

---

24. **Nginx** uchun barcha ruxsatlarni beramiz

```shell
sudo ufw allow 'Nginx Full'
```

---

25. **gunicorn** faylini yana bir marta *restart* qilib yuboramiz

```shell
sudo systemctl restart gunicorn
```

---

## Agar loyihaning har qanday fayliga biron kichik o'zgartirish kiritilsa ham, home.html, views.py, urls.py, ... Har safar quyidagi buruqni ishlatib yuborish kerak. Ya'ni 25-qadamdagi buyruqni ☝️

```shell
sudo systemctl restart gunicorn
```

___

# Endi esa linux server ip manzili brauzerda tergan holda ishlab turgan Django loyihamizni natijasini
*online* ko'rishimiz mumkin. E'tiboringiz uchun raxmat 🙂

![Django server activa state](https://media.licdn.com/dms/image/C4E12AQG1c3mW8YQREw/article-cover_image-shrink_720_1280/0/1594470540725?e=2147483647&v=beta&t=rPaCWRuOJvqiAepsl-2YfibiGYWhV70So8Dg7e_UOjk)





























