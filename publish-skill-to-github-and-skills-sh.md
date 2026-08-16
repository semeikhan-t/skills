# Публикация авторского Agent Skill на GitHub и skills.sh

Инструкция для ИИ-агента. Выполняй шаги последовательно. Не пропускай проверки.

---

## Цель

1. Подготовить skill по спецификации agentskills.io.
2. Выложить его в публичный GitHub-репозиторий.
3. Сделать skill устанавливаемым через `npx skills` / `gh skill`.
4. Обеспечить появление skill на [skills.sh](https://skills.sh) (каталог индексирует GitHub-репозитории по телеметрии установок).

---

## Предварительные требования

Агент должен иметь доступ к:

- локальной папке skill (с `SKILL.md`)
- Git
- GitHub CLI (`gh`) — желательно версии ≥ 2.90
- аутентификации GitHub (`gh auth status` должен быть успешным)

Проверь окружение:

```bash
gh --version
gh auth status
git --version
```

Если `gh` не авторизован — попроси пользователя выполнить `gh auth login`.

---

## Шаг 0. Определи параметры

Перед началом зафиксируй:

| Параметр | Пример | Описание |
|----------|--------|----------|
| `SKILL_DIR` | `./my-skill` | Путь к локальной папке skill |
| `SKILL_NAME` | `my-skill` | Имя skill (kebab-case, = имя папки и `name` в frontmatter) |
| `GITHUB_USER` | `username` | GitHub username или org |
| `REPO_NAME` | `my-skill` или `agent-skills` | Имя репозитория |
| `PUBLISH_MODE` | `standalone` или `collection` | Один skill в корне репо **или** коллекция в `skills/` |

**Рекомендация:** для одного skill используй `standalone` (SKILL.md в корне репо). Для нескольких — `collection`.

---

## Шаг 1. Валидация skill

### 1.1. Структура

Ожидаемая структура:

```text
SKILL_DIR/
├── SKILL.md              # обязательно
├── scripts/              # опционально
├── references/           # опционально
└── assets/               # опционально
```

Проверь:

```bash
test -f "$SKILL_DIR/SKILL.md" || { echo "ERROR: SKILL.md не найден"; exit 1; }
```

### 1.2. Frontmatter SKILL.md

Обязательные поля:

```yaml
---
name: my-skill
description: Что делает skill и когда его использовать. Включи триггер-слова.
---
```

Рекомендуемые:

```yaml
---
name: my-skill
description: Что делает skill и когда его использовать. Включи триггер-слова.
license: MIT
metadata:
  author: your-github-username
  version: "1.0.0"
---
```

**Строгие правила для `description` (validator-enforced):**

- Одна строка (без multi-line).
- Без кавычек `"..."` и `'...'`.
- Без последовательности `: ` (двоеточие + пробел). Перефразируй: используй `—` или `including`.
- Без символов `<` и `>`.
- Максимум 1024 символа.
- `name` = имя родительской папки, kebab-case, 2–64 символа, только `a-z`, `0-9`, `-`.

### 1.3. Запуск валидации (если доступен skill-creator)

```bash
# путь к validate-skill.sh зависит от окружения
bash /root/.grok/skills/skill-creator/scripts/validate-skill.sh "$SKILL_DIR"
```

Если скрипта нет — вручную проверь frontmatter и что `name` совпадает с именем папки.

**Не продолжай, пока валидация не пройдена.**

---

## Шаг 2. Выбор режима публикации

### Режим A — Standalone (один skill = один репозиторий)

```text
REPO_ROOT/
├── SKILL.md
├── scripts/          # если есть
├── references/       # если есть
├── assets/           # если есть
├── README.md
└── LICENSE
```

Установка пользователями:

```bash
npx skills add GITHUB_USER/REPO_NAME
# или
gh skill install GITHUB_USER/REPO_NAME
```

### Режим B — Collection (несколько skills в одном репо)

```text
REPO_ROOT/
├── skills/
│   ├── my-skill/
│   │   └── SKILL.md
│   └── another-skill/
│       └── SKILL.md
├── README.md
├── LICENSE
└── skills.sh.json    # опционально
```

Установка:

```bash
npx skills add GITHUB_USER/REPO_NAME --skill my-skill
# или все skills из репо
npx skills add GITHUB_USER/REPO_NAME
```

---

## Шаг 3. Подготовка файлов репозитория

### 3.1. README.md (минимум)

Создай `README.md` в корне репо:

```markdown
# SKILL_NAME

Краткое описание skill.

## Install

\`\`\`bash
npx skills add GITHUB_USER/REPO_NAME
\`\`\`

или

\`\`\`bash
gh skill install GITHUB_USER/REPO_NAME
\`\`\`

## Usage

Когда использовать: ...
```

### 3.2. LICENSE

Рекомендуется MIT или Apache-2.0. Если лицензии нет — создай MIT:

```text
MIT License

Copyright (c) YEAR GITHUB_USER

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

### 3.3. skills.sh.json (только для collection, опционально)

В корне репо:

```json
{
  "$schema": "https://skills.sh/schemas/skills.sh.schema.json",
  "notGrouped": "bottom",
  "groupings": [
    {
      "title": "My Skills",
      "description": "Авторские agent skills",
      "skills": ["my-skill"]
    }
  ]
}
```

В `skills` указывай slug skill (как в URL skills.sh или имя из `name:` frontmatter).

---

## Шаг 4. Создание / обновление GitHub-репозитория

### 4.1. Проверь, существует ли репо

```bash
gh repo view GITHUB_USER/REPO_NAME 2>/dev/null && echo "EXISTS" || echo "NOT_EXISTS"
```

### 4.2. Если репо не существует — создай

```bash
gh repo create GITHUB_USER/REPO_NAME \
  --public \
  --description "Agent skill: краткое описание" \
  --clone=false
```

### 4.3. Инициализация git (если локально ещё нет репо)

```bash
cd /path/to/work
mkdir -p REPO_NAME
cd REPO_NAME

# Скопируй содержимое skill
# Standalone:
cp -r SKILL_DIR/* .
# или Collection:
mkdir -p skills/SKILL_NAME
cp -r SKILL_DIR/* skills/SKILL_NAME/

# README и LICENSE уже должны быть на месте

git init
git add .
git commit -m "Add skill: SKILL_NAME"
git branch -M main
git remote add origin https://github.com/GITHUB_USER/REPO_NAME.git
git push -u origin main
```

Если репо уже клонирован — просто добавь/обнови файлы, commit, push.

### 4.4. Добавь topic для discoverability

```bash
gh repo edit GITHUB_USER/REPO_NAME --add-topic agent-skills
# опционально:
gh repo edit GITHUB_USER/REPO_NAME --add-topic skills
```

---

## Шаг 5. Публикация через `gh skill publish` (рекомендуется)

Выполняй **из корня репозитория** (где видны skills):

```bash
cd /path/to/REPO_NAME
gh skill publish
```

Интерактивно предложит:

1. Добавить topic `agent-skills` (если ещё нет).
2. Выбрать версию тега (semver, например `v1.0.0`).
3. Включить immutable releases (рекомендуется — Yes).
4. Создать GitHub Release с notes.

Неинтерактивный вариант:

```bash
gh skill publish --tag v1.0.0
```

Только проверка без публикации:

```bash
gh skill publish --dry-run
```

**После успешного publish skill уже устанавливаем:**

```bash
npx skills add GITHUB_USER/REPO_NAME
# или
gh skill install GITHUB_USER/REPO_NAME
```

Для collection с конкретным skill:

```bash
npx skills add GITHUB_USER/REPO_NAME --skill SKILL_NAME
```

---

## Шаг 6. Появление на skills.sh

skills.sh **не требует ручной регистрации**. Каталог индексирует публичные GitHub-репозитории по телеметрии установок CLI `npx skills`.

### Что нужно сделать агенту / пользователю:

1. Убедиться, что репо **public**.
2. Установить skill хотя бы один раз:

```bash
npx skills add GITHUB_USER/REPO_NAME -y
```

3. По возможности попросить других установить (больше установок → выше в leaderboard).

Страница skill появится примерно по адресу:

- Standalone: `https://skills.sh/GITHUB_USER/REPO_NAME`
- Collection: `https://skills.sh/GITHUB_USER/REPO_NAME/SKILL_NAME`

Кэш skills.sh обновляется не мгновенно. После установок страница обычно появляется в течение некоторого времени.

---

## Шаг 7. Финальная проверка

Выполни и зафиксируй результаты:

```bash
# 1. Репо существует и public
gh repo view GITHUB_USER/REPO_NAME --json name,isPrivate,url

# 2. Topics
gh repo view GITHUB_USER/REPO_NAME --json repositoryTopics

# 3. CLI видит skill
npx skills add GITHUB_USER/REPO_NAME --list

# 4. Тестовая установка (в песочнице / временной директории при необходимости)
npx skills add GITHUB_USER/REPO_NAME -y
```

Ожидаемый результат:

- `isPrivate: false`
- topic `agent-skills` присутствует
- `--list` показывает skill
- установка проходит без ошибок

---

## Чеклист для агента (кратко)

- [ ] `SKILL.md` существует, frontmatter валиден, `name` = имя папки
- [ ] description без кавычек, без `: `, без `<>`
- [ ] Выбран режим: standalone или collection
- [ ] Создан/обновлён public GitHub-репозиторий
- [ ] Добавлены README.md и LICENSE
- [ ] Добавлен topic `agent-skills`
- [ ] Выполнен `git push`
- [ ] Выполнен `gh skill publish` (или хотя бы push + tag)
- [ ] Выполнена тестовая установка `npx skills add ...`
- [ ] Пользователю выданы ссылки:
  - GitHub: `https://github.com/GITHUB_USER/REPO_NAME`
  - Install: `npx skills add GITHUB_USER/REPO_NAME`
  - skills.sh (после индексации): `https://skills.sh/GITHUB_USER/REPO_NAME`

---

## Типичные ошибки и как их исправить

| Ошибка | Решение |
|--------|---------|
| `name` не совпадает с папкой | Переименуй папку или поле `name` |
| description с `: ` или кавычками | Перепиши description без них |
| Репозиторий private | `gh repo edit GITHUB_USER/REPO_NAME --visibility public` |
| CLI не видит skill | Проверь структуру: `SKILL.md` в корне (standalone) или в `skills/NAME/` (collection) |
| `gh skill publish` не найден | Обнови gh: `gh --version` ≥ 2.90; иначе достаточно push + topic |
| Skill не появляется на skills.sh | Сделай несколько установок через `npx skills add`, подожди обновления кэша |

---

## Дополнительные команды

```bash
# Обновить skill после изменений
git add . && git commit -m "Update skill" && git push
gh skill publish --tag v1.0.1   # новая версия

# Посмотреть установленные skills
npx skills list

# Удалить skill локально
npx skills remove SKILL_NAME
```

---

## Важно

- Не создавай лишние файлы вроде CHANGELOG.md внутри папки skill — skills предназначены для агентов, не для людей.
- Не дублируй знания, которые модель уже знает; skill должен содержать нетривиальные процедуры/доменный контекст.
- Всегда проверяй frontmatter перед push.
- Для supply-chain безопасности предпочтительны immutable releases (`gh skill publish` предлагает их включить).
