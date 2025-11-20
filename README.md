# Docker Nginx + PHP + MySQL + PHPMyAdmin

## Основные команды/Установка/Использование

#### Запустить команду
```
docker-compose build
```
#### Запустить контейнеры
```
docker-compose up
```
#### Запустить без блокировки терминала
```
docker-compose up --build -d
```
#### Перезапуск контейнеров (после изменений в настройках)
```
docker-compose restart
```
#### Запуск/остановка/перезапуск определенного контейнера
```
docker container start/stop/restart container_name
```
#### Запуск командной строки внутри контейнера
```
docker exec -i -t container_name bash
```
## Куда публиковать сайты?
Сайты публиковать в папку `src/public`

## Куда создавать виртуальные хосты?
Виртуальные хосты создавать в папке `nginx/conf.d`

## PHPMyAdmin
Чтобы запустить PMA, в адресной строке нужно набрать http://localhost:8000

По умолчанию, настроены доступы:
- Сервер: `mysql`
- Логин: `root`
- Пароль: `123456`

## Как импортировать огромную базу данных в таблицу
Если база данных весит очень много и через PMA никак, то можно импортировать через терминал mysql
- Закидываем необходимую БД в папку `mysql/databases`
- Перезапускаем контейнер mysql ```docker container restart tools_php-mysql```
- Запускаем командную строку внутри контейнера mysql ```docker exec -i -t tools_php-mysql bash```
- Выполняем команду ```mysql -u root -p DBNAME < /var/www/mysql_db/DBNAME.sql```
- Ждем выполнения и готово