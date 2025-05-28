## ✅ Creating a MySQL RDS Database in AWS

### 🔧 Configuration as per the Requirement

**From the question:**

* **Database Engine**: MySQL
* **DB Instance Identifier**: `project-database`
* **Database Name**: `Intel`
* **Table Name**: `data`
* **Master Username**: `intel`
* **Master Password**: `Intel123` (note: it’s case-sensitive)

---

### 🧭 Steps to Create the RDS MySQL Database

1. **Go to AWS Console**:

   * Open the AWS Management Console.
   * Navigate to **RDS** (Relational Database Service) from the services menu.

2. **Create a New Database**:

   * Click on **“Create Database”**.

3. **Choose a Database Creation Method**:

   * Select **“Standard Create”**.

4. **Engine Options**:

   * Choose **MySQL** as the database engine.

5. **Edition and Version**:

   * Use the default MySQL version unless otherwise specified.

6. **Template**:

   * Choose **Free Tier** (if using personal AWS account).
   * ⚠️ If you're using **lab access**, select **Dev/Test** or **Production**, since Free Tier won't work with lab access.

7. **DB Instance Configuration**:

   * **DB Instance Identifier**: `project-database`
   * **Master Username**: `intel`
   * **Master Password**: `Intel123`
   * Re-enter the password to confirm.

8. **DB Instance Class and Storage**:

   * Leave default settings unless otherwise needed.
   * You may **disable storage autoscaling** if not required.

9. **Connectivity Settings**:

   * Choose the appropriate **VPC** (use the default if unsure).
   * **Public Access**: Set to **“No”** to keep the database secure in a **private subnet**.
   * **VPC Security Group**: Choose the existing **project1-security-group** (created earlier).

10. **Additional Configuration**:

    * **Initial Database Name**: `Intel`
    * Uncheck options like:

      * Enable Automatic Backups
      * Enable Encryption
      * Enable Auto minor version upgrades (optional)

11. **Maintenance Settings**:

    * Leave them as default, or uncheck to skip auto maintenance windows.

12. **Create Database**:

    * Scroll to the bottom and click **“Create Database”**.
    * Wait for the **status** to change from **Creating** to **Available**.

---

### 📌 Notes on Deployment Options in RDS

* **Single DB Instance (Default)**:

  * Launches the database in a **single Availability Zone (AZ)**.
  * No high availability; suitable for dev or low-priority environments.

* **Multi-AZ DB Instance**:

  * Provides **automatic failover** and replication.
  * Backup copies of the database are created in **multiple AZs**.
  * Good for production environments.

* **Multi-AZ DB Cluster**:

  * Main instance handles **write operations**.
  * **Read replicas** in other AZs improve performance by handling **read operations**.

---

### ✅ Checklist Before Creating:

| Item                 | Value                                 |
| -------------------- | ------------------------------------- |
| Engine               | MySQL                                 |
| DB Name              | Intel                                 |
| Table Name           | data (to be created later)            |
| Username             | intel                                 |
| Password             | Intel123                              |
| Instance ID          | project-database                      |
| Public Access        | No                                    |
| VPC SG               | project1-security-group               |
| Storage Autoscaling  | Disabled                              |
| Backup & Maintenance | Disabled                              |
| Template             | Free Tier (or Dev/Test if lab access) |

---

### ❗Final Reminders

* Database creation may take several minutes.
* Do not worry if options like Apache/web server setup are pending. Those steps will be completed later after the DB is created.
* Once your RDS status shows **Available**, proceed with table creation and connection configuration.

---

## ✅ Step-by-Step MySQL Setup and Web Form Integration (on EC2)

### 🔹 Step 1: Get RDS Endpoint

* Go to your RDS dashboard in AWS.
* Copy the **endpoint** of your RDS MySQL database.
* This is what you’ll use to connect from your EC2 instance.

---

### 🔹 Step 2: Connect to RDS from EC2

```bash
mysql -h <your-rds-endpoint> -u <username> -p
```

Example:

```bash
mysql -h mydb.abcxyz.us-east-1.rds.amazonaws.com -u intel -p
```

* Enter your password (e.g., `intel123`).
* You're now inside the MySQL shell.

---

### 🔹 Step 3: Use Your Database

```sql
SHOW DATABASES;
USE intel;
```

* This selects your created database.

---

### 🔹 Step 4: Check for Existing Tables

```sql
SHOW TABLES;
```

* You’ll likely see **“Empty set”**, since no tables exist yet.

---

### 🔹 Step 5: Create a Table

```sql
CREATE TABLE data (
  firstname VARCHAR(50),
  email VARCHAR(50)
);
```

* This table has two columns: `firstname` and `email`.

---

### 🔹 Step 6: Verify Table Creation

```sql
SHOW TABLES;
```

* You should see `data` as the result.

---

### 🔹 Step 7: Insert a Row Manually

```sql
INSERT INTO data (firstname, email)
VALUES ('Arctic', 'arctic@gmail.com');
```

To view data:

```sql
SELECT * FROM data;
```

---

### 🔹 Step 8: Edit Website Code (`index.php`)

```bash
nano index.php
```

Find this line:

```php
$servername = "old_value";
```

Replace it with:

```php
$servername = "your-rds-endpoint";
```

Example:

```php
$servername = "mydb.abcxyz.us-east-1.rds.amazonaws.com";
```

Save and exit:

* Press `Ctrl + O` → `Enter` to save
* Press `Ctrl + X` to exit

---

### 🔹 Step 9: Test Website Form

1. Go to your EC2 Public IP in browser.
2. Fill in the form:

   * Name: `John`
   * Email: `john@example.com`
3. Click Submit.

---

### 🔹 Step 10: Check if Data Reached RDS

Go back to your MySQL shell and run:

```sql
SELECT * FROM data;
```

You should now see both your **manually inserted data** and the **data from the website form**.

---

## ⚠️ Troubleshooting Tips

| Issue                                 | Fix                                                                                        |
| ------------------------------------- | ------------------------------------------------------------------------------------------ |
| `ERROR 1054 (42S22): Unknown column`  | Likely column names don’t match. Check your `CREATE TABLE` query and form field names.     |
| `ERROR 1062 (23000): Duplicate entry` | Your table might have a primary key that conflicts. Remove it or make it `AUTO_INCREMENT`. |
| Blank screen or error in browser      | Check the `index.php` file. Confirm `$servername` is correct and saved properly.           |
| Can’t see password typing in MySQL    | That’s normal — it’s hidden for security. Just type carefully and press Enter.             |

---

