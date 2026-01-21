## API: Profile

Ручки страницы **Profile**: получить профиль текущего пользователя и обновить данные профиля.

> **Base prefix:** `api` (все пути ниже уже с `api/...`)  
> Доступ: требуется авторизация **user или admin**, но **профиль доступен только для role=user** (иначе `403`).

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

## Schemas

`src/modules/profile/schemas/profile.py`

### ProfileResponse
```json
{
  "full_name": "string",
  "group": "string | null",
  "role": "string | null",
  "active_project": "string | null",
  "email": "user@example.com"
}
```
> `role` — роли пользователя в активном проекте (например: `"TEAM_LEAD, BACKEND"`).  
> Если активного проекта нет — `active_project=null`, `role="-"`.

### ProfileUpdateRequest

```json
{
  "first_name": "string | null",
  "last_name": "string | null",
  "patronymic": "string | null",
  "group_id": "uuid | null"
}
```

---

## Profile

### Api route получения профиля текущего пользователя

### (GET) `api/profile`

#### request:

`Cookie: user_access_token=...` или `Cookie: admin_access_token=...`

> ⚠️ Важно: если токен не `role=user`, вернётся `403`.

#### response:

schemas → `ProfileResponse`  
`src/modules/profile/schemas/profile.py`

|code|description|example|
|---|---|---|
|200|ok|`ProfileResponse`|
|401|unauthorized|(см. Auth выше)|
|403|forbidden|`{ "detail": "only users have profile" }`|
|401|unauthorized|`{ "detail": "invalid token subject" }`|
|404|not found|`{ "detail": "user not found" }`|
|500|internal error|`{ "detail": "Internal Server Error during get profile" }`|

---

### Api route обновления профиля текущего пользователя

### (PATCH) `api/profile`

#### request:

`Cookie: user_access_token=...` или `Cookie: admin_access_token=...`

schemas → `ProfileUpdateRequest`  
`src/modules/profile/schemas/profile.py`

```json
{
  "first_name": "Иван",
  "last_name": "Иванов",
  "patronymic": "Иванович",
  "group_id": "uuid"
}
```

> `group_id` обновится только если группа существует, иначе `404 group not found`.

#### response:

schemas → `ProfileResponse`  
`src/modules/profile/schemas/profile.py`

|code|description|example|
|---|---|---|
|200|ok|`ProfileResponse`|
|401|unauthorized|(см. Auth выше)|
|403|forbidden|`{ "detail": "only users have profile" }`|
|401|unauthorized|`{ "detail": "invalid token subject" }`|
|404|not found|`{ "detail": "user not found" }`|
|404|not found|`{ "detail": "group not found" }`|
|500|internal error|`{ "detail": "Internal Server Error during update profile" }`|