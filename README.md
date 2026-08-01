# Трекер посылок

Модуль регистрирует посылки, отслеживает смену их статуса и хранит всё в SQLite-базе.

## Возможности

- регистрация посылки (клиент + адрес) со статусом `registered`;
- последовательная смена статуса: `registered → sent → delivered`;
- изменение адреса — только пока посылка в статусе `registered`;
- удаление — только пока посылка в статусе `registered` (правило применяется прямо в SQL-запросе);
- получение всех посылок клиента.

## Схема БД

```sql
CREATE TABLE parcel
(
    number     integer
        constraint parcel_pk
            primary key autoincrement,
    client     integer      not null,
    status     VARCHAR(128) not null,
    address    VARCHAR(512) not null,
    created_at text         not null
)
```

Таблица уже создана в приложенном файле `tracker.db`.

## Структура проекта

```
.
├── main.go           # модель Parcel, бизнес-логика ParcelService, демо-сценарий в main()
├── parcel.go          # ParcelStore — доступ к SQLite через database/sql
├── parcel_test.go      # юнит-тесты (testify)
├── tracker.db          # SQLite-база с таблицей parcel
└── go.mod
```

## Запуск

```bash
go run .
```

`main()` прогоняет демонстрационный сценарий: регистрирует посылку, меняет адрес, переводит статус на `sent`, выводит посылки клиента, пытается удалить уже отправленную посылку (не удаляется — статус не `registered`), затем регистрирует и сразу удаляет новую.

## Тесты

```bash
go test -v ./...
```

Тесты используют существующий `tracker.db` — отдельная тестовая база не поднимается.

## Стек

- Go 1.21
- [modernc.org/sqlite](https://pkg.go.dev/modernc.org/sqlite) — драйвер SQLite без cgo
- [testify](https://github.com/stretchr/testify) — юнит-тесты
