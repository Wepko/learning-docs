# Урок 08 — Рефлекс-луп

> **Инструмент:** `/aif-loop`
> **Цель:** Создать Post CRUD через итеративный цикл с проверкой качества
> **Время:** ~25 минут

---

## 📌 Что вы узнаете

- Как работает `/aif-loop` — 6 фаз итеративного улучшения
- Когда использовать loop вместо implement
- Как задавать критерии качества
- Как AI останавливается при достижении цели

---

## 🎯 Сценарий

Нам нужно создать **CRUD для постов** — это сложнее чем регистрация пользователя. Здесь много задач:
- Модель Post с миграцией
- Resource Controller с 5 методами
- Валидация через Form Request
- API Resource для JSON
- Тесты на каждый endpoint
- Связь с User (post принадлежит автору)

Для такой задачи `/aif-implement` сделает всё за один проход. Но будет ли это **качественно**?

`/aif-loop` — другой подход: **сделать → проверить → улучшить → повторить**.

---

## Шаг 1. Запускаем луп

В AI-агенте выполните:

```
/aif-loop "Создать Post CRUD: create, index, show, update, destroy с валидацией и тестами"
```

### Что делает `/aif-loop`

Это **рефлекс-луп** — AI проходит через 6 фаз, повторяя цикл пока качество не достигнет порога.

**Сначала AI спросит:**

```
🔄 Reflex Loop: Post CRUD

Success criteria:
1. All 5 CRUD endpoints working
2. Form Request validation for create/update
3. API Resource for JSON transformation
4. Post belongs to User (foreign key)
5. Feature tests for all endpoints
6. At least 8 tests passing

Max iterations: 4

Proceed? (Y/n)
```

> 💡 Критерии качества — это **чеклист успеха**. AI будет итерировать пока все пункты не будут выполнены.

---

## Итерация 1

### Фаза 1: PLAN
```
[loop] Iteration 1/4 — PLAN
[loop] Planning: Post CRUD with validation and tests
[loop] Tasks:
  1. Create Post migration
  2. Create Post model
  3. Create PostController (resource)
  4. Create StorePostRequest
  5. Add routes
  6. Write tests
```

### Фаза 2: PRODUCE + PREPARE (параллельно)
```
[loop] Iteration 1/4 — PRODUCE
[loop] Creating migration: create_posts_table
  → id, user_id (FK), title, body, created_at, updated_at

[loop] Creating model: Post.php
  → fillable: title, body
  → belongsTo: User

[loop] Creating controller: PostController.php
  → index(), store(), show(), update(), destroy()

[loop] Creating request: StorePostRequest.php
  → title: required|string|max:255
  → body: required|string

[loop] Creating resource: PostResource.php
  → toArray: id, title, body, user, dates

[loop] Adding routes to api.php
```

### Фаза 3: EVALUATE (параллельные проверки)
```
[loop] Iteration 1/4 — EVALUATE

[check] CRUD endpoints: ✅ All 5 routes registered
[check] Validation: ⚠️ StorePostRequest exists but no UpdatePostRequest
[check] API Resource: ✅ PostResource created
[check] Foreign key: ✅ user_id FK configured
[check] Tests: ⚠️ Only 3 tests (need 8)
[check] Architecture: ✅ Controllers in Api/ namespace
```

### Фаза 4: CRITIQUE
```
[loop] Iteration 1/4 — CRITIQUE

Issues found:
1. [major] Missing UpdatePostRequest — update uses StorePostRequest
2. [major] Only 3/8 tests — need index, show, update, destroy tests
3. [minor] Post model missing published_at field
4. [minor] No pagination on index()

Score: 4/6 criteria met
```

### Фаза 5: REFINE
```
[loop] Iteration 1/4 — REFINE
[loop] Planning improvements for iteration 2:
  - Create UpdatePostRequest
  - Add tests: index, show, update, destroy
  - Add published_at field
  - Add pagination to index()
```

---

## Итерация 2

### Фаза 1-5:
```
[loop] Iteration 2/4 — PLAN → PRODUCE → EVALUATE → CRITIQUE → REFINE

[check] CRUD endpoints: ✅ All 5 routes working
[check] Validation: ✅ StorePostRequest + UpdatePostRequest
[check] API Resource: ✅ PostResource with relationships
[check] Foreign key: ✅ user_id FK with cascade
[check] Tests: ✅ 7/8 tests passing
[check] Architecture: ✅ All conventions followed

Issues found:
1. [minor] Missing test for unauthorized access

Score: 5/6 criteria met
```

---

## Итерация 3

```
[loop] Iteration 3/4 — PLAN → PRODUCE → EVALUATE → CRITIQUE → REFINE

[check] CRUD endpoints: ✅ All 5 routes working
[check] Validation: ✅ Both requests configured
[check] API Resource: ✅ Includes user relationship
[check] Foreign key: ✅ Configured correctly
[check] Tests: ✅ 8/8 tests passing
[check] Architecture: ✅ All conventions followed

Score: 6/6 criteria met ✅
```

### Фаза 6: STOP
```
[loop] STOP — Quality threshold reached

Final score: 6/6 criteria met
Iterations used: 3/4
Files created: 8
Tests: 8 passing

Artifact saved: .ai-factory/evolution/post-crud/artifact.md
```

---

## Шаг 2. Проверяем результат

```bash
# Созданные файлы
ls database/migrations/
# *_create_posts_table.php

ls app/Models/
# Post.php  User.php

ls app/Http/Controllers/Api/
# AuthController.php  PostController.php

ls app/Http/Requests/
# StoreUserRequest.php  StorePostRequest.php  UpdatePostRequest.php

ls app/Http/Resources/
# PostResource.php

ls tests/Feature/
# AuthRegistrationTest.php  PostCrudTest.php
```

```bash
# Запускаем все тесты
php artisan test

# Tests:    12 passed (4 auth + 8 posts)
```

---

## 🔍 6 фаз рефлекс-лупа

| Фаза | Что делает | Параллельно? |
|---|---|---|
| **PLAN** | Планирует что делать на этой итерации | ❌ |
| **PRODUCE** | Создаёт/изменяет код | ✅ с PREPARE |
| **PREPARE** | Подготавливает окружение (миграции, тесты) | ✅ с PRODUCE |
| **EVALUATE** | Проверяет критерии качества | ✅ группы проверок |
| **CRITIQUE** | Формулирует что улучшить | ❌ |
| **REFINE** | Планирует улучшения для следующей итерации | ❌ |

---

## Когда останавливается луп

Луп остановится когда **выполнится одно из условий**:

1. **Все критерии выполнены** (идеальный случай) ✅
2. **Нет major issues** — ничего критичного не осталось
3. **Стагнация** — последняя итерация не улучшила score
4. **Максимум итераций** — достигнут лимит (default: 4)

> 💡 Если луп остановился по максимуму итераций БЕЗ достижения критериев — в финальном summary будет показан «gap» (сколько не хватило).

---

## 🔍 /aif-loop vs /aif-implement

|              | `/aif-loop`                                 | `/aif-implement`            |
| ------------ | ------------------------------------------- | --------------------------- |
| **Подход**   | Итеративный                                 | Линейный                    |
| **Качество** | Проверяется на каждой итерации              | Проверка после выполнения   |
| **Когда**    | Сложные задачи, требующие качества          | Простые задачи, чёткий план |
| **Критерии** | Задаёте вы явно                             | Определяются планом         |
| **Артефакт** | `.ai-factory/evolution/<alias>/artifact.md` | Выполненный PLAN.md         |

### Когда что использовать:

**`/aif-loop`:**
- «Создать Post CRUD с полной валидацией и 100% покрытием»
- «Рефакторинг auth модуля»
- «Оптимизация производительности API»

**`/aif-implement`:**
- «Добавить endpoint /api/health»
- «Создать миграцию для tags»
- «Настроить middleware авторизации»

> 💡 Правило: **сложное и важное → loop, простое и понятное → implement**

---

## 📂 Структура проекта сейчас

```
blog-api/
├── .ai-factory/
│   ├── DESCRIPTION.md
│   ├── ARCHITECTURE.md
│   ├── ROADMAP.md
│   ├── PLAN.md
│   ├── patches/
│   └── evolution/
│       └── post-crud/
│           ├── run.json              # ✅ Состояние лупа
│           ├── history.jsonl         # ✅ История событий
│           └── artifact.md           # ✅ Финальный артефакт
├── app/
│   ├── Http/
│   │   ├── Controllers/Api/
│   │   │   ├── AuthController.php
│   │   │   └── PostController.php   # ✅ Создан через loop
│   │   ├── Requests/
│   │   │   ├── StoreUserRequest.php
│   │   │   ├── StorePostRequest.php   # ✅ Создан через loop
│   │   │   └── UpdatePostRequest.php  # ✅ Создан через loop (итерация 2)
│   │   └── Resources/
│   │       └── PostResource.php     # ✅ Создан через loop
│   └── Models/
│       └── Post.php                 # ✅ Создан через loop
├── database/migrations/
│   └── *_create_posts_table.php    # ✅ Создан через loop
└── tests/Feature/
    └── PostCrudTest.php            # ✅ Создан через loop (8 тестов)
```

---

## ✅ Чек-лист Урока 8

- [x] `/aif-loop` выполнен → Post CRUD создан
- [x] Все 6 критериев качества достигнуты
- [x] 8 тестов добавлены и проходят
- [x] Вы понимаете 6 фаз лупа
- [x] Вы понимаете когда использовать loop vs implement

---

## 🚀 Что дальше

**Следующий урок:** [Урок 09 — Эволюция →](09-evolve.md)

В нём мы:
- Запустим `/aif-evolve` для обучения AI нашим паттернам
- Увидим как AI анализирует патчи и историю
- Обновим навыки на основе извлечённых уроков

---

## 📚 Справочник: Использованные инструменты

| Инструмент | Тип | Описание |
|---|---|---|
| `/aif-loop` | Slash-команда (skill) | Итеративный цикл с проверкой качества |

### `/aif-loop`

**Когда использовать:**
- Сложные задачи требующие качества
- Рефакторинг
- Когда важно не только «сделать» но и «сделать хорошо»

**Команды:**
```
/aif-loop "цель"              # Новый луп
/aif-loop resume              # Продолжить остановленный
/aif-loop status              # Текущий статус
/aif-loop list                # Список лупов
/aif-loop stop                # Остановить текущий
/aif-loop history             # История событий
/aif-loop clean               # Очистить старые
```

**Состояния:**
```
.ai-factory/evolution/current.json       # Активный луп
.ai-factory/evolution/<alias>/run.json   # Состояние
.ai-factory/evolution/<alias>/history.jsonl  # История
.ai-factory/evolution/<alias>/artifact.md    # Артефакт
```

**Качество:**
- Задаётся явно через success criteria
- Проверяется на каждой итерации
- Показывает score/threshold gap если не достигнуто

---

**Урок 08 завершён!** 🎉
