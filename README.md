# Configuration-Reports-Power-BI

**Purpose:** A portfolio showcasing my work creating daily reports for stakeholders and production monitoring.

## Glossary

POID - Purchase Order ID.  A unique key column created to tie all of the order details together.

RCID - School Number (or RC) ID.  A unique key column created to reference school/location details.

## Queries

### Group Structure

Parameters: Easy to change values that are utilized across the entire model.

Values: Tables consisting of static attributes that rarely change.

Original Data: Central tables containing key columns.  New records are added frequently.

Collected Data: Tables with transactional data that is collected daily.

### Data Cleaning Process

#### Values

###### Date Table

StartYear and EndYear Parameters are used to limit the size of the table.

Create columns to populate Year, Month, Month Name, etc.

Create conditional columns to determine Fiscal Year, Fiscal Period, and Fiscal Quarter based on StartOfFiscalYear parameter.

Create conditional column to give boolean results on whether each date is in the current fiscal year.

Duplicate the Date column and rename for later use in visualization with specific formatting.

###### Other Tables

Minimal changes done to other Values tables besides Change type, Capitalize, and Remove unecessary columns.

#### Original Data

The source for the Orders query is a range and not a table, so the headers get promoted and certain columns correctly renamed.

Add custom column for PO_Year to use for filtering during visualization.

Add conditional column to populate applicable notes for each record.

Filter out null values and added an index for sorting.

Append Task POID's query after performing identical steps.

Task POID's is a list of generic tasks that are not tied to a specific PO but are still tracked activities.  i.e. counting accessories or AC Adapters, or performing periodic maintenance on warehouse equipment.

#### Collected Data

###### Data Entry

Point of live transactional data collection.  Input daily by techincal personnel.

The source for the Data Entry query is a range and not a table, so the headers get promoted and certain columns correctly renamed.  Also, blank rows and unecessary columns are removed, and the POID is set to all uppercase.

 In order to keep the data live, significant changes must be made to this query before it can be appended.  Several steps are performed to bring the format in line with the next query, Log.
- Add custom column to split the date and time into separate columns.
- Add custom column for the RCID, then remove it after using it to retrieve the RC#.
- Add custom column for the concatenated RC# & Location.
- Merge the Orders query by POID to retrieve the Ready By Date.
- Replace null dates with a placeholder value.
- Add custom column for the PO#.

Filter out null values from POID column.

###### Archive Log

No changes necessary after importing and setting correct data types.

###### Log

The source for the Log query is a range and not a table, so the headers get promoted and blank rows and unecessary columns are removed.

Append Data Entry and Archive Log queries.

Set POID column to all uppercase.

Set Action column to proper case.

Replace placeholder dates with a more accurate value.

Merge Statuses query and expand index for proper visualization sorting.

Add custom column time of day with no date.  

Add conditional columns with boolean results on whether the action took place after 4pm, 1:30pm, or before.

Merge Orders Query and expand Program column.

## Schema

## Measures

## Visualizations
