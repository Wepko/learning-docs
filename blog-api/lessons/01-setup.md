# Урок 01 — Подготовка проекта

> **Инструмент:** `ai-factory init` (CLI команда)
> **Цель:** Создать Laravel-проект и установить AI Factory
> **Время:** ~10 минут

---

## 📌 Что вы узнаете

- Как создать новый Laravel-проект
- Как установить AI Factory глобально
- Что делает `ai-factory init`
- Какие файлы создаются при инициализации

---

## 🎯 Сценарий

Вы начинаете с чистого листа. Нет кода, нет сервера, нет БД. Только идея — **Blog API** на Laravel.

Первый шаг — создать проект и подключить AI Factory, чтобы все последующие уроки работали с контекстом.

---

## Шаг 1. Создаём Laravel-проект

```bash
composer create-project laravel/laravel blog-api
cd blog-api
```

**Что создалось:**

```
blog-api/
├── app/                    # Код приложения (контроллеры, модели)
├── bootstrap/              # Загрузчик фреймворка
├── config/                 # Конфигурация Laravel
├── database/               # Миграции, сидеры
├── public/                 # Точка входа (index.php)
├── resources/              # Шаблоны, ассеты
├── routes/                 # Маршруты (web.php, api.php)
├── tests/                  # Тесты (PHPUnit/Pest)
├── composer.json           # PHP зависимости
└── .env.example            # Шаблон переменных окружения
```

Проверим что работает:

```bash
php artisan serve
# Laravel development server started: http://127.0.0.1:8000
```

Откройте `http://127.0.0.1:8000` — увидите стартовую страницу Laravel. Отлично!

---

## Шаг 2. Устанавливаем AI Factory

AI Factory — это **npm-пакет**, поэтому нужен Node.js (даже для PHP-проектов):

```bash
npm install -g ai-factory
```

Проверим установку:

```bash
ai-factory --version
# 2.x.x
```

---

## Шаг 3. Инициализируем AI Factory

```bash
ai-factory init
```

**Интерактивный wizard задаст 3 вопроса:**

### Вопрос 1: Какой AI-агент используете?

Выберите вашего агента из списка:

| Агент | Директория навыков | MCP |
|-------|-------------------|-----|
| Qwen Code | `.qwen/skills/` | ✅ |
| Claude Code | `.claude/skills/` | ✅ |
| Cursor | `.cursor/skills/` | ✅ |
| Roo Code | `.roo/skills/` | ✅ |
| OpenCode | `.opencode/skills/` | ✅ |
| Kilo Code | `.kilocode/skills/` | ✅ |
| ... и другие | | |

> 💡 Выберите того агента, в котором будете работать. Если не уверены — выберите **Qwen Code**.

### Вопрос 2: Какие навыки установить?

Рекомендуем: **"All base skills"** — это все 22 встроенных навыка.

### Вопрос 3: Настроить MCP?

Для начала выберите `n` (пропустим). MCP — это протокол для подключения внешних сервисов (GitHub, БД). Вернёмся к этому в Уроке 11.

---

## Шаг 4. Проверяем результат

### Что должно появиться в проекте:

```bash
# Проверьте директорию .ai-factory
ls .ai-factory/
# DESCRIPTION.md ← главный файл спецификации
```

```bash
# Проверьте навыки в директории агента
# Для Qwen Code:
ls .qwen/skills/
# aif/  aif-plan/  aif-implement/  aif-fix/ ... (22 штуки)

# Для Claude Code:
ls .claude/skills/
# те же 22 навыка
```

---

## 🔍 Что произошло за кулисами

AI Factory сделал 3 вещи:

1. **Проанализировал `composer.json`** — определил что это PHP/Laravel проект
2. **Создал `.ai-factory/DESCRIPTION.md`** — базовую спецификацию проекта
3. **Скопировал 22 навыка** в директорию вашего AI-агента

### Описание каждого действия:

### 1. Анализ проекта

AI Factory ищет известные файлы для определения стека:
- `composer.json` → PHP/Laravel
- `package.json` → Node.js
- `Cargo.toml` → Rust
- `pyproject.toml` → Python

На основе найденного формирует начальный контекст.

### 2. Создание DESCRIPTION.md

Файл `.ai-factory/DESCRIPTION.md` — это **источник истины** для всех навыков.

Сейчас он минимальный:

```markdown
# Project: blog-api

A Laravel REST API for a blog platform
```

Позже мы его заполним деталями (Урок 2).

### 3. Копирование навыков

Каждый навык — это `.md` файл с инструкцией для AI-агента.

Пример структуры навыка `aif-plan`:

```markdown
---
name: aif-plan
description: Create implementation plan for a feature
---

# /aif-plan

When invoked:
1. Read .ai-factory/DESCRIPTION.md for context
2. Read .ai-factory/ARCHITECTURE.md if exists
3. Create tasks with TaskCreate
...
```

AI-агент (Qwen, Claude, Cursor) читает эти файлы и следует инструкциям.

---

## 📂 Структура проекта сейчас

```
blog-api/
├── .ai-factory/
│   └── DESCRIPTION.md          # Спецификация проекта
├── .qwen/                      # (или .claude/, .cursor/ — зависит от агента)
│   └── skills/
│       ├── aif/
│       ├── aif-plan/
│       ├── aif-implement/
│       └── ... (22 навыка)
├── app/
├── bootstrap/
├── config/
├── database/
├── routes/
├── tests/
├── composer.json
└── .env
```

---

## ✅ Чек-лист Урока 1

Проверьте что всё готово:

- [x] Laravel-проект создан командой `composer create-project laravel/laravel blog-api`
- [x] `php artisan serve` запускается
- [x] `ai-factory` установлен глобально (`ai-factory --version`)
- [x] `ai-factory init` выполнен без ошибок
- [x] Файл `.ai-factory/DESCRIPTION.md` существует
- [x] Директория `.qwen/skills/` (или эквивалент) содержит 22 навыка

---

## 🚀 Что дальше

**Следующий урок:** [Урок 02 — Настройка контекста →](02-context.md)

В нём мы:
- Заполним DESCRIPTION.md деталями проекта
- Создадим ARCHITECTURE.md через `/aif-architecture`
- Настроим AI-агент через `/aif`

---

## 📚 Справочник: Использованные инструменты

| Инструмент | Тип | Описание |
|---|---|---|
| `ai-factory init` | CLI команда | Устанавливает навыки в проект, создаёт DESCRIPTION.md |

**Когда использовать:**
- Первый раз в новом проекте
- Когда нужно проанализировать стек проекта
- Когда нужно установить все базовые навыки

**Полезные флаги:**
```bash
ai-factory init --no-mcp    # Пропустить настройку MCP
```

---

**Урок 01 завершён!** 🎉
