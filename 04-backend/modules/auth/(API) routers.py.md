## Api route для страницы входа

## (POST) `api/user/login`

#### request:

[schemas → (POST) /api/user/login](04-backend/modules/auth/schemas/request.py.md#для-post-apiuserlogin)
```json
{   
	"login": string,    
    "password": string
}
```

> При успешном входе backend выставляет httpOnly cookie  
> `access_token=...` (и при необходимости `refresh_token=...`).  
> В теле ответа возвращается только статус операции.

#### response:
[schemas → BasicResponse](04-backend/modules/auth/schemas/response.py.md#authloginresponse)
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
