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
        DocumentRoot /var/www/SmartManagerBackend

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
