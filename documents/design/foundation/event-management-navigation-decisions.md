# Event Management Flow — Navigation Decisions
_BV360 Admin Panel | Статус: In Progress | Дата: 2026-06-05_

---

## Контекст

Редизайн інструменту створення та редагування спортивних подій в адмін-панелі BV360.

**Користувач:** трейдер / оператор  
**Середовище:** desktop, split-screen — ліворуч дерево навігації, праворуч робоча зона  
**Основний сценарій:** редагування існуючих подій щодня, десятки подій  
**Ієрархія:** Event → Markets (50+) → Outcomes (багато) → Book Details

---

## Ключові обмеження

- Правила збереження на беці незмінні: Event Details → Market → Outcome
- Робоча зона обмежена правою половиною екрану
- Markets на одну подію може бути 50+
- Основний сценарій — редагування, але creation також має працювати

---

## Навігаційна модель

### Три паттерни, чітко розподілені

| Паттерн | Коли використовується |
|---|---|
| **Modal** | Batch операції (Market Creation, Outcome Creation) |
| **Drawer** | Одиничне додавання/редагування без втрати контексту таблиці |
| **Drill-down** | Повне занурення в об'єкт з вкладеним контентом |

---

## Повна навігаційна карта

```
Event (compact header)
│
├── [Edit Details] ──────────────────→ Drawer
│                                       └── Event Details форма (17+ полів)
│
Markets таблиця (50+, search, filter)
│
├── [Batch Market Creation] ──────────→ Modal
│                                        ├── Period dropdown
│                                        ├── Market Types (multiselect + search)
│                                        └── [Generate and save]
│
├── [+ Add Market] ───────────────────→ Drawer (порожня Market форма)
│
└── [клік на Market] ─────────────────→ Drill-down
         │
         ├── Market форма (поля + статус)
         │
         └── Tabs:
              ├── [Outcomes]
              │      ├── [Batch Add] ────→ Modal
              │      │                     ├── Import from clipboard
              │      │                     ├── Import from main market
              │      │                     ├── Import from URL
              │      │                     └── Bulk Description grid
              │      │
              │      ├── [+ Add Outcome] → Drawer (порожня повна форма)
              │      │
              │      ├── inline edit ────→ Singles Only, Description,
              │      │                     Price, Max Stake, Keywords
              │      │
              │      └── [клік Outcome] → Drawer (повна форма)
              │                            └── [Audit Logs] → history view
              │
              └── [Book Details]
                     ├── [+ Add] ────────→ Inline рядок
                     └── edit ───────────→ Inline
```

---

## Creation vs Edit — один UI, два стани

### Creation (новий event)

- Event Details форма відкрита як **головний екран** (не drawer)
- Markets секція видима але **locked** з поясненням: "Збережіть деталі події щоб додати ринки"
- Після `[Save Event Details]` → перехід в Edit mode

### Edit (існуючий event)

- **Compact event header** (ключові поля + статус)
- `[Edit Details]` → відкриває Drawer
- Markets таблиця доступна одразу

### Момент переходу

Перший Save Event Details — природній перехід між режимами. Drawer для Edit Details стає доступним тільки після цього моменту.

---

## Markets таблиця

- Відображає 50+ markets
- Search + Filter (по Status, Period — фільтрація TBD)
- Компактні рядки: Name, Period, Status, кількість Outcomes

**Різниця між Add і Edit Market (навмисна):**
- `[+ Add Market]` → Drawer (бачиш таблицю позаду, не втрачаєш контекст)
- `[клік на Market]` → Drill-down (заходиш в об'єкт щоб серйозно попрацювати)

---

## Outcomes

### Два рівні взаємодії

**Inline в таблиці** (швидке редагування обмеженого набору полів):
- Singles Only
- Description
- Price
- Max Stake
- Keywords

**Drawer** (повна форма, всі 15+ полів):
- Id, FeedCode, Description, Description Template
- Keywords (з delete)
- Prices (nested table: Line Id + Price + Add new price line)
- Ordinal Position, Opponent, Status
- Linked Event IDs, Max Stake
- Hidden, Singles Only, Outcome Key
- Audit Logs (перегляд історії змін по цьому Outcome)

---

## Book Details

- Живе в окремому Tab поряд з Outcomes всередині Market drill-down
- Таблиця: Book, Version, Default Place Terms, Applied Place Terms, Fixed
- Додавання: inline рядок
- Редагування: inline

---

## Відкриті питання

- [ ] Фільтрація/групування Markets: по Period? по Status? обидва?
- [ ] Batch Market Creation — чи є щось крім Period + Types що треба врахувати
- [ ] Creation flow — як часто відносно Edit? (впливає на пріоритети)
- [ ] Максимальна кількість Outcomes на один Market
- [ ] Mobile/responsive — не згадувалось, вважаємо desktop-only

---

## Вимоги зі специфікації — що залишається відкритим для дизайну

- Як саме організувати фільтрацію Markets (search + filter criteria)
- Зрозумілість блокувань при creation: текст пояснення чому locked
- Hide All Outcomes `🟡` — механіка не визначена
- Market Status management (OPEN / SUSPENDED) — де і як змінюється
