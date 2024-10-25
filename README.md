# Kittygram

## Описание
Проект "Kittygram" представляет собой веб-приложение, позволяющее пользователям загружать фотографии своих кошек и котов.

## Автор
Евгений Кузнецов

## Используемые технологии
- **Django**
- **React**
- **PostgreSQL**
- **Docker**
- **Nginx**

## Ссылки на проект
- [**Развернутый проект**](https://kuz-kittygram.duckdns.org/)

## Бейджи
![Статус рабочего процесса](https://github.com/kuznetsov-e/kittygram_final/actions/workflows/main.yml/badge.svg)

## Инструкции по развертыванию

### Локальное развертывание
1. Клонируйте репозиторий:
   ```bash
   git clone https://github.com/kuznetsov-e/kittygram_final.git
   cd kittygram_final
   ```

2. Настройте ваши переменные окружения:
   - Создайте файл `.env` на основе `.env.example` в корне проекта.
   - Заполните необходимые значения.

3. Установите Docker, если вы еще этого не сделали.

4. Соберите и запустите Docker-контейнеры:
   ```bash
   docker-compose up --build
   ```

5. Доступ к приложению можно получить по адресу [http://localhost:9000](http://localhost:9000).

### Удаленное развертывание
1. Скопируйте файл `docker-compose.production.yml` в корневую папку проекта на сервере.
   
2. Настройте ваши переменные окружения на сервере:
   - Создайте файл `.env` на основе `.env.example` в той же папке.
   - Заполните необходимые значения.

4. Соберите и запустите Docker-контейнеры:
   ```bash
   sudo docker compose -f docker-compose.production.yml pull
   sudo docker compose -f docker-compose.production.yml down
   sudo docker compose -f docker-compose.production.yml up -d
   ```

5. Выполните миграции и соберите статические файлы:
   ```bash
   sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
   sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic
   sudo docker compose -f docker-compose.production.yml exec backend cp -r /app/collected_static/. /backend_static/static/
   ```

6. Настройте Nginx для работы с портом 9000 и укажите ваш домен в конфигурации. Пример конфигурации может выглядеть следующим образом:
   ```bash
   server {
    server_name ваш_домен;

    location /api/ {
        client_max_body_size 20M;
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:9000;
    }

    location /admin/ {
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:9000;
    }

    location /media/ {
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:9000;
    }

    location / {
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:9000;
    }
   }
   ```

7. Приложение теперь должно работать и будет доступно через ваш домен.

## Различия между продакшн и девелопмент версиями
- **Девелопмент версия**:
  - **Цель**: Используется для тестирования и разработки.
  - **Особенности**: Упрощенное отслеживание ошибок, обычно работает медленнее.
  - **Использование**: Рекомендуется для локальной разработки и тестирования.

- **Продакшн версия**:
  - **Цель**: Используется для действующих приложений.
  - **Особенности**: Оптимизирована для производительности, усиленные меры безопасности.
  - **Использование**: Рекомендуется для любых действующих сред, доступных пользователям.
