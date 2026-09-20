
---

## **List of Vulnerabilities in the Death Note Machine**

### 1. **Incorrect DNS Configuration / Internal Domain Name Disclosure**

* **Where Found:** In the source code of the page on port 80, there is a hint `deathnote.vuln`.
* **Issue:** The system relies on local DNS, but the domain is explicitly mentioned in the site’s code and can be easily resolved via `/etc/hosts`.
* **Risk:** An attacker can gain access to internal resources or hidden pages by spoofing DNS records.
* **Solution:** Do not expose sensitive domain names in public code or use virtual hosts with proper authentication.

---

### 2. **Directory Listing Enabled**

* **Where Found:** Access to a directory with `user.txt` and `notes.txt` files via the browser.
* **Issue:** Directory listing is enabled on the web server, allowing anyone to view the contents of folders.
* **Risk:** An attacker can find sensitive files (lists of logins, passwords).
* **Solution:** Disable directory listing on the web server (e.g., for Apache — `Options -Indexes`).

---

### 3. **Exposed Configuration Files (robots.txt)**

* **Where Found:** The `robots.txt` file contains a path to `important.jpg`.
* **Issue:** `robots.txt` is intended for search engine crawlers but is often misused to hide files. Attackers always check it for hidden paths.
* **Risk:** Possibility of accessing sensitive information through hidden files.
* **Solution:** Do not store sensitive paths in `robots.txt`; use authentication instead.

---

### 4. **Passwords Stored in Plaintext**

* **Where Found:** Passwords are stored in plaintext in `notes.txt` and other files.
* **Issue:** There is no encryption or hashing of passwords.
* **Risk:** Passwords can be used for brute-force attacks or unauthorized authentication.
* **Solution:** Use encryption (bcrypt, Argon2) and prohibit storing passwords in plaintext.

---

### 5. **Weak or Reused Passwords**

* **Where Found:** The password `kiraisevil` works for both the regular user `kira` and `root`.
* **Issue:** The same password is reused across multiple accounts.
* **Risk:** Compromise of one account leads to full system access.
* **Solution:** Require different passwords for root and regular users, enforce strong password policies.

---

### 6. **No Authentication Attempt Limitation (Brute Force)**

* **Where Found:** `hydra` successfully brute-forces the SSH password with no IP blocking.
* **Issue:** The SSH service has no limit on failed login attempts.
* **Risk:** Enables automated brute-force attacks.
* **Solution:** Use `fail2ban`, two-factor authentication, or rate-limiting for logins.

---

### 7. **Lack of Privilege Segregation**

* **Where Found:** The `kira` user password works for `sudo -i`.
* **Issue:** The user immediately gets root access without any additional restrictions.
* **Risk:** Anyone with the `kira` password gets full system control.
* **Solution:** Use `sudoers` with precise rules, and disable direct root access.

---

### 8. **Information Disclosure in Source Code and Files**

* **Where Found:** HTML source code, `case.wav`, and messages in `user.txt` reveal hints about passwords and paths.
* **Issue:** Public files contain internal or sensitive information.
* **Risk:** Attackers gain additional attack vectors.
* **Solution:** Remove internal information before deployment.

---

