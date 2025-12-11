### Api route загрузки уведомлений

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
      "type": NotificationType,
      "title": string,
      "message": string,
      "is_read": boolean,
      "created_at": string,      // ISO datetime, YYYY-MM-DDTHH:MM:SSZ
      "invitation_id": uuid | null   // если type == "new_invite"
    },
    {
      // другие уведомления
    }
  ],
  "unread_count": number  // количество непрочитанных уведомлений
}

```

| code   | description    | example                                                       |
| ------ | -------------- | ------------------------------------------------------------- |
| 401    | unauthorized   | `{ "success": false, "message": "authorization required" }`   |
| 500-ки | internal error | ` "success": false, "message": "внутренняя ошибка сервера" }` |

---

### Дополнительные ручки страницы уведомлений
#### Кнопка «Отметить всё прочитанным»

`(PATCH) api/user/notifications/read-all`

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

| code   | description           | example                                                        |
| ------ | --------------------- | -------------------------------------------------------------- |
| 401    | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`     |
| 500-ки | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }` |


### Api route получения информации по приглашению

### (GET) `api/user/invitations/{invitation_id}`

используется при клике на уведомление с `type == "new_invite"` – фронт берет `invitation_id` из объекта уведомления и дергает эту ручку.

#### request:

`Cookie: access_token=...`
`invitation_id` — id приглашения из поля `notification.invitation_id`
#### response:

schemas → (GET) `/api/user/invitations/{invitation_id}`  
`04-backend/modules/notifications/schemas/response.py.md#для-get-apiuserinvitationsinvitation_id`

```json
{
  "invitation_id": uuid,
  "notification_id": uuid,
  "status": InvitationStatus,
  "project": {
    "id": uuid,
    "name": string,
    "description": string,
    "team_leader": {
      "id": uuid,
      "first_name": string,
      "last_name": string,
      "roles": [TeamRole]
    },
    "participants": [
      {
        "id": uuid,
        "first_name": string,
        "last_name": string,
        "roles": [TeamRole]
      }
      /* ещё участники */
    ]
  },
}

```

| code | description           | example                                                                        |
| ---- | --------------------- | ------------------------------------------------------------------------------ |
| 401  | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`                     |
| 403  | forbidden             | `{ "success": false, "message": "у вас нет доступа к этому приглашению" }`     |
| 404  | not found             | `{ "success": false, "message": "приглашение не найдено или уже обработано" }` |
| 500  | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }`                 |