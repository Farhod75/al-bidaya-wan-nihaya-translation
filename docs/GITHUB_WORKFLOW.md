# GITHUB_WORKFLOW.md
# Структура GitHub-репозитория и процесс работы
# Проект перевода «Аль-Бидая ва-н-Нихая»
# Адаптировано из hadith-verifier: CI/CD, Git workflow, AGENTS.md
# Версия: 1.0 — Май 2026

---

## ════════════════════════════════════════════════════════
## 1. СТРУКТУРА РЕПОЗИТОРИЯ
## ════════════════════════════════════════════════════════

```
al-bidaya-translation/
│
├── README.md                        — Описание проекта, quickstart
├── PROJECT_CONSTITUTION.md          — Конституция проекта (Source of Truth)
├── AGENTS_STRUCTURE.md              — Архитектура 13 агентов
├── VERIFICATION_RESOURCES.md        — Ресурсы для верификации
├── QA_STANDARDS.md                  — Стандарты контроля качества
├── FIX_PATTERNS.md                  — База знаний типичных ошибок
├── AB_TESTING_FRAMEWORK.md          — Методология A/B тестирования
├── GITHUB_WORKFLOW.md               — Этот файл
├── PROJECT_FILES_INDEX.md           — Индекс всех файлов
├── GLOSSARY.md                      — Терминологический глоссарий
├── CHANGELOG.md                     — История изменений
│
├── source/                          — Исходные тексты
│   ├── arabic/
│   │   ├── vol01/                   — Том 1 (арабский оригинал)
│   │   │   ├── chapter_001.md
│   │   │   ├── chapter_002.md
│   │   │   └── ...
│   │   ├── vol02/
│   │   └── ...
│   └── english/
│       ├── darussalam_vol01/        — Английский перевод Darussalam
│       └── ...
│
├── translation/                     — Переводы (основной контент)
│   ├── ru/                          — Русский
│   │   ├── vol01/
│   │   │   ├── chapter_001.md
│   │   │   ├── chapter_002.md
│   │   │   └── TRANSLATION_LOG.md   — Лог перевода тома 1
│   │   ├── vol02/
│   │   └── ...
│   ├── uz/                          — Узбекский (кириллица)
│   │   ├── vol01/
│   │   └── ...
│   └── tj/                          — Таджикский
│       ├── vol01/
│       └── ...
│
├── glossary/                        — Терминологические базы
│   ├── terms_ru.json                — Русские термины
│   ├── terms_uz.json                — Узбекские термины
│   ├── terms_tj.json                — Таджикские термины
│   ├── names.json                   — Имена собственные (все языки)
│   └── places.json                  — Географические названия
│
├── verification/                    — Результаты верификации
│   ├── reports/
│   │   ├── vol01_ru_report.json
│   │   ├── vol01_uz_report.json
│   │   └── ...
│   └── golden_dataset/
│       └── fragments.json           — Эталонные фрагменты для A/B тестов
│
├── audio/                           — Аудио-разметка
│   ├── ru/
│   │   ├── vol01/
│   │   │   ├── chapter_001_ssml.xml  — SSML разметка
│   │   │   └── ...
│   │   └── ...
│   └── ...
│
├── scripts/                         — Скрипты автоматизации
│   ├── check_encoding.py            — Проверка UTF-8
│   ├── check_completeness.py        — Проверка полноты перевода
│   ├── check_glossary.py            — Проверка терминологии
│   ├── check_language.py            — Проверка языкового дрифта
│   ├── verify_quran.py              — Верификация аятов
│   ├── verify_hadith.py             — Верификация хадисов
│   └── ab_test_runner.py            — Запуск A/B тестов
│
├── .github/
│   ├── workflows/
│   │   ├── qa-checks.yml            — Автоматические проверки при push
│   │   ├── verify-islamic.yml       — Верификация исламского контента
│   │   └── release.yml              — Публикация релиза
│   ├── PULL_REQUEST_TEMPLATE.md
│   └── ISSUE_TEMPLATE/
│       ├── translation_issue.md
│       └── terminology_issue.md
│
├── .githooks/
│   └── pre-push                     — Локальные проверки перед push
│
└── .gitignore
```

---

## ════════════════════════════════════════════════════════
## 2. NAMING CONVENTIONS
## ════════════════════════════════════════════════════════

### 2.1 Ветки (Branches)

```
Формат: {тип}/{том}-{язык}-{описание}

Типы:
  translate/   — новый перевод
  edit/        — редакторская правка
  verify/      — верификация
  fix/         — исправление ошибки
  glossary/    — обновление глоссария
  audio/       — аудио-разметка
  docs/        — документация
  infra/       — скрипты, CI/CD
```

**Примеры:**
```
translate/vol01-ru-chapters-1-5
translate/vol01-uz-chapters-1-5
edit/vol01-ru-chapters-1-5
verify/vol01-ru-islamic-content
fix/vol01-ru-T001-transliteration
glossary/update-names-vol01
audio/vol01-ru-ssml-chapters-1-3
docs/update-qa-standards
infra/add-hadith-verification-script
```

### 2.2 Файлы

```
Переводы:     chapter_NNN.md           (NNN = 001, 002, ...)
Логи:         TRANSLATION_LOG.md       (один на том/язык)
Отчёты:       volNN_{lang}_report.json
Глоссарий:    terms_{lang}.json
Аудио:        chapter_NNN_ssml.xml
```

### 2.3 Коммиты (Conventional Commits — из hadith-verifier)

```
Формат: {тип}({область}): {описание}

Типы:
  translate  — новый перевод
  edit       — редакторская правка
  verify     — верификация
  fix        — исправление ошибки (+ ID паттерна из FIX_PATTERNS.md)
  glossary   — обновление терминологии
  audio      — аудио-разметка
  docs       — документация
  ci         — CI/CD изменения
  scripts    — скрипты автоматизации
```

**Примеры:**
```
translate(vol01-ru): главы 1-3
edit(vol01-ru): стилистическая правка глав 1-3
verify(vol01-ru): верификация хадисов глав 1-3
fix(vol01-ru): исправить транслитерацию (T001)
glossary(ru): добавить термины тома 1
audio(vol01-ru): SSML разметка глав 1-3
docs: обновить QA_STANDARDS.md
ci: добавить проверку хадисов в pipeline
```

### 2.4 Теги и релизы

```
Формат: v{том}.{язык}.{ревизия}

Примеры:
  v01.ru.1    — Том 1, русский, первый релиз
  v01.ru.2    — Том 1, русский, исправления
  v01.uz.1    — Том 1, узбекский, первый релиз
```

---

## ════════════════════════════════════════════════════════
## 3. ПРОЦЕСС РАБОТЫ НАД ТОМОМ
## ════════════════════════════════════════════════════════

### 3.1 Полный цикл одного тома (одного языка)

```
main ──────────────────────────────────────────────────────── main
  │                                                              ▲
  ├── translate/vol01-ru-ch1-5 ──→ PR #1 ──→ review ──→ merge ──┤
  │                                                              │
  ├── translate/vol01-ru-ch6-10 ─→ PR #2 ──→ review ──→ merge ──┤
  │                                                              │
  ├── edit/vol01-ru-full ────────→ PR #3 ──→ review ──→ merge ──┤
  │                                                              │
  ├── verify/vol01-ru-islamic ───→ PR #4 ──→ review ──→ merge ──┤
  │                                                              │
  ├── audio/vol01-ru-ssml ──────→ PR #5 ──→ review ──→ merge ──┤
  │                                                              │
  └── release v01.ru.1 ←────────────────────────────────────────┘
```

### 3.2 Коммиты после каждой главы

**Правило (из hadith-verifier CI GREEN GATE):** Коммит после КАЖДОЙ завершённой главы, не накапливать.

```bash
# После перевода каждой главы:
git add translation/ru/vol01/chapter_003.md
git commit -m "translate(vol01-ru): глава 3 — Сотворение небес"

# После обновления глоссария:
git add glossary/terms_ru.json
git commit -m "glossary(ru): новые термины из главы 3"

# После обновления TRANSLATION_LOG:
git add translation/ru/vol01/TRANSLATION_LOG.md
git commit -m "docs(vol01-ru): обновить лог перевода — глава 3 завершена"
```

**НИКОГДА не делать:**
```bash
# ❌ ЗАПРЕЩЕНО — из hadith-verifier AGENTS.md:
git add .                               # Никогда git add .
git commit -m "update"                  # Неинформативное сообщение
git commit -m "translate: главы 1-10"   # Слишком много за раз
```

### 3.3 Размер коммитов

| Максимально допустимый | Рекомендуемый |
|------------------------|---------------|
| 5 глав за коммит | 1-3 главы за коммит |
| 1 файл глоссария + 1 перевод | Отдельные коммиты для перевода и глоссария |

---

## ════════════════════════════════════════════════════════
## 4. CI/CD PIPELINE
## ════════════════════════════════════════════════════════

### 4.1 Автоматические проверки при push (qa-checks.yml)

```yaml
name: QA Checks

on:
  push:
    branches: [main]
    paths:
      - 'translation/**'
      - 'glossary/**'
  pull_request:
    branches: [main]

jobs:
  quality-checks:
    name: Quality Checks
    runs-on: ubuntu-latest
    timeout-minutes: 10

    steps:
      - uses: actions/checkout@v4

      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.12'

      - name: Install dependencies
        run: pip install -r scripts/requirements.txt

      # ── FAST checks (всегда запускаются)
      - name: Check encoding (UTF-8, no BOM)
        run: python scripts/check_encoding.py

      - name: Check completeness
        run: python scripts/check_completeness.py
        if: contains(github.event.head_commit.message, 'translate')

      - name: Check glossary consistency
        run: python scripts/check_glossary.py

      - name: Check language purity (no drift)
        run: python scripts/check_language.py

      # ── MEDIUM checks (только для translation/ изменений)
      - name: Verify Quran references
        run: python scripts/verify_quran.py
        if: contains(github.event.head_commit.message, 'translate') || contains(github.event.head_commit.message, 'verify')

      - name: Verify Hadith references
        run: python scripts/verify_hadith.py
        if: contains(github.event.head_commit.message, 'translate') || contains(github.event.head_commit.message, 'verify')
```

### 4.2 Верификация исламского контента (verify-islamic.yml)

```yaml
name: Islamic Content Verification

on:
  workflow_dispatch:
    inputs:
      volume:
        description: 'Volume number (e.g., 01)'
        required: true
      language:
        description: 'Language (ru, uz, tj)'
        required: true

jobs:
  verify:
    runs-on: ubuntu-latest
    timeout-minutes: 30
    steps:
      - uses: actions/checkout@v4
      - name: Deep verification
        run: |
          python scripts/verify_quran.py --volume=${{ inputs.volume }} --lang=${{ inputs.language }} --deep
          python scripts/verify_hadith.py --volume=${{ inputs.volume }} --lang=${{ inputs.language }} --deep
```

### 4.3 Публикация релиза (release.yml)

```yaml
name: Release Volume

on:
  push:
    tags:
      - 'v*'

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run full QA suite
        run: |
          python scripts/check_encoding.py
          python scripts/check_completeness.py --strict
          python scripts/check_glossary.py --strict
          python scripts/check_language.py --strict

      - name: Create Release
        uses: softprops/action-gh-release@v1
        with:
          files: |
            translation/${{ env.LANG }}/vol${{ env.VOL }}/*.md
            verification/reports/vol${{ env.VOL }}_${{ env.LANG }}_report.json
          generate_release_notes: true
```

### 4.4 Порядок проверок в CI (адаптация из hadith-verifier)

```
1. encoding_check      — UTF-8, без BOM              [< 5 сек]
2. language_check      — нет дрифта                   [< 5 сек]
3. glossary_check      — терминологическое соответствие [< 10 сек]
4. completeness_check  — все абзацы переведены         [< 10 сек]
5. quran_verify        — аяты сверены                  [< 30 сек]
6. hadith_verify       — хадисы верифицированы         [< 30 сек]
```

---

## ════════════════════════════════════════════════════════
## 5. ПРАВИЛА ПУЛ-РЕКВЕСТОВ И РЕВЬЮ
## ════════════════════════════════════════════════════════

### 5.1 Шаблон пул-реквеста

```markdown
## Описание
[Что сделано, какой том, какие главы, какой язык]

## Тип изменения
- [ ] Новый перевод (translate)
- [ ] Редакторская правка (edit)
- [ ] Верификация (verify)
- [ ] Исправление ошибки (fix) — Паттерн: [ID]
- [ ] Глоссарий (glossary)
- [ ] Аудио-разметка (audio)
- [ ] Документация (docs)
- [ ] Инфраструктура (infra)

## Чек-лист (заполняет автор)
- [ ] FIX_PATTERNS.md проверен перед началом работы
- [ ] Глоссарий актуален для данных глав
- [ ] Автоматические проверки прошли локально
- [ ] TRANSLATION_LOG.md обновлён
- [ ] Нет ошибок уровня CRITICAL или HIGH

## Чек-лист ревью (заполняет ревьюер)
- [ ] Прочитал перевод полностью
- [ ] Аяты Корана сверены
- [ ] Хадисы верифицированы
- [ ] Терминология соответствует глоссарию
- [ ] Стиль консистентен с предыдущими главами
```

### 5.2 Кто ревьюит что

| Тип PR | Обязательный ревьюер | Дополнительно |
|--------|---------------------|---------------|
| translate | Агент-редактор → Верификатор | Терминолог (если новые термины) |
| edit | QA Агент | — |
| verify | Координатор | — |
| fix | QA Агент | — |
| glossary | Терминолог (все 3 языка) | — |
| docs | Координатор | — |

### 5.3 Правила (из hadith-verifier CI GREEN GATE)

1. **НИКОГДА не мержить PR с красным CI** — исправить и дождаться зелёного
2. **НИКОГДА не мержить без ревью** — минимум 1 одобрение
3. **НИКОГДА не мержить с CRITICAL ошибками** — даже если CI зелёный
4. **Squash merge** для translate/ и edit/ (чистая история)
5. **Merge commit** для verify/ и fix/ (сохранить историю исправлений)

---

## ════════════════════════════════════════════════════════
## 6. ОРГАНИЗАЦИЯ РЕЛИЗОВ
## ════════════════════════════════════════════════════════

### 6.1 Критерии готовности к релизу

| Критерий | Обязательность |
|----------|---------------|
| Все главы тома переведены | MUST |
| Все CRITICAL и HIGH ошибки исправлены | MUST |
| CI зелёный | MUST |
| Верификатор одобрил | MUST |
| QA отчёт сгенерирован | MUST |
| Глоссарий финализирован | MUST |
| TRANSLATION_LOG завершён | SHOULD |
| Аудио-разметка готова | OPTIONAL |
| A/B тест промпта проведён (для первого тома) | SHOULD |

### 6.2 Процесс релиза

```bash
# 1. Убедиться, что main чистый
git checkout main
git pull origin main

# 2. Создать тег
git tag -a v01.ru.1 -m "Том 1, Русский, Релиз 1"
git push origin v01.ru.1

# 3. GitHub Actions автоматически:
#    - Запускает полный QA suite
#    - Создаёт GitHub Release
#    - Прикрепляет файлы перевода и отчёт
```

### 6.3 Версионирование

```
v{ТОМ}.{ЯЗЫК}.{РЕВИЗИЯ}

v01.ru.1  — Первый релиз Том 1 на русском
v01.ru.2  — Исправления после обратной связи
v01.uz.1  — Первый релиз Том 1 на узбекском
v02.ru.1  — Первый релиз Том 2 на русском
```

### 6.4 Хронология релизов (план)

| Квартал | Ожидаемые релизы |
|---------|-----------------|
| Q3 2026 | v01.ru.1, v01.uz.1, v01.tj.1 |
| Q4 2026 | v02.ru.1, v02.uz.1, v01.ru.2 (патч) |
| Q1 2027 | v03.ru.1, v02.uz.1, v03.uz.1 |
| ... | Далее 1-2 тома в квартал |

---

## ════════════════════════════════════════════════════════
## 7. PRE-PUSH PROTOCOL (из hadith-verifier)
## ════════════════════════════════════════════════════════

### Обязательные проверки перед push

```bash
# Для файлов перевода:
python scripts/check_encoding.py translation/ru/vol01/chapter_003.md
python scripts/check_language.py translation/ru/vol01/chapter_003.md
python scripts/check_glossary.py translation/ru/vol01/chapter_003.md

# Для глоссария:
python scripts/validate_glossary.py glossary/terms_ru.json

# Только после прохождения всех проверок:
git push origin translate/vol01-ru-ch3
```

### Git hook (pre-push)

```bash
#!/bin/bash
# .githooks/pre-push
# Адаптировано из hadith-verifier/.githooks/pre-push

echo "🔍 Pre-push checks..."

# Найти изменённые файлы перевода
CHANGED=$(git diff --name-only origin/main HEAD -- 'translation/')

if [ -n "$CHANGED" ]; then
    echo "Проверяю: $CHANGED"
    python scripts/check_encoding.py $CHANGED || exit 1
    python scripts/check_language.py $CHANGED || exit 1
    echo "✅ Все проверки пройдены"
else
    echo "ℹ️ Нет изменений в translation/ — пропускаю проверки"
fi
```

---

## ════════════════════════════════════════════════════════
## 8. .gitignore
## ════════════════════════════════════════════════════════

```gitignore
# Секреты и ключи
.env
.env.local
*.key

# OS
.DS_Store
Thumbs.db

# IDE
.vscode/
.idea/
*.swp

# Временные файлы
*.tmp
*.bak
*~

# Большие бинарные файлы (аудио хранятся отдельно)
*.mp3
*.wav
*.ogg

# Python
__pycache__/
*.pyc
.pytest_cache/
venv/

# Логи
*.log
```

---

*Адаптировано из github.com/Farhod75/hadith-verifier: CI/CD pipeline, AGENTS.md, Git workflow*  
*Последнее обновление: Май 2026*
