# Тестирование JSONPlaceholder API

Проект для ручного тестирования REST API (JSONPlaceholder).

## 📌 Цель
- Показать навыки тестирования API.
- Работу с Postman.
- Документирование тест-кейсов и багов.

## 🛠 Эндпоинты
- `GET /posts` – список постов.
- `POST /posts` – создание поста.

## 🚀 Как начать
1. Импортируй [Postman-коллекцию](postman/JSONPlaceholder_Collection.json).
2. Проверь [тест-кейсы](docs/test_cases.md).
3. Смотри [найденные баги](docs/bug_reports.md).

## 📸 Скриншоты
![GET /posts/1](screenshots/get_post.png)

## 🔧 Автотесты (опционально)
Запусти тесты:
```bash
pytest tests/test_api.py
