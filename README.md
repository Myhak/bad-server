# Проектная работа "WebLarek. Плохой сервер.", спринт 17

**Автор:** Максим Никоненко, 43 когорта, фулстек разработчик

**Репозиторий:** https://github.com/Myhak/bad-server

## Подготовка к работе
1. Склонировать репозиторий
2. Запустить docker
```bash
docker compose up -d
```
3. Наполнить базу данных
[README.md](.dump%2FREADME.md)
4. Перейти по адресу http://localhost/ и на странице должны быть продукты.
5. На странице http://localhost/login/ можно авторизоваться.
6. Админка находится по адресу http://localhost/admin/

## Исправленные уязвимости

| Уязвимость | Файл(ы) | Описание исправления |
|-----------|---------|---------------------|
| **XSS** | `src/app.ts`, `src/controllers/products.ts` | Добавлен `helmet` для безопасных HTTP-заголовков; санитизация HTML в полях продуктов через `xss` |
| **CSRF** | `src/app.ts` | Добавлена защита `csrf-csrf` (double-submit cookie) для cookie-based эндпоинтов `/auth/token`, `/auth/logout`, `/auth/me` |
| **NoSQL injection** | `src/controllers/order.ts`, `src/controllers/customers.ts` | Пользовательский ввод экранируется через `escapeRegExp` перед использованием в `RegExp` |
| **ReDoS** | `src/middlewares/validations.ts`, `src/controllers/order.ts`, `src/controllers/customers.ts` | Заменён небезопасный regex телефона; экранирование пользовательского ввода в RegExp |
| **Buffer Overflow** | `src/app.ts`, `src/middlewares/file.ts` | Лимит тела запроса 10KB; лимит размера файла 5MB в multer |
| **DDoS** | `src/app.ts` | Добавлен `express-rate-limit`: 100 req/15min общий, 10 req/min для auth |
| **Path Traversal** | `src/middlewares/serverStatic.ts` | Проверка что путь файла остаётся внутри `baseDir` через `path.resolve` |
| **MD5 → bcrypt** | `src/models/user.ts` | Хеширование паролей заменено с MD5 на bcrypt (salt rounds: 10) |
| **Admin guard** | `src/routes/customers.ts` | Добавлен `roleGuardMiddleware(Role.Admin)` на все customer-маршруты |
| **Mass Assignment** | `src/controllers/auth.ts`, `src/controllers/customers.ts` | Whitelist разрешённых полей при обновлении пользователя |
| **CORS** | `src/app.ts` | Ограничение origin через `ORIGIN_ALLOW` из env (было открыто для всех) |
| **npm audit** | `package.json` | 0 уязвимостей; удалён пакет `md5`, обновлены зависимости |

