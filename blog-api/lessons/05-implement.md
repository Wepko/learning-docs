# Урок 05 — Реализация

> **Инструменты:** `/aif-implement`, `/aif-docs`
> **Цель:** Выполнить план — создать миграции, регистрацию и документацию
> **Время:** ~20 минут

---

## 📌 Что вы узнаете

- Как `/aif-implement` читает и выполняет PLAN.md
- Как AI создаёт файлы по одной задаче
- Как обновляется ROADMAP.md автоматически
- Как `/aif-docs` обновляет документацию

---

## 🎯 Сценарий

У нас есть PLAN.md с 5 задачами. Теперь AI выполнит их по порядку — создаст модели, контроллеры, маршруты, тесты.

---

## Шаг 1. Запускаем реализацию

В AI-агенте выполните:

```
/aif-implement
```

### Что делает `/aif-implement`

Это **главная рабочая команда** — AI читает PLAN.md и выполняет задачи одну за другой.

**Лог выполнения:**

```
[Implement] Reading plan: .ai-factory/PLAN.md
[Implement] Found 5 tasks

[Implement] Task 1/5: Setup Users Migration...
  ✓ Checked existing users migration (Laravel default)
  ✓ Migration includes name, email, password
  ✓ php artisan migrate --force
  ✓ Migration successful

[Implement] Task 2/5: Update User Model...
  ✓ Updated app/Models/User.php
  ✓ Fillable fields: name, email, password
  ✓ HasApiTokens trait added (Sanctum)
  ✓ Password hashing via Laravel mutator

[Implement] Task 3/5: Create Registration Endpoint...
  ✓ Created app/Http/Requests/StoreUserRequest.php
  ✓ Updated AuthController@register method
  ✓ POST /api/auth/register returns Sanctum token
  ✓ Validation rules configured

[Implement] Task 4/5: Add API Route...
  ✓ Added route in routes/api.php
  ✓ Route::post('/register', [AuthController::class, 'register'])

[Implement] Task 5/5: Write Tests...
  ✓ Created tests/Feature/AuthRegistrationTest.php
  ✓ Test: successful registration ✓
  ✓ Test: duplicate email ✓
  ✓ Test: invalid input ✓
  ✓ Test: password hashing ✓

[docs] Plan requires documentation updates.
Run /aif-docs to update.

[Implement] Plan complete! Created 5 files.
[Implement] Would you like to commit these changes? (Y/n)
```

---

## Шаг 2. Проверяем результат

```bash
# Проверяем созданные файлы
ls app/Http/Controllers/Api/
# AuthController.php

	ls app/Http/Requests/
# StoreUserRequest.php

ls app/Models/
# User.php

ls routes/
# api.php (обновлён с маршрутом /register)

ls tests/Feature/
# AuthRegistrationTest.php
```

---

## Шаг 3. Тестируем

```bash
# Запускаем тесты
php artisan test

# Ожидаемый вывод:
# PASS  Tests\Feature\AuthRegistrationTest
# ✓ successful registration
# ✓ duplicate email
# ✓ invalid input
# ✓ password hashing
#
# Tests:    4 passed
```

---

## Шаг 4. Пробуем API вручную

Запустите сервер:

```bash
php artisan serve
```

В другом терминале:

```bash
curl -X POST http://localhost:8000/api/auth/register \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"name":"Test User","email":"test@example.com","password":"secret123"}'
```

**Ответ:**

```json
{
  "message": "User registered successfully",
  "user": {
    "id": 1,
    "name": "Test User",
    "email": "test@example.com"
  },
  "token": "1|abc123def456..."
}
```

---

## Шаг 5. Обновляем документацию

```
/aif-docs
```

### Что делает `/aif-docs`

Это **генератор документации** — AI обновляет README, создаёт API docs, и другие файлы.

**Лог выполнения:**

```
[docs] Reading project context...
[docs] Found PLAN.md with completed tasks
[docs] Checking DESCRIPTION.md for accuracy...
[docs] Updating README.md with API endpoints...
[docs] Created docs/api-endpoints.md
[docs] Created docs/getting-started.md

[docs] Documentation updated:
  - README.md (API endpoints section)
  - docs/api-endpoints.md (new)
  - docs/getting-started.md (new)
```

> 💡 `/aif-docs` читает DESCRIPTION.md + PLAN.md и понимает что изменилось. Обновляет соответствующую документацию.

---

## Шаг 6. Проверяем ROADMAP.md

`/aif-implement` **автоматически** обновил ROADMAP.md:

```bash
cat .ai-factory/ROADMAP.md
```

```markdown
## Phase 1: Core (MVP)
- [x] Database migrations for users ✓
- [x] User registration endpoint ✓
- [ ] User login + Sanctum token
- [ ] Create post endpoint
```

> 💡 Это работает потому что `/aif-implement` сравнивает выполненные задачи с ROADMAP.md и отмечает совпавшие.

---

## 🔍 Как /aif-implement читает контекст

При запуске `/aif-implement` читает файлы в порядке приоритета:

1. **`.ai-factory/DESCRIPTION.md`** — что это за проект
2. **`.ai-factory/ARCHITECTURE.md`** — как организовывать код
3. **`.ai-factory/RESEARCH.md`** — лучшие практики
4. **`.ai-factory/PLAN.md`** — какие задачи выполнять
5. **`.ai-factory/skill-context/`** — проектные правила (если есть)

Это значит: **качество контекста = качество результата**.

---

## 📂 Структура проекта сейчас

```
blog-api/
├── .ai-factory/
│   ├── DESCRIPTION.md
│   ├── ARCHITECTURE.md
│   ├── RESEARCH.md
│   ├── ROADMAP.md              # ✅ Обновлён (2 пункта выполнены)
│   └── PLAN.md                 # ✅ Выполнен
├── app/
│   ├── Http/
│   │   ├── Controllers/Api/
│   │   │   └── AuthController.php   # ✅ Создан
│   │   └── Requests/
│   │       └── StoreUserRequest.php # ✅ Создан
│   └── Models/
│       └── User.php            # ✅ Обновлён
├── database/
│   └── migrations/             # ✅ Выполнены
├── routes/
│   └── api.php                 # ✅ Обновлён
├── tests/Feature/
│   └── AuthRegistrationTest.php # ✅ Создан
├── docs/
│   ├── api-endpoints.md        # ✅ Создан через /aif-docs
│   └── getting-started.md      # ✅ Создан через /aif-docs
└── README.md                   # ✅ Обновлён через /aif-docs
```

---

## ✅ Чек-лист Урока 5

- [x] `/aif-implement` выполнен → все 5 задач выполнены
- [x] Тесты проходят (`php artisan test` — 4 passed)
- [x] API отвечает на `curl` запрос
- [x] `/aif-docs` выполнен → документация обновлена
- [x] ROADMAP.md обновлён автоматически

---

## 🚀 Что дальше

**Следующий урок:** [Урок 06 — Коммит и ревью →](06-commit-review.md)

В нём мы:
- Создадим conventional commit через `/aif-commit`
- Проведём код-ревью через `/aif-review`
- Сверим реализацию с планом через `/aif-verify`

---

## 📚 Справочник: Использованные инструменты

| Инструмент | Тип | Описание |
|---|---|---|
| `/aif-implement` | Slash-команда (skill) | Выполняет задачи из PLAN.md |
| `/aif-docs` | Slash-команда (skill) | Генерирует/обновляет документацию |

### `/aif-implement`

**Когда использовать:**
- После создания плана через `/aif-plan`
- Когда план утверждён и готов к выполнению

**Что читает:** DESCRIPTION.md + ARCHITECTURE.md + PLAN.md
**Что создаёт:** Файлы по задачам плана
**Что обновляет:** ROADMAP.md (отмечает выполненные milestone)

**Политика документации:**
- Если в плане есть `docs: yes` → mandatory docs checkpoint
- Если `docs: no` или не указано → WARN только

### `/aif-docs`

**Когда использовать:**
- После реализации фичи
- Когда README устарел
- Когда нужна API документация

**Что создаёт/обновляет:** README.md, docs/, API documentation

---

**Урок 05 завершён!** 🎉
