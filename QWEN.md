# QWEN.md — Контекст проекта Tulip.TrendTeam

## 📋 Обзор проекта

**Tulip.TrendTeam** — это форк/адаптация библиотеки **Tulip.NETCore** (неофициальный .NET Core порт индикаторов Tulip Indicators) для использования в проектах TrendTeamTrading.

### Оригинальный проект

- **Оригинал**: [Tulip.NETCore](https://github.com/hmG3/Tulip.NETCore) — порт на C# библиотеки [Tulip Indicators](https://tulipindicators.org/) (TI)
- **Лицензия**: LGPL-3.0 (бесплатно для использования, в том числе в коммерческих проектах)
- **Статус**: Это форк для интеграции с экосистемой TrendTeam

---

## 🏗️ Архитектура проекта

### Структура каталогов

```
Tulip.TrendTeam/
├── src/
│   └── Tulip.NETCore/          # Исходный код библиотеки
│       ├── Indicators/          # Реализации индикаторов (TI_*.cs)
│       ├── ArrayExtensions.cs   # Расширения для массивов
│       ├── AssemblyInfo.cs      # Информация о сборке
│       ├── GlobalUsings.cs      # Глобальные using-директивы
│       ├── Indicator.cs         # Класс-обёртка индикатора
│       ├── Indicators.cs        # Реестр всех индикаторов
│       ├── Tinet.cs             # Ядро библиотеки
│       └── Tulip.NETCore.csproj # Проект библиотеки
│
├── tests/
│   └── Tulip.NETCore.Tests/    # Юнит-тесты
│       ├── DataSets/            # Тестовые данные (JSON)
│       ├── Models/              # Модели тестовых данных
│       ├── Indicator_Tests.cs   # Тесты индикаторов
│       ├── JsonFileData.cs      # Атрибут загрузки JSON
│       └── Tulip.NETCore.Tests.csproj
│
├── Tulip.sln                    # Solution файл
├── README.md                    # Описание проекта
├── LICENSE                      # Лицензия LGPL-3.0
└── tulip.png                    # Иконка пакета NuGet
```

### Решение (Solution)

| Проект | Путь | Тип |
|--------|------|-----|
| Tulip.NETCore | `src/Tulip.NETCore/Tulip.NETCore.csproj` | Библиотека классов |
| Tulip.NETCore.Tests | `tests/Tulip.NETCore.Tests/Tulip.NETCore.Tests.csproj` | Юнит-тесты |

---

## 🛠️ Технологии и зависимости

### Основные технологии

- **Язык**: C# 12
- **Платформа**: .NET 8.0
- **Nullable reference types**: включены
- **Тестирование**: xUnit + Shouldly + Xunit.SkippableFact

### NuGet зависимости (тесты)

```xml
<PackageReference Include="Microsoft.NET.Test.Sdk" Version="17.9.0" />
<PackageReference Include="xunit" Version="2.8.0" />
<PackageReference Include="Xunit.SkippableFact" Version="1.4.13" />
<PackageReference Include="xunit.runner.visualstudio" Version="2.8.0" />
<PackageReference Include="Shouldly" Version="4.2.1" />
<PackageReference Include="coverlet.msbuild" Version="6.0.2" />
```

---

## 📦 Индикаторы

Библиотека содержит **100+ индикаторов** технического анализа, сгруппированных по категориям:

### Категории индикаторов

| Категория | Примеры |
|-----------|---------|
| **Overlap Studies** | SMA, EMA, WMA, DEMA, TEMA, KAMA, HMA, VMA, Bollinger Bands |
| **Momentum** | RSI, Stochastic, MACD, CCI, MFI, CMO, ROC, Momentum |
| **Volume** | OBV, MFI, VOSC, KVO, EMV, NVI, PVI |
| **Volatility** | ATR, Natr, StdDev, Volatility, CVI |
| **Trend** | ADX, DI, Aroon, Parabolic SAR, Linear Regression |
| **Math Transform** | Sin, Cos, Tan, Log, Exp, Sqrt, Ceil, Floor, Round |
| **Pattern Recognition** | Crossover, Crossany |
| **Price Transform** | Typical Price, Weighted Close, Median Price, Avg Price |

### Полный список индикаторов

См. файл `src/Tulip.NETCore/Indicators.cs` — свойство `IndicatorsDefinition` содержит определения всех 100+ индикаторов.

### Пример использования

```csharp
using Tulip;

// Получить индикатор SMA
var sma = Indicators.sma;

// Подготовка данных
double[] input = { 10, 11, 12, 13, 14, 15, 16, 17, 18, 19 };
double[] options = { 5 }; // период 5
int start = sma.Start<double>(options);
double[][] outputs = [new double[input.Length - start]];

// Запуск расчёта
sma.Run<double>([input], options, outputs);

// outputs[0] содержит значения SMA
```

---

## 🚀 Сборка и запуск

### Требования

- **.NET 8.0 SDK**
- **Visual Studio 2022/2026** или **VS Code**
- **PowerShell 7.5+** (рекомендуется)

### Сборка проекта

```powershell
# Перейти в директорию решения
cd C:\Users\vdv-v\source\repos\Tulip.TrendTeam

# Сборка решения
dotnet build Tulip.sln --configuration Release

# Сборка только библиотеки
dotnet build src/Tulip.NETCore/Tulip.NETCore.csproj --configuration Release
```

### Запуск тестов

```powershell
# Запуск всех тестов
dotnet test Tulip.sln

# Запуск тестов с покрытием
dotnet test --collect:"XPlat Code Coverage"

# Запуск тестов с фильтрацией
dotnet test --filter "FullyQualifiedName~Sma"
```

### Создание NuGet пакета

```powershell
# Упаковка библиотеки
dotnet pack src/Tulip.NETCore/Tulip.NETCore.csproj --configuration Release
```

---

## 🧪 Практики разработки

### Соглашения по коду

- **Nullable reference types**: включены (`<Nullable>enable</Nullable>`)
- **Target Framework**: .NET 8.0
- **Именование**:
  - Классы индикаторов: `TI_{Name}.cs` (например, `TI_Sma.cs`)
  - Методы реализации: `{Name}Start()`, `{Name}()` (например, `SmaStart()`, `Sma()`)
- **Внутренние методы**: `internal` с использованием `Tinet<T>` класса

### Структура индикатора

Каждый индикатор реализуется в отдельном файле `TI_{Name}.cs`:

```csharp
namespace Tulip;

internal static partial class Tinet<T> where T : IFloatingPointIeee754<T>
{
    private static int {Name}Start(T[] options) => ...;

    private static int {Name}(int size, T[][] inputs, T[] options, T[][] outputs)
    {
        // Валидация параметров
        // Расчёт индикатора
        // Возврат TI_OKAY (0) или код ошибки
    }
}
```

### Тестирование

- **Фреймворк**: xUnit
- **Assertion библиотека**: Shouldly
- **Тестовые данные**: JSON файлы в `tests/DataSets/`
  - `atoz.json` — основные тесты
  - `extra.json` — дополнительные тесты
  - `untest.json` — тесты с особыми условиями
- **Поддерживаемые типы**: `double` и `float`

### Покрытие тестами

Тесты используют параметризованные данные из JSON файлов:

```csharp
[SkippableTheory]
[JsonFileData("DataSets/atoz.json", typeof(double), "_")]
public void Should_Calculate_CorrectOutput_With_OKStatus_For_DoubleInput(
    TestDataModel<double> model, string fileName)
{
    // Тестирование корректности расчёта
}
```

---

## 🔧 Интеграция с проектами TrendTeam

### Добавление в решение

```powershell
# Добавить ссылку на Tulip.NETCore в проект
dotnet add reference ../Tulip.TrendTeam/src/Tulip.NETCore/Tulip.NETCore.csproj
```

### Использование в стратегиях

```csharp
using Tulip;

// В стратегии TrendTeamTrading
public class MyStrategy
{
    public void OnBar()
    {
        // Получить индикатор
        var rsi = Indicators.rsi;
        
        // Подготовить данные
        double[] close = GetClosePrices();
        double[] options = [14]; // период RSI
        
        // Рассчитать
        int start = rsi.Start<double>(options);
        double[][] outputs = [new double[close.Length - start]];
        rsi.Run<double>([close], options, outputs);
        
        // Использовать значения
        double currentRsi = outputs[0].Last();
    }
}
```

---

## 📚 Дополнительные ресурсы

### Внешние ресурсы

- **Tulip Indicators Official**: https://tulipindicators.org/
- **Оригинальный репозиторий**: https://github.com/TulipCharts/tulipindicators
- **Tulip.NETCore (оригинал)**: https://github.com/hmG3/Tulip.NETCore
- **NuGet пакет**: https://nuget.org/packages/Tulip.NETCore
- **Документация TA-Lib**: https://ta-lib.org/ (альтернативная библиотека)

### Внутренние ресурсы TrendTeam

- **TaLib.TrendTeam**: `C:\Users\vdv-v\source\repos\TaLib.TrendTeam\` — альтернативная библиотека индикаторов
- **TrendTeamTrading wiki**: `C:\Users\vdv-v\source\repos\wiki\docs\` — документация по индикаторам

---

## 🐛 Отладка и решение проблем

### Частые проблемы

1. **TI_INVALID_OPTION**
   - Проверить корректность параметров (например, период > 0)

2. **Несоответствие длины массивов**
   - Убедиться, что выходные массивы имеют правильную длину: `input.Length - start`

3. **Погрешность float вычислений**
   - Для `float` используется бо́льшая толерантность (0.1f vs 0.001d для double)

### Коды возврата

| Код | Значение |
|-----|----------|
| `TI_OKAY` (0) | Успешное выполнение |
| `TI_INVALID_OPTION` (-1) | Некорректный параметр |
| `TI_OUT_OF_MEMORY` (-2) | Недостаточно памяти |

---

## 📊 Статистика проекта

| Метрика | Значение |
|---------|----------|
| **Индикаторов** | 100+ |
| **Файлов индикаторов** | 100+ (`TI_*.cs`) |
| **Строк кода** | ~10,000+ |
| **Покрытие тестами** | ~95%+ |
| **Target Framework** | .NET 8.0 |
| **Лицензия** | LGPL-3.0 |

---

## 🎯 Типичные сценарии использования

### 1. Добавление нового индикатора

```powershell
# 1. Создать файл TI_{Name}.cs в src/Tulip.NETCore/Indicators/
# 2. Реализовать методы {Name}Start() и {Name}()
# 3. Добавить определение в Indicators.cs (IndicatorsDefinition)
# 4. Добавить статическое свойство в класс Indicators
# 5. Добавить тестовые данные в DataSets/*.json
# 6. Запустить тесты: dotnet test
```

### 2. Модификация существующего индикатора

```powershell
# 1. Найти файл индикатора: TI_{Name}.cs
# 2. Внести изменения в логику расчёта
# 3. Запустить тесты для проверки: dotnet test --filter "FullyQualifiedName~{Name}"
# 4. Обновить документацию при необходимости
```

### 3. Интеграция в стратегию TrendTeamTrading

```powershell
# 1. Добавить ссылку на Tulip.NETCore в проект стратегии
# 2. Импортировать namespace: using Tulip;
# 3. Получить индикатор: var indicator = Indicators.{name};
# 4. Рассчитать значения: indicator.Run(...)
# 5. Использовать в логике торговли
```

---

## 👥 Контакты и поддержка

- **Владелец проекта**: Дмитрий Власов (SystemsCreator)
- **Основное местоположение**: Воронеж, Россия
- **Связь с оригиналом**: Anatoliy Siryi (автор Tulip.NETCore)

---

*Последнее обновление: 8 марта 2026 г.*
