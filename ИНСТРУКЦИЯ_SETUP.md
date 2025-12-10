# Инструкция по настройке Google Apps Script для сбора данных

## Шаг 1: Создайте Google Sheet для хранения ответов
1. Перейдите на https://sheets.google.com
2. Нажмите "+" (новую таблицу)
3. Создайте заголовки в первой строке:
   - A1: `Дата`
   - B1: `Имя`
   - C1: `Участие`
   - D1: `Имя супруга/супруги`
4. Скопируйте ID таблицы из URL (между `/d/` и `/edit`)

## Шаг 2: Создайте Google Apps Script
1. Перейдите на https://script.google.com
2. Нажмите "+" (новый проект)
3. Дайте проекту имя (например, "90bday-form")
4. В редакторе замените весь код на следующий (вставьте ваш SHEET_ID):

```javascript
const SHEET_ID = 'ВСТАВЬТЕ_ID_ВАШЕЙ_ТАБЛИЦЫ'; // Скопируйте ID из шага 1
const SHEET_NAME = 'Sheet1'; // Имя листа по умолчанию

function doPost(e) {
  try {
    const params = e.parameter;
    const ss = SpreadsheetApp.openById(SHEET_ID);
    const sheet = ss.getSheetByName(SHEET_NAME);
    
    // Добавляем строку с данными
    const row = [
      new Date(),
      params.name || '',
      params.attendance || '',
      params.spouseName || ''
    ];
    sheet.appendRow(row);

    // Возвращаем успешный ответ
    return ContentService
      .createTextOutput(JSON.stringify({result: 'success'}))
      .setMimeType(ContentService.MimeType.JSON);
  } catch (err) {
    return ContentService
      .createTextOutput(JSON.stringify({result: 'error', message: err.message}))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

function doGet(e) {
  return ContentService
    .createTextOutput(JSON.stringify({result: 'ready'}))
    .setMimeType(ContentService.MimeType.JSON);
}
```

5. Нажмите Ctrl+S (сохранить)

## Шаг 3: Развёртывание Web App
1. Нажмите кнопку "Deploy" (Развернуть)
2. Выберите "New deployment" (Новое развёртывание)
3. В меню слева выберите иконку ⚙️ и выберите "Web app"
4. Установите:
   - **Execute as**: Your email (ваша почта Gmail)
   - **Who has access**: Anyone (anyone, even anonymous) — **это важно!**
5. Нажмите "Deploy"
6. Система попросит разрешение — нажмите "Authorize"
7. Скопируйте URL развёрнутого приложения (выглядит как `https://script.googleapis.com/macros/d/.../usercontent`)

## Шаг 4: Вставьте URL в вашу форму
1. Откройте `90bday.html`
2. Найдите строку: `const WEB_APP_URL = 'ВАША_WEB_APP_URL_ЗДЕСЬ';`
3. Замените `'ВАША_WEB_APP_URL_ЗДЕСЬ'` на URL из шага 3, например:
   ```javascript
   const WEB_APP_URL = 'https://script.googleapis.com/macros/d/1aBcDeFgHiJkLmNoPqRsTuVwXyZ/usercontent';
   ```
4. Сохраните файл

## Шаг 5: Тестирование
1. Откройте `90bday.html` в браузере
2. Прокрутите до формы "Қатысуыңызды растау"
3. Заполните форму и нажмите "Жіберу"
4. Должно появиться сообщение "Рахмет! Сіздің жауабыңыз қабылданды."
5. Откройте вашу Google Sheet — там должна появиться новая строка с данными!

## Как публично выложить сайт?

### Вариант 1: GitHub Pages (самый популярный, бесплатный)
```powershell
cd 'C:\Users\bakty\OneDrive\Desktop\90bday'
git init
git add 90bday.html
git commit -m "Add 90bday site"
git branch -M main
git remote add origin https://github.com/ВАШ_USERNAME/90bday.git
git push -u origin main
```

Затем в GitHub (репозиторий) → Settings → Pages → Source: main / root → Save

Сайт будет доступен по адресу: `https://ВАШ_USERNAME.github.io/90bday/`

### Вариант 2: Netlify (ещё проще, drag & drop)
1. Перейдите на https://netlify.com
2. Войдите через GitHub или почту
3. Перетащите папку `90bday` в область "Drag files here"
4. Сайт будет опубликован немедленно!

## Примечания
- Форма работает при наличии интернета и доступе к Google APIs
- Данные хранятся в вашей Google Sheet и видны только вам (проверьте разрешения Sheet)
- Если CORS блокирует ответ, данные всё равно будут отправлены в Google Sheet (браузер просто не получит подтверждение JavaScript, но локальное сообщение покажется)

## Если что-то не работает
1. Проверьте консоль браузера (F12 → Console) на ошибки
2. Убедитесь, что URL Web App скопирован правильно
3. Убедитесь, что в Apps Script выставлено "Anyone" в "Who has access"
4. Проверьте, что SHEET_ID в коде Apps Script совпадает с ID вашей таблицы
