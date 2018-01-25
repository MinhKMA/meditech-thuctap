

# Install cachet on centOS7

- update

    ``yum -y update``

- install the LAMP

    ``yum -y install httpd mod_rewrite mariadb-server mariadb git``

- install epel-release

    ```sh
    yum -y install epel-release
    yum -y update
    ```

- install Webtatic repository

    ```sh
    rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
    yum -y update
    ```

- install PHP 7.1 and all the required PHP modules

    ``yum -y install php71w php71w-openssl php71w-mysql php71w-cli php71w-mbstring php71w-dom php71w-gd php71w-simplexml php71w-mcrypt php71w-xml php71w-tokenizer``

- check the version of PHP

    ``php -v``

- start apache 

    ```sh 
    systemctl start httpd 
    systemctl enable httpd
    ```

- install Memcached

    ``yum -y install memcached``

- start memcached

    ```sh
    systemctl enable memcached 
    systemctl start memcached
    ```

- edit ``/etc/my.cf`` file, add the following line at the end of the file

    ``default-time-zone='+07:00'``

- start mariaDB 

    ```sh
    systemctl start mariadb
    systemctl enable mariadb
    ```

- set password for mariaDB 

    ``mysql_secure_installation``

    ```sh
    Enter current password for root (enter for none):
    ...
    Set root password? [Y/n] y
    New password: 
    Re-enter new password: 
    ...
    Remove anonymous users? [Y/n] y
    Disallow root login remotely? [Y/n] y
    Remove test database and access to it? [Y/n] y
    Reload privilege tables now? [Y/n] y
    ...
    ```

- create a database 

    ```sh
    mysql -u root -p
    CREATE DATABASE cachet_data;
    CREATE USER 'cachet_user'@'localhost' IDENTIFIED BY 'StrongPassword';
    GRANT ALL PRIVILEGES ON cachet_data.* TO 'cachet_user'@'localhost';
    FLUSH PRIVILEGES;
    exit
    ```

- install Composer

    ```sh
    curl -sS https://getcomposer.org/installer | php
    mv composer.phar /usr/bin/composer
    ```

- switch to the webroot directory of Apache web server and download Cachet

    ```sh
    cd /var/www 
    git clone https://github.com/cachethq/Cachet.git cachet
    cd cachet
    git tag -l
    git checkout v2.3.11

- copy the example environment configuration file

    ``cp .env.example .env``

- update the lines with the database, database username, and password, port db in ``.env`` file 

    ```sh
    DB_DATABASE=cachet_data
    DB_USERNAME=cachet_user
    DB_PASSWORD=StrongPassword
    DB_PORT=3306
    ```

- install the required Composer dependencies

    ``composer install --no-dev -o``

- gen key 

    ``php artisan key:generate``

- Once the key is generated, run the Cachet installer

    ``php artisan app:install``

- create a virtual host

    ``/etc/httpd/conf.d/status.yourdomain.com.conf``

    ```sh
    <VirtualHost *:80>
    ServerAdmin me@liptanbiswas.com
    DocumentRoot "/var/www/cachet/public"
    ServerName status.yourdomain.com
    ServerAlias www.status.yourdomain.com
    <Directory "/var/www/cachet/public">
        Options Indexes FollowSymLinks
        AllowOverride All
        Order allow,deny
        Allow from all
        Require all granted
    </Directory>
    ErrorLog "/var/log/httpd/status.yourdomain.com-error_log"
    CustomLog "/var/log/httpd/status.yourdomain.com-access_log" combined
    </VirtualHost>
    ```

- restart httpd

    ``systemctl restart httpd``

- Now you will need to provide the ownership of the application to web server

    ``chown -R apache:apache /var/www/cachet``

- disable firewalld and selinux 

    ```sh
    sed -i 's/\(^SELINUX=\).*/\SELINUX=disabled/' /etc/sysconfig/selinux
    sed -i 's/\(^SELINUX=\).*/\SELINUX=disabled/' /etc/selinux/config
    setenforce 0
    ```

- go to the link 

    ``http://status.yourdomain.com``

<img src="https://i.imgur.com/L644Fg8.png">

<img src="https://i.imgur.com/AYk698y.png">

<img src="https://i.imgur.com/pHoeJ3l.png">

<img src="https://i.imgur.com/KqT5UVL.png">

