# Субагент: Kubernetes и Minikube

Инструкции для работы с Kubernetes (Minikube) в проекте Phrase Drill: развёртывание PostgreSQL, применение манифестов, отладка, подключение приложения.

## Когда использовать

- Задачи по настройке или изменению развёртывания в Minikube.
- Создание или правка манифестов (Deployment, Service, PVC, ConfigMap и т.д.).
- Запуск/остановка Minikube, проверка подов и сервисов.
- Настройка доступа к БД из хоста (port-forward, строка подключения).

## Контекст проекта

- **Minikube** развёрнут в Docker-контейнере на локальной машине.
- **PostgreSQL** для PhraseDrillService поднят в кластере: один под с персистентным томом (PVC), образ `postgres:18-alpine`.
- Манифесты лежат в **`minicubeSettings/`** (в корне workspace): PVC, Deployment, Service. Не путать с папкой `.cursor`.
- Подключение к БД с хоста — через `kubectl port-forward` на порт 5434; строка подключения в `PhraseDrillService/PhraseDrillService/appsettings.Development.json`.

## Ключевые ресурсы

| Ресурс              | Имя / значение                          |
|---------------------|-----------------------------------------|
| Deployment          | `phrasedrill-postgres-deploy`           |
| Service             | `phrasedrill-postgres-deploy` (NodePort)|
| PVC                 | `postgres-pvc`                          |
| Label приложения    | `app: phrasedrill-postgres`             |
| Локальный порт БД   | 5434 (forward с 5432 в кластере)        |

## Типичные команды

```bash
# Применить манифесты из minicubeSettings
kubectl apply -f minicubeSettings/postgres-pvc.yaml
kubectl apply -f minicubeSettings/postgres-deployment.yaml
kubectl apply -f minicubeSettings/postgres-service.yaml

# Статус подов и сервисов
kubectl get pods -l app=phrasedrill-postgres
kubectl get pods -o wide
kubectl get svc phrasedrill-postgres-deploy

# Доступ к БД с хоста (запускать и держать в отдельном терминале)
kubectl port-forward svc/phrasedrill-postgres-deploy 5434:5432

# IP ноды Minikube (если нужен прямой доступ по NodePort)
minikube ip
```

## Строка подключения

В `appsettings.Development.json` используется:

- Host: `127.0.0.1`
- Port: `5434` (порт на хосте при активном port-forward)
- Database: `phrasedrill`
- User/Password: из манифеста Deployment (phrasedrill / phrasedrill_dev).

При изменении учётных данных в манифестах нужно синхронизировать их в `appsettings.Development.json`.

## Ограничения и соглашения

- Сейчас используется **один инстанс** PostgreSQL с PVC; репликация (primary/standby) не настроена.
- Манифесты в `minicubeSettings/` — единственный источник правды для развёртывания Postgres в Minikube; при правках не дублировать конфиг в другие места без необходимости.
- Коммитить манифесты можно в тот репозиторий, где хранится `minicubeSettings/` (если папка в корне workspace — уточнять у пользователя, в какой репо её добавить).
