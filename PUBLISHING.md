# PUBLISHING.md — как залить grug-marketplace на GitHub

Инструкция для тебя (человека) и для Claude Code, если будешь просить его
запушить это за тебя.

---

## ⚠️ Обязательное правило для Claude Code

**Ни при каких условиях не указывать Claude / Anthropic / Claude Code как
контрибьютора, соавтора или автора коммита.**

Конкретно это значит:

- Никогда не добавлять в сообщение коммита строки вида
  `Co-Authored-By: Claude <noreply@anthropic.com>`.
- Никогда не добавлять `Generated with Claude Code` или похожие подписи в
  коммит-месседж, PR-описание, README, `plugin.json` или `marketplace.json`.
- Поля `author` и `owner` в `plugin.json` / `marketplace.json` уже
  прописаны на **Lemxy** — не менять их на Claude/Anthropic и не добавлять
  туда второго автора.
- Автор коммита в git (`user.name` / `user.email`) — только данные Lemxy,
  никаких дефолтных/сгенерированных значений Claude.
- Если Claude Code сам предлагает такую подпись по умолчанию — явно её
  убрать перед коммитом.

Если что-то из этого уже стоит по умолчанию (например, глобальный git hook
или шаблон коммита добавляет подпись) — предупредить об этом и не
коммитить, пока не убрано.

---

## Что нужно перед пушем

1. Аккаунт на GitHub: [github.com/Lemxy](https://github.com/Lemxy) — уже есть.
2. Git установлен локально (`git --version`).
3. Настроен `user.name` / `user.email` на твои данные (не Claude):
   ```bash
   git config --global user.name "Lemxy"
   git config --global user.email "твоя-почта@example.com"
   ```
4. Способ аутентификации в GitHub — один из:
   - GitHub CLI: `gh auth login`
   - Personal Access Token при пуше по HTTPS
   - SSH-ключ, добавленный в аккаунт

## Шаги публикации

### 1. Создать пустой репозиторий на GitHub

Вручную на github.com/Lemxy → **New repository**:
- Name: `grug-marketplace`
- Public (обязательно — `/plugin marketplace add` тянет публичные репо)
- Без README/gitignore/license от GitHub (они уже есть в проекте)

Либо через `gh`:
```bash
gh repo create Lemxy/grug-marketplace --public --source=. --remote=origin
```

### 2. Инициализировать git в папке проекта

```bash
cd grug-marketplace
git init
git add .
git commit -m "Add grug plugin marketplace"
```

Коммит-месседж — только текст изменений. Без подписей от Claude (см. правило
выше).

### 3. Привязать remote и запушить

Если репозиторий создавался вручную на сайте:
```bash
git remote add origin https://github.com/Lemxy/grug-marketplace.git
git branch -M main
git push -u origin main
```

Если создавался через `gh repo create --source=. --remote=origin` — remote
уже привязан, просто:
```bash
git push -u origin main
```

### 4. Проверить

Открыть `https://github.com/Lemxy/grug-marketplace` — файлы должны быть на
месте: `.claude-plugin/marketplace.json`, `plugins/grug/...`.

### 5. Установить плагин из своего маркетплейса

В Claude Code (две отдельные команды):
```
/plugin marketplace add Lemxy/grug-marketplace
/plugin install grug@grug-marketplace
```

Перезапустить Claude Code — суб-агенты подхватятся при старте новой сессии.

## Обновление после правок

```bash
git add .
git commit -m "описание изменений"
git push
```

Потом в Claude Code:
```
/plugin marketplace update grug-marketplace
```

## Если просишь Claude Code сделать пуш за тебя

Дай ему ровно эту инструкцию (или ссылку на этот файл) и явно скажи:
"закоммить и запушь, автор коммита — Lemxy, никаких Co-Authored-By и никаких
упоминаний Claude/Anthropic в коммитах, PR или файлах проекта."
