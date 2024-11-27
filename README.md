# Advanced_DAX on Adventure Sales Dashboard

1) Dynamic Top-N Products by Sales:
Write a DAX measure to calculate the sales for the top 5 products dynamically based on user selection of a date range


       SalesTop5Products2 = 
            VAR DateFilteredSales =
                SUMMARIZE(
                    FILTER(
                        Sales, 
                        Sales[OrderDate] >= MIN(DateTable[Date]) &&
                        Sales[OrderDate] <= MAX(DateTable[Date])
                    ),
                    Sales[ProductKey],
                    "ProductSales", SUM(Sales[Sales])
                )
            VAR Top5Products =
                TOPN(5, DateFilteredSales, [ProductSales], DESC)
            VAR Top5Keys =
                SELECTCOLUMNS(Top5Products, "ProductKey", [ProductKey])
            RETURN
            CALCULATE(
                SUM(Sales[Sales]),
                Sales[ProductKey] IN Top5Keys
            )

2) Year-over-Year (YoY) Sales Growth:
Create a DAX measure that computes the YoY sales growth percentage for a specific product or category

        YoY Sales Change = 
        VAR CurrentPeriodSales = 
            CALCULATE(
                SUM(Sales[Sales]),
                -- Respect the current filter context of DateTable[Date] (e.g., Year, Quarter, Month)
                ALLSELECTED(DateTable[Date])  -- This filters based on the current visual's time context
            )
            
        VAR PreviousPeriodSales = 
            CALCULATE(
                SUM(Sales[Sales]),
                -- Use SAMEPERIODLASTYEAR but respect current filter context
                SAMEPERIODLASTYEAR(DateTable[Date])
            )
            
        RETURN
            IF(
                NOT ISBLANK(PreviousPeriodSales),
                DIVIDE(CurrentPeriodSales - PreviousPeriodSales, PreviousPeriodSales, 0),
                BLANK()
            )

