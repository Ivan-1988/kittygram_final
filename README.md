[![CI/CD Status](https://github.com/Ivan-1988/kittygram_final/actions/workflows/main.yml/badge.svg)](https://github.com/Ivan-1988/kittygram_final/actions/workflows/main.yml)

### Описание проекта 
Kittygram - instagram для котов.

Что умеет проект:

- Добавление новых котиков.
- Просмотр, редактирование и удаление информации о питомцах. 
- Создание новых достижений.
- Назначение уже существующих достижений котикам.
- Просмотр профилей других пользователей.
- Изучение достижений чужих котиков.

## Стек технологий

# Backend:

- Django (Python) — ядро API, ORM, админ-панель

- Django REST Framework — реализация RESTful API

- PostgreSQL — хранение данных

- Gunicorn — WSGI-сервер для production-развёртывания

# Frontend:

- React — SPA

- Инфраструктура:

- Nginx — обратный прокси, раздача статики и маршрутизация запросов

- Docker — контейнеризация сервисов

- Docker Compose — оркестрация multi-container приложения

# CI/CD:

- GitHub Actions — автоматизация тестов и деплоя

## Как развернуть проект 

1. Клонировать репозиторий:

    ```bash
    git clone git@github.com:Ivan-1988/kittygram_final.git
    ```
    ```bash
    cd kittygram
    ```
2. Создайте файл .env со следующими переменными

```
SECRET_KEY=any_key
DEBUG=False
ALLOWED_HOSTS=Список разрешённых хостов
POSTGRES_USER=django_user
POSTGRES_PASSWORD=mysecretpassword
POSTGRES_DB=django
DB_HOST=db
DB_PORT=5432
```


### Создать Docker-образы

1.  Заменить username на логин DockerHub:

    ```bash
    cd frontend
    docker build -t username/kittygram_frontend .
    cd ../backend
    docker build -t username/kittygram_backend .
    cd ../nginx
    docker build -t username/kittygram_gateway . 
    ```

2. Загрузть образы на DockerHub:

    ```bash
    docker push username/kittygram_frontend
    docker push username/kittygram_backend
    docker push username/kittygram_gateway
    ```

### Деплой на удалённом сервере

1. Подключиться серверу

    ```bash
    ssh -i путь_до_файла_с_SSH_ключом/название_файла_с_SSH_ключом имя_пользователя@ip_адрес_сервера 
    ```

2. Создать на сервере директорию для проека kittygram

    ```bash
    mkdir kittygram
    ```

3. Установить docker на сервер:

    ```bash
    sudo apt update
    sudo apt install curl
    curl -fSL https://get.docker.com -o get-docker.sh
    sudo sh ./get-docker.sh
    sudo apt-get install docker-compose-plugin
    ```

4. В директорию kittygram/ скопировать файлы docker-compose.production.yml и .env:

    ```bash
    scp -i path_to_SSH/SSH_name docker-compose.production.yml username@server_ip:/home/username/kittygram/docker-compose.production.yml
    ```

5. Поднять контейнеры:

    ```bash
    sudo docker compose -f docker-compose.production.yml up -d
    ```

6. Сделать миграции, соберить статические файлы бэкенда и скопировать их в /backend_static/static/:

    ```bash
    sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
    sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic
    sudo docker compose -f docker-compose.production.yml exec backend cp -r /app/collected_static/. /backend_static/static/
    ```

7. Добаить в Nginx location в секции server:

    ```bash
    location / {
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:9000;
    }
    ```

8. Перезапустить Nginx
    ```bash
    sudo service nginx reload
    ```

#  Как работать с репозиторием финального задания

## Что нужно сделать

Настроить запуск проекта Kittygram в контейнерах и CI/CD с помощью GitHub Actions

## Как проверить работу с помощью автотестов

В корне репозитория создайте файл tests.yml со следующим содержимым:
```yaml
repo_owner: ваш_логин_на_гитхабе
kittygram_domain: полная ссылка (https://доменное_имя) на ваш проект Kittygram
taski_domain: полная ссылка (https://доменное_имя) на ваш проект Taski
dockerhub_username: ваш_логин_на_докерхабе
```

Скопируйте содержимое файла `.github/workflows/main.yml` в файл `kittygram_workflow.yml` в корневой директории проекта.

Для локального запуска тестов создайте виртуальное окружение, установите в него зависимости из backend/requirements.txt и запустите в корневой директории проекта `pytest`.

## Чек-лист для проверки перед отправкой задания

- Проект Taski доступен по доменному имени, указанному в `tests.yml`.
- Проект Kittygram доступен по доменному имени, указанному в `tests.yml`.
- Пуш в ветку main запускает тестирование и деплой Kittygram, а после успешного деплоя вам приходит сообщение в телеграм.
- В корне проекта есть файл `kittygram_workflow.yml`.

### Автор
[Ivan-1988](https://github.com/Ivan-1988)
