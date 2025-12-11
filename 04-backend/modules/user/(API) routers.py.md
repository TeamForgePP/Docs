## Api route загрузки информации для страницы: 
## (GET) api/user/profile`

#### request:
	Cookie: access_token=...
#### response: [schemas → (GET) /api/user/profile](04-backend/modules/user/schemas/response.py.md#для-get-apiuserprofile)
```json
{
	"first_name": string,
	"last_name": string,
	"patronymic": string,
	"group": string,
	"roles": [TeamRole],
	"email": string
}
```

| code   | description           | example                                                        |
| ------ | --------------------- | -------------------------------------------------------------- |
| 401    | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`     |
| 404    | not found             | `{ "success": false, "message": "пользователь не найден" }`    |
| 500-ки | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }` |

---

### **Дополнительные ручки страницы:**

#### Кнопка "Редактировать":
`(POST) api/user/profile/edit`
#### request: [schemas → (POST) /api/user/profile/edit](04-backend/modules/user/schemas/request.py.md#для-post-apiuserprofileedit)
```json
{
	"first_name": string,
	"last_name": string,
	"patronymic": string,
	"email": string
}
```
#### response: [schemas → BasicResponse](04-backend/modules/user/schemas/response.py.md#basicresponse)
```json
{
	"success": boolean,
	"message": string
}
```

| code   | description           | example                                                                 |
| ------ | --------------------- | ----------------------------------------------------------------------- |
| 400    | bad request           | `{ "success": false, "message": "некорректные данные запроса" }`        |
| 401    | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`              |
| 403    | forbidden             | `{ "success": false, "message": "нельзя изменить эти данные профиля" }` |
| 404    | not found             | `{ "success": false, "message": "пользователь не найден" }`             |
| 500-ки | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }`          |

#### Кнопка "Разлогинить":
`(POST) api/user/profile/logout`
#### request:
	Cookie: access_token=...
#### response: [schemas → BasicResponse](04-backend/modules/user/schemas/response.py.md#basicresponse)
```json
{
	"success": boolean,
	"message": string
}
```

| code   | description           | example                                                        |
| ------ | --------------------- | -------------------------------------------------------------- |
| 401    | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`     |
| 500-ки | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }` |
#### Кнопка "Сменить пароль":
`(POST) api/user/profile/edit-password`
#### request: [schemas → (POST) /api/user/profile/edit](04-backend/modules/user/schemas/request.py.md#для-post-apiuserprofileedit)
	Cookie: access_token=...

```json
{
  "old_password": string,
  "new_password": string
}
```
#### response: [schemas → BasicResponse](04-backend/modules/user/schemas/response.py.md#basicresponse)
```json
{
	"success": boolean,
	"message": string
}
```

| code   | description           | example                                                         |
| ------ | --------------------- | --------------------------------------------------------------- |
| 400    | bad request           | { "success": false, "message": "неверный текущий пароль" }      |
| 401    | unauthorized          | { "success": false, "message": "требуется авторизация" }        |
| 403    | forbidden             | { "success": false, "message": "нельзя изменить этот профиль" } |
| 404    | not found             | { "success": false, "message": "пользователь не найден" }       |
| 500-ки | internal server error | { "success": false, "message": "внутренняя ошибка сервера" }    |
