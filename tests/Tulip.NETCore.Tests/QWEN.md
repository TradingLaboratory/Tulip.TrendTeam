# QWEN.md — Контекст проекта Tulip.NETCore.Tests

## 📋 Обзор проекта

**Tulip.NETCore.Tests** — это проект юнит-тестов для библиотеки технического анализа **Tulip.NETCore** (часть проекта Tulip.TrendTeam).

### Назначение

Проект содержит автоматизированные тесты для проверки корректности расчёта 100+ индикаторов технического анализа Tulip Indicators. Тесты используют эталонные данные из JSON-файлов и сравнивают результаты вычислений с ожидаемыми значениями.

---

## 🏗️ Архитектура проекта

### Структура каталогов

```
Tulip.NETCore.Tests/
├── DataSets/              # Тестовые данные (JSON)
│   ├── atoz.json          # Основные тесты из книги "Technical Analysis from A to Z"
│   ├── extra.json         # Дополнительные тесты
│   ├── untest.json        # Тесты с особыми условиями
│   └── testdata.schema.json  # JSON Schema для валидации данных
│
├── Models/                # Модели данных
│   ├── TestDataModel.cs   # Модель тестовых данных (generic)
│   └── JsonExtensions.cs  # Расширения для работы с JSON
│
├── AssemblyInfo.cs        # Атрибуты сборки
├── Indicator_Tests.cs     # Основные тесты индикаторов
├── JsonFileDataAttribute.cs  # Атрибут загрузки данных из JSON
├── Tulip.NETCore.Tests.csproj  # Файл проекта
└── QWEN.md                # Документация проекта
```

---

## 🛠️ Технологии и зависимости

### Основные технологии

| Технология | Версия | Назначение |
|------------|--------|------------|
| **.NET** | 8.0 | Платформа |
| **C#** | 12 | Язык программирования |
| **xUnit** | 2.8.0 | Фреймворк для тестирования |
| **Shouldly** | 4.2.1 | Assertion библиотека |
| **Xunit.SkippableFact** | 1.4.13 | Поддержка пропуска тестов |
| **coverlet.msbuild** | 6.0.2 | Покрытие кода тестами |

### Зависимости проекта

```xml
<ProjectReference Include="..\..\src\Tulip.NETCore\Tulip.NETCore.csproj" />
```

Проект ссылается на основную библиотеку `Tulip.NETCore`.

---

## 🚀 Запуск тестов

### Требования

- **.NET 8.0 SDK**
- **Visual Studio 2022/2026** или **VS Code**
- **PowerShell 7.5+** (рекомендуется)

### Команды для тестирования

```powershell
# Перейти в директорию решения
cd C:\Users\vdv-v\source\repos\Tulip.TrendTeam

# Запуск всех тестов
dotnet test Tulip.sln

# Запуск только тестов Tulip.NETCore.Tests
dotnet test tests/Tulip.NETCore.Tests/Tulip.NETCore.Tests.csproj

# Запуск с покрытием кода
dotnet test --collect:"XPlat Code Coverage"

# Запуск с фильтрацией по имени теста
dotnet test --filter "FullyQualifiedName~Sma"

# Запуск в режиме Debug
dotnet test --configuration Debug

# Запуск в режиме Release
dotnet test --configuration Release
```

### Запуск из Visual Studio

1. Откройте `Tulip.sln` в Visual Studio
2. Откройте **Test Explorer** (Ctrl+E, T)
3. Выберите тесты и нажмите **Run**

---

## 📦 Тестовые данные

### Формат данных

Тестовые данные хранятся в JSON-файлах и соответствуют схеме `testdata.schema.json`:

```json
{
  "$schema": "./testdata.schema.json",
  "_": [
    {
      "name": "sma",
      "inputs": [[10, 11, 12, 13, 14, 15, 16, 17, 18, 19]],
      "options": [5],
      "outputs": [[12, 13, 14, 15, 16, 17, 18, 19]],
      "skip": false
    }
  ]
}
```

### Поля тестовых данных

| Поле | Тип | Описание |
|------|-----|----------|
| `name` | string | Уникальное имя индикатора (например, "sma", "rsi", "macd") |
| `inputs` | array of arrays | Входные данные (цены, объёмы и т.д.) |
| `options` | array | Параметры индикатора (периоды и т.п.) |
| `outputs` | array of arrays | Ожидаемые выходные значения |
| `skip` | boolean | Флаг пропуска теста (по умолчанию false) |

### Источники данных

| Файл | Источник | Описание |
|------|----------|----------|
| `atoz.json` | "Technical Analysis from A to Z, Achelis" | Основные тесты из классической книги |
| `extra.json` | Дополнительные источники | Расширенные тесты |
| `untest.json` | Различные источники | Тесты с особыми условиями |

---

## 🧪 Структура тестов

### Основные классы тестов

#### `Indicator_Tests.cs`

Содержит два основных параметризованных теста:

1. **`Should_Calculate_CorrectOutput_With_OKStatus_For_DoubleInput`**
   - Тестирует расчёт индикаторов с типом данных `double`
   - Толерантность сравнения: `0.001d`
   - Источники данных: `atoz.json`, `extra.json`, `untest.json`

2. **`Should_Calculate_CorrectOutput_With_OKStatus_For_FloatInput`**
   - Тестирует расчёт индикаторов с типом данных `float`
   - Толерантность сравнения: `0.1f`
   - Особые условия: некоторые тесты пропускаются из-за недостаточной точности `float`

### Логика теста

```
1. Загрузка тестовых данных из JSON
2. Поиск индикатора по имени в реестре IndicatorsDefinition
3. Проверка соответствия количества параметров (options)
4. Проверка соответствия количества входов (inputs)
5. Расчёт начального смещения (Start)
6. Создание выходных массивов правильной длины
7. Запуск расчёта индикатора (Run)
8. Проверка кода возврата (должен быть 0 = TI_OKAY)
9. Сравнение результатов с эталонными значениями
```

### JsonFileDataAttribute

Пользовательский атрибут xUnit для загрузки данных из JSON:

```csharp
[SkippableTheory]
[JsonFileData("DataSets/atoz.json", typeof(double), "_")]
public void Should_Calculate_CorrectOutput_With_OKStatus_For_DoubleInput(
    TestDataModel<double> model, string fileName)
```

**Параметры:**
- `filePath` — путь к JSON-файлу
- `targetModelType` — тип данных (`double` или `float`)
- `propertyName` — имя свойства в JSON (по умолчанию "_")

---

## 📝 Модели данных

### `TestDataModel<T>`

Generic-модель для представления тестовых данных:

```csharp
public class TestDataModel<T> where T : IFloatingPointIeee754<T>
{
    public string Name { get; set; } = null!;      // Имя индикатора
    public T[][] Inputs { get; set; } = null!;     // Входные данные
    public T[] Options { get; set; } = [];         // Параметры
    public T[][] Outputs { get; set; } = null!;    // Ожидаемые выходы
    public bool Skip { get; set; }                 // Пропустить тест
}
```

**Ограничение типа:** `T : IFloatingPointIeee754<T>` — поддерживает только типы с плавающей точкой (`double`, `float`).

### `JsonExtensions.cs`

Расширения для конвертации `JsonElement` и `JsonDocument` в объекты:

- `ToObject<T>()` — десериализация в указанный тип
- `ToObject(Type)` — десериализация в тип, указанный во время выполнения

---

## 🔧 Практики тестирования

### Соглашения

1. **Именование тестов**: следует паттерну `Should_{ExpectedBehavior}_{Condition}`
2. **Parameterized tests**: используются `SkippableTheory` + `JsonFileData` для загрузки данных
3. **Толерантность**: разные значения для `double` (0.001) и `float` (0.1) из-за точности вычислений
4. **Пропуск тестов**: некоторые тесты помечаются как `Skip` из-за известных проблем (например, недостаточная точность `float`)

### Покрытие тестами

- **Целевое покрытие**: ~95%+ кода библиотеки
- **Инструмент**: coverlet.msbuild
- **Отчёт**: генерируется в формате Cobertura/XML

```powershell
# Запуск с покрытием
dotnet test --collect:"XPlat Code Coverage"

# Просмотр отчёта (требуется reportgenerator)
reportgenerator -reports:coverage.cobertura.xml -targetdir:coverage-report
```

### Особые случаи

Некоторые тесты требуют специального обращения:

```csharp
Skip.If((fileName == "untest.json" && model.Name is "ad" or "adosc") || model.Name is "kvo",
    "The precision of floating-point arithmetic is insufficient for calculating accurate results.");
```

**Индикаторы с проблемами точности:**
- `ad` (Accumulation/Distribution)
- `adosc` (Accumulation/Distribution Oscillator)
- `kvo` (Klinger Volume Oscillator)

---

## 🐛 Отладка тестов

### Частые проблемы

1. **Несоответствие длины массивов**
   ```
   Expected and calculated length of the output values should be equal
   ```
   **Решение:** Проверить расчёт `Start` и длину выходных массивов

2. **Превышение погрешности**
   ```
   Calculated values should be within expected
   ```
   **Решение:** Проверить корректность алгоритма индикатора или увеличить толерантность

3. **Индикатор не найден**
   ```
   Cannot find definition for '{Name}'
   ```
   **Решение:** Добавить определение в `Indicators.cs`

4. **Файл данных не найден**
   ```
   Could not find file at path {path}
   ```
   **Решение:** Проверить, что файлы скопированы в output directory (свойство `CopyToOutputDirectory`)

### Отладка в Visual Studio

1. Установите точку останова в `Indicator_Tests.cs`
2. Запустите тест через **Test Explorer** с опцией **Debug**
3. Используйте **Watch Window** для просмотра значений переменных

---

## 📊 Статистика проекта

| Метрика | Значение |
|---------|----------|
| **Тестовых файлов** | 3 JSON (atoz, extra, untest) |
| **Тестовых случаев** | 100+ (по одному на каждый индикатор × 2 типа данных) |
| **Фреймворк** | xUnit 2.8.0 |
| **Assertion библиотека** | Shouldly 4.2.1 |
| **Поддерживаемые типы** | `double`, `float` |
| **Target Framework** | .NET 8.0 |

---

## 🎯 Типичные сценарии использования

### 1. Добавление теста для нового индикатора

```powershell
# 1. Добавить тестовые данные в один из JSON-файлов
{
  "name": "newindicator",
  "inputs": [[...]],
  "options": [...],
  "outputs": [[...]],
  "skip": false
}

# 2. Запустить тесты
dotnet test --filter "FullyQualifiedName~newindicator"

# 3. Проверить результат
```

### 2. Исследование причины падения теста

```powershell
# 1. Запустить конкретный тест
dotnet test --filter "FullyQualifiedName~rsi" --logger "console;verbosity=detailed"

# 2. Открыть файл данных и проверить значения
# 3. Отладить в Visual Studio
```

### 3. Проверка покрытия тестами

```powershell
# Запуск с покрытием
dotnet test --collect:"XPlat Code Coverage"

# Анализ отчёта
# Отчёт находится в: TestResults/*/coverage.cobertura.xml
```

---

## 🔗 Связанные проекты

| Проект | Описание |
|--------|----------|
| **Tulip.NETCore** | Основная библиотека индикаторов |
| **Tulip.TrendTeam** | Решение всего проекта |
| **TaLib.TrendTeam** | Альтернативная библиотека индикаторов (TA-Lib) |
| **TrendTeamTrading** | Торговые стратегии, использующие индикаторы |

---

## 📚 Дополнительные ресурсы

### Внутренние документы

- **Схема данных:** `DataSets/testdata.schema.json`
- **Основная библиотека:** `../../src/Tulip.NETCore/`
- **Документация Tulip:** `../../README.md`

### Внешние ресурсы

- **Tulip Indicators Official:** https://tulipindicators.org/
- **xUnit Documentation:** https://xunit.net/
- **Shouldly Documentation:** https://shouldly.readthedocs.io/
- **Technical Analysis from A to Z:** Achelis (источник тестовых данных)

---

*Последнее обновление: 8 марта 2026 г.*
