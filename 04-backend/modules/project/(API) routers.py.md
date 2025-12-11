## Api route загрузки информации для страницы: 
## (GET) /api/project

#### request:
	Cookie: access_token=...
#### response: [schemas → (GET) /api/project](04-backend/modules/project/schemas/response.py.md#для-get-apiprojectsproject_id)
```json
{
	project_id: uuid,
	project_name: string,
	team:[
		{
			id: uuid,
			first_name: string,
			last_name: string,
			"roles": [ProjectRole],
		    status: UserStatus,
		},
		{
			//ещё участник
		}
	],
	git: string,
	description: string,
	reports:[
		{
			id: uuid,
			url: string,
			creator_id: uuid,
			creator_fn: string, //fn - first name
			creator_ln: string, //ln - last name
			title: string,
			description: string,
			teacher_note: string,
			edit: {
				editor_id: uuid,
				editor_fn: string, //может быть None
				editor_ln: string, //может быть None
				updated_at: string, //может быть None
			}
		},
		{
			//ещё отчет
		}
	],
	allowed_actions:{
		can_edit: boolean,
		can_finish: boolean,
		reports: boolean
	}
}
```

| code   | description           | example                                                                |
| ------ | --------------------- | ---------------------------------------------------------------------- |
| 401    | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`             |
| 403    | forbidden             | `{ "success": false, "message": "у вас нет доступа к этому проекту" }` |
| 404    | not found             | `{ "success": false, "message": "проект не найден" }`                  |
| 500-ки | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }`         |

---

### **Дополнительные ручки страницы:**

#### Кнопка "Редактировать":
`(POST)/api/project/edit`
#### request:
```json
{
	project_id: uuid,
	project_name: string,
	deleted: [
		{
			id: uuid,
		},
		{
			//ещё участник
		}
	],
	invited: [
		{
			id: uuid,
			"roles": [TeamRole],
		},
		{
			//ещё участник
		}
	],
	git: string,
	description: string,
}
```


### **Дополнительные ручки страницы (отчеты):**

---

#### Кнопка **"Добавить отчет"**

`(POST) /api/project/report`

#### request:

`Cookie: access_token=...`

body (json), schemas → (POST) /api/project/report/new

```json
{
  "project_id": UUID,
  "title": string,
  "description": string,
  "file_url": string
}
```

response: 

schemas → BasicResponse
```json
{
  "success": boolean,
  "message": string
}
```

| code   | description           | example                                                                   |
| ------ | --------------------- | ------------------------------------------------------------------------- |
| 400    | bad request           | `{ "success": false, "message": "некорректные или неполные данные" }`     |
| 401    | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`                |
| 403    | forbidden             | `{ "success": false, "message": "у вас нет прав для добавления отчета" }` |
| 404    | not found             | `{ "success": false, "message": "проект не найден" }`                     |
| 422    | unprocessable entity  | `{ "success": false, "message": "ошибка валидации данных отчета" }`       |
| 500-ки | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }`            |
#### Кнопка **"Удалить"** (отчет)

`(DELETE) /api/project/report/{report_id}`

#### request:

`Cookie: access_token=...`
`report_id` — id отчета, который нужно удалить
#### response: schemas → BasicResponse

schemas → BasicResponse
```json
{
  "success": boolean,
  "message": string
}
```

| code   | description           | example                                                                       |
| ------ | --------------------- | ----------------------------------------------------------------------------- |
| 401    | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`                    |
| 403    | forbidden             | `{ "success": false, "message": "у вас нет прав для удаления этого отчета" }` |
| 404    | not found             | `{ "success": false, "message": "отчет не найден или уже удален" }`           |
| 500-ки | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }`                |

#### Кнопка **"Редактировать"** (отчет)

`(POST) /api/project/report/edit`
#### request:
`Cookie: access_token=...`

body (json), schemas → (POST) /api/project/report/edit
```json
{
  "report_id": UUID,
  "project_id": UUID,
  "title": string,
  "description": string,
  "file_url": string
}
```
#### response: schemas → BasicResponse

schemas → BasicResponse
```json
{
  "success": boolean,
  "message": string
}
```

| code   | description           | example                                                                               |
| ------ | --------------------- | ------------------------------------------------------------------------------------- |
| 400    | bad request           | `{ "success": false, "message": "некорректные или неполные данные запроса" }`         |
| 401    | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`                            |
| 403    | forbidden             | `{ "success": false, "message": "у вас нет прав для редактирования этого отчета" }`   |
| 404    | not found             | `{ "success": false, "message": "отчет не найден или не принадлежит этому проекту" }` |
| 422    | unprocessable entity  | `{ "success": false, "message": "ошибка валидации данных отчета" }`                   |
| 500-ки | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }`                        |