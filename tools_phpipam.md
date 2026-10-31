# [UNUSED] PHPIpam
### Ensure apache2 installed

```
sudo apt install apache2
```

### Install PHP

PHPIpam require php7.2. Because Ubuntu 22.04 repository doesn't provide php 8 or lower, so we must add official php repository that provide php7.2.

```bash
sudo add-apt-repository ppa:ondrej/php
sudo apt update
```

This is special case, where I accidentally install php8.1 instead of php7.2. We can switch php version with this guide

```bash
# Assuming you already add php repository above
sudo apt install php7.2
# Disable php 8.1
sudo a2dismod php8.1
# Enable php 7.2
sudo a2enmod php7.2
# Switch from update-alternatives
sudo update-alternatives --set php /usr/bin/php7.2
# Validate version
php -v
```

Install php required modules

```bash
apt -y install php7.2-{mysql,curl,gd,intl,imap,memcache,pspell,tidy,xmlrpc,mbstring,gmp,json,xml,fpm} php-pear
```

### Install mariadb

```bash
# Install mariadb
sudo apt install mariadb-server mariadb-client
# configure mariadb
sudo mysql_secure_installation
# Create database
mysql -u root -p
CREATE DATABASE phpipam;
GRANT ALL ON phpipam.* TO phpipam@localhost IDENTIFIED BY 'StrongDBPassword';
FLUSH PRIVILEGES;
QUIT;
```

### Install PHPIpam & Configure

Download phpipam

```bash
cd /var/www/html
sudo wget "https://jaist.dl.sourceforge.net/project/phpipam/v1.5.2/phpipam-v1.5.2.zip"
sudo unzip phpipam-v1.5.2.zip
cd phpipam
sudo cp config.dist.php config.php
```

Configure database connection to phpipam

```bash
sudo vi config.php
# Edit config.php database connection to mariadb that previously installed
/**
* database connection details
******************************/
$db['host'] = 'localhost';
$db['user'] = 'phpipam';
$db['pass'] = 'StrongDBPassword';
$db['name'] = 'phpipam';
$db['port'] = 3306;

# Save and exit
```

### Configure apache2

```bash
sudo a2dissite 000-default.conf
sudo a2enmod rewrite

# Install apache2 modules
sudo apt -y install libapache2-mod-php php-curl php-xmlrpc php-intl php-gd

# Add site configuration
sudo vim /etc/apache2/sites-available/phpipam.conf
# phpipam.conf content
<VirtualHost *:80>
    DocumentRoot "/var/www/html/phpipam"
    ServerName ipam.playground.lokal
    ServerAlias www.ipam.playground.lokal
    <Directory "/var/www/html/phpipam">
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    ErrorLog "/var/log/apache2/phpipam-error_log"
    CustomLog "/var/log/apache2/phpipam-access_log" combined
</VirtualHost>
# Save and exit
# Set directory permission
sudo chown -R www-data:www-data /var/www/html
# Enable site and restart apache2
sudo a2ensite phpipam
sudo systemctl restart apache2
```

### Import DB

```bash
sudo mysql -u root -p phpipam < /var/www/html/phpipam/db/SCHEMA.sql
```

### Enjoy

Go check http://ipam.playground.lokal

```bash
# Default credentials
Username: admin
Password: ipamadmin
```

### Hosts availability check

```bash
# Check pcntl
php -m | grep pcntl

# Add cron script
sudo crontab -e
## Fill with bellow command
*/15 * * * * /usr/bin/php /var/www/html/phpipam/functions/scripts/pingCheck.php
*/15 * * * * /usr/bin/php /var/www/html/phpipam/functions/scripts/discoveryCheck.php
## Save

# Check crontab list
sudo crontab -l
```

### Source

* https://computingforgeeks.com/install-and-configure-phpipam-on-ubuntu-debian-linux/?expand_article=1
* https://phpipam.net/documents/installation/
* https://ostechnix.com/how-to-switch-between-multiple-php-versions-in-ubuntu/
* https://phpipam.net/news/automatic-host-availability-check/