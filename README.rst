===========
**PyActor**
===========
*The minimalistic python actor middleware*
******************************************

PyActor is a python actor middleware for an object oriented architecture
constructed with the idea of getting two remote actors
to quickly communicate in a very simple, lightweight and minimalistic way.

It supports two versions:

* Threading
* Gevent green threads


It also includes communication between machines using XMLRPC and a second version
that uses RabbitMQ message system in a transparent way.

************
Installation
************
Install using::

    python setup.py install

or::

    pip install pyactor

Check that works executing the examples that you can find by cloning `the repository <https://github.com/pedrotgn/pyactor>`_::

    cd examples
    python sample1.py
    ...

Check also the docs for a tutorial:

.. image:: https://readthedocs.org/projects/pyactor/badge/?version=latest
    :target: http://pyactor.readthedocs.io/en/latest/?badge=latest
    :alt: Documentation Status

Commits are build and tested automatically at `Travis-CI <https://travis-ci.org/pedrotgn/pyactor>`_.

.. image:: https://travis-ci.org/pedrotgn/pyactor.svg?branch=master
    :target: https://travis-ci.org/pedrotgn/pyactor

See code coverage at `codecov.io <https://codecov.io/gh/pedrotgn/pyactor>`_ or `coveralls.io <https://coveralls.io/github/pedrotgn/pyactor>`_.

.. image:: https://codecov.io/gh/pedrotgn/pyactor/branch/master/graph/badge.svg
    :target: https://codecov.io/gh/pedrotgn/pyactor

.. .. image:: https://coveralls.io/repos/github/pedrotgn/pyactor/badge.svg?branch=master
    :target: https://coveralls.io/github/pedrotgn/pyactor?branch=master

The code is also checked for its health at every push by `landscape.io <https://landscape.io/github/pedrotgn/pyactor>`_
(PEP8, common bad smells, etc.):

.. image:: https://landscape.io/github/pedrotgn/pyactor/master/landscape.svg?style=flat
   :target: https://landscape.io/github/pedrotgn/pyactor/master
   :alt: Code Health


First steps
===========

This library is implemented using two types of concurrence:

* ``'thread'`` : classic threads
* ``'green_thread'`` : Gevent

Green threads give a performance almost twice better.

You will need to specify which one you are going to use at the beginning of your
script with ``set_context('TYPE')``. Where type is one of the two keywords
above.

Then, first of all, a ``Host`` is needed in order to create some actors.
Use it to spawn actors by giving the class type of the actor to create
and one string that will identify it among the host. See example::

    h = create_host()
    actor1 = h.spawn('id1', MyClass)

The class of an actor must have defined its methods in the _tell and _ask lists
so they can be called through the proxy. In the _tell list will be named those
methods meant to be asynchronous and in the _ask list, the synchronous ones.
In this example we have a class ``MyClass`` with a sync method *ask_me()* and an
async method *tell_me()*::

    class MyClass:
        _tell =['tell_me']
        _ask = ['ask_me']
        def tell_me(self, msg):
            print msg
        def ask_me(self):
            return 'hello back'

As you can see, the async method receives a message and simply prints it while
the sync method returns a result. You can now call this methods from your main
code::

    actor1.tell_me('Hello')
    print actor1.ask_me()

Remote connections
==================

Unlike other library solutions, PyActor supports remote communication between
various machines by only giving an IP to the host. For example::

    host = create_host('http://127.0.0.1:1277/')

An this host is online, so the other machine only needs to lookup for it::

    host = create_host('http://127.0.0.1:1679')
    remote_host = host.lookup_url('http://127.0.0.1:1277/', Host)

Or directly get one of its actors::

    c = host.lookup_url('http://127.0.0.1:1277/id1', 'MyClass', 'module')

Tutorial
========
PyActor has many examples and a tutorial explaining all its functionalities.
This examples can be found in the ``'pyactor/examples'`` directory of the project
(`github <https://github.com/pedrotgn/pyactor>`_).
They are also explained in the documentation as a tutorial, hosted at
`readthedocs.org <http://pyactor.readthedocs.io/en/latest/?badge=latest>`_ which you can find above.
