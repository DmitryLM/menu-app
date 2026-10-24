# 🍽️ Меню на неделю

Веб-приложение для совместного планирования меню на неделю для семьи.

## Файлы

- `index.html` — всё приложение в одном файле (HTML + CSS + JS).

## Запуск локально

Просто открой `index.html` в браузере. Данные сохраняются в `localStorage` (только в этом браузере).

Или запусти локальный сервер:

```bash
cd menu-app
python -m http.server 8765
# открой http://localhost:8765
```

## Настройка Firebase (для шаринга с женой)

1. Зайди на https://console.firebase.google.com → **Add project** → создай проект (Google Analytics можно отключить).
2. В проекте: **Build → Authentication → Get started → Sign-in method → Anonymous → Enable**.
3. **Build → Firestore Database → Create database → Start in test mode → выбери регион** (europe-west или ближайший).
4. **Project settings (шестерёнка) → General → Your apps → Web (</>) → Register app**. Скопируй объект `firebaseConfig`.
5. Открой `index.html`, найди переменную `FIREBASE_CONFIG` в начале `<script>` и замени значения на свои (`apiKey`, `authDomain`, `projectId`, `storageBucket`, `messagingSenderId`, `appId`).
6. В Firestore → **Rules** вставь:
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /households/{hh}/{document=**} {
         allow read, write: if request.auth != null;
       }
     }
   }
   ```
   Нажми **Publish**.
7. Перезагрузи страницу — приложение перейдёт в облачный режим. Вкладка «Поделиться» покажет ссылку — отправь её жене.

## Размещение на GitHub Pages

1. Создай репозиторий на GitHub (например `menu-app`).
2. Загрузи `index.html` в корень репозитория (через веб-интерфейс или git).
3. **Settings → Pages → Source: Deploy from a branch → Branch: main → / (root) → Save**.
4. Через 1-2 минуты страница будет доступна по адресу `https://<username>.github.io/menu-app/`.

## Функционал

- **📖 Рецепты**: добавление/редактирование/удаление. Поля: название, порции, ингредиенты (название, количество, единица), процесс, ссылка на источник, скриншот, фото блюда. Автоподсчёт калорий и БЖУ (встроенная база + Open Food Facts), ручная корректировка.
- **📅 План недели**: календарь Пн–Вс, добавление блюд из рецептов, навигация по неделям, копирование плана на следующую неделю, печать.
- **🛒 Покупки**: агрегированный список ингредиентов, группировка по категориям, отметки, копирование текстом.
- **🔗 Поделиться**: ссылка для жены, настройка Firebase, пароль семьи.

## Безопасность

По умолчанию доступ по ссылке (любой со ссылкой может управлять). Можно установить пароль семьи на вкладке «Поделиться» — тогда при открытии ссылки будет запрашиваться пароль.

## Примечания

- Фото хранятся в базе как сжатые JPEG (base64), Firebase Storage не требуется.
- КБЖУ считаются по ингредиентам; для точности указывайте граммы/миллилитры.
