**csvschema** is a way of specifying a schema for tabular data.
It's built on top of CSV files.

## Two tables
There are two tables.

1. The datasets table
2. The columns table

These are canonically represented as separate files for each dataset or column,
but there is a standard (and simple) way of combining them into just two files.

## Canonical representation

### Columns
You specify each column type with a series of properties in a two-column table.

    $ cat columns/zipcode.csv
    property,value
    title,United States Zip Code
    inherits,columns/string.csv
    regex,[0-9]{5}(?:-[0-9]{4})?

### Datasets
Each dataset is represented by a series of columns, ordered as they should
appear in a data table following the schema. For each column, you specify the
column name that should be used in the data table (`colname`) and the type of
the column (`type`).

    $ cat columns/subscribers.csv
    colname,    type
    name,       columns/fullname.csv
    street,     columns/string.csv
    town,       columns/string.csv
    zip,        columns/zipcode.csv

## Identifiers
The values for `inherits` and `type` (as you've seen above) and for `
