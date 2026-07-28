# Деплой Jenkins-агента

Перед запуском workflow deploy-agent добавьте в GitHub Actions следующие secrets:

| Secret | Значение |
|---|---|
| `AGENT_CLOUD_HOST` | IP-адрес или домен сервера Jenkins-агента |
| `AGENT_CLOUD_USER` | SSH-пользователь сервера с доступом к Docker |
| `AGENT_CLOUD_DEPLOY_SSH_PRIVATE_KEY` | Приватный SSH-ключ для деплоя на сервер агента |
| `JENKINS_MASTER_PUBLIC_KEY` | Публичный SSH-ключ Jenkins-мастера |
| `TEMPO_OTLP_ENDPOINT` | Адрес OTLP-приёмника Tempo, доступный с сервера агента |

`DOCKER_GID` добавлять в secrets не нужно: workflow определяет его на сервере и сохраняет в `.env` автоматически.

После деплоя в серверном `.env` находятся:

- `JENKINS_MASTER_PUBLIC_KEY`;
- `TEMPO_OTLP_ENDPOINT`;
- `DOCKER_GID`.
