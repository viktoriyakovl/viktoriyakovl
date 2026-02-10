# ER-ДИАГРАММА

~~~ mermaid
erDiagram

    users {
        uuid id PK, FK
        text login
        text email
        boolean email_confirmed
        text phone
        timestamp when_created
        boolean is_deleted
    }

    social_networks {
        varchar social_name
        smallint social_type PK
        boolean is_active
    }

    socials {
        smallint type FK
        uuid user_id PK
        text social_id
    }

    roles {
        bigint id PK
        text name
        text description
        text claims FK
    }

    user_roles {
        uuid user_id PK
        integer role_id FK
    }

    user_claims {
        text claim PK
        text value
        text user_id
    }

    tbl_version {
        uuid id
        text filename
        timestamp execution_date
    }

    users ||--|| socials : "id : user_id"
    social_networks |o--o{ socials : "social_type : type"
    users ||--|{ user_roles : "id : user_id"
    roles ||--o{ user_roles : "id : role_id"
    user_claims }|--|| users : "user_id : id"
    roles ||..|| user_claims : "claims : claim"
   
~~~

---
<br><br><br><br><br><br>

# ОПИСАНИЕ СВЯЗЕЙ

> **Синтаксис:**
> 
> `Сущность1 |Кардинальность--Кардинальность| Сущность2 : "Текст метки"`

<br>

### 1. СВЯЗЬ ОДИН К ОДНОМУ 
<mark>||--||

Означает, что запись в одной таблице строго соответствует одной записи в другой.

```
mermaid

user_id ||--|| email : "у каждого пользователя только один email"
```

### 2. СВЯЗЬ ОДИН К НУЛЮ ИЛИ ОДНОМУ
<mark>||--o|

Запись слева обязательна, запись справа может отсутствовать или быть одна.

```
mermaid

user_id ||--o| email
```

### 3. СВЯЗЬ ОДИН КО МНОГИМ
<mark>||--|{

Одна запись слева (обязательная) связана с одной или несколькими справа (обязательными).
> *Пример: У пользователя обязательно должна быть хотя бы одна роль.*

```
mermaid

user_id ||--|{ email
```

### 4. СВЯЗЬ ОДИН К НУЛЮ ИЛИ МНОГИМ
<mark>||--o{

Запись слева (обязательна) может иметь много связей справа или ни одной.

```
mermaid

user_id ||--o{ email
```

### 5. СВЯЗЬ МНОГИЕ К ОДНОМУ
<mark>}|--||

Множество записей слева ссылаются на одну обязательную запись справа.

```
mermaid

user_id }|--|| email
```

### 6. СВЯЗЬ МНОГИЕ КО МНОГИМ
<mark>}|--|{

Обычно реализуется через промежуточную таблицу, но Mermaid позволяет рисовать напрямую.
> *Пример: Клиенты используют ключи шифрования.*

```
mermaid

user_id }|--|{ email
```

### 7. СВЯЗЬ НУЛЬ ИЛИ ОДИН К МНОГИМ 
<mark>|o--o{

Необязательная запись слева связана с необязательным множеством справа.
> *Пример: Соцсеть может не использоваться никем, а пользователь может иметь много привязок.*

```
mermaid

user_id |o--o{ email
```

### 8. ИДЕНТИФИЦИРУЮЩАЯ (`--`) И НЕИДЕНТИФИЦИРУЮЩАЯ (`..`) СВЯЗЬ 

Cплошная линия (||<mark>--</mark>||) показывает сильную связь.

Пунктир (||<mark>..</mark>||) показывает слабую связь.

```
mermaid

user_id ||..|| email
```

