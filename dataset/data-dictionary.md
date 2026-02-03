# Data Dictionary

This document describes the structure and fields of the **actual-vs-target-dataset.xlsx** file used in the Sales Performance Dashboard.

---

## Overview

**File Name:** `actual-vs-target-dataset.xlsx`  
**Format:** Microsoft Excel Workbook (.xlsx)  
**Sheets:** 3 (Actual, Targets, Calendar)

---

## Sheet 1: Actual Sales Data

### Purpose
Contains actual sales transactions recorded by the sales team.

### Structure

| Column Name | Data Type | Description | Example | Nullable |
|------------|-----------|-------------|---------|----------|
| **SalespersonID** | Integer | Unique identifier for each salesperson | 101 | No |
| **Salesperson** | Text | Full name of the salesperson | "Kaine Paddy" | No |
| **Team** | Text | Team assignment | "Dalish" | No |
| **Date** | Date | Transaction date | 2024-01-15 | No |
| **Sales** | Currency | Actual sales amount in USD | 45000.00 | No |

### Key Characteristics

- **Granularity:** One row per salesperson per month
- **Date Range:** January 2023 - December 2024
- **Total Records:** ~280 rows (20 salespeople × 14 months)
- **Primary Key:** SalespersonID + Date

### Sample Data

| SalespersonID | Salesperson | Team | Date | Sales |
|--------------|-------------|------|------|-------|
| 101 | Kaine Paddy | Dalish | 2024-01-01 | $701,200 |
| 102 | Curtis Andani | Jucies | 2024-01-01 | $759,000 |
| 103 | Kaci Walkden | Tempo | 2024-01-01 | $738,600 |

### Business Rules

- Sales amounts are always positive (returns/refunds handled separately)
- One entry per salesperson per month
- All monetary values in USD

---

## Sheet 2: Target Sales Data

### Purpose
Contains sales targets set by management for each salesperson.

### Structure

| Column Name | Data Type | Description | Example | Nullable |
|------------|-----------|-------------|---------|----------|
| **SalespersonID** | Integer | Unique identifier (matches Actual) | 101 | No |
| **Salesperson** | Text | Full name of the salesperson | "Kaine Paddy" | No |
| **Date** | Date | Target period date | 2024-01-01 | No |
| **Target** | Currency | Target sales amount in USD | 50000.00 | No |

### Key Characteristics

- **Granularity:** One row per salesperson per month
- **Date Range:** Matches Actual sheet (January 2023 - December 2024)
- **Total Records:** ~280 rows
- **Primary Key:** SalespersonID + Date

### Sample Data

| SalespersonID | Salesperson | Date | Target |
|--------------|-------------|------|--------|
| 101 | Kaine Paddy | 2024-01-01 | $703,900 |
| 102 | Curtis Andani | 2024-01-01 | $708,000 |
| 103 | Kaci Walkden | 2024-01-01 | $712,000 |

### Business Rules

- Targets set quarterly, distributed monthly
- Targets may be adjusted mid-year based on market conditions
- Each salesperson must have a target for every active month

---

## Sheet 3: Calendar Table

### Purpose
Date dimension table providing time intelligence for DAX calculations.

### Structure

| Column Name | Data Type | Description | Example | Nullable |
|------------|-----------|-------------|---------|----------|
| **Date** | Date | Unique date (primary key) | 2024-01-15 | No |
| **Year** | Integer | Calendar year | 2024 | No |
| **Quarter** | Integer | Calendar quarter (1-4) | 1 | No |
| **Month** | Integer | Month number (1-12) | 1 | No |
| **MonthName** | Text | Full month name | "January" | No |
| **MonthShort** | Text | Abbreviated month name | "Jan" | No |
| **YearMonth** | Text | Year-Month key | "2024-01" | No |
| **DayOfWeek** | Integer | Day of week (1=Sunday) | 2 | No |
| **DayName** | Text | Full day name | "Monday" | No |
| **IsWeekend** | Boolean | Weekend indicator | FALSE | No |

### Key Characteristics

- **Granularity:** Daily
- **Date Range:** January 1, 2023 - December 31, 2024
- **Total Records:** ~730 rows (2 years)
- **Primary Key:** Date

### Sample Data

| Date | Year | Quarter | Month | MonthName | YearMonth |
|------|------|---------|-------|-----------|-----------|
| 2024-01-01 | 2024 | 1 | 1 | January | 2024-01 |
| 2024-01-02 | 2024 | 1 | 1 | January | 2024-01 |
| 2024-01-03 | 2024 | 1 | 1 | January | 2024-01 |

### Special Columns

**YearMonth** (Format: YYYY-MM)
- Used for month-level aggregation
- Enables proper month sorting
- Required for "Months Target Reached" calculation

**IsWeekend** (TRUE/FALSE)
- Useful for filtering business days
- Currently not used in main dashboard

---

## Relationships

### Data Model Structure

```
Calendar (Dimension)
    ├─── Actual (Fact)
    │    └─ Relationship: Calendar[Date] 1:* Actual[Date]
    │
    └─── Targets (Fact)
         └─ Relationship: Calendar[Date] 1:* Targets[Date]

Salesperson (Dimension - implicit, part of Actual/Targets)
```

### Relationship Details

| From Table | From Column | To Table | To Column | Cardinality | Direction |
|-----------|-------------|----------|-----------|-------------|-----------|
| Calendar | Date | Actual | Date | 1:Many | Single |
| Calendar | Date | Targets | Date | 1:Many | Single |

---

## Data Quality Checks

### Validation Rules

1. **No Missing Dates**
   - Every salesperson must have an entry for each month
   - Gaps indicate data quality issues

2. **No Negative Values**
   - Sales and Targets must be ≥ 0
   - Negative values should be investigated

3. **Date Alignment**
   - Dates in Actual must match dates in Targets
   - Calendar table must cover full date range

4. **Referential Integrity**
   - All SalespersonIDs in Actual must exist in Targets
   - All Dates must exist in Calendar table

### Common Issues

| Issue | Symptom | Resolution |
|-------|---------|------------|
| Missing Months | YTD calculations incorrect | Fill gaps with zeros or previous month |
| Date Misalignment | Variance shows BLANK | Ensure dates match exactly (no time component) |
| Duplicate Entries | Inflated totals | Remove duplicates, keep most recent |

---

## Data Update Process

### Monthly Refresh Steps

1. **Export Sales Data**
   - Extract actual sales from CRM
   - Format as: SalespersonID, Salesperson, Team, Date, Sales

2. **Add New Targets**
   - Get updated targets from management
   - Add to Targets sheet

3. **Extend Calendar**
   - If entering new year, extend Calendar table
   - Maintain consistent format

4. **Validate Data**
   - Check for missing entries
   - Verify calculations in Excel
   - Run data quality checks

5. **Refresh Power BI**
   - Open .pbix file
   - Home → Refresh
   - Verify dashboard updates correctly

---

## Field Value Ranges

### Actual Sales

- **Minimum:** $0 (no sales in month)
- **Maximum:** ~$900,000 (top performer)
- **Average:** ~$750,000
- **Median:** ~$740,000

### Target Sales

- **Minimum:** $700,000
- **Maximum:** ~$875,000
- **Average:** ~$770,000

### Variance

- **Range:** -$70,000 to +$280,000
- **Mean:** -$20,000 (indicates overall underperformance)

---

## Team Assignments

| Team Name | Number of Salespeople | Description |
|-----------|----------------------|-------------|
| **Dalish** | 5 | Regional team - Northeast |
| **Jucies** | 5 | Regional team - Southeast |
| **Tempo** | 5 | Regional team - West |
| **Yummies** | 5 | Regional team - Central |

---

## Data Lineage

```
Source Systems
│
├── CRM System → Actual Sales Data
│   └── Extracted monthly via SQL query
│
├── Sales Planning System → Target Sales Data
│   └── Maintained by Sales Operations team
│
└── Calendar Generator → Calendar Table
    └── Created using DAX CALENDAR function
```

---

## Metadata

| Property | Value |
|----------|-------|
| **Created By** | Sales Operations Team |
| **Last Updated** | December 2024 |
| **Update Frequency** | Monthly |
| **Data Owner** | VP of Sales |
| **Approver** | Director of Analytics |
| **Retention Period** | 3 years |

---

## File Specifications

| Attribute | Value |
|-----------|-------|
| **File Format** | .xlsx (Excel 2016+) |
| **Compression** | Standard ZIP |
| **File Size** | ~250 KB |
| **Sheets** | 3 |
| **Total Rows** | ~1,290 |
| **Total Columns** | 16 |

---

## Usage Guidelines

### Best Practices

✅ **DO:**
- Back up file before making changes
- Maintain consistent date formats
- Use Excel Table format for structured data
- Document any manual adjustments
- Validate totals after updates

❌ **DON'T:**
- Change column names (breaks Power BI)
- Delete historical data
- Mix currencies
- Add calculated columns in Excel (use DAX instead)
- Modify file while Power BI is open

---

## Support

For questions about data definitions or data quality issues:

- **Data Owner:** sales.operations@company.com
- **Technical Support:** bi.team@company.com
- **Documentation:** See project README.md

---

**Last Updated:** February 2024  
**Version:** 1.0  
**Status:** Active
