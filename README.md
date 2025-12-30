# yml-with-collections-support

> Генератор Yandex Market Language (YML) из JSON с поддержкой коллекций.  
> Форк оригинальной библиотеки с добавлением поддержки коллекций (collections) для Яндекс.Маркета.

[![npm version](https://img.shields.io/npm/v/yml-with-collections-support.svg)](https://www.npmjs.com/package/yml-with-collections-support)
[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE.md)

## 🎯 Что это за зверь?

Библиотека для генерации YML-фидов из JSON объектов. В отличие от других решений, эта штука умеет работать с **коллекциями** (collections) - фичей, которая нужна для Яндекс.Маркета, но которую многие библиотеки игнорируют как несуществующую.

**Важно:** На момент написания README библиотека находится в стадии разработки. Используй на свой страх и риск, но в целом работает нормально.

## 📦 Установка

```bash
npm install yml-with-collections-support
```

Или через yarn, если ты из тех, кто не может жить без yarn:

```bash
yarn add yml-with-collections-support
```

## 🚀 Быстрый старт

```javascript
const yml = require('yml-with-collections-support')

const shopData = {
  name: 'Мой Магазин',
  company: 'ООО "Рога и Копыта"',
  url: 'https://example.com',
  currencies: [
    { id: 'RUR', rate: 1 }
  ],
  categories: [
    { id: '1', name: 'Электроника' }
  ],
  offers: [
    {
      id: '123',
      name: 'Телефон',
      price: 10000,
      currencyId: 'RUR',
      categoryId: '1',
      collectionId: ['collection-1']
    }
  ],
  collections: [
    {
      id: 'collection-1',
      name: 'Новинки',
      url: 'https://example.com/collections/new',
      picture: ['https://example.com/img/collection.jpg']
    }
  ]
}

// Генерируем YML и сразу получаем строку
const ymlString = yml(shopData).end({ pretty: true })
console.log(ymlString)
```

## 📖 API

### `yml(input, options)`

Основная функция для создания YML из JSON объекта.

**Параметры:**

- `input` (Object) - Объект с данными магазина (обязательный)
- `options` (Object) - Опции генерации (опциональный)
  - `validate` (Boolean) - Валидировать ли входные данные (по умолчанию `true`)
  - `date` (String|Date) - Фиксированная дата для YML в формате RFC2822 или ISO

**Возвращает:**

Объект с методами:
- `create(options)` - Создает XML элемент (xmlbuilder XMLElement)
- `end(options)` - Создает и сразу возвращает XML строку

**Пример:**

```javascript
const yml = require('yml-with-collections-support')

const data = { /* твои данные */ }

// С валидацией (по умолчанию)
const result = yml(data)
const xmlString = result.end({ pretty: true })

// Без валидации (если ты уверен в своих данных)
const result = yml(data, { validate: false })
const xmlString = result.end({ pretty: true })

// С фиксированной датой
const result = yml(data, { date: '2024-01-15' })
const xmlString = result.end({ pretty: true })
```

## 🎨 Структура входных данных

### Минимальный пример

```javascript
{
  name: 'Название магазина',
  company: 'Название компании',
  url: 'https://example.com',
  currencies: [
    { id: 'RUR', rate: 1 }
  ],
  categories: [
    { id: '1', name: 'Категория' }
  ],
  offers: [
    {
      id: '123',
      name: 'Товар',
      price: 1000,
      currencyId: 'RUR',
      categoryId: '1'
    }
  ]
}
```

### Полный пример с коллекциями

```javascript
{
  name: 'Мой Магазин',
  company: 'ООО "Магазин"',
  url: 'https://example.com',
  platform: 'Wordpress',
  version: '1.0',
  agency: 'Агентство',
  email: 'shop@example.com',
  
  currencies: [
    { id: 'RUR', rate: 1 },
    { id: 'USD', rate: 'CBRF' }
  ],
  
  categories: [
    { id: '1', name: 'Электроника' },
    { id: '2', parentId: '1', name: 'Смартфоны' }
  ],
  
  'delivery-options': [
    { cost: 300, days: [1, 20], 'order-before': 12 }
  ],
  
  offers: [
    {
      id: '123',
      available: true,
      name: 'Смартфон',
      price: 15000,
      oldprice: 18000,
      currencyId: 'RUR',
      categoryId: '2',
      picture: ['https://example.com/img/phone.jpg'],
      url: 'https://example.com/product/123',
      vendor: 'Samsung',
      vendorCode: 'SM-G991',
      description: '<p>Отличный смартфон</p>',
      store: true,
      pickup: true,
      delivery: true,
      outlets: [
        { id: '1', instock: 10 },
        { id: '2', instock: 5, booking: true }
      ],
      'delivery-options': [
        { cost: 300, days: 0, 'order-before': 12 }
      ],
      barcode: ['1234567890123'],
      param: [
        { name: 'Экран', unit: 'дюйм', value: 6.1 },
        { name: 'Память', value: '128GB' }
      ],
      collectionId: ['new-products', 'sale'] // Связь с коллекциями
    }
  ],
  
  // 🎯 Вот это и есть та самая фича - коллекции!
  collections: [
    {
      id: 'new-products',
      name: 'Новинки',
      url: 'https://example.com/collections/new',
      picture: ['https://example.com/img/collection-new.jpg'],
      description: '<p>Новые поступления</p>'
    },
    {
      id: 'sale',
      name: 'Распродажа',
      url: 'https://example.com/collections/sale',
      picture: ['https://example.com/img/collection-sale.jpg']
    }
  ]
}
```

## 🔥 Особенности

### Поддержка коллекций

Основная фишка этой библиотеки - поддержка коллекций (collections). Ты можешь:

1. Создавать коллекции в секции `collections`
2. Связывать товары с коллекциями через поле `collectionId` в офферах
3. Указывать несколько коллекций для одного товара

```javascript
{
  offers: [
    {
      id: '123',
      // ... другие поля ...
      collectionId: ['collection-1', 'collection-2'] // Массив ID коллекций
    }
  ],
  collections: [
    { id: 'collection-1', name: 'Коллекция 1', url: '...' },
    { id: 'collection-2', name: 'Коллекция 2', url: '...' }
  ]
}
```

### Валидация данных

По умолчанию библиотека валидирует входные данные через схемы tcomb. Если передашь кривые данные, получишь понятную ошибку. Если хочешь отключить валидацию (например, для тестов), передай `{ validate: false }`.

### Гибкая работа с ценами

Цену можно указать как число или как объект:

```javascript
// Просто число
price: 1000

// Объект с атрибутами
price: { from: true, value: 1000 }
```

### Работа с датами доставки

Дни доставки можно указать как число или как диапазон:

```javascript
// Одно число
days: 5

// Диапазон (будет преобразован в "1-20")
days: [1, 20]
```

## 🛠️ Разработка

Если хочешь поковырять код:

```bash
# Клонируй репозиторий
git clone https://github.com/sergeev-s/yandex-market-language-with-collections-support.git
cd yandex-market-language-with-collections-support

# Установи зависимости
npm install

# Запусти тесты
npm test

# Запусти линтер
npm run lint

# Тесты в watch режиме
npm run test:watch
```

## 📝 Лицензия

Apache License 2.0 - можешь делать с этим что хочешь, только не забывай указывать автора.

## 🤝 Контрибьютинг

Если нашел баг или хочешь добавить фичу - welcome! Создавай issue или pull request. Только не забывай писать тесты и запускать линтер.

## ⚠️ Disclaimer

Библиотека находится в стадии разработки. Автор не несет ответственности за то, что твой фид не пройдет модерацию Яндекс.Маркета. Используй на свой страх и риск, но в целом все работает как надо.

## 🔗 Ссылки

- [Репозиторий на GitHub](https://github.com/sergeev-s/yandex-market-language-with-collections-support)
- [Документация Яндекс.Маркета по YML](https://yandex.ru/support/partnermarket/yml/about-yml.xml)
- [NPM пакет](https://www.npmjs.com/package/yml-with-collections-support)

---

**Автор:** [Semyon Sergeev](https://github.com/sergeev-s)
