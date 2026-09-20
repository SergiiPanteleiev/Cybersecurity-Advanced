
---

# Capture the Flag (CTF) — CyberSploit: 1 Walkthrough

This document provides a detailed explanation of the **Capture the Flag (CTF)** challenge published on VulnHub by the author *CyberSploit*.
According to the author, the difficulty level of this CTF is **EASY**, and the goal is to gain **root access** to the target machine and capture **three flag files**.

To solve this CTF, you need basic knowledge of Linux commands and penetration testing tools.
You can download the CTF here: [https://www.vulnhub.com/entry/cybersploit-1,506/](https://www.vulnhub.com/entry/cybersploit-1,506/).

⚠️ **Note:** All vulnerable machines were launched using Oracle VirtualBox.
ParrotOS was used as the attacker machine.
The techniques demonstrated are intended **for educational purposes only**. Any misuse against real systems is strictly prohibited and will be subject to applicable law.

---

## 📋 CTF Solution Overview

1. Discover the target machine’s IP address and scan open ports using **nmap**.
2. Explore the HTTP service and capture the **first flag**.
3. Gain shell access and find the **second flag**.
4. Escalate privileges to root and capture the **third flag**.

---

## 🔎 Step-by-Step Walkthrough

### Step 1: Discover Target IP and Scan Ports

After downloading and starting the VM in VirtualBox, the first step is to identify its IP address and scan for open ports/services.
We use the command:

```bash
nmap 192.168.88.0/24 -sV
```

📌 **Result:**

* Target IP identified: `192.168.88.X`
  ⚠️ Note: IP addresses of attacker and target may differ depending on network configuration.
* Port **22 (SSH)** is open.
* Port **80 (HTTP)** is open.

Next, we will explore the web application running on port 80.

---

### Step 2: Explore the Web Application

Open the target’s IP address in a browser — it displays a simple website.

To discover hidden files/directories, run:

```bash
dirb http://192.168.88.X
```

📌 **Result:**

* A `robots.txt` file is found.

Open it in the browser → it contains an **encoded string**.

Decode it using **Burp Decoder** or the command line:

```bash
echo "base64-Encoded-String" | base64 --decode
```

📌 **Flag1:**

```
cybersploit{youtube.com/c/cybersploit}
```

---

### Step 3: Gain Shell Access

By inspecting the HTML source code, we find a **username** in comments:

```
Username: itsskv
```

Since port 22 (SSH) is open, and we already have the first flag, we try it as the password:

```bash
ssh itsskv@192.168.88.X
```

📌 **Result:** Successful SSH login! 🎉

Inside the home directory, we find `flag2.txt`.
View its contents:

```bash
cat flag2.txt
```

📌 **Result:** File contains **binary code**.

Decoding it using a binary-to-text decoder gives us **Flag2**.

---

### Step 4: Privilege Escalation to Root

We check system details:

```bash
uname -a
cat /etc/issue
```

📌 **Result:**

* OS: **Ubuntu 12.04.5**
* Kernel: **Linux 3.13.0**

This version is **outdated** and has multiple public privilege escalation exploits.
We search Exploit-DB and find a suitable exploit.

From the attacker machine, we transfer it via `scp` and compile on the target:

```bash
gcc 37292.c -o exploit
./exploit
```

📌 **Result:** Root access obtained! 🎉

Navigate to `/root/` and open the final flag:

```bash
cat finalflag.txt
```

✅ **Final Flag captured! CTF completed! 🎯**

---

## 🏁 Conclusion — What We Achieved

✔️ Identified the target machine’s IP.
✔️ Scanned ports and services.
✔️ Discovered the first flag via `robots.txt`.
✔️ Gained SSH shell access.
✔️ Captured the second flag (`flag2.txt`).
✔️ Used a kernel exploit for root privilege escalation.
✔️ Captured the final flag.

🔹 This CTF is an excellent **beginner-friendly exercise** for practicing **ethical hacking** and **penetration testing skills**.

---

## 📂 Modified Image

[Google Drive Link](https://drive.google.com/drive/folders/1bxINfhxSll6MKwqg28uDYVZY50YsaTab)

---

