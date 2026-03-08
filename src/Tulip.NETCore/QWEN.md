# QWEN.md — Контекст проекта Tulip.NETCore

## 📋 Обзор проекта

**Tulip.NETCore** — это неофициальный порт на C# библиотеки **Tulip Indicators** (TI) для .NET 8. Tulip Indicators — это открытая ANSI C библиотека функций для технического анализа финансовых временных рядов.

### Статус проекта

- **Тип**: Библиотека классов (.NET 8.0)
- **Лицензия**: LGPL-3.0 (бесплатно для использования, в том числе в коммерческих проектах)
- **Авторы**: Anatoliy Siryi (оригинальный порт), Дмитрий Власов (SystemsCreator) — адаптация для TrendTeam
- **NuGet пакет**: [Tulip.NETCore](https://nuget.org/packages/Tulip.NETCore)

### Основные возможности

- **100+ индикаторов** технического анализа
- **Генерическая реализация** с поддержкой типов `double` и `float`
- **Высокая производительность** благодаря использованию `IFloatingPointIeee754<T>`
- **Полная совместимость** с оригинальной библиотекой Tulip Indicators

---

## 🏗️ Архитектура

### Структура каталогов

```
Tulip.NETCore/
├── Indicators/              # Реализации индикаторов (TI_*.cs)
│   ├── TI_Abs.cs           # Vector Absolute Value
│   ├── TI_Sma.cs           # Simple Moving Average
│   ├── TI_Rsi.cs           # Relative Strength Index
│   ├── TI_Macd.cs          # Moving Average Convergence/Divergence
│   └── ...                 # 100+ файлов индикаторов
│
├── Tinet.cs                # Ядро библиотеки (реализация IndicatorRun/IndicatorStart)
├── Indicator.cs            # Класс-обёртка индикатора
├── Indicators.cs           # Реестр всех индикаторов (статический доступ)
├── ArrayExtensions.cs      # Расширения для массивов (Deconstruct)
├── AssemblyInfo.cs         # Информация о сборке (InternalsVisibleTo)
├── GlobalUsings.cs         # Глобальные using-директивы
├── Tulip.NETCore.csproj    # Проект библиотеки
└── QWEN.md                 # Этот файл контекста
```

### Ключевые компоненты

| Файл | Назначение |
|------|------------|
| `Tinet.cs` | Ядро библиотеки: методы `IndicatorRun`, `IndicatorStart`, вспомогательные функции (Buffer, TrueRange, Directional Movement) |
| `Indicator.cs` | Публичный класс для работы с индикаторами: свойства Name, FullName, Inputs, Options, Outputs; методы Run, Start |
| `Indicators.cs` | Реестр всех 100+ индикаторов: словарь `IndicatorsDefinition`, статические свойства для каждого индикатора |
| `TI_*.cs` | Реализация конкретного индикатора: методы `{Name}Start` (расчёт lookback) и `{Name}` (основной расчёт) |

### Паттерн реализации индикатора

Каждый индикатор реализуется в файле `TI_{Name}.cs` по единому шаблону:

```csharp
namespace Tulip;

internal static partial class Tinet<T> where T : IFloatingPointIeee754<T>
{
    private static int {Name}Start(T[] options) => <расчёт lookback>;

    private static int {Name}(int size, T[][] inputs, T[] options, T[][] outputs)
    {
        // 1. Валидация параметров (периоды > 0)
        // 2. Проверка размера данных
        // 3. Основной расчёт индикатора
        // 4. Возврат TI_OKAY (0) или TI_INVALID_OPTION (1)
    }
}
```

### Коды возврата

| Код | Константа | Значение |
|-----|-----------|----------|
| 0 | `TI_OKAY` | Успешное выполнение |
| 1 | `TI_INVALID_OPTION` | Некорректный параметр |

---

## 🛠️ Технологии и зависимости

### Стек технологий

- **Язык**: C# 12
- **Платформа**: .NET 8.0
- **Nullable reference types**: включены
- **Generics**: `IFloatingPointIeee754<T>` для поддержки `double` и `float`

### Зависимости

Библиотека **не имеет внешних зависимостей** — только стандартная библиотека .NET 8.0.

### NuGet метаданные

```xml
<TargetFramework>net8.0</TargetFramework>
<RootNamespace>Tulip</RootNamespace>
<Nullable>enable</Nullable>
<Version>0.9.0</Version>
<Authors>Anatoliy Siryi</Authors>
<Copyright>Tulip Charts LLC</Copyright>
<PackageLicenseFile>LICENSE</PackageLicenseFile>
<PackageProjectUrl>https://tulipindicators.org</PackageProjectUrl>
<RepositoryUrl>https://github.com/hmG3/Tulip.NETCore</RepositoryUrl>
```

---

## 📦 Индикаторы

Библиотека содержит **100+ индикаторов**, сгруппированных по категориям:

### Категории индикаторов

| Категория | Примеры | Кол-во |
|-----------|---------|--------|
| **Overlap Studies** | SMA, EMA, WMA, DEMA, TEMA, KAMA, HMA, VMA, Bollinger Bands | ~15 |
| **Momentum** | RSI, Stochastic, MACD, CCI, MFI, CMO, ROC, Momentum | ~20 |
| **Volume** | OBV, MFI, VOSC, KVO, EMV, NVI, PVI | ~10 |
| **Volatility** | ATR, Natr, StdDev, Volatility, CVI | ~8 |
| **Trend** | ADX, DI, Aroon, Parabolic SAR, Linear Regression | ~15 |
| **Math Transform** | Sin, Cos, Tan, Log, Exp, Sqrt, Ceil, Floor, Round | ~20 |
| **Pattern Recognition** | Crossover, Crossany | 2 |
| **Price Transform** | Typical Price, Weighted Close, Median Price, Avg Price | ~6 |
| **Cycle Indicators** | MESA Sine Wave | 1 |
| **Other** | Decay, Lag, Fisher Transform, VHF, Vidya | ~15 |

### Полный список индикаторов

См. свойство `Indicators.IndicatorsDefinition` в файле `Indicators.cs` — там определены все 100+ индикаторов.

### Примеры использования

#### Простой пример (SMA)

```csharp
using Tulip;

// Получить индикатор
var sma = Indicators.sma;

// Подготовить данные
double[] input = { 10, 11, 12, 13, 14, 15, 16, 17, 18, 19 };
double[] options = { 5 }; // период 5

// Рассчитать lookback (сколько элементов нужно пропустить в начале)
int start = sma.Start<double>(options);

// Создать выходной массив
double[][] outputs = [new double[input.Length - start]];

// Запустить расчёт
sma.Run<double>([input], options, outputs);

// outputs[0] содержит значения SMA
```

#### Пример с MACD (несколько выходов)

```csharp
using Tulip;

var macd = Indicators.macd;
double[] close = GetClosePrices();
double[] options = { 12, 26, 9 }; // short, long, signal

int start = macd.Start<double>(options);
double[][] outputs = 
[
    new double[close.Length - start], // macd
    new double[close.Length - start], // signal
    new double[close.Length - start]  // histogram
];

macd.Run<double>([close], options, outputs);

var (macdLine, signalLine, histogram) = outputs;
```

#### Пример с Aroon (несколько входов)

```csharp
using Tulip;

var aroon = Indicators.aroon;
double[] high = GetHighPrices();
double[] low = GetLowPrices();
double[] options = { 14 }; // период

int start = aroon.Start<double>(options);
double[][] outputs = 
[
    new double[high.Length - start], // aroon_down
    new double[high.Length - start]  // aroon_up
];

aroon.Run<double>([high, low], options, outputs);

var (aroonDown, aroonUp) = outputs;
```

---

## 🚀 Сборка и запуск

### Требования

- **.NET 8.0 SDK**
- **Visual Studio 2022/2026** или **VS Code**
- **PowerShell 7.5+** (рекомендуется)

### Сборка проекта

```powershell
# Перейти в директорию проекта
cd C:\Users\vdv-v\source\repos\Tulip.TrendTeam\src\Tulip.NETCore

# Сборка проекта
dotnet build Tulip.NETCore.csproj --configuration Release

# Сборка решения (из корня Tulip.TrendTeam)
cd C:\Users\vdv-v\source\repos\Tulip.TrendTeam
dotnet build Tulip.sln --configuration Release
```

### Запуск тестов

```powershell
# Перейти в директорию с тестами
cd C:\Users\vdv-v\source\repos\Tulip.TrendTeam\tests\Tulip.NETCore.Tests

# Запуск всех тестов
dotnet test

# Запуск с покрытием
dotnet test --collect:"XPlat Code Coverage"

# Запуск с фильтрацией по имени индикатора
dotnet test --filter "FullyQualifiedName~Sma"
```

### Создание NuGet пакета

```powershell
# Упаковка библиотеки
cd C:\Users\vdv-v\source\repos\Tulip.TrendTeam\src\Tulip.NETCore
dotnet pack Tulip.NETCore.csproj --configuration Release

# Пакет будет создан в bin/Release/
```

---

## 🧪 Практики разработки

### Соглашения по коду

- **Nullable reference types**: включены (`<Nullable>enable</Nullable>`)
- **Target Framework**: .NET 8.0
- **Именование файлов**: `TI_{Name}.cs` (например, `TI_Sma.cs`, `TI_Rsi.cs`)
- **Именование методов**: `{Name}Start()`, `{Name}()` (например, `SmaStart()`, `Sma()`)
- **Внутренние методы**: `internal` с использованием `Tinet<T>` класса
- **Валидация параметров**: всегда проверять `period > 0` и возвращать `TI_INVALID_OPTION` при ошибке

### Структура индикатора

```csharp
namespace Tulip;

internal static partial class Tinet<T> where T : IFloatingPointIeee754<T>
{
    /// <summary>
    /// Расчёт lookback — сколько элементов нужно пропустить в начале
    /// </summary>
    private static int {Name}Start(T[] options) => Int32.CreateTruncating(options[0]) - 1;

    /// <summary>
    /// Основной расчёт индикатора
    /// </summary>
    private static int {Name}(int size, T[][] inputs, T[] options, T[][] outputs)
    {
        // 1. Извлечение параметров из options
        var period = Int32.CreateTruncating(options[0]);

        // 2. Валидация параметров
        if (period < 1)
        {
            return TI_INVALID_OPTION;
        }

        // 3. Проверка размера данных
        if (size <= {Name}Start(options))
        {
            return TI_OKAY;
        }

        // 4. Извлечение входных/выходных массивов
        var input = inputs[0];
        var output = outputs[0];

        // 5. Основной расчёт
        // ...

        return TI_OKAY;
    }
}
```

### Вспомогательные функции в Tinet.cs

| Функция | Назначение |
|---------|------------|
| `BufferFactory`, `BufferPush`, `BufferQPush`, `BufferGet` | Работа с циклическим буфером |
| `CalcTrueRange` | Расчёт истинного диапазона (ATR) |
| `CalcDirection` | Расчёт направленного движения (DMI) |
| `Simple1`, `Simple2` | Упрощённые функции для векторных операций |

### Тестирование

- **Фреймворк**: xUnit
- **Assertion библиотека**: Shouldly
- **Тестовые данные**: JSON файлы в `tests/DataSets/`
  - `atoz.json` — основные тесты
  - `extra.json` — дополнительные тесты
  - `untest.json` — тесты с особыми условиями
- **Поддерживаемые типы**: `double` и `float`

---

## 🔧 Интеграция с проектами TrendTeam

### Добавление ссылки на проект

```powershell
# В проекте TrendTeamTrading добавить ссылку
cd C:\Users\vdv-v\source\repos\TrendTeamTrading\Strategies
dotnet add reference ../../Tulip.TrendTeam/src/Tulip.NETCore/Tulip.NETCore.csproj
```

### Использование в стратегиях

```csharp
using Tulip;

public class MyTrendStrategy
{
    private double[] _closePrices = new double[100];
    
    public void OnBar()
    {
        // Пример 1: RSI для поиска перекупленности/перепроданности
        var rsi = Indicators.rsi;
        double[] rsiOptions = [14];
        int rsiStart = rsi.Start<double>(rsiOptions);
        double[][] rsiOutputs = [new double[_closePrices.Length - rsiStart]];
        rsi.Run<double>(_closePrices, rsiOptions, rsiOutputs);
        double currentRsi = rsiOutputs[0].Last();
        
        if (currentRsi > 70)
        {
            // Сигнал на продажу
        }
        else if (currentRsi < 30)
        {
            // Сигнал на покупку
        }
        
        // Пример 2: MACD для тренда
        var macd = Indicators.macd;
        double[] macdOptions = [12, 26, 9];
        int macdStart = macd.Start<double>(macdOptions);
        double[][] macdOutputs = 
        [
            new double[_closePrices.Length - macdStart],
            new double[_closePrices.Length - macdStart],
            new double[_closePrices.Length - macdStart]
        ];
        macd.Run<double>(_closePrices, macdOptions, macdOutputs);
        var (macdLine, signalLine, histogram) = macdOutputs;
    }
}
```

### Сравнение с TaLib.TrendTeam

| Характеристика | Tulip.NETCore | TaLib.TrendTeam |
|----------------|---------------|-----------------|
| **Происхождение** | Порт Tulip Indicators (C) | Порт TA-Lib (C) |
| **Индикаторов** | 100+ | 150+ |
| **Стиль API** | `Indicators.sma.Run(...)` | `SMA.Calculate(...)` |
| **Генерики** | `IFloatingPointIeee754<T>` | Отдельные классы для double/float |
| **Лицензия** | LGPL-3.0 | BSD-style |

---

## 🐛 Отладка и решение проблем

### Частые проблемы

1. **TI_INVALID_OPTION (код 1)**
   - Проверить корректность параметров (период > 0, short < long для MACD)
   - Убедиться, что массивы имеют правильную длину

2. **Несоответствие длины массивов**
   - Выходные массивы должны иметь длину: `input.Length - start`
   - Для индикаторов с несколькими выходами все массивы одинаковой длины

3. **Погрешность float вычислений**
   - Для `float` используется бо́льшая толерантность (0.1f vs 0.001d для double)
   - При сравнении результатов использовать `ShouldBeApproximately()`

4. **MissingMethodException**
   - Проверить, что метод `{Name}Start` и `{Name}` реализованы в `Tinet<T>`
   - Убедиться, что имя файла совпадает с именем метода (TI_Sma.cs → Sma)

### Отладка расчётов

```csharp
// Включить подробную отладку
var sma = Indicators.sma;
double[] options = { 5 };
int start = sma.Start<double>(options);
Console.WriteLine($"SMA lookback: {start}"); // Ожидается: 4

// Проверить длину выходного массива
Console.WriteLine($"Output length: {input.Length - start}");
```

---

## 📚 Дополнительные ресурсы

### Внешние ресурсы

- **Tulip Indicators Official**: https://tulipindicators.org/
- **Официальный репозиторий**: https://github.com/TulipCharts/tulipindicators
- **Tulip.NETCore (оригинал)**: https://github.com/hmG3/Tulip.NETCore
- **NuGet пакет**: https://nuget.org/packages/Tulip.NETCore
- **Документация по индикаторам**: https://tulipindicators.org/indicators

### Внутренние ресурсы TrendTeam

- **TaLib.TrendTeam**: `C:\Users\vdv-v\source\repos\TaLib.TrendTeam\` — альтернативная библиотека
- **TrendTeamTrading wiki**: `C:\Users\vdv-v\source\repos\wiki\docs\` — документация
- **Tulip.TrendTeam QWEN.md**: `C:\Users\vdv-v\source\repos\Tulip.TrendTeam\QWEN.md` — контекст проекта

---

## 🎯 Типичные сценарии использования

### 1. Добавление нового индикатора

```powershell
# 1. Создать файл TI_{Name}.cs в Indicators/
# 2. Реализовать методы {Name}Start() и {Name}()
# 3. Добавить определение в Indicators.cs:
#    - В IndicatorsDefinition (словарь)
#    - Добавить статическое свойство public static Indicator {name}
# 4. Добавить тестовые данные в tests/DataSets/*.json
# 5. Запустить тесты: dotnet test
```

### 2. Модификация существующего индикатора

```powershell
# 1. Найти файл индикатора: TI_{Name}.cs
# 2. Внести изменения в логику расчёта
# 3. Запустить тесты для проверки:
#    dotnet test --filter "FullyQualifiedName~{Name}"
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

### 4. Сравнение результатов с TA-Lib

```csharp
// Рассчитать один и тот же индикатор в обеих библиотеках
var tulipRsi = Indicators.rsi.Run<double>(close, [14], ...);
var talibRsi = RSI.Calculate(close, 14);

// Сравнить результаты (допустимая погрешность 0.001)
tulipRsi.ShouldBeApproximately(talibRsi, 0.001);
```

---

## 📊 Статистика проекта

| Метрика | Значение |
|---------|----------|
| **Индикаторов** | 100+ |
| **Файлов индикаторов** | 100+ (`TI_*.cs`) |
| **Строк кода** | ~10,000+ |
| **Target Framework** | .NET 8.0 |
| **Лицензия** | LGPL-3.0 |
| **NuGet версия** | 0.9.0 |
| **Поддерживаемые типы** | `double`, `float` |

---

## 👥 Контакты и поддержка

- **Владелец проекта**: Дмитрий Власов (SystemsCreator)
- **Основное местоположение**: Воронеж, Россия
- **Связь с оригиналом**: Anatoliy Siryi (автор Tulip.NETCore)
- **GitHub**: https://github.com/hmG3/Tulip.NETCore

---

*Последнее обновление: 8 марта 2026 г.*
