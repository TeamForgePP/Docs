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
```