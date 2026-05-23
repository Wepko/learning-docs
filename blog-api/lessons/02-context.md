# Урок 02 — Настройка контекста

> **Инструменты:** `/aif`, `/aif-architecture`
> **Цель:** Заполнить DESCRIPTION.md деталями и создать ARCHITECTURE.md
> **Время:** ~15 минут

---

## 📌 Что вы узнаете

- Зачем нужен DESCRIPTION.md и как его заполнить
- Как `/aif-architecture` создаёт архитектурную основу
- Как `/aif` запускает интерактивную настройку контекста
- Как навыки читают контекст из `.ai-factory/`

---

## 🎯 Сценарий

После Урока 01 у нас есть Laravel-проект с AI Factory. Но `.ai-factory/DESCRIPTION.md` почти пустой:

```markdown
# Project: blog-api

A Laravel REST API for a blog platform
```

Это как сказать «я делаю веб-приложение» — слишком общо. Нужно объяснить AI-агенту **что именно** мы строим, на каком стеке, с какими ограничениями.

---

## Шаг 1. Заполняем DESCRIPTION.md

Откройте `.ai-factory/DESCRIPTION.md` и замените содержимое:

```markdown
# Project: blog-api

## Description
REST API for a simple blog platform. Users can create, read, update, and delete posts.
Supports comments, tags, and Sanctum API authentication.

## Stack
- **Runtime:** PHP 8.2+
- **Framework:** Laravel 11
- **Database:** SQLite (via Eloquent ORM)
- **Auth:** Laravel Sanctum (API tokens)
- **Testing:** PHPUnit
- **Validation:** Form Requests

## Key Features
1. User registration and login via Sanctum
2. CRUD for posts with tags
3. Comments on posts
4. Sanctum-based API authentication
5. Input validation via Form Requests
6. API Resources for JSON transformation

## Project Structure
```
app/
├── Http/
│   ├── Controllers/Api/   # API controllers
│   ├── Requests/          # Form requests (validation)
│   └── Middleware/        # Auth middleware
├── Models/                # Eloquent models
└── Providers/
database/
├── migrations/            # Database migrations
└── seeders/               # Database seeders
routes/
└── api.php                # API routes
tests/
├── Feature/               # Feature tests
└── Unit/                  # Unit tests
```

## Constraints
- Use Eloquent ORM (no raw SQL)
- All API endpoints must have tests
- Follow REST best practices
- Use Form Requests for validation
- API Resources for JSON transformation
```

> 💡 **Зачем это нужно?** Все 22 навыка читают этот файл. Чем точнее описание — тем точнее работа AI.

---

## Шаг 2. Создаём ARCHITECTURE.md

В AI-агенте (Qwen Code, Claude Code, Cursor) выполните:

```
/aif-architecture
```

**Что сделает AI:**

1. Прочитает DESCRIPTION.md
2. Исследует структуру Laravel-проекта
3. Создаст `.ai-factory/ARCHITECTURE.md` с:
   - Архитектурными решениями (почему API-first, почему Sanctum)
   - Паттернами проектирования (Repository, Resource Controller)
   - Правилами организации кода
   - Service Container подходом Laravel

**Пример результата:**

```markdown
# Architecture: blog-api

## Architectural Style
- API-first REST architecture
- Resource controllers for CRUD operations
- Service layer for business logic

## Key Decisions
- Sanctum for API auth (lightweight, token-based)
- SQLite for development (zero config)
- Form Requests for validation (Laravel native)
- API Resources for response transformation

## Folder Structure Rules
- All API controllers in `app/Http/Controllers/Api/`
- All validation in `app/Http/Requests/`
- All models in `app/Models/`
- Migrations in `database/migrations/`
...
```

> 💡 ARCHITECTURE.md — это **источник истины для структуры кода**. Когда `/aif-implement` создаёт файлы, он следует этим правилам.

---

## Шаг 3. Запускаем интерактивную настройку

В AI-агенте выполните:

```
/aif
```

**Что сделает AI:**

1. Прочитает DESCRIPTION.md (который мы заполнили)
2. Прочитает ARCHITECTURE.md (если существует)
3. Задаст уточняющие вопросы:
   - «Нужна ли поддержка пагинации для списка постов?»
   - «Какой формат дат использовать в API?»
   - «Нужны ли soft deletes для постов?»
4. Обновит DESCRIPTION.md на основе ответов

> 💡 `/aif` — это **диалог** с AI для уточнения спецификации. Не confundите с `ai-factory init` — это CLI команда для установки.

---

## 🔍 Как навыки читают контекст

Каждый навык при вызове читает одни и те же файлы:

```
/aif-plan        → читает DESCRIPTION.md + ARCHITECTURE.md
/aif-implement   → читает DESCRIPTION.md + ARCHITECTURE.md + PLAN.md
/aif-fix         → читает DESCRIPTION.md + skill-context
/aif-explore     → читает DESCRIPTION.md
/aif-loop        → читает DESCRIPTION.md + ARCHITECTURE.md + RULES.md
```

Это значит: **качество контекста = качество работы AI**.

Если DESCRIPTION.md пустой → AI гадает.
Если DESCRIPTION.md полный → AI знает стек, структуру, ограничения.

---

## 📂 Структура проекта сейчас

```
blog-api/
├── .ai-factory/
│   ├── DESCRIPTION.md          # ✅ Заполнен деталями
│   └── ARCHITECTURE.md         # ✅ Создан через /aif-architecture
├── .qwen/skills/               # 22 навыка
├── app/
├── config/
├── database/
├── routes/
├── tests/
├── composer.json
└── .env
```

---

## ✅ Чек-лист Урока 2

- [x] DESCRIPTION.md заполнен стеком, структурой, ограничениями
- [x] `/aif-architecture` выполнен → ARCHITECTURE.md создан
- [x] `/aif` выполнен → AI уточнил детали
- [x] Вы понимаете что все навыки читают DESCRIPTION.md

---

## 🚀 Что дальше

**Следующий урок:** [Урок 03 — Исследование →](03-explore.md)

В нём мы:
- Используем `/aif-explore` для исследования лучших практик Laravel API
- Используем `/aif-grounded` для проверки фактов
- Установим Sanctum на основе рекомендаций

---

## 📚 Справочник: Использованные инструменты

| Инструмент | Тип | Описание |
|---|---|---|
| `/aif-architecture` | Slash-команда (skill) | Генерирует ARCHITECTURE.md на основе DESCRIPTION.md |
| `/aif` | Slash-команда (skill) | Интерактивная настройка и уточнение контекста |

### `/aif-architecture`

**Когда использовать:**
- В начале нового проекта
- Когда нужно зафиксировать архитектурные решения
- Перед планированием фичи

**Что создаёт:** `.ai-factory/ARCHITECTURE.md`

### `/aif`

**Когда использовать:**
- После заполнения DESCRIPTION.md
- Когда нужно уточнить требования
- Когда AI задаёт вопросы о проекте

**Что делает:** Читает контекст, задаёт вопросы, обновляет DESCRIPTION.md

---

**Урок 02 завершён!** 🎉
