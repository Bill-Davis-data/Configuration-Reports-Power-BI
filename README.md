# Configuration-Reports-Power-BI

**Purpose:** A portfolio showcasing my work creating daily reports for stakeholders and production monitoring.

<img src="https://github.com/Bill-Davis-data/Configuration-Reports-Power-BI/blob/main/Assets/Report%20-%20Config%20daily%20production.png" width="321"><img src="https://github.com/Bill-Davis-data/Configuration-Reports-Power-BI/blob/main/Assets/Orders%20Status%20Dashboard.png" width="321">

## Glossary

POID - Purchase Order ID.  A unique key column created to tie all of the order details together.

RCID - School Number (or RC) ID.  A unique key column created to reference school/location details.

## Queries

### Group Structure

Parameters: Easy to change values that are utilized across the entire model.

Values: Dimension tables consisting of static attributes that rarely change.

Original Data: Dimension tables containing key columns.  New records are added frequently.

Collected Data: Fact tables with transactional data that is collected daily.

### Data Cleaning Process

#### Values

<details>
<summary>Date Table</summary>
<br/>
 
StartYear and EndYear Parameters are used to limit the size of the table.

Create columns to populate Year, Month, Month Name, etc.

Create conditional columns to determine Fiscal Year, Fiscal Period, and Fiscal Quarter based on StartOfFiscalYear parameter.

Create conditional column to give boolean results on whether each date is in the current fiscal year.

Duplicate the Date column and rename for later use in visualization with specific formatting.

<br/></details>

<details>
<summary>Other Tables</summary>
<br/>
 
Minimal changes done to other Values tables besides Change type, Capitalize, and Remove unecessary columns.

<br/></details>

#### Original Data

<details>
<summary>Orders</summary>
<br/>
 
The Orders table is a list of product orders that are either active or recently delivered within the past fiscal year.  It both creates and contains the primary key column, POID.

For consistency, uppercase applied to key column, add custom column for first 10 characters, and replace errors with a 0.

Add custom column for PO_Year to use for filtering during visualization.

Filter out duplicates and null values from key column.

Append Task POID's and Orders Archive queries.

Add conditional column to populate applicable notes for each record, and add an index for sorting.

<br/></details>

<details>
<summary>Task POID's</summary>
<br/>
 
Append Task POID's query to Orders table after performing identical steps.

Task POID's is a list of generic tasks that are not tied to a specific PO but are still tracked activities.  i.e. counting accessories or AC Adapters, or performing periodic maintenance on warehouse equipment.

<br/></details>

<details>
<summary>Orders Archive</summary>
<br/>
 
Append Orders Archive query to Orders table after performing identical steps.

Orders Archive is a list of older orders that have already been delivered.

<br/></details>

#### Collected Data

<details>
<summary>Data Entry</summary>
<br/>
 
Point of live transactional data collection.  Input daily by technical personnel.

The source for the Data Entry query is a range and not a table, so the headers get promoted and certain columns correctly renamed.  Also, blank rows and unecessary columns are removed, and the POID is set to all uppercase.

 In order to keep the data live, significant changes must be made to this query before it can be appended.  Several steps are performed to bring the format in line with the next query, Log.
- Add custom column to split the date and time into separate columns.
- Add custom column for the RCID, then remove it after using it to retrieve the RC#.
- Add custom column for the concatenated RC# & Location.
- Merge the Orders query by POID to retrieve the Ready By Date.
- Replace null dates with a placeholder value.
- Add custom column for the PO#.

Filter out null values from POID column.

<br/></details>

<details>
<summary>Archive Log</summary>
<br/>
 
No changes necessary after importing and setting correct data types.

<br/></details>

<details>
<summary>Log</summary>
<br/>
 
The Log table is a list of recent transaction data copied over manually each morning from Data Entry.  This data is then copied manually to the Archive Log at least once per fiscal year.

Set correct data types, remove unnecessary columns, and remove blank rows.

Append Data Entry and Archive Log queries.

Set POID column to all uppercase.

Set Action column to proper case.

Replace placeholder dates with a more accurate value.

Merge Statuses query and expand index for proper visualization sorting.

Add custom column time of day with no date.  

Add conditional columns with boolean results on whether the action took place after 4pm, 1:30pm, or before.

Merge Orders query and expand Program column.

<br/></details>

## Schema

The Date table has a single relationship to the Log table.

The Data Entry, Log, and Archive Log tables are appended together during transformation. They contain a foreign key and are the transactional data.  They each have relationships with the attribute tables: Statuses, Targets, and Interns.

Orders is the table containing the primary key, and has relationships with the attribute tables: User types and Machine Types.  The Task POID's table is appended during transformation.

RCID is an attribute table used during transformation and requires no additional relationships.

<img src="https://github.com/Bill-Davis-data/Configuration-Reports-Power-BI/blob/main/Assets/Config%20Status%20Schema.png" width="321">

## Measures

<details>
<summary>Log Table</summary>
<br/>
 
3 Month Etcher Maint:	Value used on a card.	Returns the number of days since maintenance logged using last entry by date.

AC Adapter Shortage:	Value used on a card.	Returns the difference in AC adapters logged and devices unboxed, excluding certain model types that do not apply.

Accessories Shortage:	Value used on a card.	Returns the difference in Accessory boxes logged and devices unboxed, including only certain model types that apply.

Blue Case Shortage:	Value used on a card.	Returns the difference in Blue Cases logged and devices etched, including only the certain model type that applies.

Card2Filter:	Value used on a card.	Returns the weekly average imaged quantity for the current fiscal year.

Card3Filter:	Value used on a card.	Returns the monthly average imaged quantity for the current fiscal year.

DailyAvg:	Used in multiple visuals.	Returns the average quantity for each individual day.

Fiscal Quantity:	Value used on stacked column charts.	Simple sum of orders quantity created in order to apply special formatting.

_Fiscal Quantity FormatString:	Used to format stacked column charts.	Creates formatting style to abbreviate thousands into "K".

GaugeFilter:	Used in several gauges.	Returns the Imaged daily average unless other context is selected.

GaugeFilter100W:	Used in a gauge.	Returns the Boxed daily average unless other context is selected.

IsComplete:	Used for conditional formatting.	Returns 1 if the quantity logged for any action is equal to the order quantity.

MaxRecord:	Used in context with Tech/Intern initials.	Returns the maximum quantity  logged for each individual day.

MessageHide:	Used to mask certain visuals until needed.	

Monthly Avg:	Used in other measures.	Returns the monthly average of quantity logged.

Monthly Etcher Maint:	Value used on a card.	Returns the number of days since maintenance logged using last entry by date.

ProjectSliced:	Flag used in other measures to tell if certain visuals should be unmasked.	Returns a boolean value controlling custom formatting.

SelectedAction:	Used in several gauges.	Returns "Imaged" as a default action if no context is selected.

SelectedActionECF:	Used in a gauge.	Returns "Boxed" as a default action if no context is selected.

Weekly Avg:	Value used on a card.	Returns the weekly average of quantity logged.

Weekly Etcher Maint:	Value used on a card.	Returns the number of days since maintenance logged using last entry by date.

<br/></details>

<details>
<summary>Orders</summary>
<br/>
 
Message:	Used when certain slicers are masked.	Returns either the message or a blank string based on a flag condition.

<br/></details>

<details>
<summary>Targets</summary>
<br/>
 
MaxValue:	Used for a gauge.	Returns the Imaged max value unless other context is selected.

TargetValue:	Used for a gauge.	Returns the Imaged target value unless other context is selected.

<br/></details>

## Visualizations

The centerpeice of the report, the Imaged by Model page provides an accurate graphical representation of Imaging work performed over both the current and previous weeks.  This is the most important tool when forecasting completion dates during the project.
| --- |
| <img src="https://github.com/Bill-Davis-data/Configuration-Reports-Power-BI/blob/main/Assets/Imaged%20by%20Model%20Page.png" width="642"> |

<br/>

The MBR/QBR page is a historical look spanning the entire range of records over years, as well as the last 12 months, rolling.  Created at the request of VP level personnel, this page is used to report services completed each month that can then be claimed for revenue.
| --- |
| <img src="https://github.com/Bill-Davis-data/Configuration-Reports-Power-BI/blob/main/Assets/MBR-QBR%20Page.png" width="642"> |

<br/>

The YTD page provides the ability to slice by various actions and date ranges as well as locations, machine types, and purchase orders.  This is most useful during pre-project planning for determining personnel needs.
| --- |
| <img src="https://github.com/Bill-Davis-data/Configuration-Reports-Power-BI/blob/main/Assets/YTD%20Page_1.png" width="642"> |

<br/>

Notice that the PO# slicer is masked until the Project slicer is utilized.
| --- |
| <img src="https://github.com/Bill-Davis-data/Configuration-Reports-Power-BI/blob/main/Assets/YTD%20Page_2.png" width="642"> |

<br/>

The Interns by Day page gives a closer look at each interns' performance and can be filtered by individual as well as Date range, action, or machine type.  This page helps identify the strongest performers to use when in a clutch situation, as well as those that need more training on certain tasks.
| --- |
| <img src="https://github.com/Bill-Davis-data/Configuration-Reports-Power-BI/blob/main/Assets/Interns%20by%20Day%20Page.png" width="642"> |

<br/>

The Interns Individual page provides a different perspective on intern performance including daily averages for each individual as well as a ranked list of max records for the given date range.  This information can be leveraged at the request of the team itself to increase production through friendly competition.
| --- |
| <img src="https://github.com/Bill-Davis-data/Configuration-Reports-Power-BI/blob/main/Assets/Interns%20Individual%20Page.png" width="642"> |
