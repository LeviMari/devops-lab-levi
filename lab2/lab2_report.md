# Отчет по лабораторной работе №2

**Университет:** ИТМО, факультет ПИКТ
**Курс:** Введение в веб-технологии
**Учебный год:** 2026-2027
**Группа:** ВвВТ УВБ 3.1
**ФИО:** Мария Леви
**Дата начала работы:** 15.09.2026
**Дата защиты:** _______________

## Цель работы
Научиться настраивать автоматизированные пайплайны для сборки Docker образов, их публикации в registry и автоматического деплоя при изменении кода.

## Ход выполнения работы

### 1. Подготовка проекта
Файлы приложения из первой лабораторной (`app.py`, `requirements.txt`, `Dockerfile`) скопированы в папку `lab2/` текущего репозитория. Создан аккаунт на Docker Hub, создан публичный репозиторий образа `my-flask-app`.
<img width="570" height="247" alt="Снимок экрана 2026-09-15 в 11 07 15" src="https://github.com/user-attachments/assets/42e404c8-f4ec-40bd-a9a3-07fde9690eb2" />


### 2. Настройка GitHub Actions
В корне репозитория создан файл `.github/workflows/docker-build.yml`, описывающий пайплайн сборки и публикации Docker-образа. Пайплайн запускается при пуше в ветку `main`, использует Ubuntu-раннер, выполняет checkout кода, настройку Docker Buildx, авторизацию в Docker Hub через секреты, сборку и публикацию образа с тегом `<username>/my-flask-app:latest`, а также демонстрационный шаг деплоя.<img width="723" height="484" alt="Снимок экрана 2026-09-15 в 11 09 58" src="https://github.com/user-attachments/assets/3723f0ca-510a-426e-8012-f09f1fbe39ce" />


### 3. Настройка секретов
Создан персональный токен доступа (Access Token) на Docker Hub с правами Read & Write. В настройках репозитория GitHub (Settings → Secrets and variables → Actions) добавлены секреты `DOCKER_USERNAME` и `DOCKER_PASSWORD` (в качестве пароля использован токен, а не реальный пароль аккаунта — более безопасная практика).<img width="1053" height="466" alt="Снимок экрана 2026-09-15 в 11 12 14" src="https://github.com/user-attachments/assets/d243e2f5-2d53-44db-993d-6c384f2a50fc" />

<img width="955" height="208" alt="Снимок экрана 2026-09-15 в 11 14 27" src="https://github.com/user-attachments/assets/021a8e61-e013-4b6c-a5ea-adfc4cf23cc0" />

### 4. Тестирование пайплайна
После пуша в main пайплайн запустился автоматически и завершился успешно. Образ появился в Docker Hub с тегом `latest`. Проверены логи каждого шага выполнения.
<img width="1334" height="411" alt="Снимок экрана 2026-09-15 в 11 15 51" src="https://github.com/user-attachments/assets/1d6d1b3d-dd6c-4d7d-9ab2-cbfc73e05625" />
<img width="622" height="539" alt="Снимок экрана 2026-09-15 в 11 18 33" src="https://github.com/user-attachments/assets/1fa148ee-616f-415e-b37a-3efaba75d2ce" />


**Возникшая проблема:** при повторном редактировании workflow-файла через редактор nano старое содержимое не было полностью удалено, из-за чего в файле продублировались ключи `name`, `on`, `jobs`, что привело к ошибке "Invalid workflow file". Проблема решена полной перезаписью файла через команду `cat > file << EOF` вместо ручного редактирования в nano — так исключается риск неполного удаления старого текста.

### 5. Условный деплой для разных веток (задание со звездочкой)
Пайплайн доработан: добавлен запуск для ветки `develop` (`on.push.branches: [main, develop]`), а шаг деплоя разделён на два условных шага с помощью `if: github.ref == 'refs/heads/main'` и `if: github.ref == 'refs/heads/develop'`.

Тестирование показало корректную работу условий:
- При пуше в `main` выполняется шаг "Deploy to production" (вывод "Deploying to production server..."), шаг "Deploy to development" пропускается (skipped). <img width="1429" height="745" alt="Снимок экрана 2026-09-15 в 12 56 12" src="https://github.com/user-attachments/assets/77c22762-831a-4a4a-b877-00d5393b8622" />
<img width="1423" height="756" alt="Снимок экрана 2026-09-15 в 12 52 03" src="https://github.com/user-attachments/assets/efb623cb-734e-4455-8b20-f344028d12f3" />

- При пуше в `develop` — наоборот, выполняется "Deploy to development" ("Deploying to development server..."), а "Deploy to production" пропускается. ![Uploading Снимок экрана 2026-09-15 в 12.56.12.png…]()


## Результат
- Настроен CI/CD пайплайн с GitHub Actions для автоматической сборки Docker-образа
- Образ автоматически публикуется в Docker Hub при каждом пуше
- Настроены секреты для безопасной авторизации в registry (используется токен доступа, а не пароль)
- Реализован условный деплой в зависимости от ветки (main/develop)
- Решена практическая проблема с некорректным редактированием YAML-файла

## Использованные команды
```
mkdir lab2
cp ~/Documents/flask-docker-app/app.py lab2/
mkdir -p .github/workflows
git add lab2/ .github/
git commit -m "Add CI/CD pipeline for Docker build and push"
git push
git checkout -b develop
git push -u origin develop
cat > .github/workflows/docker-build.yml << 'EOF'
...
EOF
git checkout main
git merge main
```
