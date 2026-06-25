# 📅 Date Library Project

A lightweight, educational C++ header-only library for date and time-period management.  
Built as part of a structured C++ learning curriculum, this project covers a wide range of real-world date operations — from simple formatting to business-day calculations and period-overlap detection.

---

## 📖 Overview

This library provides two cooperating classes:

| Class | Purpose |
|---|---|
| `clsDate` | Represents a single calendar date and exposes a rich set of operations on it |
| `clsPeriod` | Represents a date range (start → end) and uses `clsDate` to reason about overlaps, lengths, and membership |

Together, they form a practical toolkit that covers the kinds of date problems you encounter in real applications: scheduling, vacation planning, age calculation, calendar rendering, and more.

> **Audience:** Beginner and intermediate C++ students looking to study well-structured OOP design and date arithmetic.

---

## ✨ Features

- 🗓️ Multiple ways to construct a date (today, from string, from day/month/year, from day-order-in-year)
- ✅ Date validation with leap year awareness
- 🖨️ Flexible date formatting (custom format strings and enum-based formats)
- 🔁 Full date arithmetic — add or subtract days, weeks, months, years, decades, centuries, and millennia
- 📊 Date comparison with a clean `enDateCompare` enum (`Before`, `Equal`, `After`)
- 📅 Calendar printing — monthly and full-year views
- ⏱️ Time unit conversions — hours, minutes, and seconds in a day, month, or year
- 💼 Business-day and vacation-day calculations
- 📆 Day-of-week detection, week-end checking, and time-until-end-of-week/month/year
- 🗂️ Period creation, overlap detection, length calculation, and date-in-period checks

---

## 📂 Project Structure

```
DateLibraryProject/
├── clsDate.h      # Core date class — all date logic lives here
├── clsPeriod.h    # Period class — wraps two clsDate objects
└── clsString.h    # External string utility (required dependency)
```

> **Note:** This is a header-only library. No `.cpp` files or build system are needed. Simply `#include` the headers into your project.

---

## 🔧 Dependencies

| Dependency | Role |
|---|---|
| `clsString.h` | Provides `Split()` and `ReplaceWord()` used inside `clsDate` |
| C++ Standard Library | `<iostream>`, `<string>`, `<vector>`, `<ctime>` |

> ⚠️ **Compiler note:** `clsDate.h` uses `__declspec(property(...))` — a Microsoft-specific extension. This works out of the box on **MSVC** (Visual Studio). GCC/Clang users will need to replace property accessors with regular getter/setter calls.

---

## 🗓️ `clsDate` — Implemented Operations

### 1. Date Construction

`clsDate` offers four constructors so you can create a date in whichever way suits your context:

```cpp
clsDate d1;                          // Today's date (from system clock)
clsDate d2("25/6/2026");            // Parse from "DD/MM/YYYY" string
clsDate d3(25, 6, 2026);            // Explicit day, month, year
clsDate d4(177, 2026);              // 177th day of 2026
```

---

### 2. Properties (Getters & Setters)

Day, Month, and Year are exposed as MSVC properties, so they read and write like plain fields while still going through `GetDay`/`SetDay` etc. under the hood:

```cpp
clsDate d(25, 6, 2026);
cout << d.Day;    // 25
d.Day = 30;       // calls SetDay(30)
```

| Property | Getter | Setter |
|---|---|---|
| `Day` | `GetDay()` | `SetDay(short)` |
| `Month` | `GetMonth()` | `SetMonth(short)` |
| `Year` | `GetYear()` | `SetYear(short)` |

---

### 3. System Date & Time

```cpp
clsDate today = clsDate::GetSystemDate();       // returns today as clsDate
string now    = clsDate::GetSystemDateTimeString(); // "25/6/2026 - 14:30:00"
```

---

### 4. Date Validation

```cpp
clsDate d(31, 2, 2026);
bool ok = clsDate::IsValidDate(d);  // false — Feb has no 31st
bool ok2 = d.IsValid();             // same check on the object itself
```

Validation accounts for leap years and the correct number of days in every month.

---

### 5. Date Formatting & Output

#### Simple string conversion

```cpp
string s = clsDate::DateToString(d);  // "25/6/2026"
string s2 = d.DateToString();         // same, on the object
d.Print();                            // prints to console
```

#### Custom format string

```cpp
string s = clsDate::FormatDate(d, "yyyy-mm-dd");  // "2026-6-25"
```

The format tokens are `dd`, `mm`, and `yyyy`. Any surrounding characters are preserved.

#### Enum-based format

```cpp
// enDateFormat: DayMonthYear | YearDayMonth | MonthDayYear | LabeledDate
clsDate::PrintFormattedDate(d, clsDate::DayMonthYear, "-");  // 25-6-2026
string s = clsDate::FormatDateUsingEnums(d, clsDate::LabeledDate); // "Day:25, Month:6, Year:2026"
```

#### Parse a string back to a date

```cpp
clsDate d = clsDate::StringToDate("25/6/2026");
```

---

### 6. Leap Year Utilities

```cpp
bool leap = clsDate::isLeapYear(2024);  // true
bool leap2 = d.isLeapYear();            // checks _Year of the object
```

The rule implemented: divisible by 4 **and** not by 100, **or** divisible by 400.

---

### 7. Days, Hours, Minutes & Seconds in a Year

```cpp
short days    = clsDate::NumberOfDaysInAYear(2024);   // 366
short hours   = clsDate::NumberOfHoursInAYear(2024);  // 8784
int   minutes = clsDate::NumberOfMinutesInAYear(2024);
int   seconds = clsDate::NumberOfSecondsInAYear(2024);

clsDate::PrintYearInfo(2026);   // prints all four values at once
d.PrintYearInfo();              // same, using d's year
```

---

### 8. Days, Hours, Minutes & Seconds in a Month

```cpp
short days    = clsDate::NumberOfDaysInAMonth(2, 2024);   // 29 (leap)
short hours   = clsDate::NumberOfHoursInAMonth(2, 2024);
int   minutes = clsDate::NumberOfMinutesInAMonth(2, 2024);
int   seconds = clsDate::NumberOfSecondsInAMonth(2, 2024);

d.NumberOfDaysInAMonth();   // uses d's month and year
```

---

### 9. Day-of-Week Utilities

```cpp
short order = clsDate::DayOfWeekOrder(25, 6, 2026);  // 0=Sun … 6=Sat
short order2 = d.DayOfWeekOrder();

string name = clsDate::DayShortName(order);           // "Thu"
string name2 = clsDate::DayShortName(25, 6, 2026);   // "Thu"
string name3 = d.DayShortName();

string month = clsDate::MonthShortName(6);            // "Jun"
string month2 = d.MonthShortName();
```

---

### 10. Calendar Printing

```cpp
clsDate::PrintMonthCalendar(6, 2026);  // prints June 2026
d.PrintMonthCalendar();

clsDate::PrintYearCalendar(2026);      // prints all 12 months
d.PrintYearCalendar();
```

Output is a plain-text calendar aligned by day-of-week columns.

---

### 11. Day Order in Year

```cpp
// How many days have passed since Jan 1?
short order = clsDate::DaysFromTheBeginingOfTheYear(25, 6, 2026);  // 176
short order2 = d.DaysFromTheBeginingOfTheYear();

// Reverse: get a date from its order number in a year
clsDate d2 = clsDate::GetDateFromDayOrderInYear(176, 2026); // 25/6/2026
```

---

### 12. Date Comparison

```cpp
bool before = clsDate::IsDate1BeforeDate2(d1, d2);
bool equal  = clsDate::IsDate1EqualDate2(d1, d2);
bool after  = clsDate::IsDate1AfterDate2(d1, d2);

// Also available as object methods:
d1.IsDateBeforeDate2(d2);
d1.IsDateEqualDate2(d2);
d1.IsDateAfterDate2(d2);

// Three-way comparison with an enum result:
clsDate::enDateCompare result = clsDate::CompareDates(d1, d2);
// result == clsDate::Before | clsDate::Equal | clsDate::After
```

---

### 13. Date Arithmetic — Increasing a Date

Every "increase" operation comes in two forms: a `static` version that takes and returns a date, and an object method that modifies the object in place.

| Method | Effect |
|---|---|
| `AddOneDay` / `AddDays` | Advance by 1 day or N days |
| `IncreaseDateByOneWeek` / `IncreaseDateByXWeeks` | Advance by 1 or N weeks |
| `IncreaseDateByOneMonth` / `IncreaseDateByXMonths` | Advance by 1 or N months |
| `IncreaseDateByOneYear` / `IncreaseDateByXYears` | Advance by 1 or N years |
| `IncreaseDateByOneDecade` / `IncreaseDateByXDecades` | Advance by 10 or N×10 years |
| `IncreaseDateByOneCentury` | Advance by 100 years |
| `IncreaseDateByOneMillennium` | Advance by 1000 years |

> **Month-boundary safety:** When adding months, if the resulting day would exceed the target month's length (e.g., January 31 + 1 month), the day is clamped to the last valid day of the new month.

---

### 14. Date Arithmetic — Decreasing a Date

Mirror of the increase methods, going backwards:

| Method | Effect |
|---|---|
| `DecreaseDateByOneDay` / `DecreaseDateByXDays` | Go back 1 or N days |
| `DecreaseDateByOneWeek` / `DecreaseDateByXWeeks` | Go back 1 or N weeks |
| `DecreaseDateByOneMonth` / `DecreaseDateByXMonths` | Go back 1 or N months |
| `DecreaseDateByOneYear` / `DecreaseDateByXYears` | Go back 1 or N years |
| `DecreaseDateByOneDecade` / `DecreaseDateByXDecades` | Go back 10 or N×10 years |
| `DecreaseDateByOneCentury` | Go back 100 years |
| `DecreaseDateByOneMillennium` | Go back 1000 years |

---

### 15. Date Difference

```cpp
int diff = clsDate::GetDifferenceInDays(d1, d2);           // exclusive end
int diff2 = clsDate::GetDifferenceInDays(d1, d2, true);    // inclusive end
int diff3 = d1.GetDifferenceInDays(d2);

// If d1 is after d2, the result is negative (handled automatically via an internal swap flag).
```

---

### 16. Age Calculation

```cpp
clsDate dob(1, 1, 2000);
short ageInDays = clsDate::CalculateAgeInDays(dob);  // days from dob to today
```

---

### 17. Week & Business Day Utilities

```cpp
bool isWeekend  = clsDate::IsWeekEnd(d);    // Friday or Saturday
bool isBizDay   = clsDate::IsBusinessDay(d); // not a weekend
bool isEndOfWk  = clsDate::IsEndOfWeek(d);  // Saturday specifically

short daysLeft  = clsDate::DaysUntilTheEndOfWeek(d);   // days until Saturday
short daysLeft2 = clsDate::DaysUntilTheEndOfMonth(d);  // days until last of month
short daysLeft3 = clsDate::DaysUntilTheEndOfYear(d);   // days until Dec 31

short bizDays   = clsDate::CalculateBusinessDays(d1, d2);
short vacDays   = clsDate::CalculateVacationDays(d1, d2);

clsDate returnDate = clsDate::CalculateVacationReturnDate(startDate, 14); // 14 vacation days
```

> **Weekend definition used:** Friday = 5, Saturday = 6 (common in Middle Eastern work calendars).

---

### 18. Miscellaneous

```cpp
clsDate::SwapDates(d1, d2);         // swap two dates by reference
bool isLast = clsDate::IsLastDayInMonth(d);   // true if d is the last day of its month
bool isLastM = clsDate::IsLastMonthInYear(12); // true if month == 12
d.AddDays(45);                      // add 45 days in-place, crossing month/year boundaries
```

---

## 📆 `clsPeriod` — Implemented Operations

`clsPeriod` holds a `StartDate` and an `EndDate` (both `clsDate` objects) and builds higher-level logic on top of `clsDate`.

### 1. Period Construction

```cpp
clsDate start(1, 6, 2026);
clsDate end(30, 6, 2026);
clsPeriod june(start, end);
```

---

### 2. Period Printing

```cpp
june.Print();
// Period Start: 1/6/2026
// Period End:   30/6/2026
```

---

### 3. Overlap Detection

```cpp
clsPeriod p1(clsDate(1,1,2026),  clsDate(30,6,2026));
clsPeriod p2(clsDate(1,4,2026),  clsDate(31,12,2026));

bool overlap = clsPeriod::IsOverlapPeriods(p1, p2);  // true
bool overlap2 = p1.IsOverLapWith(p2);                // same, on the object
```

Two periods do **not** overlap only when one ends strictly before the other begins.

---

### 4. Period Length

```cpp
int days  = clsPeriod::PeriodLengthInDays(june);         // exclusive of end date
int days2 = clsPeriod::PeriodLengthInDays(june, true);   // inclusive of end date
int days3 = june.PeriodLengthInDays();
int days4 = june.PeriodLengthInDays(true);
```

Internally delegates to `clsDate::GetDifferenceInDays`.

---

### 5. Date-in-Period Check

```cpp
clsDate d(15, 6, 2026);
bool inside = clsPeriod::IsDateInPeriod(d, june);  // true
bool inside2 = june.IsDateInPeriod(d);             // same, on the object
```

A date is inside a period when it is not before the start and not after the end (inclusive on both boundaries).

---

### 6. Counting Overlap Days

```cpp
int overlap = clsPeriod::CountOverlapDays(p1, p2);
int overlap2 = p1.CountOverlapDays(p2);
```

The method iterates through the shorter period day by day and counts how many of those days fall inside the other period. Returns 0 if the periods do not overlap at all.

---

## 🏗️ OOP Concepts Applied

| Concept | Where It Appears |
|---|---|
| **Encapsulation** | `clsDate` hides `_Day`, `_Month`, `_Year` as private; access goes through getters/setters or properties |
| **Properties (MSVC)** | `__declspec(property(...))` lets `d.Day = 5` call `SetDay(5)` transparently |
| **Constructor overloading** | Four constructors cover all common initialization scenarios |
| **Static vs. instance methods** | Every operation has a `static` form (works on any date passed as a parameter) and a non-static form (works on `*this`) |
| **Delegation pattern** | Instance methods are thin wrappers: `IsValid()` just calls `IsValidDate(*this)` — logic lives in one place only |
| **Composition** | `clsPeriod` is composed of two `clsDate` members, demonstrating "has-a" relationships |
| **Enumerations** | `enDateCompare` (`Before`, `Equal`, `After`) and `enDateFormat` make comparisons and formatting readable and type-safe |

---

## 🔗 Relationship Between Classes

```
clsPeriod
├── StartDate : clsDate
└── EndDate   : clsDate
```

`clsPeriod` **depends on** `clsDate` entirely — it does not implement its own date logic. Instead, every `clsPeriod` method delegates to `clsDate` static methods:

- Overlap detection → `clsDate::CompareDates`
- Period length → `clsDate::GetDifferenceInDays`
- Date-in-period check → `clsDate::CompareDates`
- Overlap day count → `clsDate::IsDate1BeforeDate2`, `clsDate::AddOneDay`

This is a clean example of the **composition** and **single-responsibility** principles: `clsDate` owns all date knowledge; `clsPeriod` owns all range knowledge.

---

## 💻 Example Usage

### Working with `clsDate`

```cpp
#include "clsDate.h"

int main()
{
    // Create dates
    clsDate today;                     // today's date from system clock
    clsDate birthday(15, 8, 2000);
    clsDate fromString("01/01/2026");

    // Format and print
    cout << today.DateToString() << endl;            // e.g. "25/6/2026"
    cout << clsDate::FormatDate(today, "yyyy/mm/dd") << endl;  // "2026/6/25"
    today.Print();

    // Validate
    clsDate bad(31, 2, 2026);
    cout << bad.IsValid() << endl;  // 0 (false)

    // Leap year
    cout << clsDate::isLeapYear(2024) << endl;  // 1 (true)

    // Day of week
    cout << today.DayShortName() << endl;       // "Thu"

    // Arithmetic
    clsDate future = today;
    future.IncreaseDateByXDays(90);
    future.Print();

    clsDate past = today;
    past.DecreaseDateByOneYear();
    past.Print();

    // Difference
    int diff = clsDate::GetDifferenceInDays(birthday, today, true);
    cout << "Age in days: " << diff << endl;

    // Business days
    clsDate d1(1, 6, 2026), d2(30, 6, 2026);
    cout << "Business days in June: " << clsDate::CalculateBusinessDays(d1, d2) << endl;

    // Print calendar
    clsDate::PrintMonthCalendar(6, 2026);

    return 0;
}
```

---

### Working with `clsPeriod`

```cpp
#include "clsPeriod.h"

int main()
{
    clsDate start1(1, 1, 2026), end1(30, 6, 2026);
    clsDate start2(1, 4, 2026), end2(31, 12, 2026);

    clsPeriod firstHalf(start1, end1);
    clsPeriod secondHalf(start2, end2);

    firstHalf.Print();

    // Overlap detection
    if (firstHalf.IsOverLapWith(secondHalf))
        cout << "Periods overlap!" << endl;

    // How many days overlap?
    cout << "Overlapping days: "
         << clsPeriod::CountOverlapDays(firstHalf, secondHalf) << endl;

    // Period length
    cout << "First half length: " << firstHalf.PeriodLengthInDays(true) << " days" << endl;

    // Is a specific date inside a period?
    clsDate check(15, 3, 2026);
    if (firstHalf.IsDateInPeriod(check))
        cout << "Date is inside the period." << endl;

    return 0;
}
```

---

## 🎨 Design Notes

**Static + instance method pairs**  
The most important design decision is the consistent pairing of every method with a `static` version and a non-static object wrapper. This makes the library flexible: you can call methods without owning an object (`clsDate::isLeapYear(2024)`), or call them naturally on an object (`d.isLeapYear()`). The instance version simply forwards to the static version using `*this`.

**Inclusive/exclusive end-date parameter**  
`GetDifferenceInDays` and `PeriodLengthInDays` both accept an optional `bool IncludeEndDate` parameter (default `false`). This small addition covers two very common real-world needs without duplicating code.

**Negative difference handling**  
`GetDifferenceInDays` internally swaps its two dates if they are passed in the "wrong" order, and then multiplies the result by `-1` to return a negative value. Callers do not need to worry about the order.

**Month-boundary clamping**  
When adding or subtracting months, if the current day exceeds the number of days in the resulting month (e.g., March 31 minus one month), the day is automatically clamped to the last valid day of the new month. This prevents silent date corruption.

**`clsPeriod` as a thin coordination layer**  
`clsPeriod` intentionally contains no date arithmetic of its own. It exists only to hold two `clsDate` objects and coordinate comparisons between them. This keeps all date logic in one place and makes the codebase easier to maintain.
