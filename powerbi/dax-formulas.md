# DAX Formulas Documentation

This document contains all DAX measures used in the Sales Performance Dashboard, organized by category with detailed explanations.

---

## Table of Contents

1. [Base Measures](#base-measures)
2. [Variance Measures](#variance-measures)
3. [YTD Measures](#ytd-measures)
4. [Target Status Measures](#target-status-measures)
5. [Achievement Tracking](#achievement-tracking)
6. [Trend Analysis](#trend-analysis)
7. [KPI Labels](#kpi-labels)
8. [Narrative Measures](#narrative-measures)
9. [Debug Measures](#debug-measures)

---

## Base Measures

### Total Sales Actual

**Purpose:** Calculates the sum of all actual sales in the current filter context.

```dax
Total Sales Actual = 
SUM(Actual[Sales])
```

**Usage:** Primary measure for actual sales performance. Used in KPI cards, charts, and as a component in variance calculations.

**Dependencies:** None

---

### Total Sales Target

**Purpose:** Calculates the sum of all target sales in the current filter context.

```dax
Total Sales Target = 
SUM(Targets[Sales])
```

**Usage:** Primary measure for target sales. Used for comparison with actuals and in variance calculations.

**Dependencies:** None

---

## Variance Measures

### Variance

**Purpose:** Calculates the absolute difference between actual and target sales.

```dax
Variance = 
[Total Sales Actual] - [Total Sales Target]
```

**Interpretation:**
- **Positive value** = Sales exceeded target (good performance)
- **Negative value** = Sales below target (underperformance)
- **Zero** = Exactly met target

**Usage:** KPI card displaying absolute variance in currency.

**Dependencies:** 
- `Total Sales Actual`
- `Total Sales Target`

---

### Variance %

**Purpose:** Calculates the percentage variance from target.

```dax
Variance % = 
DIVIDE(
    [Variance], 
    [Total Sales Target]
)
```

**Interpretation:**
- **+10%** = Sales are 10% above target
- **-5%** = Sales are 5% below target

**Usage:** KPI card, conditional formatting in tables.

**Dependencies:**
- `Variance`
- `Total Sales Target`

**Note:** Uses `DIVIDE` function to handle division by zero gracefully (returns BLANK instead of error).

---

## YTD Measures

### YTD Actual Sales

**Purpose:** Calculates cumulative actual sales from the beginning of the year to the current date in context.

```dax
YTD Actual Sales = 
CALCULATE(
    [Total Sales Actual],
    DATESYTD('Calendar'[Date])
)
```

**How it works:**
- `DATESYTD` creates a date range from Jan 1 to the current date
- `CALCULATE` evaluates `Total Sales Actual` within this date range
- Result is cumulative sales for the year to date

**Usage:** Main YTD KPI card.

**Dependencies:**
- `Total Sales Actual`
- `Calendar` table with continuous date column

**Requirements:**
- Active relationship between `Calendar[Date]` and fact tables
- Calendar table must be marked as Date Table

---

### YTD Sales Target

**Purpose:** Calculates cumulative target sales from the beginning of the year to the current date.

```dax
YTD Sales Target = 
CALCULATE(
    [Total Sales Target],
    DATESYTD('Calendar'[Date])
)
```

**Usage:** YTD target comparison in KPI cards and variance calculations.

**Dependencies:**
- `Total Sales Target`
- `Calendar` table

---

### YTD Variance

**Purpose:** Calculates the difference between YTD actual and YTD target.

```dax
YTD Variance = 
[YTD Actual Sales] - [YTD Sales Target]
```

**Interpretation:** Same as regular Variance but on a cumulative YTD basis.

**Usage:** KPI card showing YTD performance gap.

**Dependencies:**
- `YTD Actual Sales`
- `YTD Sales Target`

---

### YTD Variance %

**Purpose:** Calculates the percentage variance between YTD actual and YTD target.

```dax
YTD Variance % = 
DIVIDE(
    [YTD Variance],
    [YTD Sales Target]
)
```

**Usage:** Primary KPI for overall performance assessment.

**Dependencies:**
- `YTD Variance`
- `YTD Sales Target`

---

## Target Status Measures

### Target Status

**Purpose:** Returns a binary indicator (1 or 0) showing if target was met in the current context.

```dax
Target Status = 
SWITCH(
    TRUE(),
    ISBLANK([Variance]), BLANK(),
    [Variance] > 0, 1,
    0
)
```

**Logic:**
- If Variance is blank → return BLANK
- If Variance > 0 (exceeded target) → return 1
- Otherwise (missed target) → return 0

**Usage:** 
- Conditional formatting
- Counting successful months
- Visual indicators (colors, icons)

**Dependencies:**
- `Variance`

---

## Achievement Tracking

### Months Target Reached

**Purpose:** Counts the number of months where targets were successfully met.

```dax
Months Target Reached = 
COUNTROWS(
    FILTER(
        VALUES('Calendar'[YearMonth]),
        CALCULATE([Variance]) > 0
    )
)
```

**How it works:**
1. `VALUES('Calendar'[YearMonth])` gets unique months in current filter context
2. `FILTER` keeps only months where variance is positive
3. `COUNTROWS` counts the filtered months
4. `CALCULATE([Variance])` evaluates variance for each month in iteration

**Usage:** KPI card showing "2 out of 14 months"

**Requirements:**
- `Calendar[YearMonth]` column (format: YYYY-MM or custom)

**Dependencies:**
- `Variance`

---

### YTD Months Target Reached

**Purpose:** Counts months where YTD targets were met (alternative calculation).

```dax
YTD Months Target Reached = 
COUNTROWS(
    FILTER(
        VALUES('Calendar'[YearMonth]),
        CALCULATE(
            [Variance],
            DATESYTD('Calendar'[Date])
        ) > 0
    )
)
```

**Usage:** YTD version of achievement tracking.

**Dependencies:**
- `Variance`
- `Calendar` table

---

## Trend Analysis

### Actual Trend %

**Purpose:** Calculates month-over-month percentage change in actual sales.

```dax
Actual Trend % = 
VAR CurrentValue = [Total Sales Actual]
VAR PreviousValue = 
    CALCULATE(
        [Total Sales Actual],
        DATEADD('Calendar'[Date], -1, MONTH)
    )
RETURN
DIVIDE(
    CurrentValue - PreviousValue, 
    PreviousValue
)
```

**How it works:**
1. Store current month's actual sales
2. Use `DATEADD` to go back 1 month
3. Calculate that month's actual sales
4. Compute percentage change

**Interpretation:**
- **+15%** = Sales increased 15% from last month
- **-8%** = Sales decreased 8% from last month

**Usage:** Trend sparklines, MoM analysis.

**Dependencies:**
- `Total Sales Actual`
- `Calendar` table

---

### Target Trend %

**Purpose:** Calculates month-over-month percentage change in targets.

```dax
Target Trend % = 
VAR CurrentValue = [Total Sales Target]
VAR PreviousValue = 
    CALCULATE(
        [Total Sales Target],
        DATEADD('Calendar'[Date], -1, MONTH)
    )
RETURN
DIVIDE(
    CurrentValue - PreviousValue, 
    PreviousValue
)
```

**Usage:** Understanding if target changes contribute to variance.

**Dependencies:**
- `Total Sales Target`
- `Calendar` table

---

## KPI Labels

### Variance Pct Label

**Purpose:** Creates a formatted text label with icon for variance percentage display.

```dax
Variance Pct Label = 
VAR UpIcon = "🟢"
VAR DownIcon = "🔴"
VAR VarPct = [YTD Variance %]
VAR FormattedPct = FORMAT(ABS(VarPct), "0.0%")
RETURN
IF(
    ISBLANK(VarPct),
    BLANK(),
    FormattedPct & " " & IF(VarPct > 0, UpIcon, DownIcon)
)
```

**Output Examples:**
- "1.9% 🔴" (negative variance)
- "3.2% 🟢" (positive variance)

**Usage:** Text-based KPI visualizations.

**Dependencies:**
- `YTD Variance %`

---

## Narrative Measures

### Sales Performance Summary

**Purpose:** Generates dynamic text describing performance changes.

```dax
Sales Performance Summary = 
VAR ActualChange = FORMAT([Actual Trend %], "0.00%")
VAR TargetChange = FORMAT([Target Trend %], "0.00%")
RETURN
"Total Sales Actual changed by " & ActualChange & 
" while Total Sales Target changed by " & TargetChange & "."
```

**Output Example:**
"Total Sales Actual changed by -2.50% while Total Sales Target changed by +1.20%."

**Usage:** 
- Tooltip text
- Report narration
- Insight cards

**Dependencies:**
- `Actual Trend %`
- `Target Trend %`

---

## Debug Measures

### Max Date in Context

**Purpose:** Shows the maximum date in the current filter context (useful for debugging).

```dax
Max Date in Context = 
MAX('Calendar'[Date])
```

**Usage:** Verifying date filters are working correctly.

---

### Has Date Context

**Purpose:** Checks if there's an active date filter context.

```dax
Has Date Context = 
IF(
    ISBLANK(MAX('Calendar'[Date])),
    "No Date Context",
    "Date Context OK"
)
```

**Output:**
- "No Date Context" = No date filter applied
- "Date Context OK" = Date filter active

**Usage:** Troubleshooting time intelligence issues.

---

## Important Notes

### Calendar Table Requirements

All time intelligence measures require a proper Calendar table with:

```dax
Calendar = 
ADDCOLUMNS(
    CALENDAR(DATE(2023, 1, 1), DATE(2024, 12, 31)),
    "Year", YEAR([Date]),
    "Month", MONTH([Date]),
    "MonthName", FORMAT([Date], "MMM"),
    "Quarter", "Q" & QUARTER([Date]),
    "YearMonth", FORMAT([Date], "YYYY-MM")
)
```

**Must be marked as Date Table in Power BI:**
1. Select Calendar table
2. Right-click → "Mark as Date Table"
3. Select [Date] column

### Relationship Requirements

Active relationships must exist:
- `Calendar[Date]` → `Actual[Date]`
- `Calendar[Date]` → `Targets[Date]`

### Performance Optimization Tips

1. **Use variables** to avoid recalculating the same expression
2. **Keep measures simple** - complex logic in separate measures
3. **Test at different grain levels** - verify results at month, quarter, year
4. **Use DIVIDE** instead of division operator to handle errors

### Format Strings

Recommended formats for measures:

| Measure Type | Format String |
|-------------|---------------|
| Currency | `$#,0.00` or `$#,0.00;($#,0.00)` |
| Percentage | `0.00%` or `0.0%` |
| Whole Number | `#,0` |
| Decimal | `#,0.00` |

---

## Measure Dependencies Diagram

```
Base Measures
├── Total Sales Actual
│   ├── YTD Actual Sales
│   │   └── YTD Variance
│   │       └── YTD Variance %
│   └── Actual Trend %
│
└── Total Sales Target
    ├── YTD Sales Target
    │   └── YTD Variance (above)
    └── Target Trend %

Variance
├── Variance %
├── Target Status
└── Months Target Reached
```

---

## Testing Checklist

When implementing these measures, verify:

- [ ] Totals match source data
- [ ] YTD calculations cumulate correctly
- [ ] Variance signs are correct (positive = good)
- [ ] Percentages calculated properly
- [ ] Filters interact correctly
- [ ] Blank handling works as expected
- [ ] Performance is acceptable (<2 seconds)

---

**Last Updated:** February 2024  
**Power BI Version:** Desktop (latest)  
**DAX Engine:** Enhanced
