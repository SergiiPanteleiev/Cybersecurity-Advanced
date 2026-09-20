
---

# 🔗 MITRE ATT\&CK Mapping — CyberSploit: 1

## 1️⃣ Reconnaissance & Discovery

**Step:** Scan network to find the target VM and enumerate open ports (`nmap`).

* **T1595 – Active Scanning**
* **T1046 – Network Service Scanning**

---

## 2️⃣ Initial Access

**Step:** Accessing hidden file `robots.txt` → decoding base64 → retrieving credentials/flag.

* **T1592.004 – Gather Victim Identity Information: File and Directory Discovery**
* **T1552 – Unsecured Credentials (credentials in code/comments/robots.txt)**

---

## 3️⃣ Credential Access

**Step:** Extracting username (`itsskv`) from HTML comments and using flag as password.

* **T1589.001 – Gather Victim Identity Information: Credentials**
* **T1078 – Valid Accounts** (use of discovered credentials for SSH login)

---

## 4️⃣ Execution

**Step:** Establish SSH session into the system with valid credentials.

* **T1059 – Command and Scripting Interpreter (Bash)**
* **T1078 – Valid Accounts**

---

## 5️⃣ Collection

**Step:** Retrieve `flag2.txt`, decode binary data to reveal flag.

* **T1005 – Data from Local System**
* **T1140 – Deobfuscate/Decode Files or Information**

---

## 6️⃣ Privilege Escalation

**Step:** Exploit vulnerable Linux kernel (CVE from Exploit-DB) to escalate to root.

* **T1068 – Exploitation for Privilege Escalation**

---

## 7️⃣ Defense Evasion

**Step:** Running a public exploit and compiling it locally with `gcc`.

* **T1622 – Debugger Evasion / Compiler Abuse**
* **T1036 – Masquerading** (optional, depending on how exploit is named/hidden)

---

## 8️⃣ Impact / Persistence

**Step:** Reading `finalflag.txt` as root in `/root/`.

* **T1005 – Data from Local System**
* **T1098 – Account Manipulation** (if persistence was attempted with root access, not in this CTF)

---

# 📊 Summary Table — MITRE ATT\&CK Mapping

| CTF Step                         | Action                                 | MITRE ATT\&CK Technique |
| -------------------------------- | -------------------------------------- | ----------------------- |
| Scan network with `nmap`         | Service discovery                      | T1046, T1595            |
| Find hidden files (`robots.txt`) | Web content discovery, data exposure   | T1592.004, T1552        |
| Extract username from HTML       | Credential harvesting                  | T1589.001               |
| SSH login with valid creds       | Remote access via valid accounts       | T1078, T1059            |
| Read `flag2.txt` (binary)        | Local data collection & decoding       | T1005, T1140            |
| Kernel exploit (root)            | Privilege escalation via vulnerability | T1068                   |
| Access final flag                | Data exfiltration (local read)         | T1005                   |

---

