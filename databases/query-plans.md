---
id: query-plans
aliases: []
tags:
  - databases
  - programming
  - performance
  - wiki
---


# Query Plans

Each time when you query a database,
it generates N query plans and figures out
which one is the cheapest to run.

DBs use two types of Scans:

- Seq Scan
        - stands for Sequential Scan and scans the whole table
- Index Scan
        - uses an index to scan only a subset of the table

## Example Query plan

```sql
Seq Scan on table_name
(cost=0.00...243.00, rows=10000, width=24)
```

*The width is the estimated average bytes returned by the query.*

## How is the cost calculated?

The DB fetches data in *pages* and converts it into *rows*. Each
page being fetched is multiplied by a *fetching cost* (`seq_page_cost`) and
is then added to the number of converted rows times the *row processing cost* (`cpu_tuple_cost`).

> num_of_pages x seq_page_cost + num_of_rows x cpu_tuple_cost

When applying a `WHERE` filter an additional performance cost is incurred (`cpu_operator_cost`).

> num_of_pages x seq_page_cost + num_of_rows x cpu_tuple_cost + num_of_rows x cpu_operator_cost

By default, the `seq_page_cost` is the most expensive and
the `cpu_operator_cost` the cheapest. These costs are settings and can be changed,
to influence which query plans the DB will deem as the most efficient.

## Index

Like table of contents in a book, an index can be used to drastically
reduce the time needed to find the relevant information you need.
This is because you're only browsing a fraction of the pages in the entire book.

Indexes are however, not a magic solution for every query related problem.
In fact, the index might not always be used, especially if it turns out to
be marginally less work than a sequential scan.

> It always depends on the shape of the data

Indexes themselves have a cost too. When updating a row, or adding one, the database
has to update the index as well! Ideally the cost of maintaining the index
is dwarfed by the cost savings in seeking rows.

> **Rule of thumb**
> Indexes are most useful for:
>
> - Filtering `WHERE`
> - Sorting `ORDER_BY`
> - Aggregating `GROUP_BY`
>
> ... on columns with a high cardinality; meaning lots of different values

A partial index can be used to filter out common values in a column.
