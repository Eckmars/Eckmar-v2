
---

# Eckmar's Marketplace Script v2.0

## Introduction

This tutorial provides instructions for installing Eckmar's Marketplace Script v2.0. Please note that software requirements may change, so always look for the latest versions online. You are free to host the Marketplace on any server that meets the specified requirements.

## Server Requirements

- VPS with at least 2GB of RAM
- Daemon for each enabled coin on the marketplace

## Software Requirements

- PHP7 (7.2 recommended)
- SQL Database (MySQL, PostgreSQL, SQLite, SQL Server)
- Elasticsearch (for search functionality)
- Redis (optional but recommended for improved performance)

## Installation Instructions

This tutorial is based on Ubuntu 18.04.

### Nginx

Install Nginx:

```bash
sudo apt-get update
sudo apt-get install nginx
sudo ufw allow 'Nginx HTTP'
```

### MySQL

Install MySQL and secure the installation:

```bash
sudo apt-get install mysql-server
mysql_secure_installation
mysql -u root -p
```

Create the Marketplace database:

```sql
CREATE DATABASE marketplace DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
exit
```

### PHP

Install PHP and edit php.ini:

```bash
sudo apt-get install php7.2-fpm php-mysql
sudo nano /etc/php/7.2/fpm/php.ini
```

Uncomment the line `cgi.fix_pathinfo=1` and set the value to `cgi.fix_pathinfo=0`.

Restart php-fpm:

```bash
sudo systemctl restart php7.2-fpm
```

Install required PHP extensions, composer, and unzip tools:

```bash
sudo apt-get install php7.2-mbstring php7.2-xml php7.2-xmlrpc php7.2-gmp php7.2-curl php7.2-gd composer unzip -y
```

### Elasticsearch

Install Oracle JDK and Elasticsearch:

```bash
sudo add-apt-repository ppa:webupd8team/java
sudo apt update
sudo apt install oracle-java8-installer
sudo update-alternatives --config java
```

Edit environment file:

```bash
sudo nano /etc/environment
```

Add:

```bash
JAVA_HOME="/usr/lib/jvm/java-8-oracle/jre/bin/java"
```

Reload the environment file:

```bash
source /etc/environment
echo $JAVA_HOME
```

Install Elasticsearch:

```bash
wget https://download.elastic.co/elasticsearch/release/org/elasticsearch/distribution/deb/elasticsearch/2.3.1/elasticsearch-2.3.1.deb
sudo dpkg -i elasticsearch-2.3.1.deb
```

Start Elasticsearch:

```bash
sudo service elasticsearch start
```

### Redis

(Optional but recommended for improved performance)

Install Redis and configure:

```bash
sudo apt-get install redis-server
sudo nano /etc/redis/redis.conf
```

Change `supervised` from `no` to `systemd`. Reload Redis:

```bash
sudo systemctl restart redis.service
```

Check if Redis is running:

```bash
redis-cli
```

### Node and NPM

Install NodeJs and NPM:

```bash
sudo apt-get install -y nodejs
sudo apt-get install -y npm
```

### Files

Copy Marketplace files to the server in a new directory within `/var/www`.

### Permissions

Set permissions for files:

```bash
sudo chown -R www-data:www-data /var/www/DIRECTORY_NAME/public
sudo chmod 755 /var/www
sudo chmod -R 755 /var/www/DIRECTORY_NAME/bootstrap/cache
sudo chmod -R 755 /var/www/DIRECTORY_NAME/storage
```

Link public directory with storage:

```bash
php artisan storage:link
sudo mkdir /var/www/DIRECTORY_NAME/storage/public/products
```

### Nginx Config

Edit Nginx config:

```bash
sudo nano /etc/nginx/sites-available/default
```

Example configuration:

```nginx
server {
    listen 80;
    listen [::]:80;
    listen 443;
    listen [::]:443;
    root /var/www/market/public;
    index index.php index.html index.htm index.nginx-debian.html;
    server_name domain.com;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/run/php/php7.2-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

Check the configuration:

```bash
sudo nginx -t
```

### Installation

Change to the directory where Marketplace files are located and run:

```bash
composer install
npm install
npm run prod
cp .env.example .env
php artisan key:generate
```

Open the `.env` file and insert database connection details.

Example database configuration:

```dotenv
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=marketplace
DB_USERNAME=root
DB_PASSWORD=password
CACHE_DRIVER=redis
```

Migrate the database:

```bash
php artisan migrate
```

Create dummy data (optional):

```bash
```

If both commands ran successfully, your basic marketplace is now operational. Congratulations!

### Connecting Coins

Configure connection parameters for supported coins in the `.env` file:

- Bitcoin: BITCOIND_HOST=server_ip
- Litecoin: LITECOIN_PASSWORD=password
- Monero: MONERO_USER=username, MONERO_PASSWORD=password
- Pivx: PIVX_USER=username, PIVX_PASSWORD=password
- Dash: DASH_PASSWORD=password
- Verge: VERGE_USER=username, VERGE_PASSWORD=password
- Bitcoin Cash: BITCOIN_CASH_USER=username, BITCOIN_CASH_PASSWORD=password

### Marketplace Configuration

Marketplace configuration is split into multiple files located in the `config` folder. The main configuration file is `marketplace.php`, and you can find additional configuration options in `experience.php` and `coins.php` for levels, experience, and profit addresses.

### Installation Support
If you prefer a hassle-free installation process with dedicated support, is ready to assist you. For just $50, you can take advantage of our paid installation support service. This service ensures a seamless setup of Eckmar's Marketplace Script v2.0 on your server, allowing you to focus on your digital ventures without the complexities of installation.

### 🌐 Visit Our Platform
Ready to elevate your digital ventures? Head over to **BotDigit.com/Eckmar** for a seamless experience. Discover the power of premium scripts and personalized support.


---

