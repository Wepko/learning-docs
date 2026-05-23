# Урок 03 — Исследование

> **Инструменты:** `/aif-explore`, `/aif-grounded`
> **Цель:** Исследовать лучшие практики Laravel API до написания кода
> **Время:** ~15 минут

---

## 📌 Что вы узнаете

- Разница между `/aif-explore` (исследование) и `/aif-grounded` (проверка фактов)
- Как исследовать архитектуру до реализации
- Как получать точные ответы с источниками

---

## 🎯 Сценарий

У нас есть DESCRIPTION.md и ARCHITECTURE.md. Мы **знаем** что хотим сделать (Blog API). Но **как лучше** — ещё не знаем.

Прежде чем писать код, исследуем:
- Как правильно организовать Laravel API?
- Sanctum или Passport для авторизации?
- Какие пакеты установить?

---

## Шаг 1. Исследуем архитектуру

В AI-агенте выполните:

```
/aif-explore Как лучше организовать структуру Laravel API с Sanctum и SQLite?
```

### Что делает `/aif-explore`

Это **режим исследования** — AI не даёт готового ответа, а изучает и формулирует выводы.

**AI сделает:**

1. Исследует текущую структуру проекта
2. Изучит best practices для Laravel API
3. Сравнит разные подходы (Resource Controllers vs ручные маршруты)
4. Создаст `.ai-factory/RESEARCH.md` с выводами

### Результат — RESEARCH.md

```markdown
# Research: Laravel API Structure

## Findings

### 1. Controller Organization
- Use Resource Controllers for CRUD (`php artisan make:controller --api --model=Post`)
- Group under `app/Http/Controllers/Api/` namespace
- Keep controllers thin — delegate to services

### 2. Authentication
- Sanctum is ideal for API tokens (lighter than Passport)
- Use `HasApiTokens` trait on User model
- Protect routes with `auth:sanctum` middleware

### 3. Validation
- Form Requests are Laravel-native and testable
- Keep validation rules separate from controllers
- Use `php artisan make:request` for each endpoint

### 4. Database
- SQLite zero-config, great for development
- Use migrations (`php artisan make:migration`)
- Seeders for test data

## Recommendations
- Follow Laravel conventions over custom patterns
- Use API Resources for JSON response transformation
- Keep business logic out of controllers
```

> 💡 RESEARCH.md — это **база знаний** для последующих решений. `/aif-plan` и `/aif-implement` тоже его читают.

---

## Шаг 2. Проверяем конкретный факт

```
/aif-grounded Какой пакет авторизации лучше для Laravel API: Sanctum или Passport?
```

### Что делает `/aif-grounded`

Это **режим проверки фактов** — AI ищет точный ответ и даёт его с обоснованием.

**AI ответит:**

```
Sanctum — лучше для нашего случая.

Сравнение:

| Критерий | Sanctum | Passport |
|----------|---------|----------|
| Сложность | Низкая | Высокая |
| Тип авторизации | API tokens | OAuth2 |
| Подходит для | SPA, mobile API | SaaS, third-party apps |
| Зависимости | Минимум | Множество |

Рекомендация: Sanctum для Blog API. Passport нужен только если 
вы планируете OAuth2 для сторонних приложений.
```

### Разница `/aif-explore` vs `/aif-grounded`

| | `/aif-explore` | `/aif-grounded` |
|---|---|---|
| **Тип** | Широкое исследование | Точная проверка факта |
| **Вопрос** | «Как лучше организовать...» | «Что лучше: A или B?» |
| **Результат** | RESEARCH.md с выводами | Краткий ответ с обоснованием |
| **Когда** | Перед планированием | Когда есть конкретный выбор |

> 💡 Используйте `/aif-explore` когда не знаете с чего начать. Используйте `/aif-grounded` когда у вас есть конкретный вопрос.

---

## Шаг 3. Устанавливаем Sanctum

На основе исследования устанавливаем зависимости:

```bash
# Устанавливаем Sanctum
composer require laravel/sanctum

# Публикуем конфиг Sanctum
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"

# Настраиваем SQLite
touch database/database.sqlite
```

В `.env` проверьте:

```env
DB_CONNECTION=sqlite
DB_DATABASE=/absolute/path/to/blog-api/database/database.sqlite
```

---

## Шаг 4. Создаём структуру API

```bash
# API контроллер для авторизации
php artisan make:controller Api/AuthController

# API контроллер для постов (Resource Controller)
php artisan make:controller Api/PostController --api

# Form Request для валидации
php artisan make:request StoreUserRequest
php artisan make:request StorePostRequest

# API Resource для трансформации JSON
php artisan make:resource PostResource
```

---

## 📂 Структура проекта сейчас

```
blog-api/
├── .ai-factory/
│   ├── DESCRIPTION.md          # Спецификация
│   ├── ARCHITECTURE.md         # Архитектура
│   └── RESEARCH.md             # ✅ Исследование (создал /aif-explore)
├── app/
│   ├── Http/
│   │   ├── Controllers/Api/
│   │   │   ├── AuthController.php
│   │   │   └── PostController.php
│   │   └── Requests/
│   │       ├── StoreUserRequest.php
│   │       └── StorePostRequest.php
│   └── Models/
├── database/
│   ├── migrations/
│   └── database.sqlite
├── routes/
│   └── api.php
├── tests/
└── composer.json
```

---

## ✅ Чек-лист Урока 3

- [x] `/aif-explore` выполнен → RESEARCH.md создан
- [x] `/aif-grounded` выполнен → точный ответ получен
- [x] Вы понимаете разницу между explore и grounded
- [x] Sanctum установлен
- [x] Структура API создана через Artisan

---

## 🚀 Что дальше

**Следующий урок:** [Урок 04 — Планирование →](04-planning.md)

В нём мы:
- Создадим ROADMAP.md через `/aif-roadmap`
- Создадим PLAN.md для первой фичи через `/aif-plan`
- Углубим план через `/aif-improve`

---

## 📚 Справочник: Использованные инструменты

| Инструмент | Тип | Описание |
|---|---|---|
| `/aif-explore` | Slash-команда (skill) | Исследование кодовой базы и лучших практик |
| `/aif-grounded` | Slash-команда (skill) | Проверка конкретных фактов о проекте |

### `/aif-explore`

**Когда использовать:**
- Перед планированием фичи
- Когда нужно понять как лучше организовать код
- Когда исследуете незнакомую кодовую базу

**Что создаёт:** `.ai-factory/RESEARCH.md`

**Примеры:**
```
/aif-explore Как лучше организовать структуру Laravel API?
/aif-explore Какие паттерны используются для комментариев в блогах?
```

### `/aif-grounded`

**Когда использовать:**
- Когда есть конкретный вопрос (A или B?)
- Когда нужна точность, а не размышления
- Перед принятием технического решения

**Что создаёт:** Ничего не создаёт — возвращает ответ в чат

**Примеры:**
```
/aif-grounded Sanctum или Passport для API?
/aif-grounded Какой пакет для тестов: PHPUnit или Pest?
/aif-grounded Есть ли уже middleware для авторизации в проекте?
```

---

**Урок 03 завершён!** 🎉
