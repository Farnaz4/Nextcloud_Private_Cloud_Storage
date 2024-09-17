# Nextcloud_Private_Cloud-based_Storage
Building a private storage solution using nextcloud hosted in amazon EC2. The storage connected with nextcloud is Amazon S3.



Step 1: 
Create an EC2 server with specific allocations. We allocated a fixed IP to the EC2 server and downloaded the key to connect it through PUTTY.

Step 2:

1. Update and Upgrade the Ubuntu Packages

*apt update -y && apt upgrade -y*

2. install Apache and MySQL Server

*apt install apache2 mariadb-server*

3. Install PHP and other Dependencies and Restart Apache

*apt install libapache2-mod-php php-bz2 php-gd php-mysql php-curl php-zip \
php-mbstring php-imagick php-ctype php-curl php-dom php-json php-posix \
php-bcmath php-xml php-intl php-gmp zip unzip wget*


4. Enable required Apache modules and restart Apache:

*a2enmod rewrite dir mime env headers*
*systemctl restart apache2*


Step2. Configure MySQL Server
1. Login to MySQL Prompt, Just type

*mysql*

2. Create MySQL Database and User for Nextcloud and Provide Permissions.

*CREATE USER 'nextcloud'@'localhost' IDENTIFIED BY 'passw@rd';
CREATE DATABASE IF NOT EXISTS nextcloud CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextcloud'@'localhost';
FLUSH PRIVILEGES;
quit;*

Step3. Download, Extract, and Apply Permissions.
Now download the latest Nextcloud archive file, Go to the Nextcloud Download Page. Or you can download from direct link: https://download.nextcloud.com/server/releases/latest.zip

1. Download and unzip in the /var/www folder

*cd /var/www/
wget https://download.nextcloud.com/server/releases/latest.zip
unzip latest.zip*

2. Remove the zip file, which is not necessary now.

*rm -rf latest.zip*

3. Change the ownership of the nextcloud content directory to the HTTP user.

*chown -R www-data:www-data /var/www/nextcloud/*

-----------------------------------------------------------------------------------
1. Run the CLI Command

*cd /var/www/nextcloud
sudo -u www-data php occ  maintenance:install --database \
"mysql" --database-name "nextcloud"  --database-user "nextcloud" --database-pass \
"passw@rd" --admin-user "admin" --admin-pass "admin123"*

If everything goes well the command will output “Nextcloud was successfully installed”. We provided a very simple user/password, during production setup, this must be a complex password.

2. nextcloud allows access only from localhost, it could through error “Access through untrusted domain”. we need to allow accessing nextcloud by using ip or domain name.


*vi /var/www/nextcloud/config/config.php*

  
*'trusted_domains' =>
  array (
    0 => 'localhost',
    1 => 'nc.mailserverguru.com',   // we Included the Sub Domain
  ),
  .....
:x    // saving the file*


3. Configure Apache to load Nextcloud from the /var/www/nextcloud folder.


*vi /etc/apache2/sites-enabled/000-default.conf*

*<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/nextcloud
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>*

*:x*
Now, Restart Apache Server
systemctl restart apache2
Now, Go to the Browser and type http:// [ ip or fqdn ] of the server, The below Nextcloud login page will appear.








