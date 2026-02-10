# Нотации C4

## Context

~~~ mermaid

C4Context
    title Архитектура Платформы (System Architecture)

    %% --- СУЩНОСТИ ---

    Person(customer, "Person, Customer", "Игрок")
    Person(admin, "Admin", "Администратор")

    System_Ext(os_cookie, "Open Source Cookie", "Cookie-баннер")

    System_Boundary(platform, "Platform") {
        
        %% Слой Фронтенда (Выравниваем вертикально)
        Container(launcher, "Launcher", "Desktop App", "Лаунчер")
        Container(web, "Web", "Web Site", "Сайт")
        Container(admin_panel, "Admin-Panel", "Web App", "Панель администратора")

        %% Слой Бэкенда (Центр)
        Container(backend, "Backend", "API / Server", "Основной бэкенд")

        %% Слой Данных (Верхний уровень)
        ContainerDb(cdn, "CDN", "File Storage", "Хранилище статики")
        ContainerDb(db, "DataBase", "PostgreSQL", "Основная БД")
        ContainerDb(broker, "Брокер сообщений", "Kafka", "Очереди событий")
    }

    System_Ext(os_source, "Open Source", "Email рассылки")
    System_Ext(payment, "Payment System", "Платежи")
    System_Ext(game, "Game", "Игра")

    %% --- СВЯЗИ (Направление Слева-Направо и Снизу-Вверх для избежания пересечений) ---

    %% 1. Входящие от пользователя (Слева -> Направо)
    Rel_R(customer, launcher, "1. Установка, запуск, покупки")
    Rel_R(customer, web, "2. Просмотр промо, покупки")
    
    %% 2. Взаимодействие фронтенда (Вертикальные и короткие связи)
    Rel_U(os_cookie, web, "Cookie-баннер")
    Rel_D(launcher, web, "Переход в магазин [Deeplinks]")
    Rel_U(web, launcher, "Запуск Лаунчера")

    %% 3. Фронтенд -> Бэкенд (Слева -> Направо)
    Rel_R(launcher, backend, "API запросы, авторизация")
    Rel_R(web, backend, "Обработка запросов")
    Rel_R(admin_panel, backend, "API конфиги")

    %% 4. Бэкенд -> Данные (Снизу -> Вверх)
    Rel_U(backend, cdn, "Загрузка медиа")
    Rel_U(backend, db, "RW / Репликация")
    Rel_U(backend, broker, "Логирование событий")

    %% 5. Данные -> Фронтенд (Обратные стрелки, но короткие)
    Rel_L(cdn, launcher, "Скачивание контента")
    Rel_L(cdn, web, "Загрузка статики")

    %% 6. Админка (Снизу -> Вверх)
    Rel_U(admin, admin_panel, "Управление статусами/аккаунтами")

    %% 7. Бэкенд -> Внешние системы (Слева -> Направо)
    Rel_R(backend, os_source, "Отправка событий")
    Rel_R(backend, payment, "Чеки, ссылки")
    Rel_R(backend, game, "Запуск, товары")

    %% 8. Обратные связи внешних систем (Справа -> Налево)
    Rel_L(payment, backend, "Webhook об оплате")
    Rel_L(game, launcher, "Deeplinks (Магазин)")
    
    %% Длинная связь (может пересекать схему, Mermaid проложит маршрут автоматически)
    Rel(os_source, customer, "Отправка писем")

~~~

---

## Container

~~~ mermaid

C4Container
    title Backend - HLA News

    %% --- КОМПОНЕНТЫ ---

    %% Клиентские приложения (Слева)
    Container(launcher, "Launcher", "Desktop App", "Лаунчер")
    Container(web, "Web", "Web Site", "Веб-сайт")
    Container(admin, "Admin-Panel", "Web App", "Панель администратора")

    %% База данных (Сверху)
    ContainerDb(db, "DataBase", "Database", "Хранилище данных")

    %% Граница системы (Центр и Право)
    System_Boundary(backend_scope, "Backend - HLA News") {
        Container(news, "News", "Backend Service", "Сервис новостей")
        
        Container(localizator, "Localizator", "Microservice", "Сервис локализации")
        Container(regions, "Regions", "Microservice", "Сервис регионов")
    }

    %% Брокер сообщений (Снизу)
    ContainerDb(broker, "Брокер сообщений", "Message Broker", "Kafka/RabbitMQ")

    %% --- СВЯЗИ ---

    %% 1. Взаимодействие с UI (Слева <-> Центр)
    %% Стрелки от News к UI согласно схеме (Data Flow)
    Rel_L(news, web, "Отображение ленты новостей пользователям", "JSON/HTTPS")
    Rel_L(news, launcher, "Отображение ленты новостей пользователям", "JSON/HTTPS")
    
    %% Админка (Двустороннее взаимодействие)
    Rel(admin, news, "Создание и публикация новостей", "HTTPS")
    Rel(news, admin, "Отображение логов изменений", "JSON")

    %% 2. Взаимодействие с БД (Центр -> Верх)
    Rel_U(news, db, "Хранение новостей", "SQL/NoSQL")

    %% 3. Взаимодействие с Брокером (Центр -> Низ)
    Rel_D(news, broker, "Фиксация изменений в новостях и их настройках", "Async Event")

    %% 4. Взаимодействие с микросервисами (Центр -> Право)
    Rel_R(news, localizator, "Локализация текстовой информации на все языки", "gRPC/HTTP")
    Rel_R(news, regions, "Фиксация доступности новости в выбранных странах", "gRPC/HTTP")

~~~
