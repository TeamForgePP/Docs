## API: Kanban (доска задач)

Ручки страницы **Kanban**: загрузка данных по задачам (по активному или выбранному спринту), список участников команды, список спринтов, просмотр задачи, создание задачи, обновление статуса.

> **Base prefix:** `api` (все пути ниже уже с `api/...`)  
> Доступ: **user** или **admin** (cookie-based auth).

---

## Auth (для всех ручек)

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

## Enums

### TaskPriority
- `high`
- `medium`
- `Low` *(да, именно так в коде)*

### TaskStatus
- `TO_DO`
- `IN_PROGRESS`
- `IN_REVIEW`
- `IN_TEST`
- `DONE`

### TeamRole
- `backend`
- `business_analyst`
- `Curator`
- `devops`
- `frontend`
- `manager`
- `product_manager`
- `team_lead`

---

## Schemas

`src/modules/kanban/schemas/kanban.py`

### KanbanResponse
```json
{
  "project": {
    "id": "uuid",
    "name": "string"
  },
  "selected_sprint": {
    "id": "uuid",
    "seq": 1
  },
  "tasks": [
    {
      "id": "uuid",
      "title": "string",
      "tag": "TeamRole | null",
      "status": "TaskStatus",
      "deadline": "YYYY-MM-DD",
      "priority": "TaskPriority",
      "performers": [
        {
          "id": "uuid",
          "first_name": "string",
          "last_name": "string",
          "avatar_url": "string"
        }
      ],
      "key": "string"
    }
  ]
}
```

### MembersResponse

```json
{
  "members": [
    {
      "id": "uuid",
      "first_name": "string",
      "last_name": "string",
      "avatar_url": "string"
    }
  ]
}
```

### SprintsResponse

```json
{
  "number": 3,
  "sprints": [
    {
      "id": "uuid",
      "label": "1 — Sprint name"
    }
  ]
}
```

### TaskResponse

```json
{
  "id": "uuid",
  "title": "string",
  "description": "string",
  "performers": [
    {
      "id": "uuid",
      "first_name": "string",
      "last_name": "string",
      "avatar_url": "string"
    }
  ],
  "priority": "TaskPriority",
  "deadline": "YYYY-MM-DD",
  "tag": "TeamRole | null",
  "key": "string"
}
```

### NewTaskRequest

```json
{
  "sprint_id": "uuid | null",
  "title": "string",
  "description": "string",
  "performers": [
    { "id": "uuid" }
  ],
  "priority": "TaskPriority",
  "deadline": "YYYY-MM-DD",
  "tag": "TeamRole | null"
}
```

### UpdateStatusRequest

```json
{
  "task_id": "uuid",
  "status": "TaskStatus"
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

## Kanban

### Api route загрузки информации для страницы (активный спринт)

### (GET) `api/kanban`

#### request:

`Cookie: user_access_token=...` или `Cookie: admin_access_token=...`

#### response:

schemas → `KanbanResponse`  
`src/modules/kanban/schemas/kanban.py`

|code|description|example|
|---|---|---|
|200|ok|`KanbanResponse`|
|401|unauthorized|(см. Auth выше)|
|404|not found|`{ "detail": "Project not found" }`|
|404|not found|`{ "detail": "Projects not found" }`|
|404|not found|`{ "detail": "Active sprint not found" }`|
|500|internal error|`{ "detail": "Internal Server Error during get info" }`|

---

### Api route получения тасок по конкретному спринту

### (GET) `api/kanban/sprints/{sprint_id}`

#### request:

`Cookie: user_access_token=...` или `Cookie: admin_access_token=...`  
`sprint_id` — UUID спринта

#### response:

schemas → `KanbanResponse`  
`src/modules/kanban/schemas/kanban.py`

|code|description|example|
|---|---|---|
|200|ok|`KanbanResponse`|
|401|unauthorized|(см. Auth выше)|
|404|not found|`{ "detail": "Sprint not found" }`|
|404|not found|`{ "detail": "Project not found" }`|
|404|not found|`{ "detail": "Projects not found" }`|
|500|internal error|`{ "detail": "Internal Server Error during get info" }`|

---

### Api route получения списка спринтов (для выбора в UI)

### (GET) `api/kanban/sprints`

#### request:

`Cookie: user_access_token=...` или `Cookie: admin_access_token=...`

#### response:

schemas → `SprintsResponse`  
`src/modules/kanban/schemas/kanban.py`

|code|description|example|
|---|---|---|
|200|ok|`SprintsResponse`|
|401|unauthorized|(см. Auth выше)|
|404|not found|`{ "detail": "Project not found" }`|
|404|not found|`{ "detail": "Projects not found" }`|
|500|internal error|`{ "detail": "Internal Server Error during get info" }`|

---

### Api route получения всех участников команды

### (GET) `api/kanban/team-members`

#### request:

`Cookie: user_access_token=...` или `Cookie: admin_access_token=...`

#### response:

schemas → `MembersResponse`  
`src/modules/kanban/schemas/kanban.py`

|code|description|example|
|---|---|---|
|200|ok|`MembersResponse`|
|401|unauthorized|(см. Auth выше)|
|404|not found|`{ "detail": "Project not found" }`|
|404|not found|`{ "detail": "Projects not found" }`|
|500|internal error|`{ "detail": "Internal Server Error during get info" }`|

---

### Api route просмотра задачи

### (GET) `api/kanban/tasks/{task_id}`

#### request:

`Cookie: user_access_token=...` или `Cookie: admin_access_token=...`  
`task_id` — UUID задачи

#### response:

schemas → `TaskResponse`  
`src/modules/kanban/schemas/kanban.py`

|code|description|example|
|---|---|---|
|200|ok|`TaskResponse`|
|401|unauthorized|(см. Auth выше)|
|404|not found|`{ "detail": "Task not found" }`|
|500|internal error|`{ "detail": "Internal Server Error during get info" }`|

---

### Api route создания новой задачи

### (POST) `api/kanban/new-task`

#### request:

`Cookie: user_access_token=...` или `Cookie: admin_access_token=...`

schemas → `NewTaskRequest`  
`src/modules/kanban/schemas/kanban.py`

> Если `sprint_id` **не указан**, задача будет создана:
> 
> - в **активном** спринте проекта (если есть),
>     
> - иначе — в **первом будущем** спринте,
>     
> - если спринтов нет — будет `404`.
>     

#### response:

schemas → `BasicResponse`  
`src/modules/kanban/schemas/kanban.py`

```json
{
  "success": true,
  "message": "Task successfully created"
}
```

|code|description|example|
|---|---|---|
|201|created|`BasicResponse`|
|401|unauthorized|(см. Auth выше)|
|400|bad request|`{ "detail": "Sprint doesn't belong to project" }`|
|404|not found|`{ "detail": "Project not found" }`|
|404|not found|`{ "detail": "Projects not found" }`|
|404|not found|`{ "detail": "Sprint not found" }`|
|404|not found|`{ "detail": "Sprint not found in project" }`|
|404|not found|`{ "detail": "Sprints not exist" }`|
|422|unprocessable entity|`{ "detail": "Invalid sprint_id format" }`|
|500|internal error|`{ "detail": "Internal Server Error during task creation" }`|

---

### Api route обновления статуса задачи

### (POST) `api/kanban/update-status`

#### request:

`Cookie: user_access_token=...` или `Cookie: admin_access_token=...`

schemas → `UpdateStatusRequest`  
`src/modules/kanban/schemas/kanban.py`

#### response:

schemas → `BasicResponse`  
`src/modules/kanban/schemas/kanban.py`

```json
{
  "success": true,
  "message": "Task status updated successfully"
}
```

|code|description|example|
|---|---|---|
|200|ok|`BasicResponse`|
|401|unauthorized|(см. Auth выше)|
|422|unprocessable entity|(ошибка валидации тела запроса)|
|500|internal error|`{ "detail": "Internal Server Error during update status" }`|