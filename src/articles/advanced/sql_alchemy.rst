SQL Alchemy
###########

:date: 2013-08-13 14:00
:slug: sql-alchemy
:summary: Notes on SQL Alchemy
:author: Joseph Watters

Intro
-----

**Pros:**

- ORM, database logic can be written out in Python.
- Impermeable to SQL insertion attacks.
- Supports many database backends.

**Cons:**

- Some API issues
- Don’t use in production
- Meant to remain small


SET UP YOUR DB
--------------

The create_engine instance: underlying constructs are Dialect and Pool.

.. code-block:: python

    from sqlalchemy import *
    db = create_engine('sqlite:///:memory:')
    db.echo = True
    metadata = MetaData(db)

Create a Table
--------------

.. code-block:: python

    users = Table('users', metadata,
        Column('user_id', Integer, primary_key=True),
        Column('user_name', String(40)),
        Column('email', String(40)),
        Column('password', String(20)))

    users.create()

Run
---

.. code-block:: python

    def run(stmt):
        rs = stmt.execute()
        for row in rs:
            print row

We will need this method later to print our queries.

Populate
--------

Your database will need some mock data, so point here for randomly generated data tables.

**Quick Populate:**

.. code-block:: python

    def add_user():
        with open('users.csv', 'rU') as f:
            reader = csv.reader(f)
            reader.next()
            for row in reader:
                row = row[0].split(',')
                append_user = users.insert()
                append_user.execute(username=row[3], email=row[2], password=row[0], user_id=row[1])

Building a Relationship
-----------------------

The creation of a related table is similar to our previous table, but with one addition. The `ForeignKey('<table.column>')` method will link rows of a secondary table with a primary table by storing a mapped value within the secondary table row. Importantly, a foreignkey must be identified when appending new rows with the intention of mapping foreignkey values with other tables.

In the following example, the user_id in the location table is mapped with the user_id column in the users table. The naming of a foreign key column does not require an exact copy of the mapped column name.

.. code-block:: python

    locations = Table('locations', metadata,
                      Column('location_id', Integer, primary_key = True),
                      Column('user_id', Integer, ForeignKey('users.user_id')),
                      Column('city', String(20),
                      Column('state', String(20),
                      Column('postal_code', Integer)))
    locations.create()


Joins
-----

Join statements combine rows from two or more tables around a common field: the dictating of commonality allows for narrowed queries. Rows without common values can still be joined with other tables, but values may result in null values wherever conditions are not met in outer joins and ignored in inner joins.

The most common join is an inner join. It returns only the rows of tables where join parameters are met. SQLAlchemy allows for at least two methods to define tables and conditions where rows are to be joined.

.. code-block:: python

    select([<table1>,<table2>],<conditions>)

    select(users,locations)

will print out the multiplicative value of the table. Adding a condition will produce a linear result. Adding further condition logic will tighten down results.

**Smart Join**

.. code-block:: python

    join(<table1>,<table2>).select()


The opposite is an outer join, which returns a more inclusive query. An outer join in SQLAlchemy defaults to a left outer join. Ie. All rows from table1 will be returned and any corresponding rows from table2.
“Outer Join”

.. code-block:: python

    outerjoin(<table1>,<table2>).select

With these two methods all it takes is a basic understanding of how joins behave in order to accurately dictate query results from multiple tables.


Fetching and Selecting:
-----------------------

The select method produces a nontype sql query. Printing the contents of the query requires the `execute()` and `fetch()` methods.

**Fetch**

.. code-block:: python
    print "Fetch:"
    fetch_query = users
    a = users.select()
    b = a.execute()
    c = b.fetchone()
    print c[0], c[1]
    print

    Fetchone()
    # To return
    # Returns a single row
    Fetchall()
    #Returns a list of rows

Select statements are going to be the workhorse when it comes to querying. Here are the permutations of logic within a select method.

.. code-block:: python
    select(<condition>)
    # Must meet condition in order to return row.

    select(and_()) | select()
    # Conditional logic, all parameters must be met.

    select(or())
    #Conditional logic, one of the parameters must be met.

    select(not_())
    # Conditional logic, none of the parameters must be met.

    select(<>.startswith(‘’))
    # Value of column must start with string value in order to return row.

    select(<>.like(‘’))
    #Value of column must be like string value in order to return row.

    select(<>.endswtih(‘’))
    # Value of column must end with string value in order to return row.

    select(<>.between(x,y))
    #Value of column must be between values in order to return row.

    select(<>.in_(<array>))
    #Value of column must be in array in order to return row.

    select([<table or column>], <condition>)
    #Primary select utilization, tables or columns join around met conditions.
    select([func.count(<table.c.column>)])

Indexing
--------

.. code-block:: python
    from sqlalchemy import Index

    Index(‘users_index’, users.c.user_name)
    User = Table(‘users’, metadata,
        Column(‘name’, String(50), index=True)


Schema Migration
----------------

Use either SQLAlchemy Migrate or Alembic for your migration needs. Although Alter statements and the DDL construct permit schema modifications, it is wise to utilize automated tools for easy migration. Documentation can be found at the links provided.


Mapper
------

The `mapper()` function takes two parameters; the class and the object to be mapped.

.. code-block:: python

    Session
    session = create_session()




