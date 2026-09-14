# Отчет по лабораторной работе №1

**Университет:** ИТМО, факультет УВБ
**Курс:** Введение в веб-технологии
**Учебный год:** 2026-2027
**Группа:** ВвВТ УВБ 3.1
**ФИО:** Мария Левина
**Дата начала работы:** 14.09.2026
**Дата защиты:** _______________

## Цель работы
Научиться работать с Docker: устанавливать Docker, создавать Dockerfile, собирать образы, запускать контейнеры и управлять ими.

## Ход выполнения работы

### 1. Установка Docker
Установлен Docker Desktop для Mac (Apple Silicon). Проверка версии (`docker --version`) и тестовый запуск (`docker run hello-world`) прошли успешно. <img width="571" height="371" alt="lab1-step1-install" src="https://github.com/user-attachments/assets/fe40fc62-6795-4b3b-8de4-d956153a5b9c" />


### 2. Работа с готовыми образами
Скачан образ `ubuntu:latest`, запущен интерактивный контейнер (`docker run -it ubuntu bash`), внутри установлен пакет curl (`apt update && apt install -y curl`), версия проверена командой `curl --version`. <img width="960" height="566" alt="7" src="https://github.com/user-attachments/assets/79e6f8f4-ba21-4c1d-9c08-013a2c1aac00" />
 <img width="579" height="651" alt="6" src="https://github.com/user-attachments/assets/9e7fe77a-c883-42c0-a689-18ff8cdde526" />


### 3. Запуск веб-сервера
Запущен контейнер с nginx (`docker run -d -p 8080:80 --name web-server nginx:alpine`). Страница проверена в браузере по адресу http://localhost:8080. Просмотрены логи контейнера (`docker logs web-server`), выполнено подключение к контейнеру (`docker exec -it web-server sh`).<img width="1359" height="704" alt="3" src="https://github.com/user-attachments/assets/250ea46c-4c04-4975-94a4-5635a62f8312" />



### 4. Управление контейнерами
Изучены и опробованы команды `docker ps`, `docker ps -a`, `docker stop`, `docker start`, `docker rm`, `docker rmi` на примере контейнера web-server. <img width="571" height="652" alt="4" src="https://github.com/user-attachments/assets/fae7d543-9b9d-4708-812a-1cabe1aaa46f" />


### 5. Работа с томами (volumes)
Создан том `my-volume` (`docker volume create`). Запущен контейнер `volume-test` с подключённым томом, внутри создан файл `/data/test.txt`. После удаления контейнера и создания нового (`volume-test2`) с тем же томом файл сохранился — подтверждена независимость данных тома от жизненного цикла контейнера.

### 6. Создание Dockerfile (задание со звездочкой)
Созданы файлы `app.py` (Flask-приложение) и `requirements.txt`. Написан `Dockerfile` на основе `python:3.9-slim`, устанавливающий системные пакеты curl и vim, Python-зависимости, создающий непривилегированного пользователя `appuser` (UID 1000) и запускающий приложение на порту 5000. <img width="1359" height="704" alt="3" src="https://github.com/user-attachments/assets/3615e8b8-4077-4c77-a460-8b17a2676929" />



Образ собран командой `docker build -t my-flask-app .` — сборка прошла успешно с первой попытки.

**Возникшие проблемы и их решение:**
- При запуске контейнера с портом 5000 возникла ошибка "address already in use" — порт занят системной службой AirPlay Receiver на macOS. Решение: контейнер запущен с проброс портом 5001:5000 (`docker run -d -p 5001:5000 ...`).
- При первом запуске приложение падало с ошибкой `ImportError: cannot import name 'url_quote' from werkzeug.urls` — несовместимость версии Flask 2.0.1 с последней версией Werkzeug, установленной автоматически. Решение: зафиксирована совместимая версия `Werkzeug==2.0.3` в requirements.txt, после чего образ пересобран и приложение успешно запустилось.<img width="570" height="368" alt="2" src="https://github.com/user-attachments/assets/fbc5ba34-fd36-4441-8862-285a0648e7bc" />


Работа проверена командой `curl http://localhost:5001`, получен ответ: "Hello from Docker!".<img width="960" height="566" alt="7" src="https://github.com/user-attachments/assets/04f9e114-2e41-475f-bca8-05bb30d6679f" />



## Результат
- Установлен и настроен Docker Desktop
- Изучены основные команды Docker (images, ps, run, stop, start, rm, rmi, logs, exec)
- Получен опыт работы с готовыми образами (Ubuntu, nginx)
- Изучена работа с volumes для сохранения данных между контейнерами
- Создан собственный Dockerfile и собран рабочий образ Flask-приложения
- Решены практические проблемы совместимости версий и конфликта портов

## Использованные команды
```
docker --version
docker run hello-world
docker pull ubuntu:latest
docker run -it ubuntu bash
docker run -d -p 8080:80 --name web-server nginx:alpine
docker logs web-server
docker exec -it web-server sh
docker stop web-server
docker start web-server
docker rm web-server
docker rmi nginx:alpine
docker volume create my-volume
docker run -it --name volume-test -d -v my-volume:/data ubuntu bash
docker build -t my-flask-app .
docker run -d -p 5001:5000 --name flask-container my-flask-app
curl http://localhost:5001
```
