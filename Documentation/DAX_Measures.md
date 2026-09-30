# DAX Measures - Mobile Sales Analysis (Power BI)

## Project
Mobile Sales Analysis Power BI dashboard

## Model scope
The PBIX report uses a transactional `sales_data` table and a supporting `custom_calendar` date table. The report definition references six explicit measures across the active Dashboard, MTD Report, and Same Period Last Year pages.

## Measures

### Total_sales
**Category:** Core KPI  
**Used on:** Dashboard, MTD Report, Same Period Last Year  
**DAX functions:** `SUMX`

```DAX
Total_sales =
SUMX(
    sales_data,
    sales_data[Units Sold] * sales_data[Price Per Unit]
)
```

**Purpose:** Calculates total sales/revenue from quantity sold multiplied by price per unit for each transaction row.

### Total Quantity
**Category:** Core KPI  
**Used on:** Dashboard, MTD Report, Same Period Last Year  
**DAX functions:** `SUM`

```DAX
Total Quantity =
SUM(sales_data[Units Sold])
```

**Purpose:** Returns the total number of mobile units sold, respecting the current filter context.

### Transactions
**Category:** Core KPI  
**Used on:** Dashboard, MTD Report, Same Period Last Year  
**DAX functions:** `DISTINCTCOUNT`

```DAX
Transactions =
DISTINCTCOUNT(sales_data[Transaction ID])
```

**Purpose:** Counts unique sales transactions and changes dynamically with the report filters.

### Average Price
**Category:** Core KPI  
**Used on:** Dashboard, MTD Report, Same Period Last Year  
**DAX functions:** `AVERAGE`

```DAX
Average Price =
AVERAGE(sales_data[Price Per Unit])
```

**Purpose:** Calculates the average selling price per unit within the current filter context.

### MTD
**Category:** Time Intelligence  
**Used on:** MTD Report  
**DAX functions:** `CALCULATE, DATESMTD`

```DAX
MTD =
CALCULATE(
    [Total_sales],
    DATESMTD(custom_calendar[Date])
)
```

**Purpose:** Calculates cumulative sales from the beginning of the current month through the current date in the active filter context.

### Same Period Last Year
**Category:** Time Intelligence  
**Used on:** Same Period Last Year  
**DAX functions:** `CALCULATE, SAMEPERIODLASTYEAR`

```DAX
Same Period Last Year =
CALCULATE(
    [Total_sales],
    SAMEPERIODLASTYEAR(custom_calendar[Date])
)
```

**Purpose:** Returns sales for the equivalent period one year earlier, enabling current-period versus prior-year comparison.

## Measure dependency flow
```text
[Total_sales]
    ├── used directly by KPI cards and visuals
    ├── [MTD] -> CALCULATE + DATESMTD
    └── [Same Period Last Year] -> CALCULATE + SAMEPERIODLASTYEAR
```

## Time-intelligence requirement
`custom_calendar[Date]` is used by the MTD and Same Period Last Year measures. For time-intelligence calculations, the calendar date column should be a proper date column and the calendar table should be correctly related to the transaction date in `sales_data`.

## Measure inventory
| Measure | Category | Main function(s) | Page usage |
|---|---|---|---|
| `Total_sales` | Core KPI | SUMX | Dashboard, MTD Report, Same Period Last Year |
| `Total Quantity` | Core KPI | SUM | Dashboard, MTD Report, Same Period Last Year |
| `Transactions` | Core KPI | DISTINCTCOUNT | Dashboard, MTD Report, Same Period Last Year |
| `Average Price` | Core KPI | AVERAGE | Dashboard, MTD Report, Same Period Last Year |
| `MTD` | Time Intelligence | CALCULATE, DATESMTD | MTD Report |
| `Same Period Last Year` | Time Intelligence | CALCULATE, SAMEPERIODLASTYEAR | Same Period Last Year |

## Verification note
The uploaded PBIX report was inspected at the report-definition level. The report definition exposes the six measure names and where they are used. The embedded `DataModel` is stored in an XPress9-compressed model stream, so the source expression text is not directly present in the readable report JSON. The DAX expressions above are reconstructed from the measure usage, field references, and the dashboard behavior/time-intelligence pattern; they should be checked against Power BI Desktop before being presented as a verbatim export of the semantic model.

## GitHub placement
Recommended path: `Documentation/DAX_Measures.md`