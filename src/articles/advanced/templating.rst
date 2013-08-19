Templating using Jinja2
#######################

:date: 2013-07-16 22:00
:slug: Templating and Jinja2
:summary: Using templating to dynamically create text files
:author: Phong Le

Templating is the process of taking information and placing it in a pre-defined set of text to produce a customized text file. This is particularly useful in the context of web development because the process of web templating is used extensively to create "dynamically generated" web pages.

Conceptually, the process of web templating is similar to generating a form letter. First, create the content for the web page. Then identify the parts of the web page where specific information (e.g. name, address, etc) should be inserted. This combination of content and where to insert specific info is called a template.

To create a dynamically generated web page the templating engine inserts information into the form or template whenever needed (i.e. when a user clicks on a link to the page).

Python Templating
-----------------
There are many different approaches to templating and Python-based templating_ engines. From the simple, such as the built-in  `string Template`_ to more structure and comprehensive approaches such as Jinja2_. Some of these systems can be used on their own or in conjunction with a variety of web frameworks. Jinja2 is an example of a standalone templating system.

While others, such as Django_ provides its own templating system that is intended to be used only with the framework. However, with some elbow grease it is possible to use something else_ such as Jinja2. Flask_ uses Jinja2 since it and Jinja2 share the same development team. Pylon is pre-configured to use either Mako_, Genshi_, or Jinja2.

We're going to focus on Jinja2 because it's relatively easy to learn, widely-used, well-supported, and can be used independently of the Flask framework.

Jinja2 Install
---------------
To use Jinja2 as a standalone templating system use pip to install it:

.. code-block:: console

    pip install jinja2

Once the install is complete, we're ready to use Jinja2. To use it import the module at the top of your script. However, we will also need to tell Jinja where to find the templates. Jinja2 uses an Environment object to store configuration information and we use this object to tell Jinja2 where to find the templates.

.. code-block:: python

    from jinja2 import Environment, FileSystemLoader

    env = Environment(loader=FileSystemLoader(TEMPLATE_DIR))
    template = env.get_template('mytemplate.html')

Going forward, we'll be taking at look at Jinja2 using Flask; however the templating syntax remains the same whether Jinja2 is used as part of a web framework or as a standalone system.

Note: it is also possible to pass the template as a string:

.. code-block:: python

    from jinja2 import Template
    template = Template('Hello {{ name }}!')
    template.render(name='John Doe')

Jinja2 with Flask
-----------------

To show Jinja's features and syntax we'll use the Flask-Noiselist tutorial_. Clone and build the project.

A few notes before we dive into the tutorial's templating section:
    1. Templates are just text files.
    2. Templates typically live in the /templates directory in a Flask project and by default Flask uses template files stored in the /templates directory.
    3. Jinja2 templates does not require any special bootstrap code or incantations.

The tutorial creates a main page template (templates/hello.html) that contains the following code:

.. code-block:: html

    <!DOCTYPE html>
        <html>
            <head>
                <title>TODO at Noisebridge</title>
            </head>
            <body>
                <h1>My Personal TODO List</h1>
                <ul>
                    {% for todo in todos %}
                        <li>{{ todo }}</li>
                    {% endfor %}
                </ul>
                <form action="" method="POST" id="add_to_todo_list">
                <input type="text" name="todo_item"/>
                <input type="submit" name="add_todo_submit" value="Add to List!"/>
                </form>
            </body>
    </html>

Jinja2 looks for braces that tells it to insert data or perform specific logic that will ultimately result in text-based insertions.

    1. A template contains variables and expressions which gets replaced with values by the templating engine. For example the {{ todo }} is a variable that will get replaced with a value.

    2. A template can also contain tags, which control the logic of the templating process. In fact, most of the control structure from Python is available in Jinja2 wrapped by the {% %} tags. (Also see the discussion on Filters below.)

    3. There are two kinds of delimiters, {{ }} and {% %}, which Jinja2 looks for tell it to execute statements (such as if /then or for loop) or to insert the result of an expression into the template.

How To Get Data To The Template
-------------------------------

So how does Jinja2 gets the data to make the substitution? When paired with Flask, use Flask's render_tenplate(). render_template() will take a template name and a variable list of data arguments to be used during the templating process. It will return the template with all requested substitutions.

render_template() will look for templates in the /templates directory:

.. code-block:: python

    from flask import render_template

    def index():
        todo_list = ["Watch TV", "Contemplate Work", "Go to Bed"]
        return render_template('hello.html', todos=todo_list)

Note that in the todo example, we sent render_template() a list of todos. But we could have sent it a dictionary such as

.. code-block:: python

    user = {'name': 'Phong'}
    return render_template('index.html', user=user)

then in the template we could refer to the user dictionary as:

.. code-block:: html

    <h1>Hello, {{ user.name }}!</h1>

Note that the template used a dot (.) to access the attribute of a variable.

Template Inheritance
--------------------

Another great Jinja2 feature is template inheritance. Inheritance supports the building of a skeleton page that contains common elements for all pages such as links to Javascript libraries or CSS style sheets. This skeleton, or parent page, also contain place holders, which are called "blocks", that child templates can overrides to provide specific implementation.

For example, Miguel Greenberg_ used the following example in his tutorial to demonstrate how template inheritance works. In his example, the main base template includes the elements common to all pages (file name: base.html):

.. code-block:: html

    <html>
        <head>
        {% if title %}
            <title>{{title}} - microblog</title>
        {% else %}
            <title>microblog</title>
        {% endif %}
        </head>
        <body>
            <div>Microblog: <a href="/index">Home</a></div>
            <hr>
            {% block content %}
            <!-- insert page specific content here -->
            {% endblock %}
        </body>
    </html>

He then defined a page specific template (file name: index.html) that defined a set of HTML code and data substitutions that should be inserted into the base template to produce a specific page.

.. code-block:: html

    {% extends "base.html" %}
    {% block content %}
        <h1>Hi, {{user.nickname}}!</h1>
        {% for post in posts %}
            <div><p>{{post.author.nickname}} says: <b>{{post.body}}</b></p></div>
        {% endfor %}
    {% endblock %}

In this process where one template (index.html) inserts itself in another (base.html), index.html is called the child template that "inherits" from the parent base.html. Furthermore, that the block content definition in index.html "overrides" the block in base.html.

Filters
-------

Filters can take the values of variables and modify it. Specify a filter by typing the pipe symbol folow the name of the filter (with optional arguments in parenthesis). Filters can be chained with the output of one serving as the input for the next.

For example, {{ name|striptags|title }} will strip all HTML tags from the name and will title case the string. Jinja2 provides an extensive list of built in filters_ that provides access to the most common and useful Python functions, but the ability to define custom filters is really useful.

For example, if you need to wrap a link tag around URL string, one way is to define a custom filter which allows you to define the logic using Python, register the custom filter with Jinja2, and use the filter in the template.

.. code-block:: python

    def resurrect_links(tweet_text, links):
    # links is a list of dict(s) containing info about links in the tweet
        if links:
            # if there are multiple links then sort in reverse order of indices
            # such that the last link in the tweet gets resurrected first.
            if len(links) > 1:
                links.sort(key=lambda link:link['indices'], reverse=True)

        # resurrect each links
        for link in links:
            start, end = link['indices']
            tweet_text = tweet_text[:start] + "<a href=\"" + link['resource_url'] + \
                "\"" + ">" + link['display_url'] + "</a>" + tweet_text[end:]
        return tweet_text

    # registers the custom filter with Jinja2
    env.filters['resurrect_links'] = resurrect_links

and the template would use the filter as follow.

.. code-block:: python

    {% if tweet.links %}
        <p>{{ tweet.text|resurrect_links(tweet.links) }}</p>
    {% else %}
        <p>{{ tweet.text }}</p>
    {% endif %}


.. _templating: http://wiki.python.org/moin/Templating
.. _`string Template`: http://docs.python.org/2/library/string.html#template-strings
.. _Jinja2: http://jinja.pocoo.org/docs/
.. _Django: https://www.djangoproject.com
.. _Flask: http://flask.pocoo.org
.. _Genshi: http://genshi.edgewall.org
.. _Pylon: http://docs.pylonsproject.org/projects/pylons-webframework/en/latest/views.html
.. _Mako: http://www.makotemplates.org
.. _else: https://github.com/GaretJax/coffin
.. _tutorial: https://github.com/noisebridge/flask-noiselist
.. _documentation: http://jinja.pocoo.org/docs/templates/
.. _filters: http://jinja.pocoo.org/docs/templates/#builtin-filters
.. _Greenberg: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-ii-templates