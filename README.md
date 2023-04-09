# CI и CD проекта API для YaMDb
[![CI](https://github.com/OlyaDiv/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg)](https://github.com/OlyaDiv/yamdb_final/actions/workflows/yamdb_workflow.yml)
### Описание
Проект YaMDb собирает отзывы пользователей на произведения.
Здесь реализована настройка для приложения  Continuous Integration и Continuous Deployment: 
* автоматический запуск тестов,
* обновление образов на Docker Hub,
* автоматический деплой на боевой сервер при пуше в главную ветку main.
### Документация
http://158.160.61.120/redoc/
### Запуск проекта:
1. Клонируйте репозиторий и перейдите в него в командной строке:
```
git clone git@github.com:OlyaDiv/yamdb_final.git
cd yamdb_final
```
2. Установите и активируйте виртуальное окружение:
```
python3 -m venv venv
. venv/Scripts/activate
```
3. Установите зависимости
```
pip install -r requirements.txt
```
### Подготовка сервера
1. Подключитесь к серверу:
```
ssh admin@84.201.161.196
# admin: имя пользователя, под которым будет выполнено подключение к серверу
# 84.201.161.196: ip-адрес сервера 
```
2. Установите docker и docker-compose
```
sudo apt install docker.io
sudo apt install docker-compose
```
3. Скопируйте файлы docker-compose.yaml и nginx/default.conf из вашего проекта на сервер в home/<ваш_username>/docker-compose.yaml и home/<ваш_username>/nginx/default.conf соответственно.
### Подготовка секретных данных
В репозитории на Github добавьте данные в Settings - Secrets - Actions secrets:
```
DOCKER_USERNAME - имя пользователя DockerHub
DOCKER_PASSWORD - пароль пользователя DockerHub
HOST - ip-адрес сервера
USER - имя пользователя для подключения к серверу
SSH_KEY - приватный ssh-ключ (публичный должен быть на сервере)
PASSPHRASE - кодовая фраза для ssh-ключа
DB_ENGINE - django.db.backends.postgresql
DB_HOST - db
DB_PORT - 5432
TELEGRAM_TO - id своего телеграм-аккаунта (можно узнать у @userinfobot)
TELEGRAM_TOKEN - токен Telegram бота
DB_NAME - имя БД
POSTGRES_USER - пользователь БД
POSTGRES_PASSWORD - пароль для БД
```
### Запуск workflow
При выполнении команды git push проект заливается в репозиторий и начинается проверка проекта согласно описанным операциям:
* проверка кода на соответствие стандарту PEP8 (с помощью пакета flake8) и запуск pytest из репозитория yamdb_final;
* сборка и доставка докер-образа для контейнера web на Docker Hub;
* автоматический деплой проекта на боевой сервер;
* отправка уведомления в Telegram о том, что процесс деплоя успешно завершился.

На Github в репозитории во вкладке Actions можно увидеть процесс проверки проекта по workflow.
### После деплоя
1. Выполните миграции
```
sudo docker-compose exec web python manage.py migrate
```
2. Создайте суперпользователя:
```
docker-compose exec web python manage.py createsuperuser
```
3. Соберите статику:
```
docker-compose exec web python manage.py collectstatic --no-input
```
4. Проверьте доступность сервиса

http://158.160.61.120/admin/
