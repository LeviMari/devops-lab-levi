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
Установлен Docker Desktop для Mac (Apple Silicon). Проверка версии (`docker --version`) и тестовый запуск (`docker run hello-world`) прошли успешно. 
<img width="571" height="371" alt="lab1-step1-install" src="https://github.com/user-attachments/assets/82384b1f-a14e-4cb2-8e2d-40af40d6d42a" />

### 2. Работа с готовыми образами
Скачан образ `ubuntu:latest`, запущен интерактивный контейнер (`docker run -it ubuntu bash`), внутри установлен пакет curl (`apt update && apt install -y curl`), версия проверена командой `curl --version`. 
<img width="570" height="368" alt="2" src="https://github.com/user-attachments/assets/b813c169-5f81-4a0f-819a-8bb29e58d7c8" />
<img width="568" height="373" alt="1" src="https://github.com/user-attachments/assets/97d9bae0-2e18-4b02-9f9d-5f91b0a8ae87" />




### 3. Запуск веб-сервера
Запущен контейнер с nginx (`docker run -d -p 8080:80 --name web-server nginx:alpine`). Страница проверена в браузере по адресу http://localhost:8080. Просмотрены логи контейнера (`docker logs web-server`), выполнено подключение к контейнеру (`docker exec -it web-server sh`).
<img width="1359" height="704" alt="3" src="https://github.com/user-attachments/assets/01bc8c08-fa36-4847-8212-9dc60baf7394" />


### 4. Управление контейнерами
Изучены и опробованы команды `docker ps`, `docker ps -a`, `docker stop`, `docker start`, `docker rm`, `docker rmi` на примере контейнера web-server. 
<img width="570" height="298" alt="8" src="https://github.com/user-attachments/assets/d3eba07d-6d81-4d38-bcbd-d60cb43ec38a" />


### 5. Работа с томами (volumes)
Создан том `my-volume` (`docker volume create`). Запущен контейнер `volume-test` с подключённым томом, внутри создан файл `/data/test.txt`. После удаления контейнера и создания нового (`volume-test2`) с тем же томом файл сохранился — подтверждена независимость данных тома от жизненного цикла контейнера.
<img width="571" height="164" alt="9" src="https://github.com/user-attachments/assets/bf481dad-b938-40f0-85c9-e1ec2887dc61" />

### 6. Создание Dockerfile (задание со звездочкой)
Созданы файлы `app.py` (Flask-приложение) и `requirements.txt`. Написан `Dockerfile` на основе `python:3.9-slim`, устанавливающий системные пакеты curl и vim, Python-зависимости, создающий непривилегированного пользователя `appuser` (UID 1000) и запускающий приложение на порту 5000.<img width="562" height="626" alt="5" src="https://github.com/user-attachments/assets/b2cd501d-36fb-4a74-afa3-4b3c547a0fb5" />
 <img width="960" height="566" alt="7" src="https://github.com/user-attachments/assets/8741a73b-d2dc-43ed-ad9e-d0ce953ccdf8" />



Образ собран командой `docker build -t my-flask-app .` — сборка прошла успешно с первой попытки.

**Возникшие проблемы и их решение:**
- При запуске контейнера с портом 5000 возникла ошибка "address already in use" — порт занят системной службой AirPlay Receiver на macOS. Решение: контейнер запущен с проброс портом 5001:5000 (`docker run -d -p 5001:5000 ...`).
- При первом запуске приложение падало с ошибкой `ImportError: cannot import name 'url_quote' from werkzeug.urls` — несовместимость версии Flask 2.0.1 с последней версией Werkzeug, установленной автоматически. Решение: зафиксирована совместимая версия `Werkzeug==2.0.3` в requirements.txt, после чего образ пересобран и приложение успешно запустилось.
<img width="579" height="651" alt="6" src="https://github.com/user-attachments/assets/29d6aec3-ab6d-4d3e-aa2c-7cfbf52a634e" />


Работа проверена командой `curl http://localhost:5001`, получен ответ: "Hello from Docker!".


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
