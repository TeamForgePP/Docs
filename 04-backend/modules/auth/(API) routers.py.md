## API: Auth

Модуль отвечает за **логин / refresh / logout** для **user** и **admin**.

> **Base prefix:** `api` (все пути ниже уже с `api/...`)  
> Авторизация/сессии — через **httpOnly cookies**.

---

## Cookies

### Admin cookies
- `admin_access_token=...`
- `admin_refresh_token=...`

### User cookies
- `user_access_token=...`
- `user_refresh_token=...`

> При `login` и `refresh` backend **выставляет/обновляет** cookies.  
> При `logout` backend **удаляет** cookies.

---

## User Auth

### Api route для входа пользователя

### (POST) `api/auth/user/login`

#### request:
schemas → `UserLoginRequest`  
`src/modules/auth/schemas/user.py`
```json
{
  "email": "user@example.com",
  "password": "string"
}
```

> При успешном входе backend выставляет httpOnly cookie:  
> `user_access_token=...` и `user_refresh_token=...`

#### response:

schemas → `UserTokenPair`  
`src/modules/auth/schemas/user.py`

```json
{
  "access_token": "string",
  "refresh_token": "string",
  "token_type": "bearer"
}
```

|code|description|example|
|---|---|---|
|200|ok|`UserTokenPair`|
|401|unauthorized|`{ "detail": "invalid credentials (attempts: N)" }`|
|429|too many requests|`{ "detail": "too many login attempts, try again later" }`|
|500|internal error|(внутренняя ошибка сервера)|

---

### Api route для обновления токенов пользователя

### (POST) `api/auth/user/refresh`

#### request:

`Cookie: user_refresh_token=...`

> refresh берётся **из cookie**, body не требуется.

#### response:

schemas → `UserTokenPair`  
`src/modules/auth/schemas/user.py`

> Backend обновляет cookies:  
> `user_access_token=...` и `user_refresh_token=...`

```json
{
  "access_token": "string",
  "refresh_token": "string",
  "token_type": "bearer"
}
```

|code|description|example|
|---|---|---|
|200|ok|`UserTokenPair`|
|401|unauthorized|`{ "detail": "refresh token missing" }`|
|401|unauthorized|`{ "detail": "invalid refresh token" }`|
|401|unauthorized|`{ "detail": "refresh token rejected" }`|
|500|internal error|(внутренняя ошибка сервера)|

---

### Api route для выхода пользователя

### (POST) `api/auth/user/logout`

#### request:

необязательно, но обычно клиент отправляет текущие cookies (если они есть)

#### response:

`204 No Content`

> Backend удаляет cookies:  
> `user_access_token` и `user_refresh_token`

|code|description|example|
|---|---|---|
|204|no content|(пусто)|
|500|internal error|(внутренняя ошибка сервера)|

---

## Admin Auth

### Api route для входа администратора

### (POST) `api/auth/admin/login`

#### request:

schemas → `AdminLoginRequest`  
`src/modules/auth/schemas/admin.py`

```json
{
  "username": "string",
  "password": "string"
}
```

> При успешном входе backend выставляет httpOnly cookie:  
> `admin_access_token=...` и `admin_refresh_token=...`

#### response:

schemas → `AdminTokenPair`  
`src/modules/auth/schemas/admin.py`

```json
{
  "access_token": "string",
  "refresh_token": "string",
  "token_type": "bearer"
}
```

|code|description|example|
|---|---|---|
|200|ok|`AdminTokenPair`|
|401|unauthorized|`{ "detail": "invalid admin credentials (attempts: N)" }`|
|429|too many requests|`{ "detail": "too many login attempts, try again later" }`|
|500|internal error|(внутренняя ошибка сервера)|

---

### Api route для обновления токенов администратора

### (POST) `api/auth/admin/refresh`

#### request:

`Cookie: admin_refresh_token=...`

#### response:

schemas → `AdminTokenPair`  
`src/modules/auth/schemas/admin.py`

> Backend обновляет cookies:  
> `admin_access_token=...` и `admin_refresh_token=...`

```json
{
  "access_token": "string",
  "refresh_token": "string",
  "token_type": "bearer"
}
```

|code|description|example|
|---|---|---|
|200|ok|`AdminTokenPair`|
|401|unauthorized|`{ "detail": "refresh token missing" }`|
|401|unauthorized|`{ "detail": "invalid refresh token" }`|
|401|unauthorized|`{ "detail": "refresh token rejected" }`|
|500|internal error|(внутренняя ошибка сервера)|

---

### Api route для выхода администратора

### (POST) `api/auth/admin/logout`

#### request:

необязательно, но обычно клиент отправляет текущие cookies (если они есть)

#### response:

`204 No Content`

> Backend удаляет cookies:  
> `admin_access_token` и `admin_refresh_token`

|code|description|example|
|---|---|---|
|204|no content|(пусто)|
|500|internal error|(внутренняя ошибка сервера)|
