# Урок 09 — Эволюция

> **Инструмент:** `/aif-evolve`
> **Цель:** Научить AI паттернам нашего проекта
> **Время:** ~15 минут

---

## 📌 Что вы узнаете

- Как `/aif-evolve` анализирует историю проекта
- Как патчи из `/aif-fix` превращаются в улучшения навыков
- Как AI становится точнее для вашего проекта

---

## 🎯 Сценарий

Мы прошли через 8 уроков. За это время AI:
- Создавал файлы
- Исправлял баги
- Следовал нашим конвенциям

Но AI **не запоминает** наши предпочтения между сессиями. Или запоминает?

`/aif-evolve` анализирует всю историю проекта и предлагает **улучшить навыки** на основе того, чему мы научили AI.

---

## Шаг 1. Запускаем эволюцию

В AI-агенте выполните:

```
/aif-evolve "all"
```

### Что делает `/aif-evolve`

Это **самоулучшение навыков** — AI анализирует паттерны проекта и предлагает целевые улучшения.

**Лог выполнения:**

```
[evolve] Analyzing project history...
[evolve] Reading DESCRIPTION.md for context...
[evolve] Reading patches (incremental, cursor-based)...

[evolve] Found 1 patch:
  - 2025-01-01-duplicate-email-validation.md

[evolve] Reading current skills...
[evolve] Analyzing patterns...

[evolve] Patterns found:
1. We always use Form Requests for validation (not inline)
2. We always use API Resources for JSON (not arrays)
3. We always put controllers in Api/ namespace
4. We use Sanctum for all protected routes
5. We write feature tests for every endpoint

[evolve] Skill gaps identified:
1. aif-plan: doesn't emphasize Form Requests enough
2. aif-implement: doesn't mention API Resources by default
3. aif-best-practices: missing Laravel API conventions

[evolve] Proposed improvements:
1. [aif-plan] Add "Use Form Request for validation" to default task template
2. [aif-implement] Add "Use API Resource for response" to guidelines
3. [aif-best-practices] Add Laravel API conventions section

Apply improvements? (Y/n)
```

---

## Шаг 2. Одобряем улучшения

После `Y` AI применит улучшения:

```
[evolve] Applying improvements...

[evolve] 1. Updated aif-plan skill:
     + Added "Use Form Request for validation" to task template
     + Added "Consider API Resource for responses"

[evolve] 2. Updated aif-implement skill:
     + Added "Use API Resource for JSON transformation" to guidelines
     + Added reminder about Resource Controllers

[evolve] 3. Updated aif-best-practices skill:
     + Added Laravel API conventions section
     + Added Sanctum best practices
     + Added Eloquent ORM patterns

[evolve] Saving evolution log...
[evolve] Created: .ai-factory/evolutions/2025-01-01-evolve-all.md

[evolve] Skills improved: 3
[evolve] Evolution #1 complete
```

---

## Шаг 3. Проверяем результат

### Что изменилось в навыках:

**До:**
```markdown
# aif-plan

When creating tasks:
- Consider models, controllers, routes, tests
```

**После:**
```markdown
# aif-plan

When creating tasks:
- Consider models, controllers, routes, tests
- Use Form Request for validation (project convention)
- Consider API Resource for response transformation (project convention)
```

### Эволюционный лог:

```bash
cat .ai-factory/evolutions/2025-01-01-evolve-all.md
```

```markdown
# Evolution: 2025-01-01 (all skills)

## Analysis
- Patches analyzed: 1
- Patterns found: 5 recurring conventions
- Skills reviewed: 3

## Changes
1. aif-plan: Added Form Request emphasis
2. aif-implement: Added API Resource guideline
3. aif-best-practices: Added Laravel API conventions

## Impact
Next /aif-plan will automatically suggest Form Requests.
Next /aif-implement will automatically use API Resources.
```

---

## 🔍 Как это работает

### Источники данных для `/aif-evolve`:

```
DESCRIPTION.md          ← контекст проекта
patches/*.md            ← история багов и фиксов
skill-context/          ← текущие проектные правила
Текущие навыки          ← что уже знает AI
```

### Процесс:

```
1. Читает патчи инкрементально (cursor-based)
   → Только новые, не обработанные ранее

2. Анализирует повторяющиеся паттерны
   → «Всегда Form Requests»
   → «Всегда API Resources»

3. Сравнивает с текущими навыками
   → Навык aif-plan НЕ упоминает Form Requests → gap!

4. Предлагает целевые улучшения
   → Не «сделать навык лучше вообще», а
     «добавить конкретное правило в конкретный навык»

5. Применяет после одобрения
   → Обновляет файлы навыков в директории агента
```

---

## 🔍 Cursor-based инкрементальность

`/aif-evolve` не обрабатывает всё каждый раз. Он использует **курсор**:

```bash
cat .ai-factory/evolutions/patch-cursor.json
# {"last_processed": "2025-01-01-duplicate-email-validation.md"}
```

При следующем запуске AI обработает только **новые** патчи после курсора.

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
│   │   └── 2025-01-01-duplicate-email-validation.md
│   ├── skill-context/
│   └── evolutions/
│       ├── patch-cursor.json                  # ✅ Курсор
│       └── 2025-01-01-evolve-all.md           # ✅ Лог эволюции
├── .qwen/skills/
│   ├── aif-plan/SKILL.md                      # ✅ Улучшен
│   ├── aif-implement/SKILL.md                 # ✅ Улучшен
│   └── aif-best-practices/SKILL.md            # ✅ Улучшен
└── ...
```

---

## ✅ Чек-лист Урока 9

- [x] `/aif-evolve "all"` выполнен
- [x] Паттерны проанализированы (5 conventions)
- [x] 3 навыка улучшены
- [x] Эволюционный лог создан
- [x] Вы понимаете cursor-based инкрементальность

---

## 🚀 Что дальше

**Следующий урок:** [Урок 10 — Правила проекта →](10-rules.md)

В нём мы:
- Создадим RULES.md через `/aif-rules`
- Добавим лучшие практики через `/aif-best-practices`
- Зафиксируем конвенции как источник истины

---

## 📚 Справочник: Использованные инструменты

| Инструмент | Тип | Описание |
|---|---|---|
| `/aif-evolve` | Slash-команда (skill) | Самоулучшение навыков на основе истории |

### `/aif-evolve`

**Когда использовать:**
- После нескольких фич/фиксов — когда накопились паттерны
- Периодически (раз в неделю) для поддержания актуальности
- Когда заметили что AI повторяет одни и те же mistakes

**Режимы:**
```
/aif-evolve "all"              # Анализ всех паттернов
/aif-evolve "aif-plan"         # Анализ одного навыка
/aif-evolve "aif-implement"    # Анализ одного навыка
```

**Что читает:** DESCRIPTION.md + patches + skill-context + текущие навыки
**Что создаёт:** Обновлённые навыки + лог в `evolutions/`

**Инкрементальность:**
```
.ai-factory/evolutions/patch-cursor.json  ← курсор (last processed patch)
```
Обрабатываются только новые патчи после курсора.

---

**Урок 09 завершён!** 🎉
