# DAX Measures

Paste these into Power BI Desktop under **Modeling → New measure**. They are grouped so that each measure appears after the measures it depends on.

## 1. Base Measures

```dax
Total Amount = SUM(Shipments[Amount])

Total Boxes = SUM(Shipments[Boxes])

Total Costs = SUM(Shipments[Cost])

Total Profit = [Total Amount] - [Total Costs]

Shipment Count = COUNTROWS(Shipments)
```

## 2. Ratios

```dax
Profit % = DIVIDE([Total Profit], [Total Amount])

Amount per Box = DIVIDE([Total Amount], [Total Boxes])
```

## 3. Prior Year

```dax
Total Amount (last year) =
    CALCULATE([Total Amount], SAMEPERIODLASTYEAR(Calendar[cal_date]))

Total Boxes (last year) =
    CALCULATE([Total Boxes], SAMEPERIODLASTYEAR(Calendar[cal_date]))
```

## 4. Year-over-Year Variance

```dax
Total Amount (12 month variance) =
    VAR ta_ly = [Total Amount (last year)]
RETURN
    IF(ISBLANK(ta_ly), BLANK(), [Total Amount] - ta_ly)

Total Boxes (12 month variance) =
    VAR tb_ly = [Total Boxes (last year)]
RETURN
    IF(ISBLANK(tb_ly), BLANK(), [Total Boxes] - tb_ly)
```
