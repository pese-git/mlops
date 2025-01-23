Инструкция по настройке и запуску LakeFS с использованием Docker Compose с предварительно написанными конфигурациями и скриптами:

### Подготовка

1. **Создайте файл конфигурации `.env`:**

   Сохраните следующую конфигурацию в файле `.env` в вашей рабочей директории:

   ```dotenv
   LAKEFS_DATABASE_TYPE=postgres
   LAKEFS_DATABASE_POSTGRES_CONNECTION_STRING=postgres://lakefs:lakefs@postgres:5432/lakefs?sslmode=disable
   LAKEFS_BLOCKSTORE_TYPE=s3
   LAKEFS_BLOCKSTORE_S3_FORCE_PATH_STYLE=true
   LAKEFS_BLOCKSTORE_S3_ENDPOINT=http://minio:9000
   LAKEFS_BLOCKSTORE_S3_CREDENTIALS_ACCESS_KEY_ID=minio
   LAKEFS_BLOCKSTORE_S3_CREDENTIALS_SECRET_ACCESS_KEY=minio123
   LAKEFS_BLOCKSTORE_S3_BUCKET_NAME=lakefs-bucket
   LAKEFS_AUTH_ENCRYPT_SECRET_KEY=supersecretkey123
   LAKEFS_LOGGING_LEVEL=INFO
   LAKEFS_STATS_ENABLED=1
   LAKEFS_INSTALLATION_USER_NAME=admin
   LAKEFS_INSTALLATION_ACCESS_KEY_ID=AKIAIOSFOLKFSSAMPLES
   LAKEFS_INSTALLATION_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
   LAKECTL_SERVER_ENDPOINT_URL=http://localhost:8000

   POSTGRES_DB=lakefs
   POSTGRES_USER=lakefs
   POSTGRES_PASSWORD=lakefs

   MINIO_ACCESS_KEY=minio
   MINIO_SECRET_KEY=minio123
   MINIO_REGION=us-east-1
   ```

Конфигурационный файл `.env` содержит ряд переменных окружения, которые используются для настройки сервиса LakeFS и связанных зависимостей, таких как PostgreSQL и MinIO. Ниже перечислены все переменные с их описаниями.

### Переменные для настройки LakeFS

- **`LAKEFS_DATABASE_TYPE`:** Тип базы данных, используемой LakeFS. В примере это `postgres`, указывающий на использование PostgreSQL в качестве СУБД.

- **`LAKEFS_DATABASE_POSTGRES_CONNECTION_STRING`:** Строка подключения к базе данных PostgreSQL, содержащая информацию о пользователе, пароле, хосте, порте и базе данных.

- **`LAKEFS_BLOCKSTORE_TYPE`:** Тип объекта хранилища, который использует LakeFS. В данном случае — `s3`.

- **`LAKEFS_BLOCKSTORE_S3_FORCE_PATH_STYLE`:** Указывает, следует ли использовать "path style" для URL объектного хранилища S3. Обычно используется при тестировании с MinIO или другими несовместимыми S3 сервисами.

- **`LAKEFS_BLOCKSTORE_S3_ENDPOINT`:** URL-адрес конечной точки S3 совместимого объектного хранилища (в примере — MinIO).

- **`LAKEFS_BLOCKSTORE_S3_CREDENTIALS_ACCESS_KEY_ID` и `LAKEFS_BLOCKSTORE_S3_CREDENTIALS_SECRET_ACCESS_KEY`:** Учетные данные для доступа к S3 объектному хранилищу. Они необходимы для использования MinIO или AWS S3.

- **`LAKEFS_BLOCKSTORE_S3_BUCKET_NAME`:** Имя S3 бакета, который будет использоваться LakeFS для хранения данных.

- **`LAKEFS_AUTH_ENCRYPT_SECRET_KEY`:** Секретный ключ для шифрования учетных данных пользователями внутри LakeFS.

- **`LAKEFS_LOGGING_LEVEL`:** Уровень логгирования для LakeFS. Может быть `DEBUG`, `INFO`, `WARN`, `ERROR`.

- **`LAKEFS_STATS_ENABLED`:** Флаг для включения или отключения сбора статистических данных. `1` означает, что сбор включен.

- **`LAKEFS_INSTALLATION_USER_NAME`:** Имя пользователя для администратора при первоначальной установке LakeFS.

- **`LAKEFS_INSTALLATION_ACCESS_KEY_ID` и `LAKEFS_INSTALLATION_SECRET_ACCESS_KEY`:** Учетные данные для первоначальной настройки доступа к LakeFS.

- **`LAKECTL_SERVER_ENDPOINT_URL`:** URL-адрес endpoint сервера LakeFS, который будет использоваться утилитой `lakectl`.

### Переменные для настройки PostgreSQL

- **`POSTGRES_DB`:** Имя базы данных, которую будет использовать LakeFS.

- **`POSTGRES_USER`:** Имя пользователя для подключения к базе данных PostgreSQL.

- **`POSTGRES_PASSWORD`:** Пароль пользователя для подключения к базе данных PostgreSQL.

### Переменные для настройки MinIO

- **`MINIO_ACCESS_KEY`:** Access Key для доступа к хранилищу MinIO. Аналогичен AWS Access Key.

- **`MINIO_SECRET_KEY`:** Secret Access Key для доступа к MinIO. Аналогичен AWS Secret Key.

- **`MINIO_REGION`:** Регион MinIO хранилища, который будет применяться к бакетам.

Эти переменные используются в `docker-compose.yml` и должны быть внимательно настроены в соответствии с вашим окружением и предпочтениями. Убедитесь, что учетные данные и другие чувствительные параметры безопасно сохраняются и не передаются третьим лицам.

2. **Создайте файл `docker-compose.yml`:**

   Сохраните следующую конфигурацию в файле `docker-compose.yml` в той же директории:

   ```yaml
   version: '3.7'

   services:
     lakefs:
       image: treeverse/lakefs:latest
       container_name: lakefs
       ports:
         - "8000:8000"
       environment:
         - LAKEFS_DATABASE_TYPE=${LAKEFS_DATABASE_TYPE}
         - LAKEFS_DATABASE_POSTGRES_CONNECTION_STRING=${LAKEFS_DATABASE_POSTGRES_CONNECTION_STRING}
         - LAKEFS_BLOCKSTORE_TYPE=${LAKEFS_BLOCKSTORE_TYPE}
         - LAKEFS_BLOCKSTORE_S3_FORCE_PATH_STYLE=${LAKEFS_BLOCKSTORE_S3_FORCE_PATH_STYLE}
         - LAKEFS_BLOCKSTORE_S3_ENDPOINT=${LAKEFS_BLOCKSTORE_S3_ENDPOINT}
         - LAKEFS_BLOCKSTORE_S3_CREDENTIALS_ACCESS_KEY_ID=${LAKEFS_BLOCKSTORE_S3_CREDENTIALS_ACCESS_KEY_ID}
         - LAKEFS_BLOCKSTORE_S3_CREDENTIALS_SECRET_ACCESS_KEY=${LAKEFS_BLOCKSTORE_S3_CREDENTIALS_SECRET_ACCESS_KEY}
         - LAKEFS_BLOCKSTORE_S3_BUCKET_NAME=${LAKEFS_BLOCKSTORE_S3_BUCKET_NAME}
         - LAKEFS_AUTH_ENCRYPT_SECRET_KEY=${LAKEFS_AUTH_ENCRYPT_SECRET_KEY}
         - LAKEFS_LOGGING_LEVEL=${LAKEFS_LOGGING_LEVEL}
         - LAKEFS_STATS_ENABLED=${LAKEFS_STATS_ENABLED}
         - LAKEFS_INSTALLATION_USER_NAME=${LAKEFS_INSTALLATION_USER_NAME}
         - LAKEFS_INSTALLATION_ACCESS_KEY_ID=${LAKEFS_INSTALLATION_ACCESS_KEY_ID}
         - LAKEFS_INSTALLATION_SECRET_ACCESS_KEY=${LAKEFS_INSTALLATION_SECRET_ACCESS_KEY}
         - LAKECTL_SERVER_ENDPOINT_URL=${LAKECTL_SERVER_ENDPOINT_URL}
       depends_on:
         - postgres
         - minio
         - init-minio
       volumes:
         - lakefs_data:/data

     postgres:
       image: postgres:13
       container_name: postgres
       environment:
         POSTGRES_DB: ${POSTGRES_DB}
         POSTGRES_USER: ${POSTGRES_USER}
         POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
       volumes:
         - postgres_data:/var/lib/postgresql/data

     minio:
       image: minio/minio:latest
       container_name: minio
       ports:
         - "9000:9000"
         - "9001:9001"
       environment:
         MINIO_ACCESS_KEY: ${MINIO_ACCESS_KEY}
         MINIO_SECRET_KEY: ${MINIO_SECRET_KEY}
         MINIO_REGION: ${MINIO_REGION}
       command: server /data --address :9000 --console-address :9001
       volumes:
         - minio_data:/data

     init-minio:
       image: amazon/aws-cli
       entrypoint: [ "/bin/sh", "-c" ]
       command: >
         "until curl -s http://minio:9000; do
           echo 'Waiting for MinIO to start...';
           sleep 2;
         done; aws --endpoint-url=http://minio:9000 s3api create-bucket --bucket ${LAKEFS_BLOCKSTORE_S3_BUCKET_NAME} --region ${MINIO_REGION} --create-bucket-configuration LocationConstraint=${MINIO_REGION} && echo 'Bucket created';"
       environment:
         AWS_ACCESS_KEY_ID: ${MINIO_ACCESS_KEY}
         AWS_SECRET_ACCESS_KEY: ${MINIO_SECRET_KEY}
       depends_on:
         - minio

   volumes:
     lakefs_data:
     postgres_data:
     minio_data:
   ```

### Запуск

1. **Запустите все сервисы с помощью Docker Compose:**

   Откройте терминал и выполните следующую команду в директории, где находится ваш `docker-compose.yml`:

   ```bash
   docker-compose up -d
   ```

   Это создаст и запустит контейнеры для LakeFS, PostgreSQL, и MinIO, а также скрипт инициализации MinIO.

2. **Проверьте состояние контейнеров:**

   Чтобы проверить, что все контейнеры успешно запущены, выполните:

   ```bash
   docker-compose ps
   ```

3. **Доступ к LakeFS:**

   После успешного запуска, вы сможете получить доступ к LakeFS через веб-интерфейс, открыв в браузере:

   ```
   http://localhost:8000
   ```

   Используйте имя пользователя и ключи доступа, указанные в вашем `.env` файле (`admin` и соответствующие кредитные данные).

4. **Работа с `lakectl`**

   Убедитесь, что файл конфигурации для `lakectl` (`~/.lakectl.yaml`) указан верно:

   ```yaml
   server:
     endpoint_url: http://localhost:8000
   credentials:
     access_key_id: AKIAIOSFOLKFSSAMPLES
     secret_access_key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
   ```

   Теперь вы можете использовать `lakectl` для выполнения различных операций.

### Завершение

Чтобы остановить и удалить все работающие контейнеры, выполните:

```bash
docker-compose down
```

Эта инструкция позволяет настроить и запустить LakeFS локально с помощью Docker Compose, используя предварительно определенные конфигурации и скрипты.
