# Урок 07 — Быстрый фикс

> **Инструмент:** `/aif-fix`
> **Цель:** Исправить баг без плана — быстро и точно
> **Время:** ~10 минут

---

## 📌 Что вы узнаете

- Когда использовать `/aif-fix` вместо `/aif-implement`
- Как AI исследует, находит и исправляет баг
- Разница между «фиксом» и «фичей»

---

## 🎯 Сценарий

Мы обнаружилили баг: при попытке зарегистрировать пользователя с уже существующим email, Laravel возвращает **500 Internal Server Error** вместо **422 Validation Error**.

Это не фича — это баг. И для него не нужен план на 5 задач. Нужен быстрый фикс.

---

## Шаг 1. Воспроизводим баг

Первый запрос — регистрация нового пользователя:

```bash
curl -X POST http://localhost:8000/api/auth/register \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"name":"User One","email":"duplicate@example.com","password":"secret123"}'
```

Ответ: ✅ 200 OK, пользователь создан.

Второй запрос — тот же email:

```bash
curl -X POST http://localhost:8000/api/auth/register \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"name":"User Two","email":"duplicate@example.com","password":"secret456"}'
```

Ответ: ❌ **500 Internal Server Error**

```json
{
  "message": "SQLSTATE[23000]: Integrity constraint violation: 19 UNIQUE constraint failed: users.email"
}
```

**Проблема:** Валидация `unique:users` в StoreUserRequest не работает корректно.

---

## Шаг 2. Исправляем через /aif-fix

В AI-агенте выполните:

```
/aif-fix "Duplicate email возвращает 500 вместо 422. Ожидаемое поведение: 422 с сообщением валидации"
```

### Что делает `/aif-fix`

Это **быстрый фикс без плана** — AI сам исследует код, находит причину, исправляет.

**Лог выполнения:**

```
[fix] Investigating: Duplicate email returns 500 instead of 422
[fix] Reading DESCRIPTION.md for context...
[fix] Reading skill-context rules...

[fix] Investigation:
  → Checking StoreUserRequest.php
  → Found validation rule: 'email' => 'required|email|unique:users'
  → Checking AuthController.php
  → Found: register() method uses StoreUserRequest
  → Testing endpoint...

[fix] Root cause: StoreUserRequest exists but validation rule
      'unique:users' uses wrong table reference. SQLite needs
      explicit table name. Current rule may not be applied correctly.

[fix] Fix: Adding explicit unique rule with proper table reference
  → Updated app/Http/Requests/StoreUserRequest.php:
    'email' => ['required', 'email', Rule::unique('users', 'email')]

[fix] Adding test for the bug:
  → Updated tests/Feature/AuthRegistrationTest.php:
    Added test: duplicate email returns 422

[fix] Running tests...
  ✓ successful registration
  ✓ duplicate email returns 422
  ✓ invalid input
  ✓ password hashing

[fix] Bug fixed. Suggesting test coverage added.
```

---

## Шаг 3. Проверяем фикс

```bash
# Повторяем запрос с дубликатом
curl -X POST http://localhost:8000/api/auth/register \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"name":"User Two","email":"duplicate@example.com","password":"secret456"}'
```

**Теперь ответ:** ✅ 422 Unprocessable Entity

```json
{
  "message": "The email has already been taken.",
  "errors": {
    "email": ["The email has already been taken."]
  }
}
```

---

## 🔍 /aif-fix vs /aif-implement

Это **ключевое различие** в AI Factory:

| | `/aif-fix` | `/aif-implement` |
|---|---|---|
| **Тип** | Быстрый фикс бага | Выполнение плана фичи |
| **План** | Не нужен | Обязателен (PLAN.md) |
| **Процесс** | Исследуй → Найди → Исправь | Задача 1 → Задача 2 → ... |
| **Файлы** | `.ai-factory/patches/` | По плану из PLAN.md |
| **Когда** | «Что-то сломалось» | «Хочу новую фичу» |
| **Время** | 1-2 минуты | 10-30 минут |

### Когда что использовать:

**`/aif-fix`:**
- «TypeError: Cannot read property 'name'»
- «Endpoint возвращает 500 вместо 404»
- «Тест падает после последнего коммита»

**`/aif-implement`:**
- «Добавить CRUD для постов»
- «Создать систему комментариев»
- «Интегрировать OAuth авторизацию»

> 💡 Простое правило: **баг = fix, фича = implement**

---

## Шаг 4. Патч self-improvement

`/aif-fix` создал патч в `.ai-factory/patches/`:

```bash
ls .ai-factory/patches/
# 2025-01-01-duplicate-email-validation.md
```

Этот патч описывает что было сломано и как исправлено. Позже `/aif-evolve` использует эти патчи для обучения навыков.

---

## 📂 Структура проекта сейчас

```
blog-api/
├── .ai-factory/
│   ├── DESCRIPTION.md
│   ├── ARCHITECTURE.md
│   ├── ROADMAP.md
│   ├── PLAN.md
│   └── patches/
│       └── 2025-01-01-duplicate-email-validation.md  # ✅ Создан /aif-fix
├── app/Http/Requests/
│   └── StoreUserRequest.php   # ✅ Исправлен
├── tests/Feature/
│   └── AuthRegistrationTest.php  # ✅ Добавлен тест на баг
└── ...
```

---

## ✅ Чек-лист Урока 7

- [x] Баг воспроизведён (500 → 422)
- [x] `/aif-fix` выполнен → баг исправлен
- [x] Тест на баг добавлен
- [x] Вы понимаете разницу между fix и implement

---

## 🚀 Что дальше

**Следующий урок:** [Урок 08 — Рефлекс-луп →](08-loop.md)

В нём мы:
- Создадим Post CRUD через `/aif-loop`
- Увидим итеративный цикл с проверкой качества
- Поймём как AI улучшает код через многократные проходы

---

## 📚 Справочник: Использованные инструменты

| Инструмент | Тип | Описание |
|---|---|---|
| `/aif-fix` | Slash-команда (skill) | Быстрый фикс бага без плана |

### `/aif-fix`

**Когда использовать:**
- Обнаружен баг в существующем коде
- Нужно быстро исправить без планирования
- Тест падает или endpoint возвращает ошибку

**Что читает:** DESCRIPTION.md + skill-context + ограниченная история патчей
**Что создаёт:** Исправленный код + тест + патч в `.ai-factory/patches/`

**Примеры:**
```
/aif-fix "TypeError: Cannot read property 'name'"
/aif-fix "Endpoint возвращает 500 вместо 404"
/aif-fix "Тест падает после последнего коммита"
```

---

**Урок 07 завершён!** 🎉
