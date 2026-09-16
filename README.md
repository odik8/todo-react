# Todo React

Небольшое todo-приложение на React 19 + Vite. Без роутера и стейт-менеджера: собственный мини-роутер, `useReducer` + Context для состояния, переключаемый источник данных (localStorage или json-server).

Демо: https://odik8.github.io/todo-react/

## Возможности

- Добавление, удаление и отметка задач выполненными
- Удаление всех задач разом
- Поиск по задачам с подсветкой совпадений
- Счётчик «выполнено из всех»
- Анимации появления/исчезновения задач
- Страница отдельной задачи по адресу `/tasks/:id`

## Стек

React 19, Vite 7, SCSS-модули, ESLint. Данные — localStorage либо json-server (`db.json5`).

## Запуск

```bash
npm install
npm run dev
```

По умолчанию в dev-режиме используется json-server, поэтому в соседнем терминале нужно поднять API:

```bash
npm run server   # http://localhost:3001/tasks
```

Чтобы работать без бэкенда (данные в localStorage), создайте `.env.local`:

```
VITE_STATIC_BACKEND=true
```

В продакшен-сборке этот флаг уже включён через `.env.production`.

## Скрипты

| Команда | Что делает |
| --- | --- |
| `npm run dev` | dev-сервер Vite |
| `npm run server` | json-server на порту 3001 |
| `npm run build` | сборка в `dist` |
| `npm run preview` | предпросмотр сборки |
| `npm run lint` | ESLint |
| `npm run deploy` | сборка + публикация на GitHub Pages |

## Структура

Организация по слоям в духе Feature-Sliced Design:

```
src/
  app/         точка входа, стили, роутинг
  pages/       TasksPage, TaskPage
  widgets/     Todo — сборка страницы задач
  features/    add-task, search-task, stats
  entities/    todo — модель задач (Context, useTasks)
  shared/      api, ui-кит (Button, Field, RouterLink), хуки, утилиты, ассеты
```

Алиас `@` указывает на `src`.

### Слой данных

`src/shared/api/tasks/index.js` выбирает реализацию по флагу `VITE_STATIC_BACKEND`:

- `local.js` — localStorage с искусственной задержкой, имитирует сеть;
- `server.js` — REST-запросы к json-server.

Обе реализации дают одинаковый интерфейс: `getAll`, `getById`, `add`, `delete`, `deleteAll`, `toggleComplete`.

### Роутинг

`src/app/routing/Router.jsx` — самописный роутер на `history API` и `popstate`, поддерживает параметры вида `/tasks/:id`. Для GitHub Pages `predeploy` копирует `index.html` в `404.html`, чтобы прямые ссылки на вложенные маршруты работали.
