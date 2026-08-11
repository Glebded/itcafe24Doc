# Setup New Documentation Project

## Introduction.

Самый удобный, красивый и современный способ сделать документацию для аддона Blender — использовать MkDocs с темой Material и бесплатным хостингом GitHub Pages. Это даст вам чистый сайт с поиском, тёмной темой и удобной навигацией из простых Markdown-файлов.

Ниже пошаговое руководство по настройке автоматической публикации документации.

## Шаг 1. Создайте структуру файлов в репозитории:

Подготовка папки docs и конфигурации.

В корне вашего репозитория аддона создайте папку **docs** и файл **mkdocs.yml**.

Ваша структура проекта должна выглядеть так:

    it_kafe_docs/
    ├── docs/
    │   └── index.md
    └── mkdocs.yml

## Шаг 2. Заполните файл конфигурации mkdocs.yml

Настройка названия, темы и меню

Откройте файл mkdocs.yml и добавьте базовые настройки своего проекта:

```yaml
site_name: Название вашего софта
site_description: Документация и руководство пользователя
site_url: https://username.github.io/repository-name/

theme:
name: material
palette:
    scheme: slate # Тёмная тема по умолчанию (как в Blender)
    primary: indigo
features:
    - navigation.tabs
    - search.suggest

nav:
- Главная: index.md
- Установка: installation.md
- Быстрый старт: quickstart.md
```

## Шаг 3. Напишите материалы документации:

Заполнение страниц в формате Markdown.

- В папке docs/ создайте файлы, указанные в меню (index.md, installation.md и т. д.), и напишите текст.
- В docs/index.md напишите краткое описание аддона и его ключевые возможности.
- Для скриншотов создайте папку docs/assets/ и ссылайтесь на них через стандартный Markdown:

``` text
![Скриншот](assets/screenshot.png).
```

## Шаг 4. Настройте автоматическую сборку через GitHub Actions:

Автодеплой при каждом коммите в ветку main.

- В корне репозитория создайте файл .github/workflows/deploy.yml.
- Добавьте в него готовый скрипт, который будет автоматически собирать и публиковать сайт:

```yaml
name: Publish Documentation

on:
push:
    branches:
    - main

permissions:
contents: write

jobs:
deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4

    - name: Configure Python
        uses: actions/setup-python@v5
        with:
        python-version: 3.x

    - run: echo "cache_id=$(date --utc +'%Y%V')" >> $GITHUB_ENV

    - uses: actions/cache@v4
        with:
        key: mkdocs-material-${{ env.cache_id }}
        path: ~/.cache/pip
        restore-keys: |
            mkdocs-material-

    - run: pip install mkdocs-material

    - run: mkdocs gh-deploy --force
```

## Шаг 5. Включите GitHub Pages в настройках репозитория:

Финальная активация хостинга.

- Отправьте изменения в GitHub (git push).
- Перейдите в ваш репозиторий на GitHub: Settings > Pages.
- В поле Source выберите Deploy from a branch.
- В поле Branch выберите ветку gh-pages (она автоматически создастся после выполнения GitHub Action из предыдущего шага) и папку / (root).
- Нажмите Save.
  
Через 1–2 минуты ваш сайт будет доступен по адресу:

    https://<ваш-логин>.github.io/<имя-репозитория>/.