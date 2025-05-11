# Тест-кейсы для JSONPlaceholder API  
**Base URL**: `https://jsonplaceholder.typicode.com`  

## Позитивные сценарии  

| Метод | URL                | Параметры/Тело       | Ожидаемый результат                          | Проверяемые поля                     |
|-------|--------------------|----------------------|---------------------------------------------|---------------------------------------|
| GET   | `/posts`           | -                    | 200 OK, 100 постов                          | `userId`, `id`, `title`, `body`       |
| GET   | `/posts/1`         | -                    | 200 OK, пост с `id=1`                       | `userId=1`, `title` не пустой         |
| GET   | `/comments`        | `postId=1`           | 200 OK, комментарии с `postId=1`            | `postId=1`, `email` валиден           |
| GET   | `/comments`        | -                    | 200 OK, 500 комментариев                    | `postId`, `name`, `body`              |
| GET   | `/photos`          | -                    | 200 OK, 5000 фото                           | `url`, `thumbnailUrl` не пусты        |
| GET   | `/users`           | -                    | 200 OK, 10 пользователей                    | `name`, `email`, `address.city`       |
| GET   | `/todos`           | -                    | 200 OK, 200 задач                           | `title`, `completed` (boolean)        |
| GET   | `/todos`           | `userId=1`           | 200 OK, задачи пользователя 1               | Все задачи имеют `userId=1`           |
| GET   | `/todos`           | `completed=true`     | 200 OK, завершенные задачи                  | Все задачи имеют `completed=true`     |
| POST  | `/posts`           | `{"title": "New Post", "body": "Content", "userId": 1}` | 201 Created | `id: 101`, поля соответствуют запросу |
| POST  | `/comments`        | `{"postId": 1, "name": "Test", "email": "test@test.com", "body": "Test comment"}` | 201 Created | `id: 501`, все поля присутствуют |
| POST  | `/todos`           | `{"title": "New Todo", "completed": false, "userId": 1}` | 201 Created | `id: 201`, `completed` имеет тип boolean |
| POST  | `/users`           | `{"name": "John", "email": "john@test.com", "address": {"city": "Moscow"}}` | 201 Created | `id: 11`, поле `address` присутствует |

## Негативные сценарии  

| Метод | URL                | Параметры/Тело          | Ожидаемый результат       | Фактический результат (баг)          |
|-------|--------------------|-------------------------|--------------------------|--------------------------------------|
| GET   | `/posts/0`         | -                       | 404 Not Found            | 200 OK (пост с `id=0` не существует) |
| GET   | `/todos?userId=abc`| `userId=abc`            | 400 Bad Request          | 200 OK (некорректный параметр)       |
| POST  | `/posts`           | `{}`                    | 400 Bad Request          | 201 Created (баг: пустое тело)       |
| POST  | `/todos`           | `{"completed": "yes"}`  | 400 Bad Request          | 201 Created (баг: неверный тип)      |
| POST  | `/users`           | `{"email": "invalid"}`  | 400 Bad Request          | 201 Created (баг: некорректный email)|
| POST  | `/invalid-endpoint`| `{"data": "test"}`      | 404 Not Found            | 404 Not Found                        |
