# Тест-кейсы для JSONPlaceholder API  
**Base URL**: `https://jsonplaceholder.typicode.com`  

---

## Позитивные сценарии  

| Метод   | URL                  | Параметры/Тело                          | Ожидаемый результат                          | Проверяемые поля                          |
|---------|----------------------|-----------------------------------------|---------------------------------------------|-------------------------------------------|
| GET     | `/posts`             | -                                       | 200 OK, 100 постов                          | `userId`, `id`, `title`, `body`           |
| GET     | `/posts/1`           | -                                       | 200 OK, пост с `id=1`                       | `userId=1`, `title` не пустой             |
| GET     | `/comments`          | `postId=1`                              | 200 OK, комментарии с `postId=1`            | `postId=1`, валидный `email`              |
| GET     | `/comments`          | -                                       | 200 OK, 500 комментариев                    | `postId`, `name`, `body`                  |
| GET     | `/photos`            | -                                       | 200 OK, 5000 фото                           | `url`, `thumbnailUrl` не пусты            |
| GET     | `/users`             | -                                       | 200 OK, 10 пользователей                    | `name`, `email`, `address.city`           |
| GET     | `/todos`             | -                                       | 200 OK, 200 задач                           | `title`, `completed` (boolean)            |
| GET     | `/todos`             | `userId=1`                              | 200 OK, задачи пользователя 1               | Все задачи имеют `userId=1`               |
| GET     | `/todos`             | `completed=true`                        | 200 OK, завершенные задачи                  | Все задачи имеют `completed=true`         |
| GET     | `/albums`            | -                                       | 200 OK, 100 альбомов                        | `userId`, `id`, `title`                   |
| GET     | `/albums/1`          | -                                       | 200 OK, альбом с `id=1`                     | `userId=1`, `title` не пустой             |
| GET     | `/albums/1/photos`   | -                                       | 200 OK, фото альбома 1                      | Все фото имеют `albumId=1`                |
| GET     | `/posts/1/comments`  | -                                       | 200 OK, комментарии к посту 1               | Все комментарии имеют `postId=1`          |
| PUT     | `/posts/1`           | `{"title": "Updated", "body": "New"}`   | 200 OK                                     | Ответ содержит обновленные поля           |
| PATCH   | `/posts/1`           | `{"title": "Patched Title"}`            | 200 OK                                     | Только `title` изменен                    |
| DELETE  | `/posts/1`           | -                                       | 200 OK                                     | Пустой ответ (или JSON с `{}`)            |
| POST    | `/posts`             | `{"title": "New Post", "body": "Content", "userId": 1}` | 201 Created | `id: 101`, поля соответствуют запросу     |
| POST    | `/comments`          | `{"postId": 1, "name": "Test", "email": "test@test.com", "body": "Test comment"}` | 201 Created | `id: 501`, все поля присутствуют          |
| POST    | `/todos`             | `{"title": "New Todo", "completed": false, "userId": 1}` | 201 Created | `id: 201`, `completed` имеет тип boolean  |
| POST    | `/users`             | `{"name": "John", "email": "john@test.com", "address": {"city": "Moscow"}}` | 201 Created | `id: 11`, поле `address` присутствует     |

---

## Негативные сценарии  

| Метод   | URL                  | Параметры/Тело                          | Ожидаемый результат       | Фактический результат (баг)               |
|---------|----------------------|-----------------------------------------|--------------------------|-------------------------------------------|
| GET     | `/posts/0`           | -                                       | 404 Not Found            | 200 OK (пост с `id=0` не существует)      |
| GET     | `/todos?userId=abc`  | `userId=abc`                            | 400 Bad Request          | 200 OK (некорректный параметр)            |
| POST    | `/posts`             | `{}`                                    | 400 Bad Request          | 201 Created (баг: пустое тело принято)    |
| POST    | `/todos`             | `{"completed": "yes"}`                  | 400 Bad Request          | 201 Created (баг: неверный тип)           |
| POST    | `/users`             | `{"email": "invalid"}`                  | 400 Bad Request          | 201 Created (баг: некорректный email)     |
| POST    | `/invalid-endpoint`  | `{"data": "test"}`                      | 404 Not Found            | 404 Not Found                             |
| PUT     | `/posts/0`           | `{"title": "Test"}`                     | 404 Not Found            | 200 OK (несуществующий ресурс)            |
| PATCH   | `/albums/999`        | `{"title": "Test"}`                     | 404 Not Found            | 200 OK (несуществующий альбом)            |
| DELETE  | `/todos/201`         | -                                       | 404 Not Found            | 200 OK (несуществующая задача)            |
| GET     | `/users/11`          | -                                       | 404 Not Found            | 200 OK (несуществующий пользователь)      |
| PUT     | `/posts/1`           | `{}` (пустое тело)                      | 400 Bad Request          | 200 OK (баг: пустое тело принято)         |
| GET     | `/albums?userId=abc` | `userId=abc`                            | 400 Bad Request          | 200 OK (некорректный параметр)            |

---

## Специфические проверки для связей ресурсов  

| Метод   | URL                  | Проверка                                | Ожидаемый результат       |
|---------|----------------------|-----------------------------------------|--------------------------|
| GET     | `/users/1/albums`    | Альбомы пользователя 1                 | Все альбомы с `userId=1` |
| GET     | `/users/1/todos`     | Задачи пользователя 1                  | Все задачи с `userId=1`  |
| GET     | `/users/1/posts`     | Посты пользователя 1                   | Все посты с `userId=1`   |
| GET     | `/posts/1/comments`  | Альтернатива `/comments?postId=1`      | Те же данные             |

---

## Особенности JSONPlaceholder  
1. **Изменения не сохраняются**:  
   - PUT/PATCH/DELETE-запросы возвращают "успех", но данные остаются исходными.  
   - Пример: После `PUT /posts/1` данные поста не меняются (можно проверить повторным GET).  

2. **Связи ресурсов**:  
   ```bash
   # Пользователь → Посты
   GET /users/1/posts
   # Пользователь → Альбомы
   GET /users/1/albums
   # Альбом → Фото
   GET /albums/1/photos

