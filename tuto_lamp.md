Here's a step-by-step guide to install **LAMP (Linux, Apache, MySQL/MariaDB, PHP)** stack and **Nextcloud** on an Ubuntu server.

### **1\. Install LAMP Stack**

#### 1.1 **Update Package Index**

> Start by updating your package repository:

```
sudo apt update
```

#### 1.2 **Install Apache Web Server**

> Install Apache:

```
sudo apt install apache2 -y
```

> Once installed, you can check if Apache is running by visiting your server's IP address in a web browser (e.g., `http://your-server-ip`). You should see the Apache default page.

#### 1.3 **Install MySQL or MariaDB**

> Next, install MariaDB (which is a fork of MySQL):

```
sudo apt install mariadb-server mariadb-client -y
```

> Start the MariaDB service and enable it to start on boot:

```
sudo systemctl start mariadb
sudo systemctl enable mariadb
```

#### 1.4 **Secure MariaDB Installation**

> Run the `mysql_secure_installation` script to secure your MariaDB installation and set a root password:

```
sudo mysql_secure_installation
```
> Follow the prompts to improve security.

#### 1.5 **Install PHP**

> Install PHP and required extensions for Nextcloud:

```
sudo apt install php php-mysql libapache2-mod-php php-gd php-curl php-xml php-zip php-mbstring php-bcmath php-ldap php-imagick php-intl php-xmlrpc php-redis -y
```

#### 1.6 **Restart Apache**

> Restart Apache to ensure all changes are applied:
```
sudo systemctl restart apache2
```

### **2\. Install Nextcloud**

#### 2.1 **Create a Database for Nextcloud**

> Log in to MariaDB and create a database for Nextcloud:

```
sudo mysql -u root -p
```

> Create the database and user:

```
CREATE DATABASE nextcloud;
CREATE USER 'nextclouduser'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextclouduser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

#### 2.2 **Download Nextcloud**

> Navigate to your web root directory and download Nextcloud:

```
cd /var/www/ 
sudo wget https://download.nextcloud.com/server/releases/nextcloud-30.0.5.zip
```

> Unzip the Nextcloud archive:

```
sudo apt install unzip
sudo unzip nextcloud-30.0.5.zip
```

> Change the ownership of the Nextcloud directory:

```
sudo chown -R www-data:www-data /var/www/nextcloud
```

#### 2.3 **Set Up Apache for Nextcloud**

> Create a new Apache configuration file for Nextcloud:

```
sudo nano /etc/apache2/sites-available/nextcloud.conf
```

> Add the following configuration:


```
<VirtualHost *:80>
    DocumentRoot /var/www/nextcloud
    ServerName yourdomain.com

    <Directory /var/www/nextcloud/>
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

>Make sure to replace `yourdomain.com` with your actual domain or IP address.

>Enable the new site and necessary Apache modules:

```
sudo a2ensite nextcloud.conf
sudo a2enmod rewrite headers env dir mime
```

> Restart Apache:

```
sudo systemctl restart apache2
```

```
sudo -u www-data php /var/www/nextcloud/occ maintenance:install --database "mysql" --database-name "nextcloud" --database-user "nextclouduser" --database-pass "password" --admin-user "admin" --admin-pass "adminpassword"
```

Replace `nextclouduser` and `password` with the database credentials you created earlier, and set your desired **admin** username and password for Nextcloud.
