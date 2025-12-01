## Api route загрузки информации для страницы: 
## (GET) api/user/home`

#### request:
	Cookie: access_token=...
#### response: [schemas → (GET) /api/user/home](04-backend/modules/home/schemas/response.py.md#для-get-apiuserhome)
```json
{
	projects:[
		{
			id: uuid,
			name: string,
			is_complited: boolean,
			current_sprint_name: string,
			current_sprint_seq: number,
			"roles": [
        "team_lead"|"backend"|"frontend"|"devops"|
        "manager"|"product_manager"|"business_analyst"|"curator"
        //может быть несколько ролей, перечисляются через запятную
		    ],
			nearest_deadline: string, // YYYY-MM-DD
			sprint_map:[
				{
					id: uuid,
					name: string,
					seq: number,
					deadline: string
				},
				{
					// другие спринты
				}
			],
			allowed_actions:{
				can_delete: boolean,
				can_leave: boolean
			}
		},
		{
			// другие проекты
		}
	]
}
```

| code | description    | example                                                                              |
| ---- | -------------- | ------------------------------------------------------------------------------------ |
| 401  | unauthorized   | `{ "success": false, "code": "UNAUTHORIZED", "message": "authorization required" }`  |
| 500  | internal error | `{ "success": false, "code": "INTERNAL_ERROR", "message": "internal server error" }` |


---
### **Дополнительные ручки страницы:**

#### Кнопка "Новый проект":
`(POST) api/user/home/new-project`
request:  [schemas → (POST) api/user/home/new-project](04-backend/modules/home/schemas/request.py.md#для-post-apiuserhomenew-project)
```json
{
	"name": string,
	"description": string,
	"team": [
		{
			"id": uuid,
			"roles": [
			"team_lead"|"backend"|"frontend"|"devops"|
	        "manager"|"product_manager"|"business_analyst"|"curator"
	        //может быть несколько ролей, перечисляются через запятную
			]
	    },
	    {
		      // ещё приглашенный участрник
	    }
	],
	"git_organization": string
}
```
response: [schemas → BasicResponse](04-backend/modules/home/schemas/response.py.md#basicresponse)
```json
{
	success: boolean,
	message: string,
}
```

| code | description           | example                                                                                |
| ---- | --------------------- | -------------------------------------------------------------------------------------- |
| 400  | bad request           | `{ "success": false, "message": "неверные или неполные данные запроса" }`              |
| 401  | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`                             |
| 403  | forbidden             | `{ "success": false, "message": "у вас нет прав для создания проекта" }`               |
| 409  | conflict              | `{ "success": false, "message": "проект с таким названием уже существует" }`           |
| 422  | unprocessable entity  | `{ "success": false, "message": "ошибка валидации данных или неверный формат ролей" }` |
| 500  | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }`                         |

#### Кнопка "удалить":
`(DELETE) user/home/{project_id}/delete`
**request:***
	`Cookie: access_token=...`
**response:** [schemas → BasicResponse](04-backend/modules/home/schemas/response.py.md#basicresponse)
```json
{
	success: boolean,
	message: string,
}
```

| code | description           | example                                                              |
| ---- | --------------------- | -------------------------------------------------------------------- |
| 401  | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`           |
| 403  | forbidden             | `{ "success": false, "message": "нет прав" }`                        |
| 404  | not found             | `{ "success": false, "message": "проект не найден или уже удалён" }` |
| 500  | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }`       |

#### Кнопка "покинуть":
`(POST) user/home/{project_id}/leave`
**request:***
	`Cookie: access_token=...`
**response:** [schemas → BasicResponse](04-backend/modules/home/schemas/response.py.md#basicresponse)
```json
{
	success: boolean,
	message: string,
}
```

| code | description           | example                                                        |
| ---- | --------------------- | -------------------------------------------------------------- |
| 401  | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`     |
| 403  | forbidden             | `{ "success": false, "message": "нет прав" }`                  |
| 404  | not found             | `{ "success": false, "message": "проект не найден" }`          |
| 500  | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }` |


## Модуль входа

### Api route для страницы входа

### (POST) `api/user/login`

#### request:

`Content-Type: application/json`

schemas → (POST) `/api/user/login`  
`04-backend/modules/auth/schemas/request.py.md#для-post-apiuserlogin`

```json
{   
	"login": string,    
    "password":
}
```

> При успешном входе backend выставляет httpOnly cookie  
> `access_token=...` (и при необходимости `refresh_token=...`).  
> В теле ответа возвращается только статус операции.

#### response:

schemas → `AuthLoginResponse`  
`04-backend/modules/auth/schemas/response.py.md#authloginresponse`

```json
{   
	"success": boolean,   
	"message": string 
}
```

| code | description           | example                                                                            |
| ---- | --------------------- | ---------------------------------------------------------------------------------- |
| 400  | bad request           | `{ "success": false, "message": "неверные или неполные данные запроса" }`          |
| 401  | unauthorized          | `{ "success": false, "message": "неверный логин или пароль" }`                     |
| 429  | too many requests     | `{ "success": false, "message": "слишком много попыток входа, попробуйте позже" }` |
| 500  | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }`                     |


## Модуль уведомлений

### Api route загрузки информации для страницы уведомлений

### (GET) `api/user/notifications`

#### request:

`Cookie: access_token=...`

#### response:

schemas → (GET) `/api/user/notifications`  
`04-backend/modules/notifications/schemas/response.py.md#для-get-apiusernotifications`

`
```json
{
  "notifications": [
    {
      "id": uuid,
      "type": "new_task" | "new_invite" | "project_closed" | "deadline" | "removed_from_project",
      "title": string,
      "message": string,
      "project_id": uuid | null,
      "is_read": boolean,
      "created_at": string,      // ISO datetime, YYYY-MM-DDTHH:MM:SSZ
      "invitation_id": uuid | null   // если type == "new_invite", иначе null
    },
    {
      // другие уведомления
    }
  ],
  "unread_count": number  // количество непрочитанных уведомлений
}

```

|code|description|example|
|---|---|---|
|401|unauthorized|`{ "success": false, "code": "UNAUTHORIZED", "message": "authorization required" }`|
|500|internal error|`{ "success": false, "code": "INTERNAL_ERROR", "message": "internal server error" }`|

---

### Дополнительные ручки страницы уведомлений

#### Кнопка «Отметить прочитанным» (одно уведомление)

`(PATCH) api/user/notifications/{notification_id}/read`

**request:**  
`Cookie: access_token=...`

без тела — id берётся из пути.

**response:**  
schemas → `BasicResponse`  
`04-backend/modules/notifications/schemas/response.py.md#basicresponse`

```json
{   
	"success": boolean,
	"message": string
}
```

| code | description           | example                                                                |
| ---- | --------------------- | ---------------------------------------------------------------------- |
| 401  | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`             |
| 403  | forbidden             | `{ "success": false, "message": "нет прав на изменение уведомления" }` |
| 404  | not found             | `{ "success": false, "message": "уведомление не найдено" }`            |
| 500  | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }`         |

---

#### Кнопка «Отметить всё прочитанным»

`(POST) api/user/notifications/read-all`

**request:**  
`Cookie: access_token=...`

без тела.

**response:**  
schemas → `BasicResponse`  
`04-backend/modules/notifications/schemas/response.py.md#basicresponse`

```json
{   
"success": boolean,   
"message": string 
}
```

| code | description           | example                                                        |
| ---- | --------------------- | -------------------------------------------------------------- |
| 401  | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`     |
| 500  | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }` |

---

#### (опционально) Кнопка «Удалить уведомление»

Если будете позволять пользователю очищать список.

`(DELETE) api/user/notifications/{notification_id}`

**request:**  
`Cookie: access_token=...`

**response:**  
schemas → `BasicResponse`  
`04-backend/modules/notifications/schemas/response.py.md#basicresponse`

```json
{   
"success": boolean,   
"message": string 
}
```

| code | description           | example                                                               |
| ---- | --------------------- | --------------------------------------------------------------------- |
| 401  | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`            |
| 403  | forbidden             | `{ "success": false, "message": "нет прав на удаление уведомления" }` |
| 404  | not found             | `{ "success": false, "message": "уведомление не найдено" }`           |
| 500  | internal server error | `{ "success": false", "message": "внутренняя ошибка сервера" }`       |
