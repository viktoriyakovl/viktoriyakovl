@startuml
left to right direction

' Настройки внешнего вида
hide circle
skinparam linetype ortho

' Описание таблиц
entity "news" as news {
  *id : UUID <<U>> <<PK>> 
  --
  *type_id : UUID <<FK>>
  title : text
  body : text
  when_published: timestamp
  when_created : timestamp DEFAULT now()
  created_by: text
  is_published : boolean DEFAULT false
}

entity "news_attaches" as news_attaches {
  *news_id : UUID <<PK, FK>>
  --
  when_modified : timestamp
  modified_by: text
}

entity "news_types" as news_types {
  *id : UUID <<PK>>
  --
  name : text
}

' Описание связей

' Связь 1: Таблица news (1) к news_attaches (0 или 1)
' Одна новость может иметь одну запись о закреплении или не иметь её вовсе.
news ||-----o| news_attaches : "Has attachment"

' Связь 2: Таблица news_types (1) к news (Многие)
' Один тип (из справочника) может быть присвоен множеству новостей.
' При этом у одной конкретной новости есть строго один тип.
news_types ||-----o{ news : "Defines type"

@enduml
