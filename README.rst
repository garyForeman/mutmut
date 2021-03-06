mutmut - python mutation tester
===============================

.. image:: https://travis-ci.org/boxed/mutmut.svg?branch=master
    :target: https://travis-ci.org/boxed/mutmut
 
.. image:: https://readthedocs.org/projects/mutmut/badge/?version=latest
    :target: https://mutmut.readthedocs.io/en/latest/?badge=latest
    :alt: Documentation Status

Mutmut is a mutation testing system for Python 2 and 3, with a strong focus on
ease of use. If you don't know what mutation testing is try starting with `this article <https://hackernoon.com/mutmut-a-python-mutation-testing-system-9b9639356c78>`_.

Some highlight features:

- Found mutants can be applied on disk with a simple command making it very easy to work with the results
- Supports all test runners (because mutmut only needs an exit code from the test command)
- Extremely small and simple implementation (less than a thousand lines)
- Battle tested on tri.struct, tri.declarative, tri.form and tri.table
- Can use coverage data to only do mutation testing on covered lines


Install and run
---------------

You can get started with a simple:

.. code-block:: shell

   > pip install python
   > mutmut

This will by default run pytest on tests in the "tests" or "test" folder and it will try to figure out where the code to mutate lies. Run

.. code-block:: shell

    mutmut --help

for the available flags, to use other runners, etc. The recommended way to use mutmut if
the defaults aren't working for you is to add a block in `setup.cfg`. Then when you
come back to mutmut weeks later you don't have to figure out the flags again, just run
`mutmut` and it works. Like this:

.. code-block:: ini

    [mutmut]
    paths_to_mutate=src/
    backup=False
    runner=python -m pytest
    tests_dir=tests/
    dict_synonyms=Struct, NamedStruct

The output of the mutation tests (if you have surviving mutants) looks like:

.. code-block:: shell

    FAILED: mutmut f/bar.py --apply --mutation "    if foo > bar:⤑0"

You can just copy paste everything after "FAILED:" and run it directly and you'll get the
mutant on disk. You should REALLY have the file you mutate under source code control and committed before you mutate it!


Whitelisting
------------

You can mark lines like this:

.. code-block:: python

    some_code_here()  # pragma: no mutate

to stop mutation on those lines. Some cases we've found where you need to whitelist lines are:

- The version string on your library. You really shouldn't have a test for this :P
- Optimizing break instead of continue. The code runs fine when mutating break to continue, but it's slower.


Example mutations
-----------------

- Integer literals are changed by adding 1. So 0 becomes 1, 5 becomes 6, etc.
- < is changed to <=
- break is changed to continue and vice versa

In general the idea is that the mutations should be as subtle as possible. See `__init__.py` for the full list.


Workflow
--------

This section describes how to work with mutmut to enhance your test suite.

1. Run mutmut. A full run is preferred but if you're just getting started you can exit in the middle and start working with what you have found so far.
2. Apply a surviving mutant to disk by copying and running the apply command from the output.
3. Write a new test that fails
4. Revert the mutant on disk
5. Rerun the new test to see that it now passes
6. Go back to point 2.

Mutmut keeps a result cache in `.mutmut-cache/` so if you want to make sure you run a full mutmut run just delete this directory.

To get a list of the found mutants without rerunning the full mutation testing run:

.. code-block:: shell

    > mutmut --print-cache

You can also tell mutmut to just check a single mutant:

.. code-block:: shell

    > mutmut f/bar.py --mutation "    if foo > bar:⤑0"
