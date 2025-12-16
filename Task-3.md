# Task 3.1. 

**1\. Общая информация**

**Цель эндпоинта:** Регистрация нового пользователя в системе.  
**Метод вызова:** POST  
**URL:** /api/v1/auth/register  
**Формат данных:** application/json

**2\.  Входные параметры (тело запроса, JSON)**

| Наименование параметра | Тип данных | Обязательность  | Ограничения |
| :---- | :---- | :---- | :---- |
| firstName  | string  | Да | Длина от 1 до 50 символов; только буквы и дефис |
| lastName  | string  | Да  | Длина от 1 до 50 символов;  только буквы и дефис |
| userName  | string  | Да  | Длина от 1 до 50 символов;  только буквы и дефис.Unique (Должен быть уникальным в системе) |
| password  | string  | Да  | Длина от 8 до 128 символов;  содержит хотя бы  1 цифру,  1 заглавную и  1 строчную букву |
| captchaToken  | string  | Да  |  Токен, полученный от сервиса CAPTCHA |

**3\.  Выходные параметры при успешном ответе (201 Created)**

| Наименование параметра | Тип данных | Обязательность  | Описание |
| :---- | :---- | :---- | :---- |
| userId  | UUID  | Да  | Уникальный идентификатор пользователя |
| userName  | string  | Да  | Имя пользователя (логин) |
| firstName  | string  | Да  | Имя |
| lastName  | string  | Да  | Фамилия |
| accessToken  | string  | Да  | JWT Token для последующей авторизации (Bearer Token) |
| tokenType  | string  | Да  | Тип токена ("Bearer") |
| expiresIn  | integer  | Да  |  Срок действия токена в секундах (например, 3600\) |
| createdAt  | string  | Да  | Дата и время создания аккаунта ISO 8601 (RFC 3339\) |

      
**4\.  Выходные параметры и коды ответов при ошибке**  
При возникновении ошибки возвращается стандартизированный JSON-объект.

| Наименование параметра | Тип данных | Обязательность  | Описание |
| :---- | :---- | :---- | :---- |
| timestamp  | string  | Да  | Время возникновения ошибки (ISO 8601\) |
| status  | integer  | Да  | HTTP-код ответа |
| error  | string  | Да  | Краткое описание типа ошибки |
| message  | string  | Да  | Сообщение об ошибке для клиента/разработчика |

      
**5\.  Описание кодов ответов (HTTP-Статусы):**

| HTTP-код |  Класс ошибки | Описание ошибки | Пример сообщения  |
| :---- | :---- | :---- | :---- |
| 400 Bad Request | Клиентская  | Не пройдена проверка CAPTCHA.  | Не удалось подтвердить, что вы не робот. Попробуйте ещё раз. |
| 409 Conflict | Клиентская  | Имя пользователя (UserName) уже занято. | Пользователь с таким логином уже зарегистрирован. |
| 422 Unprocessable Entity | Клиентская  | Слабый пароль | Пароль не соответствует требованиям безопасности. Он должен содержать не менее 8 символов, включая заглавную букву, строчную букву и цифру. |
| 500 Internal Server Error | Серверная  | Непредвиденная ошибка на стороне сервера (например, БД недоступна).   | При обработке вашего запроса произошла непредвиденная ошибка. Пожалуйста, повторите попытку позже. |

              
**6\.  Пример запроса**

POST /api/v1/auth/register HTTP/1.1  
Host: api.example.com  
Content-Type: application/json

{  
  "firstName": "Ivan",  
  "lastName": "Petrov",  
  "userName": "ivan\_p\_seller",  
  "password": "JkedxckhFC390239^@)",  
  "captchaToken": "g-recaptcha-response-token-from-frontend"  
}

**7\.  Примеры ответов**

**Успешный ответ (201 Created)**  
HTTP/1.1 201 Created  
Content-Type: application/json

{  
  "userId": "123e4567-e89b-12d3-a456-426614174000",  
  "userName": "ivan\_p\_seller",  
  "firstName": "Ivan",  
  "lastName": "Petrov",  
  "accessToken": "eyJhbGciOiJIUzI1NiIsInR...",  
  "tokenType": "Bearer",  
  "expiresIn": 3600,  
  "createdAt": "2025-12-12T10:30:00Z"  
}

**Ошибка: плохой запрос (400 Bad Request)**

Пользователь не поставил галочку в чек-бокс «Я не робот».

HTTP/1.1 400 Bad Request  
Content-Type: application/json

{  
  "timestamp": "2025-12-12T20:26:00.000+03:00",  
  "status": 400,  
  "error": "INVALID\_CAPTCHA",  
   "message": "Не удалось подтвердить, что вы не робот. Попробуйте ещё раз.",  
}

**Ошибка: userName уже занят (409 Conflict)**  
Имя пользователя уже занято другим аккаунтом.

HTTP/1.1 409 Conflict  
Content-Type: application/json

{  
    "timestamp": "2025-12-12T20:26:00.000+03:00",  
    "status": 409,  
    "error": "USERNAME\_ALREADY\_EXISTS",  
    "message": "Пользователь с таким логином уже зарегистрирован"  
}

**Ошибка: слабый пароль (422 Unprocessable Entity)**  
Пароль не соответствует уровню сложности.

HTTP/1.1 422 Unprocessable Entity  
Content-Type: application/json

{  
    "timestamp": "2025-12-12T20:26:00.000+03:00",  
    "status": 422,  
    "error": "WEAK\_PASSWORD",  
    "message": "Пароль должен содержать не менее 8 символов, включая заглавную букву, строчную букву и цифру",  
}

**Ошибка: внутренняя ошибка сервера (500 Internal Server Error)**  
Указывает на общую серверную ошибку.

HTTP/1.1 500 Internal Server Error  
Content-Type: application/json

{  
    "timestamp": "2025-12-12T20:26:00.000+03:00",  
    "status": 500,  
    "error": "INTERNAL\_SERVER\_EXCEPTION",   
    "message": "При обработке вашего запроса произошла непредвиденная ошибка. Пожалуйста, повторите попытку позже"  
}

**8\.  OpenAPI 3.0 спецификация (YAML)**  
Effective\_mobile11 | 1.0.0 | Goodtime | Studio  
openapi: 3.0.3  
info:  
  title: Auth Service API  
  description: API для регистрации и аутентификации пользователей  
  version: 1.0.0  
  contact:  
    name: API Support  
    email: support@example.com

servers:  
  \- url: https://api.example.com/api/v1  
    description: Production server

paths:  
  /auth/register:  
    post:  
      summary: Регистрация нового пользователя  
      description: Создаёт новый аккаунт и возвращает JWT-токен для немедленной авторизации.  
      operationId: registerUser  
      requestBody:  
        required: true  
        content:  
          application/json:  
            schema:  
              $ref: '\#/components/schemas/RegisterRequest'  
      responses:  
        '201':  
          description: Пользователь успешно зарегистрирован  
          content:  
            application/json:  
              schema:  
                $ref: '\#/components/schemas/RegisterResponse'  
        '400':  
          description: Ошибка CAPTCHA или отсутствие обязательных данных  
          content:  
            application/json:  
              schema:  
                $ref: '\#/components/schemas/ErrorResponse'  
        '409':  
          description: Логин уже занят  
          content:  
            application/json:  
              schema:  
                $ref: '\#/components/schemas/ErrorResponse'  
        '422':  
          description: Некорректные данные (например, слабый пароль)  
          content:  
            application/json:  
              schema:  
                $ref: '\#/components/schemas/ErrorResponse'  
        '500':  
          description: Внутренняя ошибка сервера  
          content:  
            application/json:  
              schema:  
                $ref: '\#/components/schemas/ErrorResponse'

components:  
  schemas:  
    RegisterRequest:  
      type: object  
      required:  
        \- firstName  
        \- lastName  
        \- userName  
        \- password  
        \- captchaToken  
      properties:  
        firstName:  
          type: string  
          minLength: 1  
          maxLength: 50  
          pattern: '^\[a-zA-Zа-яА-ЯёЁ\\s\\-\]+$'  
          example: "Ivan"  
        lastName:  
          type: string  
          minLength: 1  
          maxLength: 50  
          pattern: '^\[a-zA-Zа-яА-ЯёЁ\\s\\-\]+$'  
          example: "Petrov"  
        userName:  
          type: string  
          minLength: 1  
          maxLength: 50  
          pattern: '^\[a-zA-Zа-яА-ЯёЁ\\s\\-\]+$'  
          example: "ivan\_p\_seller"  
        password:  
          type: string  
          minLength: 8  
          maxLength: 128  
          example: "JkedxckhFC390239^@)"  
        captchaToken:  
          type: string  
          description: Токен, полученный от сервиса CAPTCHA (например, reCAPTCHA)  
          example: "g-recaptcha-response-token-from-frontend"

RegisterResponse:  
      type: object  
      required:  
        \- userId  
        \- userName  
        \- firstName  
        \- lastName  
        \- accessToken  
        \- tokenType  
        \- expiresIn  
        \- createdAt  
      properties:  
        userId:  
          type: string  
          format: uuid  
          example: "123e4567-e89b-12d3-a456-426614174000"  
        userName:  
          type: string  
          example: "ivan\_p\_seller"  
        firstName:  
          type: string  
          example: "Ivan"  
        lastName:  
          type: string  
          example: "Petrov"  
        accessToken:  
          type: string  
          format: jwt  
          example: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."  
        tokenType:  
          type: string  
          enum: \[Bearer\]  
          example: "Bearer"  
        expiresIn:  
          type: integer  
          format: int32  
          example: 3600  
        createdAt:  
          type: string  
          format: date-time  
          example: "2025-12-12T10:30:00Z"

    ErrorResponse:  
      type: object  
      required:  
        \- timestamp  
        \- status  
        \- error  
        \- message  
      properties:  
        timestamp:  
          type: string  
          format: date-time  
          example: "2025-12-12T20:26:00.000+03:00"  
        status:  
          type: integer  
          format: int32  
          example: 400  
        error:  
          type: string  
          example: "INVALID\_CAPTCHA"  
        message:  
          type: string  
          example: "Не удалось подтвердить, что вы не робот. Попробуйте ещё раз."

  responses:  
    BadRequest:  
      description: Ошибка CAPTCHA или отсутствие обязательных полей  
      content:  
        application/json:  
          schema:  
            $ref: '\#/components/schemas/ErrorResponse'

    Conflict:  
      description: Имя пользователя уже занято  
      content:  
        application/json:  
          schema:  
            $ref: '\#/components/schemas/ErrorResponse'

    UnprocessableEntity:  
      description: Некорректные данные (например, слабый пароль)  
      content:  
        application/json:  
          schema:  
            $ref: '\#/components/schemas/ErrorResponse'

    InternalServerError:  
      description: Внутренняя ошибка сервера  
      content:  
        application/json:  
          schema:  
            $ref: '\#/components/schemas/ErrorResponse'

# Task 3.2.

Регистрация нового пользователя на стороне бэкенда при вызове эндпоинта   
POST /api/v1/auth/register.

**Шаг 0: Получение запроса**  
Фронтенд отправляет POST /api/v1/auth/register с JSON-телом:  
{  
  "firstName": "...",  
  "lastName": "...",  
  "userName": "...",  
  "password": "...",  
  "captchaToken": "..."  
}

**Шаг 1: Проверка структуры запроса (валидация JSON)**

Убедиться, что тело запроса — валидный JSON.  
Проверить наличие всех обязательных полей:  
firstName  
lastName  
userName  
password  
captchaToken

Если какое-то поле отсутствует: вернуть 400 Bad Request с error: "MISSING\_REQUIRED\_FIELD".

**Шаг 2: Валидация формата полей**  
Для каждого поля выполнить проверку по регулярным выражениям и длине:

Поле  Правила  
firstName  Длина 1–50, только буквы (латиница/кириллица), пробел, дефис  
lastName  Длина 1–50, только буквы (латиница/кириллица), пробел, дефис  
userName  Длина 1–50, только буквы, пробел, дефис  
password  Длина 8–128, содержит ≥1 цифру, ≥1 заглавную, ≥1 строчную букву  
captchaToken  Не пустая строка

Если любая проверка не пройдена: вернуть 422 Unprocessable Entity с error: "INVALID\_FIELD\_FORMAT" или WEAK\_PASSWORD.

**Шаг 3: Проверка сложности пароля**  
Убедиться, что пароль соответствует всем 3 условиям:  
Содержит хотя бы одну цифру (\\d)  
Содержит хотя бы одну заглавную букву (\[A-ZА-Я\])  
Содержит хотя бы одну строчную букву (\[a-zа-я\])

Если не выполнено хотя бы одно условие: вернуть 422 Unprocessable Entity с error: "WEAK\_PASSWORD".

**Шаг 4: Проверка CAPTCHA**  
Отправить captchaToken в внешний сервис CAPTCHA (например, Google reCAPTCHA):  
http  
POST https://www.google.com/recaptcha/api/siteverify  
?secret=СЕКРЕТНЫЙ\_КЛЮЧ  
\&response={captchaToken}

Получить ответ от сервиса:  
Если success: true и score (для v3) ≥ порога: CAPTCHA пройдена.   
Если success: false: вернуть 400 Bad Request с error: "INVALID\_CAPTCHA".

**Шаг 5: Проверка уникальности userName**  
Выполнить запрос к базе данных:  
sql  
SELECT 1 FROM users WHERE userName \= ? LIMIT 1;

Если запись найдена: вернуть 409 Conflict с error: "USERNAME\_ALREADY\_EXISTS".

**Шаг 6: Хеширование пароля**  
Сгенерировать криптостойкий хеш пароля.  
Никогда не сохранять пароль в открытом виде\!

**Шаг 7: Создание пользователя в БД**  
Выполнить транзакцию в базе данных:  
sql  
INSERT INTO users (  
  userId,  
  userName,  
  firstName,  
  lastName,  
  passwordHash,  
  createdAt  
) VALUES (  
  UUID(),  
  ?,  
  ?,  
  ?,  
  ?,  
  NOW()  
);  
Если ошибка БД: вернуть 500 Internal Server Error.

**Шаг 8: Генерация JWT-токена**  
Создать access token:  
json  
{  
  "sub": "userId",  
  "username": "userName",  
  "iat": 1702387200,  
  "exp": 1702390800  // \+3600 сек  
}  
Подписать токен с использованием секретного ключа (или приватного ключа).

**Шаг 9: Формирование успешного ответа**  
Вернуть HTTP 201 Created с телом:  
json  
{  
  "userId": "UUID",  
  "userName": "ivan\_p\_seller",  
  "firstName": "Ivan",  
  "lastName": "Petrov",  
  "accessToken": "eyJ...",  
  "tokenType": "Bearer",  
  "expiresIn": 3600,  
  "createdAt": "2025-12-15T10:30:00Z"  
}

**UML Activity Diagram**

@startuml  
title Регистрация нового пользователя (бэкенд)

start

:Получить POST-запрос\\n/api/v1/auth/register\\nс JSON-телом;  
note right  
  {  
    "firstName": "...",  
    "lastName": "...",  
    "userName": "...",  
    "password": "...",  
    "captchaToken": "..."  
  }  
end note

if (Все обязательные поля\\nприсутствуют?) then (нет)  
  :Вернуть 400 Bad Request\\nerror \= "MISSING\_REQUIRED\_FIELD";  
  stop  
endif

if (Поля firstName, lastName,\\nuserName соответствуют\\nформату (1-50 симв.,\\nтолько буквы/дефис)?) then (нет)  
  :Вернуть 422 Unprocessable Entity\\nerror \= "INVALID\_FIELD\_FORMAT";  
  stop  
endif

if (Пароль соответствует\\nтребованиям сложности?\\n(≥8 симв., заглавная,\\nстрочная, цифра)) then (нет)  
  :Вернуть 422 Unprocessable Entity\\nerror \= "WEAK\_PASSWORD";  
  stop  
endif

:Отправить captchaToken\\nво внешний сервис CAPTCHA\\n(например, reCAPTCHA);  
note right  
  POST https://www.google.com/recaptcha/api/siteverify  
end note

if (CAPTCHA пройдена?) then (нет)  
  :Вернуть 400 Bad Request\\nerror \= "INVALID\_CAPTCHA";  
  stop  
endif

:Проверить уникальность userName\\nв базе данных;  
note right  
  SELECT 1 FROM users WHERE userName \= ?  
end note

if (userName уже существует?) then (да)  
  :Вернуть 409 Conflict\\nerror \= "USERNAME\_ALREADY\_EXISTS";  
  stop  
endif

:Хешировать пароль\\n(например, bcrypt);  
note right  
  hashed \= bcrypt.hash(password)  
end note

:Создать пользователя в БД\\n(в транзакции);  
note right  
  INSERT INTO users (...) VALUES (...)  
end note

:Сгенерировать JWT access token\\n(срок действия: 3600 сек);  
note right  
  {  
    "sub": "userId",  
    "username": "userName",  
    "exp": ...  
  }  
end note

:Вернуть 201 Created\\nс данными пользователя\\nи accessToken;  
note right  
  {  
    "userId": "...",  
    "userName": "...",  
    "accessToken": "...",  
    "expiresIn": 3600,  
    ...  
  }  
end note

stop  
@enduml

