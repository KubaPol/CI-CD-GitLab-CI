# CI-CD-GitLab-CI
GitLab-dokku-deploy-pipeline пример настройки автоматического деплоя приложения через gitLab CI/CD на сервер с Dokku (Docker-based-PaaS)
## Автоматический деплой через GitLab CI/CD и Dokku

Этот проект настроен для автоматического деплоя на сервер, использующий **Dokku** — легковесную PaaS-платформу на базе Docker.

### Что реализовано:

- CI/CD процесс через **GitLab CI**
- Деплой приложения на сервер через SSH
- Использование переменных и секрета SSH-ключа
- Работа через ветку `main`
- Образ CI: `dokku/ci-docker-image`

### Как это работает:

1. При каждом пуше в ветку `main`, запускается пайплайн в `.gitlab-ci.yml`.
2. GitLab Runner подключается по SSH к серверу, где установлен Dokku.
3. С помощью команды `dokku-deploy` происходит обновление приложения на сервере.
4. После деплоя выполняется `dokku-unlock`, если приложение было заблокировано.

### Пример конфигурации `.gitlab-ci.yml`:

```yaml
image: dokku/ci-docker-image

stages:
  - deploy

variables:
  GIT_DEPTH: 0

deploy:
  stage: deploy
  only:
    - main
  variables:
    GIT_REMOTE_URL: ssh://dokku@dokku.me:22/myapp
  script:
    - dokku-deploy
  after_script:
    - dokku-unlock
  interruptible: true
