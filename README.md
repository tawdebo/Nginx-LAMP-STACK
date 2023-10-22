# Nginx-LAMP-STACK
NGINX LEMP Stack Setup PHP 8.1, Nginx, MySQL, Git, Composer


#  LEMP Stack Setup - Ubuntu
====Install Nginx =====
```
sudo apt update
sudo apt install nginx
sudo ufw status
sudo ufw app list
```

i.	sudo ufw allow 'Nginx Full'
ii.	sudo ufw allow 'OpenSSH'

Check Ip Address
	ip addr show
	curl icanhazip.com

#  ==== Install MySQL ====

```
CREATE USER 'blog'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password_here';
GRANT ALL ON blog.* TO 'blog'@'localhost';

sudo apt install mysql-server
sudo mysql_secure_installation 
Add new Database and User
sudo mysql
   CREATE DATABASE example_database;
   CREATE USER 'username'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
   GRANT ALL ON example_database.* TO 'username'@'%';
   mysql -u example_user -p
   SHOW DATABASES;
```

#  === Install PHP 8.1 ====

Update machine 

```
sudo apt update && sudo apt upgrade -y  
```


#Install dependencies

```
sudo apt install software-properties-common apt-transport-https -y  
#Add the PPA
sudo add-apt-repository ppa:ondrej/php -y  
#Now to install PHP 8.1 FPM and its modules 
sudo apt install php8.1-fpm php8.1-common php8.1-mysql php8.1-xml php8.1-xmlrpc php8.1-curl php8.1-gd php8.1-imagick php8.1-cli php8.1-dev php8.1-imap php8.1-mbstring php8.1-opcache php8.1-soap php8.1-zip php8.1-intl php8.1-bcmath
```


#  ====Install Git ====

```
sudo apt install git
```

# ===Install Composer ====


```
sudo apt install wget php-cli php-zip unzip
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
HASH="$(wget -q -O - https://composer.github.io/installer.sig)"
php -r "if (hash_file('SHA384', 'composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
composer
```


# === Deploy Laravel ===

1.	Direct file upload
2.	Using git

Using git

```
ssh-keygen -t rsa -b 4096 -C "username@something.com"
cat ~/root/.ssh/id_rsa.pub
git clone ssh_project_url project
cd project
composer install
cp .env.example .env
php artisan key:generate
nano .env
chown -R www-data:www-data storage/
chown -R www-data:www-data bootstrap/
php artisan migrate
```



# ==== nginx server setup ====
cd /etc/nginx/sites-available
nano default

server {
    server_name ip_address_here;
    root /var/www/html/project/public;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.html index.htm index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
