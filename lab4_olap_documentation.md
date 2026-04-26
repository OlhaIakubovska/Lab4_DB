# Лабораторна робота №4 — Аналітичні SQL-запити (OLAP)

**Студентка:** Якубовська О.В., група ІО-45  
**База даних:** Платформа для онлайн-курсів  

---

## Схема бази даних

База даних складається з 7 таблиць:

| Таблиця | Опис |
|---------|------|
| `User` | Супертип користувача (ім'я, email, пароль) |
| `Student` | Підтип User — академічний рівень, бали |
| `Instructor` | Підтип User — біо, спеціалізація, рейтинг |
| `Course` | Курс (назва, опис, ціна, викладач) |
| `Module` | Модуль курсу (порядок, URL) |
| `Enrollment` | Зарахування студента на курс (прогрес, статус) |
| `Review` | Відгук студента на курс (оцінка, коментар) |

---

## Розділ 1: Агрегаційні функції

### 1.1 — Загальна кількість сутностей
Підраховує кількість студентів, викладачів та курсів за допомогою трьох вкладених `COUNT(*)` у підзапитах SELECT. Дає загальне уявлення про розмір бази.
<img width="724" height="131" alt="image" src="https://github.com/user-attachments/assets/958c8553-6435-4242-a2de-fdbcbbf7c128" />

### 1.2 — Статистика цін курсів
Використовує `AVG`, `MIN`, `MAX`, `SUM` для обчислення середньої, мінімальної, максимальної та сумарної ціни всіх курсів.
<img width="737" height="122" alt="image" src="https://github.com/user-attachments/assets/24624387-23c3-4477-9843-ca8d99c4eea9" />

### 1.3 — Кількість зарахувань по статусах
Групує таблицю `Enrollment` за полем `Status`, рахує кількість записів кожного статусу (`Active` / `Completed`).
<img width="288" height="84" alt="image" src="https://github.com/user-attachments/assets/6477cb98-6173-43ba-87e3-438edf98b76f" />

### 1.4 — Статистика рейтингу викладачів
Обчислює середній, мінімальний та максимальний рейтинг серед усіх викладачів.
<img width="926" height="171" alt="image" src="https://github.com/user-attachments/assets/7502c66d-6f00-4f72-a498-dc3ba824a5fb" />

### 1.5 — Кількість модулів у кожному курсі
`LEFT JOIN` між `Course` та `Module`, потім `COUNT(ModuleID)` по кожному курсу. `LEFT JOIN` гарантує включення курсів без модулів.
<img width="503" height="268" alt="image" src="https://github.com/user-attachments/assets/c014bf44-9b7b-4cf1-93f2-5b247777bd1c" />

### 1.6 — Середній прогрес студентів
`AVG(Progress)` по всій таблиці `Enrollment` — загальний середній відсоток виконання курсів.
<img width="269" height="123" alt="image" src="https://github.com/user-attachments/assets/25230f0f-5df0-4f04-9b2c-37b1d3c9d1ab" />

### 1.7 — Загальна сума балів студентів
`SUM(TotalPoints)` по таблиці `Student` — підсумовує бали всіх студентів.
<img width="211" height="70" alt="image" src="https://github.com/user-attachments/assets/ccff2b17-f3e6-4958-bfbb-a588417cdfe1" />

---

## Розділ 2: GROUP BY та HAVING

### 2.1 — Кількість студентів за академічним рівнем
`GROUP BY AcademicLevel` рахує кількість студентів у кожній категорії (бакалавр, магістр, аспірант).
<img width="303" height="94" alt="image" src="https://github.com/user-attachments/assets/8e8260e0-481e-4acf-8f41-dcff625c72f6" />

### 2.2 — Кількість зарахувань на кожен курс
`LEFT JOIN` + `GROUP BY` показує популярність кожного курсу. Курси без зарахувань теж включені.
<img width="469" height="238" alt="image" src="https://github.com/user-attachments/assets/e6681a38-4f37-45cd-b8cb-741599e7221c" />

### 2.3 — Середній рейтинг відгуків по курсах
Об'єднує `Course` та `Review`, групує по курсу, рахує кількість відгуків та середню оцінку.
<img width="578" height="247" alt="image" src="https://github.com/user-attachments/assets/33fe361f-fd02-4569-b747-d04ea85841fc" />

### 2.4 — Курси з ≥ 2 зарахуваннями (HAVING)
Фільтрує результати `GROUP BY` через `HAVING COUNT(...) >= 2` — залишає лише популярні курси.
<img width="473" height="136" alt="image" src="https://github.com/user-attachments/assets/bb731b68-f7d3-4a31-93bc-1c59966aaf49" />

### 2.5 — Викладачі з більше одним курсом (HAVING)
Рахує кількість курсів на кожного викладача, через `HAVING COUNT > 1` залишає лише продуктивних.
<img width="395" height="135" alt="image" src="https://github.com/user-attachments/assets/9ca6c7ba-3d3a-48c4-9dc6-98e97af90c3a" />

### 2.6 — Курси з середнім відгуком > 4 (HAVING + AVG)
`HAVING AVG(r.Rating) > 4` відбирає лише курси з високою оцінкою від студентів.
<img width="489" height="173" alt="image" src="https://github.com/user-attachments/assets/411917a6-634f-4fe8-b15b-78886944ccb8" />

### 2.7 — Студенти із середнім прогресом > 50%
Групує зарахування по студентах, через `HAVING AVG(Progress) > 50` залишає активних студентів.
<img width="423" height="136" alt="image" src="https://github.com/user-attachments/assets/aba2985e-84e2-4ead-810e-c38f0876a263" />

---

## Розділ 3: JOIN-запити

### 3.1 — INNER JOIN: студенти та їхні курси
Об'єднує 4 таблиці (`Enrollment`, `Student`, `User`, `Course`). Показує лише ті комбінації, де студент реально записаний на курс.
<img width="868" height="319" alt="image" src="https://github.com/user-attachments/assets/82d0630f-27f9-49d8-984b-91493b6c4fe1" />

### 3.2 — LEFT JOIN: всі курси та кількість зарахувань
`Course LEFT JOIN Enrollment` — включає курси навіть без жодного студента (значення 0).
<img width="633" height="246" alt="image" src="https://github.com/user-attachments/assets/9eab816f-1172-4691-84a1-52842b9a397e" />

### 3.3 — LEFT JOIN: курси та відгуки з іменами студентів
Ланцюг `LEFT JOIN` через `Review → Student → User`. Курси без відгуків все одно відображаються (значення NULL).
<img width="1037" height="281" alt="image" src="https://github.com/user-attachments/assets/8c4bca6f-db3b-4c42-ab64-35263b72cb8b" />

### 3.4 — RIGHT JOIN: всі відгуки
`Course RIGHT JOIN Review` — повертає всі відгуки, навіть якби курс був видалений. Корисно для перевірки цілісності.
<img width="837" height="247" alt="image" src="https://github.com/user-attachments/assets/f8a0f66d-1393-43a5-8b00-b6f453c078a5" />

### 3.5 — FULL JOIN: студенти та зарахування
`FULL JOIN` об'єднує всі рядки з обох таблиць, включаючи незв'язані записи з обох сторін (жодних в реальних даних немає через обмеження FK).
<img width="656" height="427" alt="image" src="https://github.com/user-attachments/assets/8fa1968e-7762-4d45-9088-8c361aea8b28" />

### 3.6 — CROSS JOIN: декартів добуток студентів і курсів
Генерує всі можливі пари «студент — курс». Корисно для аналізу того, які комбінації ще не використані.
<img width="510" height="608" alt="image" src="https://github.com/user-attachments/assets/a885f24b-ac71-496b-a018-2dab5f84b659" />

### 3.7 — Triple JOIN: викладач → курс → кількість студентів
Триджоін з агрегацією. Показує навантаження кожного викладача по кожному курсу.
<img width="878" height="248" alt="image" src="https://github.com/user-attachments/assets/75145bf2-5340-414f-9c7c-8ff750c4bd77" />

---

## Розділ 4: Підзапити

### 4.1 — Підзапит у SELECT
Для кожного рядка `Course` виконується корельований підзапит, який рахує кількість зарахувань саме на цей курс.
<img width="633" height="247" alt="image" src="https://github.com/user-attachments/assets/2b3f1303-306e-4b8a-ae82-9a21fba67c9e" />

### 4.2 — Підзапит у WHERE (IN)
`WHERE UserID IN (SELECT StudentID FROM Enrollment WHERE Status = 'Completed')` — відбирає студентів, які завершили хоча б один курс.
<img width="628" height="137" alt="image" src="https://github.com/user-attachments/assets/59b32043-4597-493a-bcc8-22c29b4a41bb" />

### 4.3 — Підзапит у WHERE (порівняння з AVG)
`WHERE Price > (SELECT AVG(Price) FROM Course)` — відфільтровує курси дорожчі за середню ціну по платформі.
<img width="447" height="136" alt="image" src="https://github.com/user-attachments/assets/96ca210a-6abd-4467-93ac-4343f60276aa" />

### 4.4 — Підзапит у WHERE (NOT IN)
`WHERE UserID NOT IN (SELECT DISTINCT StudentID FROM Review)` — знаходить студентів, які ніколи не писали відгуків.
<img width="346" height="117" alt="image" src="https://github.com/user-attachments/assets/269f88bf-782f-4ce4-b368-a97864b02b03" />

### 4.5 — Підзапит у HAVING
`HAVING COUNT(...) > (SELECT AVG(cnt) FROM (...) sub)` — двоступеневий підзапит у похідній таблиці знаходить курси з популярністю вище середньої.
<img width="479" height="138" alt="image" src="https://github.com/user-attachments/assets/5df9ae05-0325-475f-a75f-5465c6d4a838" />

### 4.6 — Корельований підзапит
Для кожного курсу обчислює власний середній рейтинг і порівнює його із загальним середнім по всій платформі.
<img width="493" height="138" alt="image" src="https://github.com/user-attachments/assets/0a1c1af0-4a93-4900-b296-3c9a90827eb2" />

### 4.7 — Підзапит у FROM (похідна таблиця)
Запит знаходить агрегати у підзапиті, а зовнішній запит їх сортує і обмежує через `LIMIT 3` — ТОП-3 курсів за рейтингом.
<img width="578" height="173" alt="image" src="https://github.com/user-attachments/assets/c20f28b1-d562-4579-8745-51cf3c293062" />

---

## Розділ 5: Комбіновані аналітичні запити

### 5.1 — Зведена статистика по курсах
Найбільш комплексний запит: об'єднує `Course`, `Instructor`, `User`, `Enrollment`, `Review` і обчислює по кожному курсу кількість студентів, середній прогрес, кількість і середній рейтинг відгуків.
<img width="1284" height="246" alt="image" src="https://github.com/user-attachments/assets/d0762fff-0f0a-4b13-aea0-7b3befa450ea" />

### 5.2 — Активність студентів
По кожному студенту: кількість записаних курсів, середній прогрес, кількість написаних відгуків. Дозволяє знайти найбільш залучених користувачів.
<img width="1107" height="175" alt="image" src="https://github.com/user-attachments/assets/8b9f826a-5e7f-48a0-84e2-7d34483fe805" />

### 5.3 — Продуктивність викладачів
Агрегує по викладачах: кількість курсів, загальна кількість студентів, середній рейтинг від студентів. Корисно для рейтингу викладачів.
<img width="1119" height="174" alt="image" src="https://github.com/user-attachments/assets/020b7065-ab5e-4f79-bb6e-1541b39d0a95" />

### 5.4 — Розподіл по академічному рівню
Групує студентів за рівнем навчання та порівнює їх середній прогрес і середню кількість балів — показує, яка група найбільш активна.
<img width="721" height="139" alt="image" src="https://github.com/user-attachments/assets/36e26e47-fa37-489c-b8aa-d46da9860744" />

---

## Підсумок виконаних вимог

| Вимога | Виконано |
|--------|----------|
| Мінімум 4 запити з агрегацією (COUNT, SUM, AVG, MIN, MAX, GROUP BY) | ✅ 18 запитів |
| Мінімум 3 запити з різними типами JOIN (INNER, LEFT, RIGHT, FULL, CROSS) | ✅ 7 запитів (всі 5 типів) |
| Мінімум 3 запити з підзапитами (SELECT, WHERE, HAVING) | ✅ 7 запитів (всі варіанти) |
