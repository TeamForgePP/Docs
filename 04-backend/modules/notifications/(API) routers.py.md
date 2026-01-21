### Api route загрузки уведомлений

### (GET) `api/notifications`

#### request:

`Cookie: access_token=...`

#### response:

schemas → `NotificationsResponse`  
`src/modules/notifications/shemas/notifications.py`

```json
{
  "notifications": [
    {
      "id": "uuid",
      "type": "NotificationType",
      "title": "string",
      "message": "string",
      "is_read": false,
      "created_at": "2026-01-21T22:43:26Z",
      "invitation_id": "uuid | null"
    }
  ],
  "unread_count": 1
}
```

| code | description    | example                                                       |
| ---- | -------------- | ------------------------------------------------------------- |
| 200  | ok             | `{ "notifications": [...], "unread_count": 1 }`               |
| 401  | unauthorized   | `{ "detail": "not authenticated" }`                           |
| 500  | internal error | `{ "detail": "Internal Server Error during get notifications info" }` |

---

### Дополнительные ручки страницы уведомлений

#### Кнопка «Отметить всё прочитанным»

`(PATCH) api/notifications/read-all`

**request:**  
`Cookie: access_token=...`

**response:**  
schemas → `BasicResponse`  
`src/modules/notifications/shemas/notifications.py`

```json
{
  "success": true,
  "message": "All notifications read successfully"
}
```

| code | description           | example                                                         |
| ---- | --------------------- | --------------------------------------------------------------- |
| 200  | ok                    | `{ "success": true, "message": "All notifications read successfully" }` |
| 401  | unauthorized          | `{ "detail": "not authenticated" }`                             |
| 500  | internal server error | `{ "detail": "Internal Server Error during read_all" }`         |

---

### Api route получения информации по приглашению

### (GET) `api/user/invitations/{invitation_id}`

Используется при клике на уведомление с `type == "new_invite"` — фронт берёт `invitation_id` из объекта уведомления и дергает эту ручку.
#### request:

`Cookie: access_token=...`
`invitation_id` — id приглашения из поля `notification.invitation_id`
#### response:

schemas → (GET) `/api/user/invitations/{invitation_id}`  
`04-backend/modules/notifications/schemas/response.py.md#для-get-apiuserinvitationsinvitation_id`

```json
{
  "invitation_id": "uuid",
  "notification_id": "uuid",
  "status": "InvitationStatus",
  "project": {
    "id": "uuid",
    "name": "string",
    "description": "string",
    "team_leader": {
      "id": "uuid",
      "first_name": "string",
      "last_name": "string",
      "roles": ["TeamRole"]
    },
    "participants": [
      {
        "id": "uuid",
        "first_name": "string",
        "last_name": "string",
        "roles": ["TeamRole"]
      }
      /* ещё участники */
    ]
  }
}
```

| code | description           | example                                                                        |
| ---- | --------------------- | ------------------------------------------------------------------------------ |
| 401  | unauthorized          | `{ "success": false, "message": "требуется авторизация" }`                     |
| 403  | forbidden             | `{ "success": false, "message": "у вас нет доступа к этому приглашению" }`     |
| 404  | not found             | `{ "success": false, "message": "приглашение не найдено или уже обработано" }` |
| 500  | internal server error | `{ "success": false, "message": "внутренняя ошибка сервера" }`                 |
