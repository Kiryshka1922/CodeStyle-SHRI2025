# CodeStyle Guide

**Участники группы**: 
1. Усков Максим 
2. Белоусов Кирилл 
3. Кадыргулов Айдар

## 1. Стиль кодирования

### Основные правила

#### Отступы и форматирование

- Отступы: 2 пробела - принято как командный стандарт
- Максимальная ширина строки с автопереносом для улучшения читаемости кода
- Единый стиль форматирования


### Именование

- Переменные/функции: camelCase для поддержания единообразия в кодовой базе
- React-компоненты/классы: PascalCase согласно стандартам React
- Константы: UPPER_SNAKE_CASE
- Используйте осмысленные и описательные имена


### Комментарии

- Язык: английский для международной поддержки кода
- Комментируйте только сложную бизнес-логику для уменьшения информационного шума
- TODO для отслеживания текущих незавершенных задач
- JSDoc для API и хелперов, только для разработки

```typescript
/**
 * Calculates order total with all applicable discounts
 * @param {Order} order - Current order
 * @param {Discount[]} discounts - Available discounts
 * @returns {number} Final price
 */
function calculateOrderTotal(order: Order, discounts: Discount[]): number {
  // TODO: Add support for bundle discounts
  
  // Complex business logic for layered discounts
  const applicableDiscounts = discounts
    .filter(discount => isDiscountValid(order, discount))
    .sort((a, b) => b.priority - a.priority);
    
  // Apply discounts sequentially based on priority
  return applicableDiscounts.reduce((total, discount) => {
    return applyDiscount(total, discount);
  }, order.subtotal);
}
```

## 2. Инструменты и настройки

### ESLint и Prettier

- Автофикс сортировки импортов для улучшения организации кода
- Пробелы между группами импортов для улучшения читаемости
- Стандартные настройки без избыточных правил для баланса между качеством и удобством
- Автозапуск через Husky (pre-commit) для автоматизации проверок

```json
{
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "prettier"
  ],
  "plugins": ["import"],
  "rules": {
    "import/order": ["error", {
      "groups": [
        ["builtin", "external"],
        "internal",
        ["parent", "sibling"],
        "index"
      ],
      "newlines-between": "always"
    }]
  }
}
```

## 3. Рабочий процесс

### Git и PR

- Формат коммитов: Conventional Commits для улучшения отслеживания изменений
- Обязательное описание изменений в PR для лучшего понимания изменений
- Минимум 2 апрува (включая старших разработчиков) для обеспечения качества
- Запрет прямых коммитов в main
- Серверные хуки для проверки формата
- Обязательное прохождение линтеров

```bash
# ✅ Правильно
git commit -m "feat(auth): implement OAuth2 login"
git commit -m "fix(cache): resolve memory leak in user session"
git commit -m "refactor(api): optimize database queries"

# ❌ Неправильно
git commit -m "fix bug"
git commit -m "updates"
git commit -m "wip"
```

### Валидация кода

- Серверные хуки для проверки формата
- Обязательное прохождение линтеров
- Автоматические тесты

```yaml
# .github/workflows/validate.yml
name: Validate
on: [push, pull_request]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Install dependencies
        run: npm ci
      - name: Lint
        run: npm run lint
      - name: Test
        run: npm run test
```

## 4. Критерии качества

### Code Review

- Прохождение всех тестов как обязательное требование
- Чистая архитектура с разделением логики и компонентов
- Оптимизация производительности
- Акцент на читаемость кода, а не поиск ошибок
- Конструктивные обсуждения без холиваров

### Работа с багами

- Критические баги: немедленное исправление
- Некритические: в следующем релизе
- Полное покрытие тестами исправлений
- Ответственные: Автор кода + ревьюверы

## 5. Требования к готовому коду

### Критерии приемки

- Прохождение код-ревью
- Соответствие CodeStyle
- Отсутствие "стыдных" решений
- Покрытие тестами для логики
- Интеграция в общую архитектуру

### Линтинг и форматирование

- ESLint + Prettier
- Pre-commit хуки (Husky)
- Запрет мержа без успешного линтинга

```json
// package.json
{
  "scripts": {
    "lint": "eslint . --ext .ts,.tsx",
    "format": "prettier --write \"src/**/*.{ts,tsx}\"",
    "test": "jest --coverage",
    "prepare": "husky install"
  },
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged",
      "pre-push": "npm test"
    }
  },
  "lint-staged": {
    "*.{ts,tsx}": [
      "eslint --fix",
      "prettier --write"
    ]
  }
}
```
