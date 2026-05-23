# Урок 04 — Планирование

> **Инструменты:** `/aif-roadmap`, `/aif-plan`, `/aif-improve`
> **Цель:** Создать стратегический ROADMAP и тактический PLAN для первой фичи
> **Время:** ~15 минут

---

## 📌 Что вы узнаете

- Разница между ROADMAP (стратегия) и PLAN (тактика)
- Как `/aif-roadmap` создаёт дорожную карту
- Как `/aif-plan` создаёт детальный план фичи
- Как `/aif-improve` углубляет план
- Когда использовать `fast` vs `full` режим планирования

---

## 🎯 Сценарий

У нас есть:
- ✅ DESCRIPTION.md — что строим
- ✅ ARCHITECTURE.md — как организуем код
- ✅ RESEARCH.md — лучшие практики
- ✅ Sanctum установлен, структура создана

Теперь нужно решить: **что делать первым?**

---

## Шаг 1. Создаём ROADMAP

В AI-агенте выполните:

```
/aif-roadmap "Создать MVP блога: регистрация, логин, CRUD постов, комментарии"
```

### Что делает `/aif-roadmap`

Это **стратегическое планирование** — AI создаёт высокоуровневый план фаз, а не конкретных задач.

**AI создаст `.ai-factory/ROADMAP.md`:**

```markdown
# Blog API Roadmap

## Phase 1: Core (MVP)
- [ ] Database migrations for users
- [ ] User registration endpoint
- [ ] User login + Sanctum token
- [ ] Create post endpoint
- [ ] Get all posts endpoint
- [ ] Get single post endpoint

## Phase 2: Features
- [ ] Update/delete post
- [ ] Comments on posts
- [ ] Tags for posts
- [ ] Sanctum middleware for protected routes

## Phase 3: Polish
- [ ] Form Request validation
- [ ] API Resources for JSON
- [ ] Tests for all endpoints
- [ ] API documentation
```

> 💡 ROADMAP.md — это **стратегический чеклист**. Он показывает путь, но не говорит как идти.

---

## Шаг 2. Планируем первую фичу

```
/aif-plan "Создать миграцию пользователей и endpoint регистрации"
```

### Что делает `/aif-plan`

Это **тактическое планирование** — AI создаёт конкретные задачи с шагами.

**AI задаст вопросы:**

```
- Нужны ли тесты? → Y (обязательно!)
- Нужно ли логирование? → Y
- Нужна ли документация? → Y
```

> 💡 Всегда отвечайте `Y` на тесты. AI Factory уважает выбор — если скажете `N`, тестов не будет.

**AI создаст `.ai-factory/PLAN.md` (fast режим):**

```markdown
# Feature: User Registration & Database Setup

## Tasks

### Task 1: Setup Users Migration
- [ ] Check existing users migration (Laravel default)
- [ ] Run migration: php artisan migrate

### Task 2: Update User Model
- [ ] Ensure fillable fields: name, email, password
- [ ] Add HasApiTokens trait (Sanctum)
- [ ] Ensure password is hashed (Laravel mutator)

### Task 3: Create Registration Endpoint
- [ ] Update app/Http/Controllers/Api/AuthController.php
- [ ] POST /api/auth/register
- [ ] Create StoreUserRequest for validation
- [ ] Return Sanctum token on success

### Task 4: Add API Route
- [ ] Add route in routes/api.php
- [ ] Group under /api/auth prefix

### Task 5: Write Tests
- [ ] Feature test: successful registration
- [ ] Feature test: duplicate email
- [ ] Feature test: invalid input
- [ ] Feature test: password hashing

### Commit Checkpoint
- feat: add user registration with Sanctum
```

---

## Шаг 3. Fast vs Full режим

`/aif-plan` имеет два режима:

| | `fast` | `full` |
|---|---|---|
| **Команда** | `/aif-plan "текст"` | `/aif-plan full "текст"` |
| **Файл** | `.ai-factory/PLAN.md` | `.ai-factory/plans/<branch>.md` |
| **Git ветка** | Не создаёт | Создаёт ветку |
| **Когда** | Для простых фич | Для сложных/рискованных |

```bash
# Fast — сохраняет в PLAN.md, без ветки
/aif-plan "Добавить регистрацию пользователей"

# Full — создаёт ветку feature-user-auth, сохраняет в plans/
/aif-plan full "Добавить систему регистрации и авторизации"
```

> 💡 Для нашего туториала используем `fast` — проще и нагляднее.

---

## Шаг 4. Углубляем план (опционально)

Если план слишком поверхностный — углубите:

```
/aif-improve
```

### Что делает `/aif-improve`

Это **режим уточнения плана** — AI добавляет детали:
- Конкретные имена файлов
- Дополнительные задачи (обработка ошибок, edge cases)
- Связи между задачами

**До:**
```
### Task 3: Create Registration Endpoint
- [ ] Update AuthController.php
- [ ] POST /api/auth/register
```

**После `/aif-improve`:**
```
### Task 3: Create Registration Endpoint
- [ ] Update app/Http/Controllers/Api/AuthController.php
- [ ] Add register() method
- [ ] POST /api/auth/register
- [ ] Create StoreUserRequest with validation rules:
      name: required|string|max:255
      email: required|email|unique:users
      password: required|string|min:8|confirmed
- [ ] Return Sanctum token on success
- [ ] Return 422 on validation error
```

---

## Шаг 5. Изучаем план

Откройте `.ai-factory/PLAN.md` и проверьте:

- [ ] Все ли задачи есть?
- [ ] Правильный ли порядок?
- [ ] Ничего не забыли (обработка ошибок, edge cases)?

Если что-то не так — запустите `/aif-improve` ещё раз.

---

## 🔍 ROADMAP vs PLAN — когда что использовать

|               | ROADMAP                        | PLAN               |
| ------------- | ------------------------------ | ------------------ |
| **Команда**   | `/aif-roadmap`                 | `/aif-plan`        |
| **Уровень**   | Стратегия (фазы)               | Тактика (задачи)   |
| **Файл**      | `ROADMAP.md`                   | `PLAN.md`          |
| **Когда**     | В начале проекта               | Перед каждой фичей |
| **Обновляет** | `/aif-implement` автоматически | Вы вручную         |
|               |                                |                    |

**Типичный цикл:**
```
/aif-roadmap          ← общая картина
/aif-plan             ← план одной фичи
/aif-implement        ← выполнение
                      ← ROADMAP.md обновляется автоматически
/aif-plan             ← план следующей фичи
```

---

## 📂 Структура проекта сейчас

```
blog-api/
├── .ai-factory/
│   ├── DESCRIPTION.md          # Спецификация
│   ├── ARCHITECTURE.md         # Архитектура
│   ├── RESEARCH.md             # Исследование
│   ├── ROADMAP.md              # ✅ Стратегический план
│   └── PLAN.md                 # ✅ Тактический план фичи
├── app/
├── database/
├── routes/
└── tests/
```

---

## ✅ Чек-лист Урока 4

- [x] `/aif-roadmap` выполнен → ROADMAP.md создан
- [x] `/aif-plan` выполнен → PLAN.md создан
- [x] Вы понимаете разницу между roadmap и plan
- [x] Вы понимаете разницу между fast и full
- [x] `/aif-improve` изучен (опционально)

---

## 🚀 Что дальше

**Следующий урок:** [Урок 05 — Реализация →](05-implement.md)

В нём мы:
- Выполним PLAN.md через `/aif-implement`
- Обновим документацию через `/aif-docs`
- Протестируем результат

---

## 📚 Справочник: Использованные инструменты

| Инструмент | Тип | Описание |
|---|---|---|
| `/aif-roadmap` | Slash-команда (skill) | Создаёт стратегическую дорожную карту |
| `/aif-plan` | Slash-команда (skill) | Создаёт тактический план фичи |
| `/aif-improve` | Slash-команда (skill) | Углубляет существующий план |

### `/aif-roadmap`

**Когда использовать:**
- В начале проекта — задать направление
- Периодически — проверить прогресс
- Когда нужно добавить/изменить приоритеты

**Что создаёт:** `.ai-factory/ROADMAP.md`

### `/aif-plan`

**Когда использовать:**
- Перед реализацией любой фичи
- Когда нужен чёткий список задач

**Режимы:**
```bash
/aif-plan "текст"           # fast → PLAN.md
/aif-plan full "текст"      # full → plans/<branch>.md + git branch
```

**Что создаёт:** `.ai-factory/PLAN.md` или `.ai-factory/plans/<branch>.md`

### `/aif-improve`

**Когда использовать:**
- Когда план слишком поверхностный
- Когда нужны детали в задачах
- Перед запуском `/aif-implement` для качества

**Что делает:** Углубляет существующий план, добавляет детали

---

**Урок 04 завершён!** 🎉
