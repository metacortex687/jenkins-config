# Jenkins для taxi-manager

Репозиторий разворачивает готовый Jenkins в Docker.

После запуска в Jenkins автоматически создаются:

- пользователь Jenkins;
- SSH-доступ к GitHub;
- Job `taxi-manager-ci`;
- плагины и настройки.

## Требования

Нужны:

- Git;
- Docker;
- Docker Compose;
- SSH-ключ с доступом к репозиторию `taxi-manager`.

Проверьте Docker:

```bash
docker ps
docker compose version
```

## Установка

### 1. Клонируйте репозиторий

```bash
git clone https://github.com/metacortex687/jenkins-config.git
cd jenkins-config
```

### 2. Создайте `.env`

```bash
cp .env.example .env
```

Откройте файл:

```bash
nano .env
```

Укажите логин, пароль, email и порт Jenkins:

```dotenv
JENKINS_ADMIN_ID=admin
JENKINS_ADMIN_PASSWORD=change-me
JENKINS_PORT=8045
JENKINS_ADMIN_EMAIL=admin@example.com
DOCKER_GID=1001
```

Узнайте правильный `DOCKER_GID`:

```bash
stat -c '%g' /var/run/docker.sock
```

Запишите полученное число в `.env`.

### 3. Добавьте SSH-ключ

Публичная часть SSH-ключа должна быть добавлена в GitHub и иметь доступ к репозиторию `taxi-manager`.

Поместите приватную часть SSH-ключа в защищённый каталог `secrets` внутри проекта `jenkins-config`.

Все команды выполняются из корня проекта:

```bash
cd ~/jenkins-config

mkdir -p secrets
cp ~/.ssh/jenkins-git secrets/github_ssh_key

chmod 700 secrets
chmod 600 secrets/github_ssh_key

### 4. Создайте каталог данных Jenkins

```bash
mkdir -p jenkins_home
sudo chown -R 1000:1000 jenkins_home
```

### 5. Запустите Jenkins

```bash
docker compose up -d --build
```

Посмотреть логи:

```bash
docker compose logs -f jenkins
```

Первый запуск может занять несколько минут.

## Вход

Откройте:

```text
http://127.0.0.1:8045
```

Используйте логин и пароль из `.env`.

На главной странице автоматически появится Job:

```text
taxi-manager-ci
```

Запустите его кнопкой **Build Now**.

## Управление

Остановить Jenkins:

```bash
docker compose down
```

Запустить снова:

```bash
docker compose up -d
```

Обновить конфигурацию:

```bash
git pull
docker compose up -d --build
```

## Проверка Docker внутри Jenkins

```bash
docker compose exec jenkins docker version
```

В выводе должны присутствовать:

```text
Client:
Server:
```

## Возможные ошибки

### Нет прав на `jenkins_home`

```bash
docker compose down
sudo chown -R 1000:1000 jenkins_home
docker compose up -d
```

### Нет доступа к Docker socket

Проверьте GID:

```bash
stat -c '%g' /var/run/docker.sock
```

Исправьте `DOCKER_GID` в `.env` и пересоздайте контейнер:

```bash
docker compose up -d --force-recreate
```

