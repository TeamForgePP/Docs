## API: Admin (панель администратора)

Все ручки ниже защищены: нужен **admin access token** в cookie.  
> refresh-токен для этих ручек **нельзя** (будет `401`).

**request (для всех ручек):**  
`Cookie: admin_access_token=...`

| code | description  | example |
| ---- | ------------ | ------- |
| 401  | unauthorized | `{ "detail": "not authenticated" }` |
| 401  | unauthorized | `{ "detail": "invalid token" }` |
| 401  | unauthorized | `{ "detail": "refresh token is not allowed here" }` |
| 401  | unauthorized | `{ "detail": "token rejected" }` |
| 401  | unauthorized | `{ "detail": "token missing subject" }` |
| 403  | forbidden    | `{ "detail": "admin only" }` |

---

## Groups

### Api route создания группы

### (POST) `api/admin/groups`

#### request:
`Cookie: admin_access_token=...`

schemas → `GroupCreate`  
`src/modules/admin/schemas/group.py`

#### response:
schemas → `GroupRead`  
`src/modules/admin/schemas/group.py`

| code | description    | example |
| ---- | -------------- | ------- |
| 201  | created        | `GroupRead` |
| 401  | unauthorized   | `{ "detail": "not authenticated" }` |
| 403  | forbidden      | `{ "detail": "admin only" }` |
| 409  | conflict       | `{ "detail": "Group already exists" }` |
| 500  | internal error | `{ "detail": "Internal Server Error during group creation" }` |

---

### Api route получения списка групп

### (GET) `api/admin/groups`

#### request:
`Cookie: admin_access_token=...`

#### response:
schemas → `list[GroupRead]`  
`src/modules/admin/schemas/group.py`

| code | description    | example |
| ---- | -------------- | ------- |
| 200  | ok             | `[GroupRead, ...]` |
| 401  | unauthorized   | `{ "detail": "not authenticated" }` |
| 403  | forbidden      | `{ "detail": "admin only" }` |
| 500  | internal error | (внутренняя ошибка сервера) |

---

### Api route получения группы по id

### (GET) `api/admin/groups/{group_id}`

#### request:
`Cookie: admin_access_token=...`  
`group_id` — UUID группы

#### response:
schemas → `GroupRead`  
`src/modules/admin/schemas/group.py`

| code | description  | example |
| ---- | ------------ | ------- |
| 200  | ok           | `GroupRead` |
| 401  | unauthorized | `{ "detail": "not authenticated" }` |
| 403  | forbidden    | `{ "detail": "admin only" }` |
| 404  | not found    | `{ "detail": "Group not found" }` |

---

### Api route получения студентов группы

### (GET) `api/admin/groups/{group_id}/students`

#### request:
`Cookie: admin_access_token=...`  
`group_id` — UUID группы

#### response:
schemas → `list[UserRead]`  
`src/modules/admin/schemas/user.py`

| code | description  | example |
| ---- | ------------ | ------- |
| 200  | ok           | `[UserRead, ...]` |
| 401  | unauthorized | `{ "detail": "not authenticated" }` |
| 403  | forbidden    | `{ "detail": "admin only" }` |
| 404  | not found    | `{ "detail": "Group not found" }` |

---

### Api route обновления группы

### (PATCH) `api/admin/groups/{group_id}`

#### request:
`Cookie: admin_access_token=...`  
`group_id` — UUID группы

schemas → `GroupUpdate`  
`src/modules/admin/schemas/group.py`

#### response:
schemas → `GroupRead`  
`src/modules/admin/schemas/group.py`

| code | description    | example |
| ---- | -------------- | ------- |
| 200  | ok             | `GroupRead` |
| 401  | unauthorized   | `{ "detail": "not authenticated" }` |
| 403  | forbidden      | `{ "detail": "admin only" }` |
| 404  | not found      | `{ "detail": "Group not found" }` |
| 409  | conflict       | `{ "detail": "Group with this name already exists" }` |
| 500  | internal error | `{ "detail": "Internal Server Error during group update" }` |

---

### Api route удаления группы

### (DELETE) `api/admin/groups/{group_id}`

#### request:
`Cookie: admin_access_token=...`  
`group_id` — UUID группы

#### response:
`204 No Content`

| code | description  | example |
| ---- | ------------ | ------- |
| 204  | no content   | (пусто) |
| 401  | unauthorized | `{ "detail": "not authenticated" }` |
| 403  | forbidden    | `{ "detail": "admin only" }` |
| 404  | not found    | `{ "detail": "Group not found" }` |

---

## Users

### Api route создания пользователя

### (POST) `api/admin/users`

#### request:
`Cookie: admin_access_token=...`

schemas → `UserCreate`  
`src/modules/admin/schemas/user.py`

#### response:
schemas → `UserRead`  
`src/modules/admin/schemas/user.py`

```json
{
  "id": "uuid",
  "last_name": "string",
  "first_name": "string",
  "patronymic": "string | null",
  "email": "user@example.com",
  "user_type": "UserRole",
  "avatar_url": "string | null",
  "group_id": "uuid | null",
  "in_team": false,
  "created_at": "2026-01-21T22:43:26Z",
  "updated_at": "2026-01-21T22:43:26Z"
}

```

|code|description|example|
|---|---|---|
|201|created|`UserRead`|
|401|unauthorized|`{ "detail": "not authenticated" }`|
|403|forbidden|`{ "detail": "admin only" }`|
|400|bad request|`{ "detail": "User with this email already exists" }`|
|409|conflict|`{ "detail": "User already exists" }`|
|500|internal error|`{ "detail": "Internal Server Error during user creation" }`|

---

### Api route получения пользователя по id

### (GET) `api/admin/users/{user_id}`

#### request:

`Cookie: admin_access_token=...`  
`user_id` — UUID пользователя

#### response:

schemas → `UserRead`  
`src/modules/admin/schemas/user.py`

|code|description|example|
|---|---|---|
|200|ok|`UserRead`|
|401|unauthorized|`{ "detail": "not authenticated" }`|
|403|forbidden|`{ "detail": "admin only" }`|
|404|not found|`{ "detail": "User not found" }`|

---

### Api route получения пользователя по email

### (GET) `api/admin/users/by-email/{email}`

#### request:

`Cookie: admin_access_token=...`  
`email` — email пользователя

#### response:

schemas → `UserRead`  
`src/modules/admin/schemas/user.py`

|code|description|example|
|---|---|---|
|200|ok|`UserRead`|
|401|unauthorized|`{ "detail": "not authenticated" }`|
|403|forbidden|`{ "detail": "admin only" }`|
|404|not found|`{ "detail": "User not found" }`|

---

### Api route обновления профиля пользователя

### (PATCH) `api/admin/users`

> ⚠️ Важно: в логике сервиса **email обязателен**, иначе будет `400 Email is required`.

#### request:

`Cookie: admin_access_token=...`

schemas → `UserProfileUpdate`  
`src/modules/admin/schemas/user.py`

#### response:

schemas → `UserRead`  
`src/modules/admin/schemas/user.py`

|code|description|example|
|---|---|---|
|200|ok|`UserRead`|
|401|unauthorized|`{ "detail": "not authenticated" }`|
|403|forbidden|`{ "detail": "admin only" }`|
|400|bad request|`{ "detail": "Email is required" }`|
|400|bad request|`{ "detail": "Email cannot be null" }`|
|400|bad request|`{ "detail": "User with this email already exists" }`|
|404|not found|`{ "detail": "User not found" }`|
|500|internal error|`{ "detail": "Internal Server Error during user update" }`|

---

### Api route обновления пароля пользователя

### (PATCH) `api/admin/users/password`

> ⚠️ Важно: в логике сервиса **email обязателен**, иначе будет `400 Email is required`.

#### request:

`Cookie: admin_access_token=...`

schemas → `UserPasswordUpdate`  
`src/modules/admin/schemas/user.py`

#### response:

schemas → `UserRead`  
`src/modules/admin/schemas/user.py`

|code|description|example|
|---|---|---|
|200|ok|`UserRead`|
|401|unauthorized|`{ "detail": "not authenticated" }`|
|403|forbidden|`{ "detail": "admin only" }`|
|400|bad request|`{ "detail": "Email is required" }`|
|404|not found|`{ "detail": "User not found" }`|
|500|internal error|`{ "detail": "Internal Server Error during user password update" }`|

---

### Api route удаления пользователя

### (DELETE) `api/admin/users/{user_id}`

#### request:

`Cookie: admin_access_token=...`  
`user_id` — UUID пользователя

#### response:

`204 No Content`

|code|description|example|
|---|---|---|
|204|no content|(пусто)|
|401|unauthorized|`{ "detail": "not authenticated" }`|
|403|forbidden|`{ "detail": "admin only" }`|
|404|not found|`{ "detail": "User not found" }`|
