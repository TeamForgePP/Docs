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

| code | description    | example                                                                                                              |
| ---- | -------------- | -------------------------------------------------------------------------------------------------------------------- |
| 401  | unauthorized   | `{ "success": false, "code": "UNAUTHORIZED", "message": "authorization required" }`                                  |
| 500  | internal error | `{ "success": false, "code": "INTERNAL_ERROR", "message": "internal server error" }`                                 |


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
