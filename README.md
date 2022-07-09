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
## Usage

```python
import foobar

# returns 'words'
foobar.pluralize('word')

# returns 'geese'
foobar.pluralize('goose')

# returns 'phenomenon'
foobar.singularize('phenomena')
```

## License
[MIT](https://choosealicense.com/licenses/mit/)