# PHP Application Setup

## Clone the Project

```bash
git clone <repo-url>
cd <directory-name>
```

## Install PHP and Required Packages

```bash
sudo apt update -y
sudo apt install php php-fpm php-mysql -y
```

## Verify and Start PHP

```bash
php -v
sudo systemctl start php8.5-fpm
sudo systemctl status php8.5-fpm
sudo systemctl enable php8.5-fpm
```

## Add DB Credentials as Environment Variables in PHP-FPM

Edit the PHP-FPM pool configuration:

```bash
sudo nano /etc/php/8.5/fpm/pool.d/www.conf
```

Find the existing `env[...]` lines and add the following:

```ini
env[DB_HOST] = <DB_HOST>
env[DB_USER] = <DB_USER>
env[DB_PASS] = <DB_PASS>
env[DB_NAME] = <DB_NAME>
```

For example:

```ini
env[DB_HOST] = 127.0.0.1
env[DB_USER] = admin
env[DB_PASS] = <DB_PASSWORD>
env[DB_NAME] = student
```

Save the file:

```text
Ctrl + O
Enter
Ctrl + X
```

## Create a Directory for Application on Nginx Path

```bash
sudo mkdir -p /var/www/html/php
```

## Copy Your PHP Application

```bash
sudo cp -r * /var/www/html/php/
```

## Edit the Nginx Default Conf

```bash
sudo nano /etc/nginx/sites-available/default
```

## replace this 

```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html/php;
    index index.php index.html index.htm;

    server_name _;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.5-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

## Test and Reload Nginx & PHP

```bash
sudo nginx -t
sudo systemctl reload nginx
sudo systemctl restart php8.5-fpm.service
```

## Access Website with EC2 IP

```text
http://<ec2-publicip>
```
