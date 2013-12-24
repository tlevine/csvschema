**csvschema** is a way of specifying a schema for tabular data.
It's built on top of CSV files.

## Two tables
There are two tables.

1. The datasets table
2. The column types table

These are canonically represented as separate files for each dataset or column,
but there is a standard (and simple) way of combining them into just two files.

## Canonical representation

### Column types
You specify each column type with a series of properties in a two-column table.

    $ cat types/zipcode.csv
    property,value
    title,United States Zip Code
    inherits,types/string.csv
    regex,[0-9]{5}(?:-[0-9]{4})?

### Tables
Each dataset is represented by a series of columns, ordered as they should
appear in a data table following the schema. For each column, you specify the
column name that should be used in the data table (`colname`) and the type of
the column (`type`).

    $ cat tables/addresses.csv
    colname,    type
    name,       types/fullname.csv
    street,     types/string.csv
    town,       types/string.csv
    zip,        types/zipcode.csv

## Two-file version

## Identifiers
The values for `inherits`, `type` and `table` (as you've seen above)
can be whatever you want, actually. But a csvschema
parser will do fancy things if you use URIs or file paths. If a csvschema
parser, validator, &c. chooses to do anything fancy with these references,
here is how it should resolve them.

The parser should accept an arbitrary for this lookup. For example, let's
say there's a `validate` function written in R. Here's how we might validate
a particular dataset called `subscribers.csv`

```R
validate('data/subscribers.csv', 'tables/addresses.csv', func = read.csv)
```

This means that references in the two different tables will be resolved by
calling `read.csv` on the reference string, which is always a file in this
case. In R, `read.csv`, also handles web URLs. Specifically, it does this.

1. Check for a protocal (like `http://`). If the reference string contains
    one, use that protocal to look up the data table.
2. If it doesn't contain a protocal, try looking up the data table as a
    file on the local filesystem.

This sort of function should be the default function in a csvschema
parser/validator; that is, this should do the same thing.

```R
validate('data/subscribers.csv', 'tables/addresses.csv')
```

You could choose to store your schemas and whatnot in some custom place,
like a relational database or a hosting service that requires authentication.
In that case, you can specify your own function.

```R
validate('data/subscribers.csv', 'address', func = function(id, kind){
  # `kind` is either "type" or "table".
  sqldf(paste('SELECT * FROM,kind,'WHERE "id" = \'',id,'\''), dbname = 'foo.sqlite')
})
```
