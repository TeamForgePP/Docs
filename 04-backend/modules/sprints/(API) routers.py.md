## Страница "Студент спринты"

## Api route загрузки информации для страницы: 
## (GET) api/projects/sprints

#### request:
	Cookie: access_token=...
#### response: [schemas → (GET) /api/projects/sprints](04-backend/modules/sprints/schemas/response.py.md#для-get-apiprojetssprtints)

```json
{
  "current_sprint": {
    "id": uuid,
    "seq": number,
    "name": string,
    "goal": string,
    "description": string,
    "estimated_deadline": string, // YYYY-MM-DD
  },
  "future_sprints": [
    {
      "id": uuid,
      "seq": number,
      "name": string
    }
  ],
  "completed_sprints": [
    {
      "id": uuid,
      "seq": number,
      "name": string
    }
  ]
}
```

| code   | description           | example                                                                                 |
| ------ | --------------------- | --------------------------------------------------------------------------------------- |
| 401    | unauthorized          | `{ "success": false, "code": "UNAUTHORIZED", message": "authorization required" }`      |
| 500-ки | internal server error | `{ "success": false, "code": "INTERNAL_ERROR", message": "внутренняя ошибка сервера" }` |


### **Дополнительные ручки страницы:**

#### Кнопка "Новый спринт"
`(POST) api/projects/sprints/new-sprint
request:  [schemas → (POST) api/projects/sprints/new-sprint](04-backend/modules/sprints/schemas/request.py.md#для-apiprojectskanbannew-sprint)

```json
{
  "name": string,
  "start_date": string,
  //YYYY-MM-DD
  "end_date": string,
  //YYYY-MM-DD
  "goal": string,
  "description": string
}
```

response: [schemas → BasicResponse](04-backend/modules/sprints/schemas/response.py.md#basicresponse)
```json
{
	success: boolean,
	message: string
}
```

| code   | description           | example                                                        |
| ------ | --------------------- | -------------------------------------------------------------- |
| 201    | created               | `{ "success": true, "message": "спринт успешно создан" }`      |
| 400    | bad request           | `{ "success": false, "message": "введены неверные данные" }`   |
| 401    | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`     |
| 409    | conflict              | `{ "success": false, "message": "спринт уже существует" }`     |
| 500-ки | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }` |


#### Ручка для просмотра спринта
`(GET) api/projects/sprints/{sprint_id}

#### request:
	Cookie: access_token=...
#### response: [schemas → (GET) /api/projects/sprints/{sprint_id}](04-backend/modules/sprints/schemas/response.py.md#для-get-apiprojetssprtintssprint-id)

```json
{
  "name": string,
  "start_date": string, //YYYY-MM-DD
  "end_date": string, //YYYY-MM-DD
  "goal": string,
  "description": string,
}
```

| code   | description           | example                                                   |
| ------ | --------------------- | --------------------------------------------------------- |
| 401    | unauthorized          | `{ "success": false, message": "требуется авторизация" }` |
| 404    | not found             | `{ "success": false, message": "спринт не найден" }`      |
| 500-ки | internal server error | `{ "success": false, "внутренняя ошибка сервера" }`       |


#### Кнопка "Редактировать"
`(PATCH) api/projects/sprints/{sprint_id}
request:  [schemas → (PATCH) api/projects/sprints/{sprint_id}](04-backend/modules/sprints/schemas/request.py.md#для-apiprojectssprintssprint-id)

```json
{
  "name": string,
  "start_date": string,
  //YYYY-MM-DD
  "end_date": string,
  //YYYY-MM-DD
  "goal": string,
  "description": string
}
```

response: [schemas → BasicResponse](04-backend/modules/sprints/schemas/response.py.md#basicresponse)
```json
{
	success: boolean,
	message: string,
}
```

| code   | description           | example                                                           |
| ------ | --------------------- | ----------------------------------------------------------------- |
| 200    | OK                    | `{ "success": true, "message": "спринт успешно отредактирован" }` |
| 400    | bad request           | `{ "success": false, "message": "неверно введены данные" }`       |
| 401    | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`        |
| 404    | not found             | `{ "success": false, "message": "спринт не найден" }`             |
| 409    | conflict              | `{ "success": false, "message": "спринт уже завершен" }`          |
| 500-ки | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }`    |


#### Кнопка "Завершить спринт и начать новый"
`(POST) api/projects/sprints/{sprint_id}/complete
request:  [schemas → (POST) api/projects/sprints/{sprint_id}/complete](04-backend/modules/sprints/schemas/request.py.md#для-apiprojectskanbansprint-idcomplete)

без тела

response: [schemas → BasicResponse](04-backend/modules/sprints/schemas/response.py.md#basicresponse)
```json
{
	success: boolean,
	message: string,
}
```

| code   | description           | example                                                                                              |
| ------ | --------------------- | ---------------------------------------------------------------------------------------------------- |
| 200    | OK                    | `{ "success": true, "message": "спринт завершен" }`                                                  |
| 401    | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`                                           |
| 404    | not found             | `{ "success": false, "message": "спринт не найден" }`                                                |
| 409    | conflict              | `{ "success": false, "message": "спринт уже завершен или нет следующего спринта для переключения" }` |
| 500-ки | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }`                                       |
