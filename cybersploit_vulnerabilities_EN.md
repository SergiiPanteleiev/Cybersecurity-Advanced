
# 🕵️ CyberSploit: 1 — Detailed Vulnerability Report

---

## 🔍 1. Unprotected Files on the Web Server (`robots.txt`)

**Vulnerability:**  
The `robots.txt` file contains an **encoded fragment** that leads to sensitive information — the first flag.

**Issue:**  
`robots.txt` is not a security feature and can be easily checked manually or with scanners like `dirb`, `gobuster`.

**Impact:**  
Leakage of hidden resources → **discovery of the first flag**.

---

## 🔍 2. Hardcoded or Guessable Credentials

**Vulnerability:**  
The username (`itsskv`) is found in HTML comments.  
The password (likely the first flag) is easily guessable.

**Issue:**  
Sensitive data is left in HTML comments accessible to any visitor.

**Impact:**  
**SSH access** to the target system without real brute-force or bypass.

---

## 🔍 3. Information in Plaintext or Encoded Format

**Vulnerability:**  
The second flag is stored in a public file (`flag2.txt`) in **binary code**.

**Issue:**  
The file has no access control, and the binary code can be **easily decoded**.

**Impact:**  
Sensitive information disclosure without needing escalation.

---

## 🔍 4. Outdated Software (Old Ubuntu Kernel)

**Vulnerability:**  
The machine runs **Ubuntu 12.04.5 with Linux kernel 3.13.0**, which contains **publicly known local privilege escalation vulnerabilities**.

**Issue:**  
The system is end-of-life and lacks updates. Exploits are easy to find on Exploit-DB or GitHub.

**Impact:**  
Attacker can gain **root access** using a published exploit.

---

## 🔍 5. Lack of User Isolation

**Vulnerability:**  
Once a user account is compromised, there are **no additional security restrictions** (AppArmor, SELinux, containerization).

**Issue:**  
Nothing prevents a compromised user from escalating or accessing system files.

**Impact:**  
**Full system compromise** is possible.

---

## 🔐 Summary Table

| Vulnerability                 | Impact                                       |
|-------------------------------|----------------------------------------------|
| Data leakage via `robots.txt` | Access to hidden resources / first flag       |
| HTML comments with credentials| Unauthorized system access (SSH login)       |
| Public binary-encoded files   | Access to sensitive data / second flag        |
| Outdated Ubuntu kernel        | Privilege escalation to root                 |
| No isolation mechanisms       | Full compromise of the system                |

---

## ✅ Lessons Learned

- Do not rely on `robots.txt` for hiding data.  
- Never store credentials in HTML comments.  
- Protect files with proper **permissions and access control**.  
- Always **update OS and kernel** to avoid public exploits.  
- Implement **defense-in-depth**: AppArmor, SELinux, or containers.

---

