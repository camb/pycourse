Testing: Unittest and Nosetest
##############################

:date: 2013-08-20
:slug: unittest_nosetest
:summary: Introduction to Unittest and Nosetest
:author: James Sutterfield

Levels of Testing
-----------------
Unit Testing (aka Component Testing)
    Tests that verify functionality of specific section of code
    Usually at the function level
    Ensures building blocks of software works independently
Integration Testing
    Tests that major parts of the system work well together
    Usually done between dependent modules to demonstrate that they work together correctly
System Testing
    Testing a complete and fully integrated softward production
    Concentrated on finding bugs/defects based on software application behavior, software design and expectation of end
    user.
Acceptance Testing
    Acceptance tests are created to mirror the user stories created by/for the software's customer. 
    If the acceptance tests all pass, it means the software should meet all of the user's requirements. 

Unittest and Nosetest are, you guessed it, Unittests.

Why Write Unit Tests?
---------------------
*"Code without tests is broken by design."* - Jacob Kaplan Moss
    * Think of as insurance policy
    * Ensures code won't regress after a bug fix from future changes
    * Concrete proof that code works correctly, as advertised
    * If contributing to Open Source, it's the first step in bug fixes, or if you're contributing a package, it gives your code a great deal more credibility.
    * If you have close to full test coverage, you can run your full test suite each time you make a change to the code base to ensure you haven't broken anything. This technique is used extensively by advocates of "Continuous Integretation" and allows code to be deployed to production a great deal faster, and more reliably than before.

Important Unit Test Concepts
----------------------------
*Test Fixture*
    A test fixture represents the preparation needed to perform one or more tests, and any associate cleanup actions. This may involve, for example, creating temporary or proxy databases, directories, or starting a server process. (For those who attended Joe's presentation (which I unfortunately didn't), think initializing the db before adding data to it).
*Test Case*
    A test case is the smallest unit of testing. It checks for a specific response to a particular set of inputs. unittest provides a base class, TestCase, which may be used to create new test cases. (eg assert(2+2 == 4)) 
*Test Suite*
    A test suite is a collection of test cases, test suites, or both. It is used to aggregate tests that should be executed together. (eg a module which contains test cases)
*Test Runner*
    A test runner is a component which orchestrates the execution of tests and provides the outcome to the user. The runner may use a graphical interface, a textual interface, or return a special value to indicate the results of executing the tests. (ie, a program which will run all of your test fixtures to set-up necessary components for the test, then execute all of the test cases which are found in test suites, and finally report the results back to you. UnitTest and Nosetest).

Difference Between UnitTest & Nosetest
--------------------------------------
*UnitTest*
    * Part of the Python Standar Library
    * Unit Testing Framwork, ie, supports test automation, setup and teardown code for tests, aggregation of tests into collections, and independence of the tests from the reporting framework. 
    * Uses classes which form bulk of api for testing.
*Nosetest*
    * Nosetest is a 3rd party package (pip install nose)
    * Extends UnitTest, giving it more capabilites, features, and plugins. 
    * Can run testsuites which aren't stricly UnitTest, ie, it can run simple test functions, as well as test classes that are not subclasses of unittest.TestCase.

Examples
--------
Here's a simple module which contains the class "Rectangle." Rectangle objects take a length and width during instantiation,
and have methods for returning area and perimeter, setting length and width to new values, and returning the current value of length and width. Length and width must obviously be great than 0. We'll run over how you could test this code using UnitTest and Nosetest.

.. code-block:: python

    # rect.py

    class Rectangle(object):
        def __init__(self, length, width):
            if length <= 0 or width <= 0:
                raise InvalidSideException("Length and Width must be greater than 0")
            self.length = length
            self.width = width

        def get_area(self):
            return self.length * self.width

        def get_perim(self):
            return self.length + self.width * 2

        def set_sides(self):
            if length <= 0 or width <= 0:
                raise InvalidSideException("Length and Width must be greater than 0")
            self.length = length
            self.width = width

        def get_sides(self):
            return x, y

    class InvalidSideException(Exception):
        pass


UnitTest
--------
The basic workflow for testing with UnitTest is the following

    1) You define your own class derived from unittest.TestCase.
    2) Then you fill it with functions that start with "test_".
    3) You run the tests by placing unittest.main() in your file, usually at the bottom.

So, to create a tests to make sure our Rectangle class is operating as expected, we could create
a *test suite* that like this which tests all of its major functionalities:

.. code-block:: python

    # "Test Suite"
    # test_rect.py

    import unittest
    from rect import Rectangle, InvalidSideException

    class RectangleTestCase(unittest.TestCase):

        # "Test Fixture"
        def setUp(self):
            """ Ran before each test """
            self.rectangle = Rectangle(2, 5)

        # "Test Case"
        def test_area(self):
            self.assertEqual(self.rectangle.get_area(), 10)

        # "Test Case"
        def test_perim(self):
            self.assertEqual(self.rectangle.get_perim(), 14)

        # "Test Case"
        def test_get_sides(self):
            self.assertEqual((2, 5), self.rectangle.get_sides())

        # "Test Case"
        def test_set_sides(self):
            self.rectangle.set_sides(10, 20)
            self.assertEqual((10, 20), self.rectangle.get_sides())

        # "Test Case"
        def test_exception(self):
            self.assertRaises(InvalidSideException, self.rectangle.set_sides, -5, 10)

    if __name__ == '__main__':
        unittest.main()

You'll notice a common pattern of using self.assert[condition] when writing a test case. That's essentially all you're doing is checking that a single piece of code has a value or behavior that you'd expect. Here we used assertEqual and assertRaises. but there's a bunch of others at your disposal\:

    * assertEqual(a, b)   a == b   
    * assertNotEqual(a, b)    a != b   
    * assertTrue(x)   bool(x) is True  
    * assertFalse(x)  bool(x) is False     
    * assertIs(a, b)  a is b  2.7
    * assertIsNot(a, b)   a is not b  2.7
    * assertIsNone(x) x is None   2.7
    * assertIsNotNone(x)  x is not None   2.7
    * assertIn(a, b)  a in b  2.7
    * assertNotIn(a, b)   a not in b  2.7
    * assertIsInstance(a, b)  isinstance(a, b)    2.7
    * assertNotIsInstance(a, b)

You'll also notice a good deal of boilerplate. You have to import unittest, create a sublcass of unittest.TestCase and create methods for that class. Avoiding all of that overhead is something where nose shines. Let's look at how we could write that same test with nose.

NoseTest
--------
Unlike UnitTest, nose doesn't have just a single api and syntax required for unit tests. It can run unit tests written using the UnitTest library, but it can also run doctests, and standalone functions as well. Here's how we could write our rect.py tests using functions instead\:

.. code-block:: python

    from rect import Rectangle

    def test_area():
        rectangle = Rectangle(2, 5)
        assert rectangle.get_area() == 10

    def test_perim():
        rectangle = Rectangle(2, 5)
        assert rectangle.get_perim() == 14

    def test_get_sides():
        rectangle = Rectangle(2, 5)
        assert rectangle.get_sides() == (2, 5)

    def test_set_sides():
        rectangle = Rectangle(2, 5)
        rectangle.set_sides(10, 20)
        assert rectangle.get_sides() == (10, 20)

**Much** less boilerplate to get the tests up and running. Here, nose is really just running as a stripped down test runner. It finds testcases, executes them, and reports back the results. If you were also felt inclined to use doctests in your code (be warned: Liz will probably yell at you for this), you could do that as well. Here's our rect.py module with a doc test included:

.. code-block:: python

    # rect.py w/ doctests

    class Rectangle(object):
        """
        >>> x = Rectangle(5, 5)
        >>> x.get_area()
        5
        """
        def __init__(self, length, width):
            if length <= 0 or width <= 0:
                raise InvalidSideException("Length and Width must be greater than 0")
            self.length = length
            self.width = width

        def get_area(self):
            return self.length * self.width

        def get_perim(self):
            return self.length + self.width * 2

        def set_sides(self, length, width):
            if length <= 0 or width <= 0:
                raise InvalidSideException("Length and Width must be greater than 0")
            self.length = length
            self.width = width

        def get_sides(self):
            return self.length, self.width

    class InvalidSideException(Exception):
        pass

To have nose look for doctests, just be sure to include the --with-doctest option.

Nose doesn't have to operate as a minimalistic testing framework either, it supports fixtures at the module, class, and function level. Here's our test_rect.py written with a set-up fixture which creates our instance of rectangle each time:

.. code-block:: python

    from rect import Rectangle
    from nose import with_setup

    def setup_function():
        global rectangle
        rectangle = Rectangle(2, 5)

    @with_setup(setup_function)
    def test_area():
        assert rectangle.get_area() == 10

    @with_setup(setup_function)
    def test_perim():
        assert rectangle.get_perim() == 14

    @with_setup(setup_function)
    def test_get_sides():
        assert rectangle.get_sides() == (2, 5)

    def test_set_sides():
        rectangle = Rectangle(2, 5)
        rectangle.set_sides(10, 20)
        assert rectangle.get_sides() == (10, 20)
