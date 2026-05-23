# Урок 06 — Коммит и ревью

> **Инструменты:** `/aif-commit`, `/aif-review`, `/aif-verify`
> **Цель:** Создать чистый коммит, проверить качество кода, сверить с планом
> **Время:** ~15 минут

---

## 📌 Что вы узнаете

- Как `/aif-commit` создаёт conventional commit
- Как `/aif-review` проверяет качество кода
- Как `/aif-verify` сверяет реализацию с планом
- Разница между тремя командами качества

---

## 🎯 Сценарий

Регистрация работает, тесты проходят. Прежде чем двигаться дальше — зафиксируем результат и проверим качество.

Три команды для трёх разных целей:

| Команда       | Что делает      | Когда                              |
| ------------- | --------------- | ---------------------------------- |
| `/aif-commit` | Создаёт коммит  | Перед переходом к следующей задаче |
| `/aif-review` | Код-ревью       | После коммита или перед мержем     |
| `/aif-verify` | Сверка с планом | После реализации                   |

---

## Шаг 1. Создаём коммит

В AI-агенте выполните:

```
/aif-commit
```

### Что делает `/aif-commit`

Это **генератор conventional commits** — AI анализирует изменения и создаёт правильный коммит.

**Лог выполнения:**

```
[commit] Analyzing changes...
[commit] Files changed: 5
  - app/Http/Controllers/Api/AuthController.php (new)
  - app/Http/Requests/StoreUserRequest.php (new)
  - app/Models/User.php (modified)
  - routes/api.php (modified)
  - tests/Feature/AuthRegistrationTest.php (new)

[commit] Suggested commit message:
feat: add user registration with Sanctum authentication

Body:
- Add AuthController with register endpoint
- Create StoreUserRequest for validation
- Add HasApiTokens trait to User model
- Configure Sanctum API authentication
- Add feature tests for registration

Commit? (Y/n)
```

**Результат в git:**

```bash
git log -1
# commit abc1234
# Author: You <you@example.com>
# Date: 2025-01-01
#
#     feat: add user registration with Sanctum authentication
#
#     - Add AuthController with register endpoint
#     - Create StoreUserRequest for validation
#     - Add HasApiTokens trait to User model
```

> 💡 Conventional Commits: `type: description` где type = `feat`, `fix`, `docs`, `refactor`, `test`, `chore`

---

## Шаг 2. Код-ревью

```
/aif-review
```

### Что делает `/aif-review`

Это **автоматический код-ревью** — AI проверяет последние изменения на:
- Правильность кода
- Безопасность
- Производительность
- Следование конвенциям 

**Лог выполнения:**

```
[review] Analyzing changes since last commit...
[review] Checking: app/Http/Controllers/Api/AuthController.php
[review] Checking: app/Http/Requests/StoreUserRequest.php
[review] Checking: app/Models/User.php
[review] Checking: routes/api.php
[review] Checking: tests/Feature/AuthRegistrationTest.php

[review] Results:
✅ Validation rules properly separated in Form Request
✅ Password hashing confirmed (bcrypt via Laravel mutator)
✅ Sanctum token returned on success
✅ Test coverage: 4 tests, all passing
⚠️ [minor] Consider adding rate limiting to registration endpoint
⚠️ [minor] Consider adding API Resource for user response

[review] Summary: 2 warnings, 0 errors. Good quality.
```

> 💡 `/aif-review` не блокирует — это рекомендации. Но если есть серьёзные проблемы (ошибки безопасности), он укажет явно.

---

## Шаг 3. Сверка с планом

```
/aif-verify
```

### Что делает `/aif-verify`

Это **сверка реализации с планом** — AI читает PLAN.md и проверяет что все задачи выполнены.

**Лог выполнения:**

```
[verify] Reading plan: .ai-factory/PLAN.md
[verify] Found 5 tasks

[verify] Task 1: Setup Users Migration
  ✅ Migration checked and executed

[verify] Task 2: Update User Model
  ✅ Fillable fields configured
  ✅ HasApiTokens trait added
  ✅ Password hashing configured

[verify] Task 3: Create Registration Endpoint
  ✅ AuthController updated
  ✅ StoreUserRequest created
  ✅ Endpoint returns Sanctum token

[verify] Task 4: Add API Route
  ✅ Route added in api.php

[verify] Task 5: Write Tests
  ✅ 4 tests passing

[verify] Summary: 5/5 tasks completed. Plan fully implemented.
```

> 💡 `/aif-verify` — это **контрактная проверка**. Если задачи не выполнены — он скажет явно что именно пропущено.

---

## 🔍 Три команды качества — когда что использовать

### `/aif-commit`
- **Цель:** Зафиксировать изменения в git
- **Когда:** После завершения фичи/задачи
- **Результат:** Conventional commit message

### `/aif-review`
- **Цель:** Проверить качество кода
- **Когда:** После коммита или перед мержем
- **Результат:** Список замечаний

### `/aif-verify`
- **Цель:** Сверить с планом
- **Когда:** После реализации фичи
- **Результат:** N/M задач выполнено

**Типичный цикл:**
```
/aif-implement    ← реализация
/aif-verify       ← все ли задачи выполнены?
/aif-commit       ← коммит с conventional message
/aif-review       ← качество кода OK?
                  ← если OK → следующая фича
                  ← если нет → исправить → повторить
```

---

## 📂 Структура проекта сейчас

```
blog-api/
├── .ai-factory/
│   ├── DESCRIPTION.md
│   ├── ARCHITECTURE.md
│   ├── RESEARCH.md
│   ├── ROADMAP.md
│   └── PLAN.md                 # ✅ Проверен через /aif-verify
├── app/
├── routes/
├── tests/
└── docs/

# Git: коммит создан через /aif-commit
# git log показывает: feat: add user registration with Sanctum authentication
```

---

## ✅ Чек-лист Урока 6

- [x] `/aif-commit` выполнен → conventional commit создан
- [x] `/aif-review` выполнен → качество проверено
- [x] `/aif-verify` выполнен → все задачи плана подтверждены
- [x] Вы понимаете разницу между тремя командами

---

## 🚀 Что дальше

**Следующий урок:** [Урок 07 — Быстрый фикс →](07-fix.md)

В нём мы:
- Смоделируем баг (500 вместо 422 при duplicate email)
- Исправим через `/aif-fix` (без плана, быстро)
- Поймём разницу между `/aif-fix` и `/aif-implement`

---

## 📚 Справочник: Использованные инструменты

| Инструмент | Тип | Описание |
|---|---|---|
| `/aif-commit` | Slash-команда (skill) | Создаёт conventional commit |
| `/aif-review` | Slash-команда (skill) | Автоматический код-ревью |
| `/aif-verify` | Slash-команда (skill) | Сверка реализации с планом |

### `/aif-commit`

**Когда использовать:**
- После завершения фичи
- На checkpoint'ах в плане (5+ задач)
- Перед пушем

**Что создаёт:** Git commit с conventional message

**Формат коммита:**
```
type: description

Body:
- detail 1
- detail 2
```

Где type: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`

### `/aif-review`

**Когда использовать:**
- После коммита
- Перед мержем ветки
- Периодически для контроля качества

**Что проверяет:**
- Правильность кода
- Безопасность
- Производительность
- Следование конвенциям

### `/aif-verify`

**Когда использовать:**
- После `/aif-implement`
- Когда нужно убедиться что план полностью выполнен

**Что читает:** PLAN.md
**Что проверяет:** Каждая задача отмечена как выполненная?

---

**Урок 06 завершён!** 🎉
