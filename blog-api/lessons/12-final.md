# Урок 12 — Финал: полный цикл

> **Инструменты:** Все изученные — полный цикл разработки
> **Цель:** Создать последнюю фичу (комментарии) через весь workflow
> **Время:** ~25 минут

---

## 📌 Что вы узнаете

- Как использовать весь набор AI Factory в реальном цикле
- Как выглядит полный цикл от плана до деплоя
- Что получилось за 12 уроков

---

## 🎯 Сценарий

Последняя фича — **комментарии к постам**. Пройдём полный цикл:

```
plan → implement → commit → review → verify
```

---

## Шаг 1. Проверяем ROADMAP

```bash
cat .ai-factory/ROADMAP.md
```

```markdown
# Blog API Roadmap

## Phase 1: Core (MVP)
- [x] Database migrations for users ✓
- [x] User registration endpoint ✓
- [x] User login + Sanctum token
- [x] Create post endpoint ✓
- [x] Get all posts endpoint ✓
- [x] Get single post endpoint ✓

## Phase 2: Features
- [x] Update/delete post ✓
- [ ] Comments on posts          ← ДЕЛАЕМ СЕЙЧАС
- [ ] Tags for posts
- [x] Sanctum middleware ✓

## Phase 3: Polish
- [x] Form Request validation ✓
- [x] API Resources for JSON ✓
- [x] Tests for all endpoints ✓
- [ ] API documentation
```

Осталось: комментарии + документация.

---

## Шаг 2. Планируем фичу

```
/aif-plan "Добавить комментарии к постам: create, index, delete. Комментарий принадлежит пользователю и посту."
```

**AI создаст PLAN.md:**

```markdown
# Feature: Comments on Posts

## Tasks

### Task 1: Create Comments Migration
- [ ] Create migration: create_comments_table
- [ ] Fields: id, post_id (FK), user_id (FK), body, timestamps

### Task 2: Create Comment Model
- [ ] Create app/Models/Comment.php
- [ ] belongsTo: Post, User
- [ ] fillable: body

### Task 3: Create CommentController
- [ ] Create app/Http/Controllers/Api/CommentController.php
- [ ] index($postId) — all comments for a post
- [ ] store($postId) — add comment to a post
- [ ] destroy($id) — delete comment (author only)

### Task 4: Create Form Requests
- [ ] Create StoreCommentRequest
- [ ] Validation: body required, max 1000 chars

### Task 5: Create API Resource
- [ ] Create CommentResource
- [ ] Include: id, body, user (name), created_at

### Task 6: Add Routes
- [ ] GET  /api/posts/{post}/comments
- [ ] POST /api/posts/{post}/comments
- [ ] DELETE /api/comments/{id}

### Task 7: Write Tests
- [ ] Test: create comment on post
- [ ] Test: get all comments for post
- [ ] Test: delete own comment
- [ ] Test: cannot delete other's comment
- [ ] Test: validation — empty body

### Commit Checkpoint
- feat: add comments to posts
```

---

## Шаг 3. Реализуем

```
/aif-implement
```

**Лог выполнения:**

```
[Implement] Reading plan: .ai-factory/PLAN.md
[Implement] Found 7 tasks

[Implement] Task 1/7: Create Comments Migration...
  ✓ Created database/migrations/*_create_comments_table.php
  ✓ Fields: post_id (FK), user_id (FK), body

[Implement] Task 2/7: Create Comment Model...
  ✓ Created app/Models/Comment.php
  ✓ belongsTo: Post, User

[Implement] Task 3/7: Create CommentController...
  ✓ Created app/Http/Controllers/Api/CommentController.php
  ✓ index(), store(), destroy() methods

[Implement] Task 4/7: Create Form Requests...
  ✓ Created app/Http/Requests/StoreCommentRequest.php

[Implement] Task 5/7: Create API Resource...
  ✓ Created app/Http/Resources/CommentResource.php

[Implement] Task 6/7: Add Routes...
  ✓ Added routes in api.php

[Implement] Task 7/7: Write Tests...
  ✓ Created tests/Feature/CommentTest.php
  ✓ 5 tests all passing

[docs] Plan requires documentation updates.
Run /aif-docs to update.

[Implement] Plan complete! Created 7 files.
[Implement] Would you like to commit these changes? (Y/n)
```

---

## Шаг 4. Коммит + ревью + верификация

```
/aif-commit
```

```
[commit] feat: add comments to posts with CRUD operations

Body:
- Add Comment model with Post/User relationships
- Create CommentController with index, store, destroy
- Add StoreCommentRequest for validation
- Create CommentResource for API responses
- Add 5 feature tests
```

```
/aif-verify
```

```
[verify] Reading plan: .ai-factory/PLAN.md
[verify] 7/7 tasks completed. Plan fully implemented. ✅
```

```
/aif-review
```

```
[review] Results:
✅ Relationships properly configured
✅ Validation in Form Request
✅ API Resource for consistent JSON
✅ Authorization: users can only delete own comments
✅ Test coverage: 5 tests
⚠️ [minor] Consider adding pagination for index

[review] Summary: 1 warning, 0 errors. Good quality.
```

---

## Шаг 5. Обновляем ROADMAP

`/aif-implement` обновил ROADMAP.md автоматически:

```bash
cat .ai-factory/ROADMAP.md
```

```markdown
## Phase 2: Features
- [x] Update/delete post ✓
- [x] Comments on posts ✓       ← ОБНОВЛЕНО
- [ ] Tags for posts
- [x] Sanctum middleware ✓
```

---

## Шаг 6. Обновляем документацию

```
/aif-docs
```

```
[docs] Updating API documentation...
[docs] Added Comments endpoints to docs/api-endpoints.md
[docs] Updated README.md with new endpoints
```

---

## 📊 Итоги: что получилось

### За 12 уроков мы создали:

| Компонент | Что сделано |
|---|---|
| **Auth** | Регистрация + Sanctum tokens |
| **Posts** | Полный CRUD + валидация + API Resources |
| **Comments** | Create, index, delete + авторизация |
| **Тесты** | 17 тестов, все проходят |
| **Docker** | Dockerfile + docker-compose |
| **CI/CD** | GitHub Actions (test + lint + build) |
| **Документация** | README + API docs |
| **Контекст** | DESCRIPTION + ARCHITECTURE + RULES + ROADMAP |

### Файлы проекта:

```
blog-api/
├── .ai-factory/
│   ├── DESCRIPTION.md          # Спецификация
│   ├── ARCHITECTURE.md         # Архитектура
│   ├── RESEARCH.md             # Исследование
│   ├── ROADMAP.md              # 80% выполнено
│   ├── RULES.md                # Конвенции
│   ├── PLAN.md                 # Последняя фича выполнена
│   ├── patches/                # 1 патч (email validation)
│   └── evolutions/             # 1 эволюция
├── .github/workflows/ci.yml    # CI/CD
├── Dockerfile                  # Docker
├── docker-compose.yml
├── app/
│   ├── Models/
│   │   ├── User.php
│   │   └── Comment.php
│   ├── Http/
│   │   ├── Controllers/Api/
│   │   │   ├── AuthController.php
│   │   │   ├── PostController.php
│   │   │   └── CommentController.php
│   │   ├── Requests/
│   │   │   ├── StoreUserRequest.php
│   │   │   ├── StorePostRequest.php
│   │   │   ├── UpdatePostRequest.php
│   │   │   └── StoreCommentRequest.php
│   │   └── Resources/
│   │       ├── PostResource.php
│   │       └── CommentResource.php
│   └── ...
├── tests/Feature/
│   ├── AuthRegistrationTest.php   # 4 теста
│   ├── PostCrudTest.php           # 8 тестов
│   └── CommentTest.php            # 5 тестов
├── routes/api.php
├── docs/
│   ├── api-endpoints.md
│   └── getting-started.md
└── README.md
```

---

## 🎯 Все инструменты AI Factory — итоговая таблица

| # | Инструмент | Урок | Когда использовать |
|---|---|---|---|
| 1 | `ai-factory init` | 01 | Первый раз в проекте |
| 2 | `/aif` | 02 | Интерактивная настройка контекста |
| 3 | `/aif-architecture` | 02 | Создание ARCHITECTURE.md |
| 4 | `/aif-explore` | 03 | Исследование лучших практик |
| 5 | `/aif-grounded` | 03 | Проверка конкретных фактов |
| 6 | `/aif-roadmap` | 04 | Стратегическая дорожная карта |
| 7 | `/aif-plan` | 04 | Тактический план фичи |
| 8 | `/aif-improve` | 04 | Углубление плана |
| 9 | `/aif-implement` | 05, 12 | Выполнение плана |
| 10 | `/aif-docs` | 05, 12 | Обновление документации |
| 11 | `/aif-commit` | 06, 12 | Conventional commit |
| 12 | `/aif-review` | 06, 12 | Код-ревью |
| 13 | `/aif-verify` | 06, 12 | Сверка с планом |
| 14 | `/aif-fix` | 07 | Быстрый фикс бага |
| 15 | `/aif-loop` | 08 | Итеративный цикл с качеством |
| 16 | `/aif-evolve` | 09 | Обучение AI паттернам |
| 17 | `/aif-rules` | 10 | Генерация правил проекта |
| 18 | `/aif-best-practices` | 10 | Лучшие практики |
| 19 | `/aif-dockerize` | 11 | Docker конфиги |
| 20 | `/aif-ci` | 11 | CI/CD pipeline |

**Все 20 инструментов задействованы.** ✅

---

## 🔄 Типичный рабочий цикл (шпаргалка)

### Новая фича:
```
/aif-plan "описание фичи"
/aif-improve                    # опционально
/aif-implement
/aif-docs                       # если нужна документация
/aif-commit
/aif-review
```

### Нашёл баг:
```
/aif-fix "описание бага"
/aif-commit
```

### Сложная задача:
```
/aif-loop "цель с критериями качества"
/aif-commit
/aif-review
```

### Периодически:
```
/aif-evolve "all"               # раз в неделю
/aif-roadmap                    # проверить прогресс
```

---

## ✅ Финальный чек-лист

- [x] Все 12 уроков пройдены
- [x] Все 20 инструментов AI Factory использованы
- [x] Blog API работает (Auth + Posts + Comments)
- [x] 17 тестов проходят
- [x] Docker контейнер работает
- [x] CI/CD pipeline настроен
- [x] Документация обновлена
- [x] ROADMAP.md на 80% выполнен

---

## 🎓 Поздравляем!

Вы прошли полный курс AI Factory. Теперь вы умеете:

- 🛠️ Устанавливать и настраивать AI Factory
- 📋 Планировать фичи через `/aif-plan`
- 🔧 Реализовывать через `/aif-implement`
- 🔄 Использовать рефлекс-луп для качества
- 🐛 Быстро фиксить баги через `/aif-fix`
- 📝 Коммитить и ревьюить код
- 📦 Контейнеризировать и настраивать CI/CD
- 🧠 Обучать AI паттернам проекта

---

## 📚 Все уроки курса

1. [Урок 01 — Подготовка проекта](01-setup.md)
2. [Урок 02 — Настройка контекста](02-context.md)
3. [Урок 03 — Исследование](03-explore.md)
4. [Урок 04 — Планирование](04-planning.md)
5. [Урок 05 — Реализация](05-implement.md)
6. [Урок 06 — Коммит и ревью](06-commit-review.md)
7. [Урок 07 — Быстрый фикс](07-fix.md)
8. [Урок 08 — Рефлекс-луп](08-loop.md)
9. [Урок 09 — Эволюция](09-evolve.md)
10. [Урок 10 — Правила проекта](10-rules.md)
11. [Урок 11 — DevOps](11-devops.md)
12. [Урок 12 — Финал](12-final.md) ← вы здесь

---

## 🌐 Дополнительные ресурсы

- [Официальная документация AI Factory](../../docs/)
- [Getting Started](../../docs/getting-started.md)
- [Workflow Guide](../../docs/workflow.md)
- [Loop Guide](../../docs/loop.md)
- [Skills Reference](../../docs/skills.md)
- [skills.sh](https://skills.sh) — маркетплейс навыков

---

**Курс завершён!** 🎉🎓
