Общая структура проекта:
```
src/
├── config/ - загрузка конфигурации проекта из config.toml
├── core/
│   ├── db/
│   │   ├── base.py
│   │   ├── db.py
│   │   ├── session.py 
│   │   ├── models/ - модели таблиц db 
│   │   └── reposytory/ - репозитории для взаимодействия с моделями db
│	└── interfaces/ - ABC класс - шаблон для репозиториев и сервисов
├── modules/
│   ├── home/ 
│   │   ├── router.py       
│   │   ├── schemas.py      
│   │   ├── repository.py    
│   │   └── service.py      
│   ├── profile/
│   │   ├── router.py
│   │   ├── schemas.py
│   │   ├── repository.py
│   │   └── service.py
│   ├── project/
│   │   ├── router.py
│   │   ├── schemas.py
│   │   ├── repository.py
│   │   └── service.py
│   ├── kanban/
│   │   ├── router.py
│   │   ├── schemas.py
│   │   ├── repository.py
│   │   └── service.py
│   └── sprints/
│       ├── router.py
│       ├── schemas.py
│       ├── repository.py
│       └── service.py
├── api.py        # собираешь все роутеры отсюда
├── main.py       # create_app / FastAPI(app) и include_router
├── config.toml #пример файла со всем необходимым для работы проекта
└── .env #пример файла со всем необходимым для запуска проекта

```