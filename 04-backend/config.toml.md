Основной конфигурационный файл проекта. Хранит параметры подключения к базе данных, MinIO и другие настройки, используемые приложением во время работы.

```toml
[database]  
postgres_username = "postgres"  
postgres_db = "teamforge"  
postgres_port = 5432  
postgres_host = "postgres"  
postgres_password = "postgres"  
  
[minio]  
minio_endpoint = "http://minio:9000"  
minio_access_key = "minio"  
minio_secret_key = "12345678"  
minio_root_user = "minio"  
minio_root_password = "12345678"  
minio_bucket = "media"  
  
[logging]  
level = "INFO"  
  
[admin]  
login = "admin"  
password = "admin"  
  
[admin.cookies]  
access = "admin_access_token"  
refresh = "admin_refresh_token"  
  
[jwt]  
secret = "gchgkluiyrtudysfhxgcnhvfjdtsrgfxcvgryetydshfxcnbvmhkgfyruetydsh"  
access_token_minutes = 1800 #30минут  
refresh_token_days = 604800 #7 дней  
algorithm = "HS256"  
  
[redis]  
url = "redis://redis:6379/0"  
login_attempts_prefix = "auth:admin_login_attempts:"  
login_attempts_ttl_seconds = 300   # 5 минут  
login_attempts_max = 5             # макс попыток за TTL
```