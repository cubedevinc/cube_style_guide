# Cube Style Guide

Use this style guide when working on `cube_analytics` project, building demos, writing documentation. When working with customers, follow customer style guide if they have any, otherwise use this one.

* Default to YAML for data modeling. Use JS data modeling when you need to have dynamic data models.
* Use snake case, even with JS data models.
* Cubes must remain private, set `shown: false` for all cubes. Only views can be exposed to visualization tools.

## Structure of our Cube project

Views and cubes folders structure should reflect business units structure.

Views should be designed for data consumers and optimized for consumption in the visualization tools.

```
cube_project
└── schema
    ├── cubes
    │   ├── finance
    │   │   ├── stripe_invoices.yml
    │   │   └── stripe_payments.yml
    │   └── sales
    │       └── base_opportunities.yml
    └── views
        ├── product
        │   └── cloud_tenants.yml
        └── sales
            └── opportunities.yml 
```


## Cubes

* A cube's name should represent business entiity and be plural. If cube's name may clash with view's name use prefix `base_` for cube's name, e.g. `base_opportunities.yml`.
* Applicable cube parameters should be ordered as:
  - sql
  - description
  - pre_aggregations
  - joins
  - measures
  - dimensions
* Primary keys for the cube should be the first dimension listed.

### Dimensions & measures

* Applicable dimensions and measures parameters should be ordered as:
  - name
  - description 
  - sql
  - type
  - primary_key
  - sub_query
  - shown
  - format
  - filter
  - drill_members
* Use description if name is not intuitive.

### Example cube


```yaml
cubes:
  - name: line_items
    sql: SELECT * FROM public.line_items
      
    joins:
      - name: products
        sql: "{CUBE}.product_id = {products}.id"
        relationship: belongs_to
        
      - name: orders
        sql: "{CUBE}.order_id = {orders}.id"
        relationship: belongs_to

    measures:
      - name: count
        type: count

      - name: total_amount
        sql: price
        type: sum

    dimensions:
      - name: id
        sql: id
        type: number
        primary_key: true
        
      - name: created_date
        sql: created_at
        type: time
```

## Views

* Applicable views parameters should be ordered as
  - name
  - description
  - includes
  - measures
  - dimensions
* Use comments with `includes` property of view to delineate measures and dimensions


### Example view

```yaml
views:
  - name: customers_view

    includes:
      # measures
      - users.total_orders_amount
      - users.average_lifetime_value

      # dimensions
      - users.signup_date
      - users.most_recent_order_date
      - users.number_of_orders

    measures:
      - name: count
        sql: '{users.customers_count}'
        type: number
```

## SQL style guide

* Use trailing commas.
* Keywords and function names should all be uppercase.
* Use `!=` instead of `<>`
* Always use the `AS` keyword when aliasing columns, expressions, and tables.
* Unless SQL query is `SELECT * FROM table_name` start SQL query from new line.
* Use new lines, optimize for readability and maintainability
* Use CTEs rather than subqueries.
* When joining multiple tables, always prefix the column names with the table name/alias.
* Use single quotes for strings.
* Avoid initialisms and unnecessary table aliases.
* If there's only one thing, put it on the same line as the opening keyword.
* If there are multiple things, put each one on its own line (including the first one), indented one level more than the opening keyword.
* Indents should be 2 spaces.


### Example SQL

```yaml
cubes:
  - name: california_users
    sql: 
      SELECT 
        id,
        first_name,
        last_name
      FROM public.users
      WHERE state = 'CA'

    measures:
      - name: count
        type: count

    dimensions:
      - name: id
        sql: id
        type: number
        primary_key: true

      - name: first_name
        sql: first_name
        type: string

      - name: last_name
        sql: last_name
        type: string

```

## YAML style guide

* Use `.yml` extension instead of `.yaml`
* Indents should use two spaces.
* List items should be indented.
* Use a new line to separate list items that are dictionaries, where appropriate.
* Lines of YAML should be no longer than 80 characters.

### Example YAML

```yaml
cubes:
  - name: users
    sql: SELECT * FROM public.users

    measures:
      - name: count
        type: count

      - name: total_orders_amount
        sql: '{lifetime_value}'
        type: sum

    dimensions:
      - name: id
        sql: id
        type: number
        primary_key: true

      - name: city
        sql: city
        type: string
        
      - name: lifetime_value
        sql: '{line_items.total_amount}'
        type: number
        sub_query: true
```

## Credits

This style guide was inspired in part by:

* [Brooklyn Data' SQL style guide](https://github.com/brooklyn-data/co/blob/main/sql_style_guide.md)
* [LAMS Style Guide](https://looker-open-source.github.io/look-at-me-sideways/rules.html)

