# Configuration-Reports-Power-BI

**Purpose:** A portfolio showcasing my work creating daily reports for stakeholders and production monitoring

[TOC]

## Queries

### Group Structure

Values: Tables consisting of static attributes that rarely change.

Parameters: Easy to change values that are utilized across the entire model.

Original Data: Central tables containing key columns.  New records are added frequently.

Collected Data: Tables with transactional data that is collected daily.

### Data Cleaning Process

#### Date Table
StartYear and EndYear Parameters are used to limit the size of the table.

Columns created to populate Year, Month, Month Name, etc.

Conditional columns created to determine Fiscal Year, Fiscal Period, and Fiscal Quarter based on StartOfFiscalYear parameter.

Conditional column created to give boolean results if each date is in the current fiscal year.

Duplicated Date column and renamed for later use in visualization with specific formatting.


## Schema

## Measures

## Visualizations
