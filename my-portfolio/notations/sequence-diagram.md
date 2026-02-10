```mermaid
sequenceDiagram
    autonumber
    actor Admin as Администратор
    participant AP as Admin-Panel
    participant MC as MediaContent
    participant CDN
    participant Shop as Shop Service

    Admin->>AP: Загрузка изображения товара
    AP->>MC: POST /upload (File)
    MC->>MC: Генерация UUID
    MC->>CDN: Сохранение файла
    MC-->>AP: Возврат UUID
    
    Admin->>AP: Сохранить товар
    AP->>Shop: POST /save (Content Data + UUIDs)

    Note over Shop: Сохранение массива ID картинок в БД
```
