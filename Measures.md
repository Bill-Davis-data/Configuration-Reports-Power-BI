# Log
```
MEASURE 'Log'[3 Month Etcher Maint] = 
VAR LAST_DATE =
	CALCULATE(
		MAX('Log'[Date no time]),
		'Log'[School Name] = "0000 - 3 MONTH MAINTENANCE"
	)
RETURN
	IF(NOT ISBLANK(LAST_DATE), VALUE(TODAY() - LAST_DATE))

MEASURE 'Log'[AC Adapter Shortage] = 
VAR TOTAL_ADAPTERS =
	CALCULATE(
		SUM('Log'[Quantity]),
		'Log'[School Name] IN { "0000 - AC ADAPTERS" }
	)
VAR TOTAL_UNBOXED = 
	CALCULATE(
		SUM('Log'[Quantity]),
		'Log'[Date no time] > DATE(2024, 8, 20),
		'Log'[Action] = "UNBOXED",
        	MachineTypes[AC Adapters] = TRUE
	)
RETURN
	IF(NOT ISBLANK(TOTAL_UNBOXED), TOTAL_ADAPTERS - TOTAL_UNBOXED)

MEASURE 'Log'[Accessories Shortage] = 
VAR TOTAL_ACCESSORIES =
	CALCULATE(
		SUM('Log'[Quantity]),
		'Log'[School Name] IN { "0000 - TINY ACCESSORY BOXES" }
	)
VAR TOTAL_UNBOXED = 
	CALCULATE(
		SUM('Log'[Quantity]),
		'Log'[Date no time] > DATE(2024, 8, 20),
		'Log'[Action] = "UNBOXED",
	        MachineTypes[Accessory Boxes] = TRUE
	)
RETURN
	IF(NOT ISBLANK(TOTAL_UNBOXED), TOTAL_ACCESSORIES - TOTAL_UNBOXED)

MEASURE 'Log'[Blue Case Shortage] = 
VAR TOTAL_CASES =
	CALCULATE(
		SUM('Log'[Quantity]),
		'Log'[School Name] IN { "0000 - BLUE CASES" }
	)
VAR TOTAL_ETCHED = 
	CALCULATE(
		SUM('Log'[Quantity]),
		'Log'[Date no time] > DATE(2024, 8, 20),
		'Log'[Action] = "ETCHED",
		'Log'[Type] = "500W"
	)
RETURN
	IF(NOT ISBLANK(TOTAL_ETCHED), TOTAL_CASES - TOTAL_ETCHED)

MEASURE 'Log'[Card2Filter] = 
IF(
	ISBLANK(SELECTEDVALUE('DateTable'[Fiscal Year])), 
	CALCULATE(
		[Weekly Avg], 
		'DateTable'[CurrentFY]=1
	), 
	[Weekly Avg]
)

MEASURE 'Log'[Card3Filter] = 
IF(
	ISBLANK(SELECTEDVALUE('DateTable'[Fiscal Year])), 
	CALCULATE(
		[Monthly Avg], 
		'DateTable'[CurrentFY]=1
	), 
	[Monthly Avg]
)

MEASURE 'Log'[DailyAvg] = AVERAGEX(DateTable,CALCULATE(SUM('Log'[Quantity])))

MEASURE 'Log'[Fiscal Quantity] = SUM([Quantity])

MEASURE 'Log'[_Fiscal Quantity FormatString] = 
VAR QQ = [Fiscal Quantity]
VAR DecimalFormat = 
SWITCH(
	TRUE(),
	QQ < 1000, "0",
	QQ >= 1000, "#,.#K"
)
RETURN DecimalFormat

MEASURE 'Log'[GaugeFilter] = 
IF(
	ISBLANK(SELECTEDVALUE('Log'[Action])), 
	CALCULATE(
		[DailyAvg], 
		'Log'[Action]="Imaged"
	), 
	[DailyAvg]
)

MEASURE 'Log'[GaugeFilter100W] = 
IF(
	ISBLANK(SELECTEDVALUE('Log'[Action])), 
	CALCULATE(
		[DailyAvg], 
		'Log'[Action]="Boxed"
	), 
	[DailyAvg]
)

MEASURE 'Log'[IsComplete] = 
IF(
    SUM([Quantity]) = SELECTEDVALUE(Orders[Qty]), 
    1, 
    0
)

MEASURE 'Log'[MaxRecord] = MAXX(DateTable,CALCULATE(SUM('Log'[Quantity])))

MEASURE 'Log'[MessageHide] = IF([ProjectSliced],"#FFFFFF00", "White")

MEASURE 'Log'[Monthly Avg] = DIVIDE(SUM('Log'[Quantity]), DISTINCTCOUNT(DateTable[Month]))

MEASURE 'Log'[Monthly Etcher Maint] = 
VAR LAST_DATE =
	CALCULATE(
		MAX('Log'[Date no time]),
		'Log'[School Name] = "0000 - MONTHLY MAINTENANCE"
	)
RETURN
	IF(NOT ISBLANK(LAST_DATE), VALUE(TODAY() - LAST_DATE))

MEASURE 'Log'[ProjectSliced] = ISFILTERED('Log'[Program])

MEASURE 'Log'[SelectedAction] = 
IF(
	ISBLANK(SELECTEDVALUE('Log'[Action])), 
	"Imaged", 
	SELECTEDVALUE('Log'[Action])
)

MEASURE 'Log'[SelectedActionECF] = 
IF(
	ISBLANK(SELECTEDVALUE('Log'[Action])), 
	"Boxed", 
	SELECTEDVALUE('Log'[Action])
)

MEASURE 'Log'[Weekly Avg] = DIVIDE(SUM('Log'[Quantity]), DISTINCTCOUNT(DateTable[Week of Year]))

MEASURE 'Log'[Weekly Etcher Maint] = 
VAR LAST_DATE =
	CALCULATE(
		MAX('Log'[Date no time]),
		'Log'[School Name] = "0000 - WEEKLY MAINTENANCE"
	)
RETURN
	IF(NOT ISBLANK(LAST_DATE), VALUE(TODAY() - LAST_DATE))
```

# Orders
```
MEASURE Orders[Message] = IF([ProjectSliced],"","Select a project in order to show results")
```

# Targets
```
MEASURE Targets[MaxValue] = 
IF(
	ISBLANK(SELECTEDVALUE('Log'[Action])), 
	CALCULATE(
		MAX('Targets'[Max]), 
		'Targets'[Action]="Imaged"
	), 
	CALCULATE(
		MAX('Targets'[Max]), 
		'Targets'[Action] = SELECTEDVALUE('Log'[Action])
	)
)

MEASURE Targets[TargetValue] = 
IF(
	ISBLANK(SELECTEDVALUE('Log'[Action])), 
	CALCULATE(
		MAX(Targets[Target]), 
		'Targets'[Action]="Imaged"
	), 
	CALCULATE(
		MAX('Targets'[Target]), 
		'Targets'[Action] = SELECTEDVALUE('Log'[Action])
	)
)
```
