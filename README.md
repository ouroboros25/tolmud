# Толмуд

Справочник и база знаний

# DEVops

### 1. Генерация и скачивание дампа со стороннего сервера Mysql 

##### 1.1 Генерация дампа на серверею Дамп упадет в корневую папку

```bash
mysqldump -u main -p boardroom > data_dump.sql --no-tablespaces
```
##### 1.2 Скачивание дампа на локальную машину с помощью scp
```bash
scp -i "ecommerce-boardroom.pem" ubuntu@ec*-**-***-**-***.compute-1.amazonaws.com:/var/www/html/data_dump.sql /Users/admin/projects

/Users/admin/projects - папка на локальной машине
/var/www/html/data_dump.sql - путь к файлу на сервере
-i "ecommerce-boardroom.pem" - ключ
ubuntu@ec*-**-***-**-***.compute-1.amazonaws.com - логин и хост
```
##### 1.3 Скачивание дампа на локальную машину с помощью scp
C помощью Mysql Workbench или подобных гуи подключится к серверу и залить импорт
### 2. Наcтройка Apache на AWS EC2
##### 2.1 .htaccess в корне проекта
```bash
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteRule ^(.*)$ public/$1 [L]
</IfModule>
```
##### 2.2 .htaccess в public
```bash
Options +FollowSymLinks -Indexes
RewriteEngine On

RewriteCond %{HTTP:Authorization} .
RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^ index.php [L]
```
##### 2.3 /etc/apache2/sites-available/названиеконфига.conf
```bash
<VirtualHost *:80>
        # The ServerName directive sets the request scheme, hostname and port that
        # the server uses to identify itself. This is used when creating
        # redirection URLs. In the context of virtual hosts, the ServerName
        # specifies what hostname must appear in the request's Host: header to
        # match this virtual host. For the default virtual host (this file) this
        # value is not decisive as it is used as a last resort host regardless.
        # However, you must set it for any further virtual host explicitly.
        #ServerName www.example.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/SmartManagerBackend/public

        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
        #LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        <Directory "/var/www/SmartManagerBackend/public">
        AllowOverride All
        </Directory>
        # For most configuration files from conf-available/, which are
        # enabled or disabled at a global level, it is possible to
        # include a line for only one particular virtual host. For example the
        # following line enables the CGI configuration for this host only
        # after it has been globally disabled with "a2disconf".
        #Include conf-available/serve-cgi-bin.conf
</VirtualHost>
```
##### 2.4 Либы (версии php могут разниться)
```
sudo apt-get install libapache2-mod-php7.2
```

### 3 Настройки SSL (Ubuntu, EC2. Apache)

##### 3.1 CertBot - https://certbot.eff.org/instructions
##### 3.2 В AWS в Security Groups добавить разрезение на Inbound запросов для HTTPS


### 4 Nginx на примере фронтенда и бекенда на одном сервере
##### 4.1 /etc/nginx/sites-available/smartmanager.backend  
```
server {
    listen 80;
    server_name smartmanager.space; # fake IP address
    root /var/www/SmartManagerFrontend/dist; #path to static directory
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";
    index index.html index.htm index.php;
    charset utf-8;
    location / {
            try_files $uri $uri/ /index.html;
    }
    location /api {
            alias /var/www/SmartManagerBackend/public;
            try_files $uri $uri/ @laravelapi;
            location ~ \.php$ {
                    include snippets/fastcgi-php.conf;
                    fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
                    fastcgi_param SCRIPT_FILENAME $request_filename;
            }
    }
location @laravelapi {
            rewrite /api/(.*)?$ /api/index.php?$is_args$args last;
    }
location = /favicon.ico { access_log off; log_not_found off; }
location = /robots.txt  { access_log off; log_not_found off; }
error_page 404 /index.php;
location ~ /\.(?!well-known).* {
        deny all;
    }
}
```
Не работать может если:
1) Не установлен php8.1-fpm.sock или его версия неправильно указана

##### 4.2 sudo ln -s /etc/nginx/sites-available/smartmanager.space /etc/nginx/sites-enabled/
##### 4.3 sudo systemctl restart nginx