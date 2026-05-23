# Урок 10 — Правила проекта

> **Инструменты:** `/aif-rules`, `/aif-best-practices`
> **Цель:** Зафиксировать конвенции как источник истины
> **Время:** ~10 минут

---

## 📌 Что вы узнаете

- Зачем нужен RULES.md и чем он отличается от ARCHITECTURE.md
- Как `/aif-rules` извлекает конвенции из проекта
- Как `/aif-best-practices` добавляет внешние рекомендации
- Как правила влияют на последующие команды

---

## 🎯 Сценарий

За 9 уроков мы выработали много конвенций:
- Все API контроллеры в `Api/` namespace
- Form Requests для валидации
- API Resources для JSON
- Sanctum для авторизации
- Feature тесты для каждого endpoint

Но эти конвенции «в голове» AI. Нужно их **зафиксировать** — чтобы все последующие команды следовали им автоматически.

---

## Шаг 1. Создаём RULES.md

В AI-агенте выполните:

```
/aif-rules
```

### Что делает `/aif-rules`

Это **генератор правил проекта** — AI извлекает неявные конвенции и формализует их.

**Лог выполнения:**

```
[rules] Analyzing project context...
[rules] Reading DESCRIPTION.md...
[rules] Reading ARCHITECTURE.md...
[rules] Reading RESEARCH.md...
[rules] Analyzing existing code patterns...

[rules] Generated rules:

## Code Organization
- All API controllers in `app/Http/Controllers/Api/`
- All Form Requests in `app/Http/Requests/`
- All API Resources in `app/Http/Resources/`
- All models in `app/Models/`

## API Conventions
- Use Resource Controllers for CRUD endpoints
- Use Form Requests for all validation
- Use API Resources for all JSON responses
- Return proper HTTP status codes (201 for create, 422 for validation)

## Authentication
- Use Sanctum for API authentication
- Protected routes use `auth:sanctum` middleware
- Public endpoints: register, login

## Testing
- Feature tests for all API endpoints
- Tests in `tests/Feature/` with descriptive names
- Test both success and error cases

## Database
- Use Eloquent ORM (no raw SQL)
- Migrations for all schema changes
- Foreign keys with cascade deletes

[rules] Created: .ai-factory/RULES.md
```

---

## Шаг 2. Добавляем лучшие практики

```
/aif-best-practices
```

### Что делает `/aif-best-practices`

Это **генератор лучших практик** — AI добавляет внешние рекомендации (не только из проекта, но и из general knowledge).

**Лог выполнения:**

```
[best-practices] Analyzing project stack...
[best-practices] Stack: Laravel 11, Sanctum, SQLite, PHPUnit

[best-practices] Generated best practices:

## Laravel Best Practices
- Use Artisan commands for repetitive tasks
- Keep controllers thin (delegate to services)
- Use Eloquent events for side effects
- Use policy-based authorization

## API Best Practices
- Version your API (/api/v1/)
- Use consistent error response format
- Implement rate limiting
- Use pagination for list endpoints

## Security Best Practices
- Hash all passwords (Laravel does this by default)
- Use CSRF protection (automatic for web routes)
- Validate all user input (Form Requests)
- Use prepared statements (Eloquent does this)

## Testing Best Practices
- Use factories for test data
- Test the happy path first, then edge cases
- Use `assertStatus()` for explicit status codes
- Mock external services

[best-practices] Appended to: .ai-factory/RULES.md
```

> 💡 `/aif-best-practices` **дополняет** RULES.md внешними рекомендациями, а не извлечёнными из проекта.

---

## Шаг 3. Проверяем RULES.md

```bash
cat .ai-factory/RULES.md
```

```markdown
# Project Rules: blog-api

## Code Organization
- All API controllers in `app/Http/Controllers/Api/`
- All Form Requests in `app/Http/Requests/`
- All API Resources in `app/Http/Resources/`
- All models in `app/Models/`

## API Conventions
- Use Resource Controllers for CRUD endpoints
- Use Form Requests for all validation
- Use API Resources for all JSON responses
- Return proper HTTP status codes

## Authentication
- Use Sanctum for API authentication
- Protected routes use `auth:sanctum` middleware

## Testing
- Feature tests for all API endpoints
- Test both success and error cases

## Database
- Use Eloquent ORM (no raw SQL)
- Migrations for all schema changes

## Laravel Best Practices
- Keep controllers thin
- Use Artisan commands for repetitive tasks
- Use factories for test data

## API Best Practices
- Use pagination for list endpoints
- Implement rate limiting
- Consistent error response format
```

---

## 🔍 RULES.md vs ARCHITECTURE.md vs DESCRIPTION.md

Три файла контекста — три разных уровня:

| Файл | Уровень | Что содержит | Кто создаёт |
|---|---|---|---|
| **DESCRIPTION.md** | «Что» | Стек, фичи, структура | `ai-factory init` + вы |
| **ARCHITECTURE.md** | «Как устроено» | Решения, паттерны, зависимости | `/aif-architecture` |
| **RULES.md** | «Как делать» | Конвенции, правила, стандарты | `/aif-rules` + `/aif-best-practices` |

**Аналогия:**
- DESCRIPTION.md = «Мы строим дом из кирпича, 3 комнаты»
- ARCHITECTURE.md = «Фундамент ленточный, крыша двускатная»
- RULES.md = «Кладка в полкирпича, швы 10мм, каждый 5й ряд армировать»

---

## Как правила влияют на команды

После создания RULES.md:

```
/aif-plan "Добавить комментарии"
  → Читает RULES.md
  → Включает задачу: "Create StoreCommentRequest (rule: Form Requests)"
  → Включает задачу: "Create CommentResource (rule: API Resources)"

/aif-implement
  → Читает RULES.md
  → Создаёт контроллер в Api/ namespace (rule: Code Organization)
  → Добавляет auth:sanctum middleware (rule: Authentication)
```

> 💡 RULES.md — это **не просто документация**. Это активные правила, которые читают ВСЕ навыки.

---

## 📂 Структура проекта сейчас

```
blog-api/
├── .ai-factory/
│   ├── DESCRIPTION.md          # «Что строим»
│   ├── ARCHITECTURE.md         # «Как устроено»
│   ├── RESEARCH.md             # Исследование
│   ├── ROADMAP.md              # Стратегия
│   ├── PLAN.md                 # Тактика
│   ├── RULES.md                # ✅ Правила (конвенции)
│   ├── patches/
│   └── evolutions/
└── ...
```

---

## ✅ Чек-лист Урока 10

- [x] `/aif-rules` выполнен → RULES.md создан
- [x] `/aif-best-practices` выполнен → правила дополнены
- [x] Вы понимаете разницу между DESCRIPTION, ARCHITECTURE, RULES
- [x] Вы понимаете как правила влияют на последующие команды

---

## 🚀 Что дальше

**Следующий урок:** [Урок 11 — DevOps →](11-devops.md)

В нём мы:
- Создадим Dockerfile через `/aif-dockerize`
- Создадим GitHub Actions CI через `/aif-ci`
- Проверим что всё работает в контейнере

---

## 📚 Справочник: Использованные инструменты

| Инструмент | Тип | Описание |
|---|---|---|
| `/aif-rules` | Slash-команда (skill) | Генерирует RULES.md из контекста проекта |
| `/aif-best-practices` | Slash-команда (skill) | Добавляет лучшие практики в RULES.md |

### `/aif-rules`

**Когда использовать:**
- После нескольких реализованных фич
- Когда конвенции сформировались
- Перед тем как передавать проект другому

**Что читает:** DESCRIPTION.md + ARCHITECTURE.md + RESEARCH.md + код проекта
**Что создаёт:** `.ai-factory/RULES.md`

### `/aif-best-practices`

**Когда использовать:**
- После `/aif-rules` для дополнения
- Когда нужны внешние рекомендации
- Перед код-ревью для сверки со стандартами

**Что добавляет:** Laravel, API, Security, Testing best practices

---

**Урок 10 завершён!** 🎉
