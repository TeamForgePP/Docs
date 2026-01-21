## API: Sprints (спринты)

Ручки страницы **Sprints**: список спринтов (current/future/completed), создание спринта, просмотр спринта, редактирование, завершение спринта.

> **Base prefix:** `api` (все пути ниже уже с `api/...`)  
> Доступ: **user** или **admin** (cookie-based auth) — **только для части ручек** (см. request у конкретной ручки).

---

## Auth (где требуется)

#### request:
`Cookie: user_access_token=...` **или** `Cookie: admin_access_token=...`

| code | description  | example |
| ---- | ------------ | ------- |
| 401  | unauthorized | `{ "detail": "not authenticated" }` |
| 401  | unauthorized | `{ "detail": "invalid token" }` |
| 401  | unauthorized | `{ "detail": "refresh token is not allowed here" }` |
| 401  | unauthorized | `{ "detail": "token rejected" }` |
| 401  | unauthorized | `{ "detail": "token missing subject" }` |

---

## Schemas

`src/modules/sprints/schemas/sprints.py`

### AllSprintsResponse
```json
{
  "current_sprint": {
    "id": "uuid",
    "seq": 1,
    "name": "string",
    "goal": "string",
    "description": "string",
    "estimated_deadline": "YYYY-MM-DD"
  },
  "future_sprints": [
    {
      "id": "uuid",
      "seq": 2,
      "name": "string"
    }
  ],
  "completed_sprints": [
    {
      "id": "uuid",
      "seq": 0,
      "name": "string"
    }
  ]
}
```
> `current_sprint` может быть `null`, если активного спринта нет.

### Sprint

```json
{
  "name": "string",
  "start_date": "YYYY-MM-DD",
  "end_date": "YYYY-MM-DD",
  "goal": "string",
  "description": "string"
}
```

### BasicResponse

```json
{
  "success": true,
  "message": "string"
}
```

---

## Sprints

### Api route загрузки информации для страницы (спринты по текущему проекту)

### (GET) `api/sprints`

#### request:

`Cookie: user_access_token=...` или `Cookie: admin_access_token=...`

> Логика выбора проекта:
> 
> - **user**: берётся “незавершённый проект” пользователя, иначе `404`
>     
> - **admin**: берётся первый “незавершённый проект” из списка, иначе `404`
>     

#### response:

schemas → `AllSprintsResponse`  
`src/modules/sprints/schemas/sprints.py`

|code|description|example|
|---|---|---|
|200|ok|`AllSprintsResponse`|
|401|unauthorized|(см. Auth выше)|
|404|not found|`{ "detail": "You don't have uncompleted project" }`|
|404|not found|`{ "detail": "Projects not found" }`|
|500|internal error|`{ "detail": "Internal Server Error during get sprints" }`|

---

### Api route создания нового спринта

### (POST) `api/sprints/new-sprint`

#### request:

`Cookie: user_access_token=...` или `Cookie: admin_access_token=...`

schemas → `Sprint`  
`src/modules/sprints/schemas/sprints.py`

```json
{
  "name": "Sprint 3",
  "start_date": "2026-01-22",
  "end_date": "2026-02-05",
  "goal": "string",
  "description": "string"
}
```

> Новый спринт становится:
> 
> - `ACTIVE`, если активного спринта в проекте нет
>     
> - иначе `UPCOMING`
>     

#### response:

schemas → `BasicResponse`  
`src/modules/sprints/schemas/sprints.py`

```json
{
  "success": true,
  "message": "Sprint successfully created"
}
```

|code|description|example|
|---|---|---|
|201|created|`BasicResponse`|
|401|unauthorized|(см. Auth выше)|
|404|not found|`{ "detail": "You don't have uncompleted project" }`|
|404|not found|`{ "detail": "Projects not found" }`|
|500|internal error|`{ "detail": "Internal Server Error during sprint creation" }`|

---

### Api route просмотра спринта по id

### (GET) `api/sprints/{sprint_id}`

#### request:

`Cookie: не требуется` _(по текущему коду dependency на auth здесь не стоит)_  
`sprint_id` — UUID спринта

#### response:

schemas → `Sprint`  
`src/modules/sprints/schemas/sprints.py`

```json
{
  "name": "Sprint 3",
  "start_date": "2026-01-22",
  "end_date": "2026-02-05",
  "goal": "string",
  "description": "string"
}
```

|code|description|example|
|---|---|---|
|200|ok|`Sprint`|
|404|not found|`{ "detail": "Sprint not found" }`|
|500|internal error|`{ "detail": "Internal Server Error during get sprint" }`|

---

### Api route редактирования спринта

### (PATCH) `api/sprints/{sprint_id}`

#### request:

`Cookie: не требуется` _(по текущему коду dependency на auth здесь не стоит)_  
`sprint_id` — UUID спринта

schemas → `Sprint`  
`src/modules/sprints/schemas/sprints.py`

```json
{
  "name": "Sprint 3 (updated)",
  "start_date": "2026-01-22",
  "end_date": "2026-02-06",
  "goal": "string",
  "description": "string"
}
```

#### response:

schemas → `BasicResponse`  
`src/modules/sprints/schemas/sprints.py`

```json
{
  "success": true,
  "message": "Sprint successfully edited"
}
```

|code|description|example|
|---|---|---|
|200|ok|`BasicResponse`|
|404|not found|`{ "detail": "Sprint not found" }`|
|500|internal error|`{ "detail": "Internal Server Error during edit sprint" }`|

---

### Api route завершения спринта и переключения на следующий

### (POST) `api/sprints/{sprint_id}/complete`

#### request:

`Cookie: не требуется` _(по текущему коду dependency на auth здесь не стоит)_  
`sprint_id` — UUID спринта  
body отсутствует

#### response:

schemas → `BasicResponse`  
`src/modules/sprints/schemas/sprints.py`

```json
{
  "success": true,
  "message": "Sprint successfully completed"
}
```

|code|description|example|
|---|---|---|
|200|ok|`BasicResponse`|
|400|bad request|`{ "detail": "Sprint cannot be completed" }`|
|400|bad request|`{ "detail": "Cannot complete sprint: no upcoming sprint found" }`|
|404|not found|`{ "detail": "Sprint not found" }`|
|500|internal error|`{ "detail": "Internal Server Error during complete sprint" }`|