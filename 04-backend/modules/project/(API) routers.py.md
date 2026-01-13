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
			file_key: string,
			view_url: string,
			status: "uploading" | "ready" | "failed",
			content_type: string | null,
			size_bytes: number | null,
			creator_id: uuid,
			creator_fn: string,
			creator_ln: string,
			title: string,
			description: string | null,
			teacher_note: string | null,
			edit: {
				editor_id: uuid | null,
				editor_fn: string | null,
				editor_ln: string | null,
				updated_at: string | null
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

#### Кнопка **"Добавить отчет"** (создать черновик + получить ссылку для загрузки)

`(POST) /api/project/report`

#### request:

`Cookie: access_token=...`

body (json):

```json
{
  "project_id": "UUID",
  "title": "string",
  "description": "string | null",
  "teacher_note": "string | null",
  "file": {
    "content_type": "string | null",
    "size_bytes": "number"
  }
}
```

#### response:

```json
{
  "success": true,
  "message": "string",
  "report": {
    "id": "UUID",
    "file_key": "string",
    "status": "uploading",
    "view_url": "string",
    "upload": {
      "method": "POST",
      "url": "string",
      "fields": {
        "key": "string",
        "Content-Type": "string",
        "...": "..."
      }
    }
  }
}
```

| code   | description           | example                                                                   |
| ------ | --------------------- | ------------------------------------------------------------------------- |
| 400    | bad request           | `{ "success": false, "message": "некорректные или неполные данные" }`     |
| 401    | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`                |
| 403    | forbidden             | `{ "success": false, "message": "у вас нет прав для добавления отчета" }` |
| 404    | not found             | `{ "success": false, "message": "проект не найден" }`                     |
| 422    | unprocessable entity  | `{ "success": false, "message": "ошибка валидации данных" }`              |
| 500-ки | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }`            |

---

#### Загрузка файла (со стороны фронта)

Фронт загружает файл напрямую в MinIO по `report.upload` (presigned POST).
После успешной загрузки нужно вызвать `finalize`.

---

#### метод **"Finalize"** (подтверждение загрузки файла)

`(POST) /api/project/report/finalize`

#### request:

`Cookie: access_token=...`

body (json):

```json
{
  "report_id": "UUID"
}
```

#### response:

```json
{
  "success": true,
  "message": "string",
  "report": {
    "id": "UUID",
    "file_key": "string",
    "status": "ready",
    "view_url": "string",
    "content_type": "string | null",
    "size_bytes": "number | null"
  }
}
```

| code   | description           | example                                                                    |
| ------ | --------------------- | -------------------------------------------------------------------------- |
| 400    | bad request           | `{ "success": false, "message": "ошибка проверки файла" }`                 |
| 401    | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`                 |
| 403    | forbidden             | `{ "success": false, "message": "у вас нет прав для подтверждения отчета" }` |
| 404    | not found             | `{ "success": false, "message": "отчет не найден" }`                       |
| 500-ки | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }`             |

---

#### Кнопка **"Удалить"** (отчет)

`(DELETE) /api/project/report/{report_id}`

#### request:

`Cookie: access_token=...`

`report_id` — id отчета, который нужно удалить

#### response:

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

---

#### Кнопка **"Редактировать"** (отчет)

`(POST) /api/project/report/edit`

#### request:

`Cookie: access_token=...`

body (json):

```json
{
  "report_id": "UUID",
  "project_id": "UUID",
  "title": "string",
  "description": "string | null",
  "teacher_note": "string | null"
}
```

#### response:

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
