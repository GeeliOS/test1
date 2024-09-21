# Лабораторная работа №1 - Настройка Nginx по заданному ТЗ

## <u>1.1. Установка nginx</u>

Установка Nginx — это простой процесс. Следуйте этим шагам, чтобы установить Nginx на вашу систему (В реалях данного проекта я буду пользоваться ОС от ubuntu):

### Шаг 1: Обновите список пакетов

Прежде чем установить новое программное обеспечение, рекомендуется обновить список пакетов. Откройте терминал и выполните следующую команду:

```nginx
sudo apt update
```

### Шаг 2: Установите Nginx

После обновления списка пакетов установите Nginx, выполнив следующую команду:

```nginx
sudo apt install nginx
```

### Шаг 3: Запустите Nginx

После завершения установки можно запустить Nginx. Выполните следующую команду:

```nginx
sudo systemctl start nginx
```

### Шаг 4: Проверьте статус Nginx

Чтобы убедиться, что Nginx запущен и работает, проверьте его статус:

```nginx
sudo systemctl status nginx
```

Вы должны увидеть сообщение о том, что Nginx активен и работает (active (running)).

### Шаг 5: Проверьте установку

Теперь откройте веб-браузер и введите `http://your_server_ip` или `http://localhost`. Вы должны увидеть страницу приветствия Nginx, которая подтверждает, что установка прошла успешно.

![Welcome to nginx](./img/1.jpg)

### Шаг 6: Настройка Nginx (опционально)

Основной файл конфигурации Nginx находится по адресу `/etc/nginx/nginx.conf`, а файлы для виртуальных хостов (если используются) находятся в каталогах `/etc/nginx/sites-available/` и `/etc/nginx/sites-enabled/`. Вы можете редактировать эти файлы для настройки Nginx под ваши нужды.

### Шаг 7: Перезапустите Nginx после изменений (если нужно)

После внесения изменений в конфигурацию не забудьте перезапустить Nginx для применения изменений:

```nginx
sudo systemctl restart nginx
```

Теперь вы успешно установили и настроили Nginx!

## 1. Настройка HTTPS с сертификатом

Для работы по HTTPS вам потребуется действующий SSL-сертификат. Вы можете получить сертификат через Let's Encrypt или использовать другой удостоверяющий центр. После получения сертификата, вам нужно будет указать путь к сертификату и ключу в конфигурации Nginx.

Пример конфигурации для HTTPS:

nginx
server {
listen 443 ssl;
server_name yourdomain.com; # Замените на ваш домен

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
server_name yourdomain.com; # Замените на ваш домен

    # Принудительное перенаправление на HTTPS
    return 301 https://$host$request_uri;

}

## 3. Использование alias для создания псевдонимов путей

Вы можете использовать директиву `alias` для создания псевдонимов к файлам или каталогам на сервере. Например, если у вас есть каталог `files` и вы хотите получить доступ к его содержимому по URL `yourdomain.com/files/`, то ваша конфигурация может выглядеть следующим образом:

nginx
location /files/ {
alias /var/www/yourdomain/files/; # Путь к реальному каталогу на сервере
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
server_name yourdomain.com; # Замените на ваш домен
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
sudo nginx -t # Проверка синтаксиса конфигурации
sudo systemctl restart nginx # Перезагрузка Nginx

Теперь Nginx настроен для работы по HTTPS, перенаправляет HTTP-запросы на HTTPS, использует alias для псевдонимов и обслуживает несколько доменных имен на одном сервере.

В этом документе описаны основные шаги настройки Nginx в соответствии с указанным техническим заданием, с примерами конфигурации, которые можно использовать.
