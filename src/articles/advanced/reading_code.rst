Reading Code: Standards and Idioms
##################################

:date: 2013-10-08 19:00
:slug: reading-code
:summary: Notes on Reading Code
:author: Cameron Beck

Intro
-----

Programs must be written for people to read, and only incidentally for machines to execute.
    --Abelson & Sussman, Structure and Interpretation of Computer Programs

the PEPs
--------

**PEP** = Python Enhancement Proposal

PEP 20 - The Zen of Python
--------------------------
There are 19 aphorisms in The Zen of Python. All relate to how you write code. Line 7 is "Readability counts."

You can access PEP 20 in a python interactive interpreter by typing the following:

.. code-block:: python

    import this

PEP 8
-----

PEP 8 codifies how python code should look.

“The guidelines provided here are intended to improve the readability of code and make it consistent across the wide spectrum of Python code.”

“A style guide is about consistency...But most importantly: know when to be inconsistent -- sometimes the style guide just doesn't apply...In particular: do not break backwards compatibility just to comply with this PEP!”

**Some PEP-8 tidbits to remember:**

- limit lines to 79 characters
- 4 spaces instead of tabs
- single space after every comma and operator
- CamelCase for classes
- lower_case_with_underscores for functions
- UPPER_CASE_WITH_UNDERSCORES for constants
- Two blank lines between classes and top-level function definitions
- One blank line between class methods.
- Imports should be on separate lines, and should be explicit

Avoid using wildcard imports like such:

.. code-block:: python

    from module import *

Wildcard imports can be dangerous and pollute the local namespace. Multiple wildcards can also confuse which wildcard an offending import is coming from. An imported function can shadow another local or even built-in function.

The following code is broken and will result in a traceback error. The wildcard import of the os module overrides Python's built-in open() function.

.. code-block:: python

    from os import *

    f = open('text.txt')
    print f.read()

It's much better to reference names through their module(fully qualified identifiers)

.. code-block:: python

    import module.name
    module.name

import a long module using a shorter name (alias; recommended)

.. code-block:: python

    import long_module_name as mod
    mod.name

or explicitly import just the names you need.

.. code-block:: python

    from module import name
    name

Module Structure
----------------

.. code-block:: python

    """module docstring"""

    # imports
    # constants
    # exception classes
    # interface functions
    # classes
    # internal functions & classes

    def main(...):
        ...

    if __name__ == '__main__':
        status = main()
        sys.exit(status)
    

Docstrings
----------

A comment on a segment of code that is retained throughout the runtime of the program.

**PEP 257** - Docstring Conventions

Docstrings are written with triple double quotes as the first line of a function, class, or method definition.

Docstrings explain *how* to use the code, made for users.
Comments explain *why* and are for maintainers.

To view a docstring use the help() function.

Self-documenting Code
---------------------

Through adherence to standards and good practices, we can achieve self-documenting code. This means that the code is easier to read and understand, and reduces the need to consult external documentation.


Python Idioms
-------------

An idiom is a means of expressing a recurring construct.

“Generally speaking, a programming idiom is an expression of a simple task, algorithm, or data structure that is not a built-in feature in the programming language being used, or, conversely, the use of an unusual or notable feature that is built into a programming language.”

Multi-line Statements
---------------------

We know that it is possible to use a backslash(\) to continue a statement onto another line. However, a trailing space will invalidate the backslash and break your code. Instead, we can use parentheses because they have implicit continuation.

We can also change the contents without worrying about the location of a backslash.

.. code-block:: python

    value = very_long_variable_name \
            another_longer_variable_name

    value = (very_long_variable_name
            + another_longer_variable_name)

Testing for Truth
-----------------

When testing for truth, it's elegant and efficient to take advantage of the intrinsic truth values (or Boolean values) of Python objects.

.. code-block:: python

    # do this:        
    if x:
        pass

    # not this:
    if x == True:
        pass

    # when testing a list:
    # do this:
    if items:
        pass

    # not this:
    if len(items) != 0:
        pass

Building Strings from Substrings
--------------------------------

Start with a list of strings:
colors = ['red', 'blue', 'green', 'yellow']

We want to join all the strings together into one large string. Especially when the number of substrings is large...

Don't do this:

.. code-block:: python

    result = ''
    for s in colors:
        result += s

This is very inefficient.

It has terrible memory usage and performance patterns. The "summation" will compute, store, and then throw away each intermediate step.

Instead, use .join():

.. code-block:: python

    result = ''.join(colors)

The join() string method does all the copying in one pass.


.. code-block:: python

    x = range(4000000)
    x = [str(i) for i in x]

    results = ''
    for i in x:
        results += i

[Finished in 4.6s]

.. code-block:: python

    x = range(4000000)
    x = [str(i) for i in x]

    results = ''
    results = ''.join(x)

[Finished in 3.0s]

Swapping Values
---------------

We can avoid lengthy value swapping assignments:

.. code-block:: python

    temp = a
    a = b
    b = temp

In python we can write:

.. code-block:: python

    b, a = a, b

It's much more succinct.

You've probably seen this before. But do you know how it works? The comma is the tuple constructor syntax.

Python let's us do single line multi variable assignment through tuple unpacking. This may be difficult to read at first, but it saves space and the use of extra, intermediary variables.

- A tuple is created on the right with our existing values(tuple packing).
- A tuple is the target on the left (tuple unpacking).

In this case, we unpack one tuple into another, both tuples are created on the fly without prior definitions.

We can use this to create a basic Fibonacci sequence generator.

.. code-block:: python

    def fib_gen():
        x, y = 0, 1
    
        while True:
            yield x
            x, y = y, x + y

    x = fib_gen()
    for i in range(6):
        print x.next()

We can take the concept of unpacking further:

.. code-block:: python

    user = ['Jim', 'Salesman', 'Male']
    name, job, gender = user

We unpack the list user into the tuple (name, job, gender)
Python recognizes the spaces in the list as delimiters and assigns each word to the corresponding value.

We can now call name, job, or gender independently and Python returns the value.

We can unpack other lists into potentially useful tuples. For example, getting the numeric day of the week:

.. code-block:: python

    (MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY) = range(1, 8)

We can push this concept further to apply tuple unpacking to a sequence:

.. code-block:: python

    employee_list = [('Jim', 'Salesman', 35000), ('Sue', 'Manager', 45000), ('Jack', 'Assistant', 25000)]

    for name, job, salary in employee_list:
        if salary > 30000:
            print job, name

Advanced String Formatting
--------------------------

.. code-block:: python

    user_1 = {'name': 'Jim', 'age': 27, 'job': 'salesman', 'state': 'Alaska', 'salary': 35000.00}

    print "%(name)s is %(age)i years old and from %(state)s. %(name)s works as a %(job)s and makes $%(salary).2f." % user_1

This could have been written, passing individual keys. Which results in the ugly, unmanageable code below.

.. code-block:: python

    print "%s is %i years old and from %s. %s works as a %s and makes $%.2f." % (user_1['name'], user_1['age'], user_1['state'], user_1['name'], user_1['job'], user_1['salary'])

Revisiting List Comprehensions
------------------------------

As we've learned before, list comprehensions are powerful and compact.

List comprehensions are syntax shortcuts for this general pattern:

.. code-block:: python

    new_list = []
    for item in a_list:
        if condition(item):
            new_list.append(fn(item))

As a list comprehension:

.. code-block:: python

    new_list = [fn(item) for item in a_list if condition(item)]

List comprehensions are clear and concise, up to a point. You can have multiple for-loops and if-conditions in a list comp, but beyond two or three total, or if the conditions are complex, regular for loops should be used. Applying the Zen of Python, choose the more readable way.

Here's a example of multiple for loops and if statements. It has been spaced across multiple lines to make the components very clear. This code returns unique pairs of values in the range 0-12 whose sum is 12.

.. code-block:: python

    li = [(x, y)
         for x in range(13)
         for y in range(13)
         if x + y == 12
         if x >= y]

    print li

List comprehensions can accomplish the same thing as for loops and if statements, in certain cases one maybe be better suited than the other.

Additionally, map() filter() and reduce() can accomplish many of the same things.

**map()**

takes 2 arguments, a function and a sequence and returns a list of return values.

An example that doubles the values in a list:

.. code-block:: python

    def double(n):
        return n*2

    sample_list = [1, 3, 4, 7]

    print map(double, sample_list)

**filter()**

takes 2 arguments, a function and a sequence and returns a sequence containing the sequence items which the function evaluates as true.

An example that filters and returns off numbers:

.. code-block:: python

    def odd(n):
        return n % 2

    sample_list = [1, 3, 8, 22, 17]

    print filter(odd, sample_list)

**reduce()**

takes 2 arguments, a function and a sequence and returns a single value constructed by calling the function on the first two items, then the result on the next item, and so on.

An example that sums the values in a list:

.. code-block:: python

    def sum_func(x, y):
        return x + y

    print reduce(sum_func, range(10))

List comprehensions are a newer addition to python, and in many cases have replaced the need for the previous functions. It is generally more pythonic to use list comprehensions, but there are certain cases where it may be appropriate to use one of these functions. Either way, it is important to be able to recognize them.


SOURCES
-------

**PEP-8**

http://www.python.org/dev/peps/pep-0008/

**PEP-257**

http://www.python.org/dev/peps/pep-0257/

**Code Like a Pythonista: Idiomatic Python**

http://python.net/~goodger/projects/pycon/2007/idiomatic/handout.html

**Idioms and Anti-idioms in Python**

http://docs.python.org/dev/howto/doanddont.html

**Python List Comprehension vs Map**

http://stackoverflow.com/questions/1247486/python-list-comprehension-vs-map