## Api route загрузки информации для страницы (актуальный спринт): 
## (GET) api/projects/kanban/

#### request:
	Cookie: access_token=...
#### response: [schemas → (GET) /api/projects/kanban](04-backend/modules/kanban/schemas/response.py.md#для-get-apiprojetskanban)

```json
{
  "project": {
    "id": uuid,
    "name": string
  },
  "selected_sprint": {
      "id": uuid,
      "seq": number
  },
  "tasks": [
    {
      "id": uuid,
      "title": string,
      "tags": [TeamRole],
      "status": TaskStatus,
      "deadline": string, // YYYY-MM-DD
      "priority": TaskPriority, 
      "performes": [
	      {
	        "id": uuid,
	        "first_name": string,
	        "last_name": string,
	        "avatar_url": string
	      },
	      {
		      //ещё исполнители
	      }
      ],
      "key": string,
    },
    {
	    //ещё таски
    }
  ]
}
```

| code   | description    | example                                                       |
| ------ | -------------- | ------------------------------------------------------------- |
| 401    | unauthorized   | `{ "success": false, "message": "authorization required" }`   |
| 500-ки | internal error | ` "success": false, "message": "внутренняя ошибка сервера" }` |

# Ручка на получение тасок по конкретному спринту
## (GET) api/projects/kanban/{sprint_id}

#### request:
	Cookie: access_token=...
#### response: [schemas → (GET) /api/projects/kanban](04-backend/modules/kanban/schemas/response.py.md#для-get-apiprojetskanban)

```json
{
  "project": {
    "id": uuid,
    "name": string
  },
  "selected_sprint": {
      "id": uuid,
      "seq": number
  },
  "tasks": [
    {
      "id": uuid,
      "title": string,
      "tags": [TeamRole],
      "status": TaskStatus,
      "deadline": string, // YYYY-MM-DD
      "priority": TaskPriority, 
      "performes": [
	      {
	        "id": uuid,
	        "first_name": string,
	        "last_name": string,
	        "avatar_url": string
	      },
	      {
		      //ещё исполнители
	      }
      ],
      "key": string,
    },
    {
	    //ещё таски
    }
  ]
}
```

| code | description    | example                                                        |
| ---- | -------------- | -------------------------------------------------------------- |
| 401  | unauthorized   | `{ "success": false, "message": "требуется авторизация" }`     |
| 500  | internal error | `{ "success": false, "message": "внутренняя ошибка сервера" }` |


### **Дополнительные ручки страницы:**

#### Кнопка "Новая задача"
`(POST) api/projects/kanban/new-task
request:  [schemas → (POST) api/projects/kanban/new-task](04-backend/modules/kanban/schemas/request.py.md#для-apiprojectskanbannew-task)

```json
{
  "title": string,
  "description": string,
  "performes": [
	  {
		  	"id": uuid,
	  },
	  {
		  //ещё исполнитель
	  }
  ],
  "priority": TaskPriority, 
  "deadline": string, // YYYY-MM-DD
}
```
response: [schemas → BasicResponse](04-backend/modules/kanban/schemas/response.py.md#basicresponse)
```json
{
	success: boolean,
	message: string,
}
```

| code   | description           | example                                                          |
| ------ | --------------------- | ---------------------------------------------------------------- |
| 201    | created               | `{ "success": true, "message": "задача успешно создана" }`       |
| 400    | bad request           | `{ "success": false, "message": "ошибка валидации данных" }`     |
| 401    | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`       |
| 403    | forbidden             | `{ "success": false, "message": "нет прав на создание задачи" }` |
| 404    | not found             | `{ "success": false, "message": "указанный user не найден" }`    |
| 500-ки | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }`   |

#### Ручка для получения всех участников команды
`(GET) api/projects/kanban/team-members

#### request:
	Cookie: access_token=...
#### response: [schemas → (GET) /api/projects/kanban/team-members](04-backend/modules/kanban/schemas/response.py.md#для-get-apiprojetskanbanteam-members)

```json
{
  "members": [
    {
      "id": uuid,
      "first_name": string,
      "last_name": string,
      "avatar_url": string
    }
  ]
}
```

| code   | description           | example                                                                                 |
| ------ | --------------------- | --------------------------------------------------------------------------------------- |
| 200    | OK                    | `{ "success": true, message": "список участников успешно получен" }`                    |
| 401    | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`                              |
| 403    | forbidden             | `{ "success": false, "message": "недостаточно прав для просмотра участников команды" }` |
| 500-ки | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }`                          |

#### Ручка для просмотра задачи
`(GET) api/projects/kanban/{task_id}

#### request:
	Cookie: access_token=...
#### response: [schemas → (GET) /api/projects/kanban/{task_id}](04-backend/modules/kanban/schemas/response.py.md#для-get-apiprojetskanbantask-id)

```json
{
  "id": uuid,
  "title": string,
  "description": string,
  "user": {
    "id": uuid,
    "first_name": string,
    "last_name": string,
    "avatar_url": string
  },
  "priority": ["high" | "medium" | "low"], 
  "deadline": string, // YYYY-MM-DD
}
```

| code   | description           | example                                                        |
| ------ | --------------------- | -------------------------------------------------------------- |
| 200    | OK                    | `{ "success": true, "message": "задача успешно получена" }`    |
| 401    | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`     |
| 403    | forbidden             | `{ "success": false, "message": "доступ к задаче запрещен }`   |
| 404    | not found             | `{ "success": false, "message": "задача не найдена" }`         |
| 500-ки | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }` |

#### Ручка для обновления статуса задачи
`(POST) api/projects/kanban/update-status
request:  [schemas → (POST) api/projects/kanban/update-status](04-backend/modules/kanban/schemas/request.py.md#для-apiprojectskanbanupdate-status)

```json
{
  "task_id": uuid,
  "status": TaskStatus
}
```

response: [schemas → BasicResponse](04-backend/modules/kanban/schemas/response.py.md#basicresponse)
```json
{
	success: boolean,
	message: string,
}
```

| code   | description           | example                                                                   |
| ------ | --------------------- | ------------------------------------------------------------------------- |
| 200    | OK                    | `{ "success": true, "message": "статус задачи успешно обновлен" }`        |
| 400    | bad request           | `{ "success": false, "message": "ошибка валидации данных" }`              |
| 401    | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`                |
| 403    | forbidden             | `{ "success": false, "message": "нет прав на изменение статуса задачи" }` |
| 404    | not found             | `{ "success": false, "message": "указанный user не найден" }`             |
| 500-ки | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }`            |
|        |                       |                                                                           |

==ручка на обновление всей задачи==