# Cube Style Guide

* Use YAML for data modeling.
* Cubes must remain private, only views can be exposed to visualization tools.

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

* A cube's name should represent business entiity and be plural if possible. If cube's name may clash with view's name use postfix `_base` for cube's name, e.g. `opportunities_base.yml`
* Cubes properties should be ordered as
  - sql
  - description
  - pre_aggregations
  - joins
  - measures
  - dimensions
* Primary keys for the cube should be the first dimension listed

### Dimensions & measures

* Dimensions and measures properties should be ordered as:
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
* Use description if name is not intuitive

## Views

* Views prioperties should be ordered as
  - name
  - description
  - includes
  - measures
  - dimensions


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

* Use trailing commas

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

