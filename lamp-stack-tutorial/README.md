# Installation and Configuration of LAMP Stack on Ubuntu

A step-by-step guide to installing and configuring a LAMP stack (Linux, Apache, MySQL, PHP) on an Ubuntu EC2 instance, including database connectivity verification.

📄 [Full tutorial PDF](./Installation_and_Configuration_of_LAMP_Stack.pdf)

---

## What is a LAMP Stack?

A LAMP stack is a group of four open-source components used to build dynamic websites and web applications:

- **L** — Linux (operating system)
- **A** — Apache (web server)
- **M** — MySQL (database management system)
- **P** — PHP (programming language)

Linux provides the operating environment, Apache handles web requests, MySQL stores and manages data, and PHP creates dynamic web pages. It's widely used because it's reliable, cost-effective, flexible, and easy to deploy.

---

## Step-by-Step Implementation

### Step 1: Launch an Ubuntu Server on EC2

- **AMI**: Ubuntu Server (latest LTS version)
- **Instance Type**: `t2.micro` or `t3.micro`
- **Key Pair**: create or select an existing one for SSH access
- **Network Settings**: allow SSH (port 22) and HTTP (port 80)

Keep the remaining settings as default and launch the instance.

### Step 2: Connect to the EC2 Instance

Select the launched instance and click **Connect**.

### Step 3: Install Apache Web Server

Update packages and install Apache:
```bash
sudo apt update && sudo apt -y upgrade
sudo apt install -y apache2
```

Paste the instance's public IP into a browser — the default Apache page confirms a successful install.

### Step 4: Install and Configure the Database Server

Install MySQL:
```bash
sudo apt install -y mysql-server
sudo mysql_secure_installation
```

During setup, choose:
- Skip the password validation component (not required)
- Remove anonymous users: **Yes**
- Disallow root login remotely: **Yes**
- Reload privilege tables: **Yes**

Log in and set up the database:
```bash
sudo mysql -u root -p
```
```sql
create database test_db;
show databases;
create user 'test_user'@'localhost' identified by 'Example_Password';
grant all privileges on test_db.* to 'test_user'@'localhost';
flush privileges;
exit;
```
> Replace `Example_Password` with your own secure password.

### Step 5: Install PHP

Install PHP and common extensions:
```bash
sudo apt install -y php
sudo apt install -y php-{common,mysql,xml,xmlrpc,curl,gd,imagick,cli,dev,imap,mbstring,opcache,soap,zip,intl}
sudo systemctl restart apache2
```

Create a test file to confirm PHP is working:
```bash
sudo nano /var/www/html/info.php
```
```php
<?php
phpinfo();
```

Visit `http://<public-ip>/info.php` in a browser — a PHP info page confirms it's working.

### Step 6: Verify PHP–MySQL Connectivity

Create a test script:
```bash
sudo nano /var/www/html/database_test.php
```
```php
<?php
$conn = new mysqli('localhost', 'test_user', 'Example_Password', 'test_db');
if ($conn->connect_error) {
    die("Database connection failed: " . $conn->connect_error);
}
echo "Database connection was successful";
```
> Replace `Example_Password` with the password you set earlier.

Visit `http://<public-ip>/database_test.php` — a "Database connection was successful" message confirms everything is wired up correctly.

---

## Conclusion

The LAMP stack was successfully installed and configured on an Ubuntu EC2 instance — Apache as the web server, MySQL as the database, and PHP as the scripting language. This included launching and connecting to the instance, installing and securing each component, creating a database and user, and verifying PHP-to-MySQL connectivity end to end. This setup demonstrates how a LAMP stack can be deployed on a cloud-based Ubuntu server to host dynamic, database-driven web applications.
