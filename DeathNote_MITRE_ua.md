
---

## **Мапінг вразливостей Death Note → MITRE ATT\&CK**

| № | Вразливість                                                 | Тактика MITRE ATT\&CK                  | Техніка (ID) та опис                                                                                       |
| - | ----------------------------------------------------------- | -------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| 1 | **Витік внутрішнього доменного імені (DNS)**                | Reconnaissance                         | **T1590.001 – Gather Victim Network Information: Domain Properties**<br>Збір даних про домени та хости.    |
| 2 | **Індексація директорій (Directory Listing)**               | Discovery                              | **T1083 – File and Directory Discovery**<br>Отримання інформації про файли й директорії в системі.         |
| 3 | **Відкриті конфігураційні файли (robots.txt)**              | Reconnaissance                         | **T1592.002 – Gather Victim Identity Information: Website Content**<br>Збір інформації з веб-контенту.     |
| 4 | **Зберігання паролів у відкритому вигляді**                 | Credential Access                      | **T1552.001 – Unsecured Credentials: Local Files**<br>Паролі у відкритому вигляді або слабозахищені файли. |
| 5 | **Слабкі або повторно використані паролі**                  | Credential Access                      | **T1110.001 – Brute Force: Password Guessing**<br>Атаки підбором через слабкі паролі.                      |
| 6 | **Відсутність обмеження на кількість спроб автентифікації** | Credential Access                      | **T1110.003 – Brute Force: Password Spraying**<br>Автоматизований підбір без блокування.                   |
| 7 | **Відсутність сегрегації привілеїв (sudo без обмежень)**    | Privilege Escalation / Defense Evasion | **T1078 – Valid Accounts**<br>Отримання root-доступу через зламані облікові записи.                        |
| 8 | **Інформаційні витоки у вихідному коді та файлах**          | Reconnaissance / Discovery             | **T1592 – Gather Victim Identity Information**<br>Витік службових даних через код, конфіги, коментарі.     |

---

## **Тактики MITRE, які покриваються цими вразливостями**

* **Reconnaissance** – збір даних: домени, веб-контент, конфіги.
* **Discovery** – виявлення файлів, директорій, облікових записів.
* **Credential Access** – отримання паролів, brute-force атаки.
* **Privilege Escalation** – підвищення прав доступу (sudo/root).
* **Defense Evasion** – уникнення обмежень і захистів системи.

---
