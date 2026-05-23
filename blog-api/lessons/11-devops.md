# Урок 11 — DevOps

> **Инструменты:** `/aif-dockerize`, `/aif-ci`
> **Цель:** Создать Docker-контейнер и CI/CD pipeline
> **Время:** ~15 минут

---

## 📌 Что вы узнаете

- Как `/aif-dockerize` генерирует Docker конфиги для Laravel
- Как `/aif-ci` создаёт GitHub Actions workflow
- Как запустить Laravel в Docker-контейнере
- Что такое production-ready конфигурация

---

## 🎯 Сценарий

Наш Blog API работает локально. Но для production нужно:
- **Docker** — чтобы запустить на любом сервере
- **CI/CD** — чтобы тесты запускались автоматически при каждом пуше

Обе вещи — типовые, но требуют знания стека. AI знает Laravel и создаст правильные конфиги.

---

## Шаг 1. Создаём Docker конфиг

В AI-агенте выполните:

```
/aif-dockerize
```

### Что делает `/aif-dockerize`

Это **генератор Docker конфигов** — AI создаёт Dockerfile и docker-compose.yml для Laravel.

**Лог выполнения:**

```
[dockerize] Analyzing project stack...
[dockerize] Stack: Laravel 11, PHP 8.2+, SQLite

[dockerize] Generated files:

## Dockerfile
- Base: php:8.2-fpm-alpine
- Extensions: pdo_sqlite, mbstring, xml, ctype, tokenizer
- Composer install with --no-dev --optimize-autoloader
- Laravel cache warming
- Non-root user for security

## docker-compose.yml
- Service: app (PHP-FPM)
- Service: nginx (web server)
- Service: sqlite (persistent volume)
- Ports: 8000:80
- Volume: .:/var/www/html (development)

## .dockerignore
- vendor/
- node_modules/
- .git/
- .ai-factory/

[dockerize] Created:
  - Dockerfile
  - docker-compose.yml
  - .dockerignore
  - docker/nginx.conf
```

---

## Шаг 2. Проверяем Docker

```bash
# Собираем и запускаем
docker compose up -d

# Проверяем
docker compose ps
# NAME          STATUS          PORTS
# blog-api-app  Up              9000/tcp
# blog-api-nginx Up             0.0.0.0:8000->80/tcp

# Тестируем
curl -X POST http://localhost:8000/api/auth/register \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"name":"Docker User","email":"docker@test.com","password":"secret123"}'

# Ответ: ✅ 200 OK, пользователь создан
```

---

## Шаг 3. Создаём CI/CD

```
/aif-ci "GitHub Actions"
```

### Что делает `/aif-ci`

Это **генератор CI/CD pipeline** — AI создаёт workflow файл для GitHub Actions.

**Лог выполнения:**

```
[ci] Generating GitHub Actions workflow...
[ci] Stack: Laravel 11, PHP 8.2+, SQLite, PHPUnit

[ci] Generated workflow:

## Jobs:
1. test
   - PHP 8.2
   - composer install
   - php artisan migrate
   - php artisan test

2. lint
   - PHP 8.2
   - composer install
   - php -l (syntax check)

3. build-docker
   - docker build
   - (only on main branch)

## Triggers:
- push: main, feature/*
- pull_request: main

[ci] Created: .github/workflows/ci.yml
```

---

## Шаг 4. Проверяем CI конфиг

```bash
cat .github/workflows/ci.yml
```

```yaml
name: CI

on:
  push:
    branches: [main, 'feature/*']
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        php: ['8.2']

    steps:
      - uses: actions/checkout@v4

      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: ${{ matrix.php }}
          extensions: pdo_sqlite, mbstring, xml, ctype, tokenizer

      - name: Install dependencies
        run: composer install --prefer-dist --no-interaction

      - name: Setup environment
        run: |
          cp .env.example .env
          touch database/database.sqlite
          php artisan key:generate

      - name: Run migrations
        run: php artisan migrate

      - name: Run tests
        run: php artisan test

  lint:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: '8.2'

      - name: Install dependencies
        run: composer install --prefer-dist --no-interaction

      - name: Syntax check
        run: find app -name "*.php" -exec php -l {} \;
```

---

## Шаг 5. Коммитим и пушим

```bash
git add .
git commit -m "ci: add Docker configuration and GitHub Actions workflow"
git push origin main
```

После пуша зайдите в **Actions** на GitHub — увидите запущенный pipeline.

---

## 🔍 Что проверяет CI pipeline

### Job: test
- ✅ Установка зависимостей
- ✅ Миграции проходят
- ✅ Все тесты проходят (12 тестов)

### Job: lint
- ✅ Синтаксис PHP файлов OK
- ✅ Нет синтаксических ошибок

### Job: build-docker
- ✅ Dockerfile собирается (только на main)

> 💡 Если любой job падает — PR не может быть вмержен. Это **гарантия качества**.

---

## 📂 Структура проекта сейчас

```
blog-api/
├── .ai-factory/
│   ├── DESCRIPTION.md
│   ├── ARCHITECTURE.md
│   ├── ROADMAP.md
│   ├── RULES.md
│   └── PLAN.md
├── .github/
│   └── workflows/
│       └── ci.yml                  # ✅ CI/CD pipeline
├── docker/
│   └── nginx.conf                  # ✅ Nginx конфиг
├── .dockerignore                   # ✅ Исключения для Docker
├── Dockerfile                      # ✅ Образ приложения
├── docker-compose.yml              # ✅ Оркестрация сервисов
├── app/
├── routes/
├── tests/
├── composer.json
└── ...
```

---

## ✅ Чек-лист Урока 11

- [x] `/aif-dockerize` выполнен → Dockerfile + docker-compose.yml
- [x] Docker работает → `docker compose up -d`
- [x] API отвечает в контейнере
- [x] `/aif-ci` выполнен → GitHub Actions workflow
- [x] CI конфиг правильный → test + lint + build-docker

---

## 🚀 Что дальше

**Следующий урок:** [Урок 12 — Финал →](12-final.md)

В нём мы:
- Повторим полный цикл: plan → implement → commit → review
- Создадим последнюю фичу (комментарии к постам)
- Подведём итоги всего курса

---

## 📚 Справочник: Использованные инструменты

| Инструмент | Тип | Описание |
|---|---|---|
| `/aif-dockerize` | Slash-команда (skill) | Генерирует Docker конфиги для проекта |
| `/aif-ci` | Slash-команда (skill) | Генерирует CI/CD pipeline |

### `/aif-dockerize`

**Когда использовать:**
- Когда нужно контейнеризировать проект
- Перед деплоем на production
- Для создания одинакового dev/prod окружения

**Что создаёт:**
```
Dockerfile              # Образ приложения
docker-compose.yml      # Оркестрация
.dockerignore           # Исключения
docker/nginx.conf       # Web server конфиг
```

**Поддерживаемые стеки:**
- Laravel (PHP-FPM + Nginx)
- Node.js (Express, Next.js)
- Python (Django, FastAPI)
- И другие

### `/aif-ci`

**Когда использовать:**
- При настройке нового проекта
- Когда нужен автоматический запуск тестов
- Перед первым релизом

**Поддерживаемые платформы:**
```
/aif-ci "GitHub Actions"     # .github/workflows/ci.yml
/aif-ci "GitLab CI"          # .gitlab-ci.yml
```

---

**Урок 11 завершён!** 🎉
