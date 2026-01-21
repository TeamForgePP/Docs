## API: Home (Main / Projects)

Ручки страницы **Main**: список проектов, создание проекта, выход из проекта, удаление проекта, а также список пользователей для команды.

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

## Projects

### Api route создания проекта

### (POST) `api/main`

#### request:
`Cookie: user_access_token=...` или `Cookie: admin_access_token=...`

schemas → `CreateProjectRequest`  
`src/modules/home/schemas/home.py`

```json
{
  "name": "string",
  "description": "string | null",
  "team": [
    {
      "id": "uuid",
      "roles": ["team_lead", "backend"]
    }
  ],
  "github_url": "string"
}
```

⚠️Важные правила:
> - если запрос делает **user** — teamlead это сам пользователь из токена; роль `team_lead` у других участников будет игнорироваться.
>     
> - если запрос делает **admin** — в `team` должен быть **ровно один** участник с ролью `team_lead`, иначе будет `400`.
>     

#### response:

schemas → `BasicResponse`  
`src/modules/home/schemas/home.py`

```json
{
  "success": true,
  "message": "Project successfully created"
}
```

|code|description|example|
|---|---|---|
|201|created|`BasicResponse`|
|400|bad request|`{ "detail": "No team leader in the project" }`|
|400|bad request|`{ "detail": "Only one teamlead can be in project" }`|
|401|unauthorized|(см. Auth выше)|
|422|unprocessable entity|`{ "detail": "Invalid team member id format" }`|
|422|unprocessable entity|`{ "detail": "Invalid team role format" }`|
|500|internal error|`{ "detail": "Internal Server Error during project creation" }`|

---

### Api route получения информации для страницы (список проектов)

### (GET) `api/main`

#### request:

`Cookie: user_access_token=...` или `Cookie: admin_access_token=...`

#### response:

schemas → `ProjectsResponse`  
`src/modules/home/schemas/home.py`

```json
{
  "projects": [
    {
      "id": "uuid",
      "name": "string",
      "is_completed": false,
      "current_sprint_name": "string",
      "current_sprint_seq": 1,
      "role": ["backend", "team_lead"],
      "nearest_deadline": "YYYY-MM-DD | null",
      "sprint_map": [
        {
          "id": "uuid",
          "name": "string",
          "seq": 1,
          "active": true,
          "deadline": "YYYY-MM-DD"
        }
      ],
      "allowed_actions": {
        "can_delete": true,
        "can_leave": false
      }
    }
  ]
}

```

|code|description|example|
|---|---|---|
|200|ok|`ProjectsResponse`|
|401|unauthorized|(см. Auth выше)|
|500|internal error|`{ "detail": "Internal Server Error during get info" }`|

---

### Api route получения пользователей для команды

### (GET) `api/main/users-for-team`

Используется для подбора участников при создании проекта.

#### request:

`Cookie: user_access_token=...` или `Cookie: admin_access_token=...`

#### response:

schemas → `UsersResponse`  
`src/modules/home/schemas/home.py`

```json
{
  "users": [
    {
      "id": "uuid",
      "name": "string",
      "last_name": "string",
      "in_team": false
    }
  ]
}
```

|code|description|example|
|---|---|---|
|200|ok|`UsersResponse`|
|401|unauthorized|(см. Auth выше)|
|500|internal error|`{ "detail": "Internal Server Error during get users" }`|

---

### Api route покинуть проект

### (POST) `api/main/{project_id}/leave`

#### request:

`Cookie: user_access_token=...` или `Cookie: admin_access_token=...`  
`project_id` — UUID проекта

#### response:

schemas → `BasicResponse`  
`src/modules/home/schemas/home.py`

```json
{
  "success": true,
  "message": "You left the project successfully"
}
```

|code|description|example|
|---|---|---|
|200|ok|`BasicResponse`|
|401|unauthorized|(см. Auth выше)|
|403|forbidden|`{ "detail": "Teamlead can't leave project" }`|
|403|forbidden|`{ "detail": "You're not a project member" }`|
|404|not found|`{ "detail": "Project not found" }`|

---

### Api route удалить проект

### (DELETE) `api/main/{project_id}`

#### request:

`Cookie: user_access_token=...` или `Cookie: admin_access_token=...`  
`project_id` — UUID проекта

#### response:

schemas → `BasicResponse`  
`src/modules/home/schemas/home.py`

```json
{
  "success": true,
  "message": "Project successfully deleted"
}
```

| code | description  | example                                                     |
| ---- | ------------ | ----------------------------------------------------------- |
| 200  | ok           | `BasicResponse`                                             |
| 401  | unauthorized | (см. Auth выше)                                             |
| 403  | forbidden    | `{ "detail": "Only teamlead or admin can delete project" }` |
| 404  | not found    | `{ "detail": "Project not found or already deleted" }`      |