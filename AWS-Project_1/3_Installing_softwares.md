### ✅ **1. Connect to EC2 & Become Root**

You're logged in as a normal user (e.g., `ubuntu`). To avoid typing `sudo` every time:

```bash
sudo -i
```

Now you're root and don't need to prefix commands with `sudo`.

---

### ✅ **2. Install Apache Web Server**

```bash
apt install apache2 -y
```

This installs Apache2 and starts the web server.

---

### ✅ **3. Confirm Apache is Working**

* Copy your **EC2 Public IP**
* Paste it into a browser using HTTP (e.g., `http://<your-ip>`)
* You should see the **Apache2 default page**

---

### ✅ **4. Navigate to Default Web Directory**

```bash
cd /var/www/html
ls
```

You should see the default `index.html` file.

---

### ✅ **5. Replace Default Website with PHP File**

* Remove the default index:

  ```bash
  rm index.html
  ```
* Create a new `index.php`:

  ```bash
  nano index.php
  ```
* Paste your PHP code inside
* Save with `Ctrl + S`, exit with `Ctrl + X`
  (If `nano` isn’t working, use `vim` or `vi` and type `:wq` to save and exit)

If copy/paste isn't working:

* Use **Ctrl + Shift + V** in most terminals to paste.

---

### ✅ **6. Install PHP (Legacy Version 5.6)**

#### ➤ Add required PHP repository:

```bash
add-apt-repository ppa:ondrej/php
apt update
```

> This allows installation of legacy PHP versions (like 5.6) that aren't in default Ubuntu repos.

---

### ✅ **7. Install PHP 5.6 and Required Packages**

```bash
apt install php5.6 mysql-client php5.6-mysql -y
```

**Explanation of Packages:**

* `php5.6`: Core PHP interpreter
* `mysql-client`: For accessing MySQL databases
* `php5.6-mysql`: PHP extension to interact with MySQL

---

### ✅ **8. Verify PHP is Working**

* Reload your browser: `http://<your-ip>`
* If the PHP file is correct and Apache has `php` module, you'll see the expected PHP output.

---

### 🛠️ **Troubleshooting Tips**

* If you see **PHP code as plain text**, run:

  ```bash
  a2enmod php5.6
  systemctl restart apache2
  ```
* If errors show up, they might be due to missing PHP modules or incorrect PHP syntax.

---
