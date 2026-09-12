# Как запускать и останавливать контейнеры (docker-compose)

Тут собраны команды и краткие пояснения для запуска, просмотра логов, остановки
и удаления контейнеров проекта на основе `docker compose`.

Требования
- Docker Engine и `docker compose` (v2) установлены и работают.
- Запускать команды из корня проекта (где лежит `Docker-compose.yml`).

Запуск (сборка и старт в фоне)
```bash
# Собрать образ и запустить сервисы в фоне
docker compose up --build -d

# (Альтернатива для более старых установок)
# docker-compose up --build -d
```

Просмотр статуса контейнеров
```bash
docker compose ps
```

Просмотр логов
```bash
# Логи одного сервиса (например pythonapp)
docker compose logs -f pythonapp

# Логи всех сервисов
docker compose logs -f
```

Применение миграций и создание суперпользователя
```bash
# Выполнить миграции (если не выполнены автоматически)
docker compose exec pythonapp python manage.py migrate

# Создать суперпользователя
docker compose exec pythonapp python manage.py createsuperuser
```

Остановка контейнеров
```bash
docker compose stop
```

Остановка и удаление контейнеров (сеть и анонимные тома остаются)
```bash
docker compose down
```

Полное удаление вместе с томом базы данных
```bash
docker compose down -v
```

Пересборка образа и перезапуск (force rebuild)
```bash
docker compose build --no-cache
docker compose up -d
```

Подключение к работающему контейнеру для интерактивной отладки
```bash
docker compose exec pythonapp bash
```

Проверка, что порт проброшен на хост
```bash
# На macOS / Linux
ss -tlnp | grep 8080 || lsof -iTCP:8080 -sTCP:LISTEN -n -P || true

# Быстрый запрос страницы из хоста
curl -v http://localhost:8080
```

Советы по отладке проблем
- Если страница не открывается — проверьте `docker compose ps` и логи сервиса `pythonapp` и `mysql`.
- Частая причина проблем — неправильные переменные окружения в `todolist/settings.py` (HOST/PORT/USER/PASSWORD).
- Для DNS внутри сетей Docker используйте имя сервиса (`mysql`) вместо жёсткого IP.

Примечания
- Команды `docker compose` относятся к плагину Compose v2; если у вас установлен старый `docker-compose`, используйте соответствующий двоичный файл.
- Команды в файле предполагают, что `Docker-compose.yml` находится в текущей директории.

Если нужно — могу добавить шаги для автоматизации запуска в скрипте или systemd unit (macOS не использует systemd), либо подготовить `Makefile`.

