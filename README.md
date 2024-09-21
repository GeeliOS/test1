
# Настройка Nginx по заданному ТЗ

## 1. Настройка HTTPS с сертификатом

Для работы по HTTPS вам потребуется действующий SSL-сертификат. Вы можете получить сертификат через Let's Encrypt или использовать другой удостоверяющий центр. После получения сертификата, вам нужно будет указать путь к сертификату и ключу в конфигурации Nginx.

Пример конфигурации для HTTPS:

nginx
server {
    listen 443 ssl;
    server_name yourdomain.com;  # Замените на ваш домен

    ssl_certificate /path/to/your/certificate.crt;  # Путь к SSL-сертификату
    ssl_certificate_key /path/to/your/private.key;   # Путь к приватному ключу

    location / {
        root /var/www/yourdomain;  # Путь к корневой директории вашего сайта
        index index.html index.htm;
    }
}


## 2. Принудительное перенаправление HTTP на HTTPS

Для обеспечения безопасного соединения нужно настраивать перенаправление HTTP (порт 80) на HTTPS (порт 443). Это делается в отдельном блоке `server`:

nginx
server {
    listen 80;
    server_name yourdomain.com;  # Замените на ваш домен

    # Принудительное перенаправление на HTTPS
    return 301 https://$host$request_uri;
}


## 3. Использование alias для создания псевдонимов путей

Вы можете использовать директиву `alias` для создания псевдонимов к файлам или каталогам на сервере. Например, если у вас есть каталог `files` и вы хотите получить доступ к его содержимому по URL `yourdomain.com/files/`, то ваша конфигурация может выглядеть следующим образом:

nginx
location /files/ {
    alias /var/www/yourdomain/files/;  # Путь к реальному каталогу на сервере
}


## 4. Настройка виртуальных хостов для обслуживания нескольких доменных имен

Чтобы настроить несколько доменных имен на одном сервере, создайте отдельные блоки `server` для каждого домена. Пример конфигурации для двух доменов:

nginx
server {
    listen 443 ssl;
    server_name domain1.com www.domain1.com;

    ssl_certificate /path/to/domain1/certificate.crt;
    ssl_certificate_key /path/to/domain1/private.key;

    location / {
        root /var/www/domain1;
        index index.html index.htm;
    }
}

server {
    listen 443 ssl;
    server_name domain2.com www.domain2.com;

    ssl_certificate /path/to/domain2/certificate.crt;
    ssl_certificate_key /path/to/domain2/private.key;

    location / {
        root /var/www/domain2;
        index index.html index.htm;
    }
}


## Полная конфигурация Nginx

Объединив все вышеописанные настройки, ваш файл конфигурации может выглядеть следующим образом:

nginx
# Перенаправление HTTP на HTTPS
server {
    listen 80;
    server_name yourdomain.com;  # Замените на ваш домен
    return 301 https://$host$request_uri;
}

# Конфигурация для первого домена
server {
    listen 443 ssl;
    server_name yourdomain.com;

    ssl_certificate /path/to/your/certificate.crt;
    ssl_certificate_key /path/to/your/private.key;

    location / {
        root /var/www/yourdomain;
        index index.html index.htm;
    }

    location /files/ {
        alias /var/www/yourdomain/files/;
    }
}

# Конфигурация для второго домена
server {
    listen 443 ssl;
    server_name anotherdomain.com;

    ssl_certificate /path/to/anotherdomain/certificate.crt;
    ssl_certificate_key /path/to/anotherdomain/private.key;

    location / {
        root /var/www/anotherdomain;
        index index.html index.htm;
    }
}


Не забудьте перезагрузить Nginx после внесения изменений:

bash
sudo nginx -t  # Проверка синтаксиса конфигурации
sudo systemctl restart nginx  # Перезагрузка Nginx


Теперь Nginx настроен для работы по HTTPS, перенаправляет HTTP-запросы на HTTPS, использует alias для псевдонимов и обслуживает несколько доменных имен на одном сервере.


В этом документе описаны основные шаги настройки Nginx в соответствии с указанным техническим заданием, с примерами конфигурации, которые можно использовать.