---

## 🔧 Step 5: Change Hostname in Website (PHP) to Point to RDS

When you migrate your MySQL database to Amazon RDS, your website (PHP) needs to **connect to the new RDS database instance** instead of a local MySQL server. This requires updating the **hostname** in your PHP code where the database connection is established.

---

### ✅ Steps to Change the Hostname

#### 1. **Get the RDS Endpoint (Hostname)**

* Go to the **AWS Console > RDS > Databases**
* Click on your database instance (named as per your setup).
* Copy the **Endpoint** (e.g., `your-db-instance.abc123xyz.us-east-1.rds.amazonaws.com`)
* Note the **port** (default for MySQL is `3306`)

#### 2. **Update Your PHP Code**

Your PHP website likely has a file for database connection, such as:

* `config.php`, `db.php`, or inside `index.php`
* It might look like this:

```php
<?php
$host = "localhost";
$user = "root";
$password = "yourpassword";
$database = "intel";

$conn = mysqli_connect($host, $user, $password, $database);
?>
```

#### 🔁 Change `localhost` to the RDS endpoint:

```php
<?php
$host = "your-db-instance.abc123xyz.us-east-1.rds.amazonaws.com"; // RDS endpoint
$user = "admin";        // RDS Master username (use what you set)
$password = "intel123"; // RDS password (as per the problem statement)
$database = "intel";

$conn = mysqli_connect($host, $user, $password, $database);

// Optional: Check connection
if (!$conn) {
    die("Connection failed: " . mysqli_connect_error());
}
?>
```

---

### 💡 Notes

* **Don't forget to allow traffic from EC2 to RDS** (you do this by modifying the RDS security group to allow inbound MySQL traffic from EC2's security group).
* Make sure **PHP has `mysqli` or `PDO` installed**, depending on what your code uses.

---
