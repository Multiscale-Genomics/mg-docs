.. See the NOTICE file distributed with this work for additional information
   regarding copyright ownership.

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.

MuG Coding Guidelines
=====================

The purpose of this document is to provide a description of the standards that
code should conform to so that everything can be share and developed with ease.

Language and Versions
---------------------

- Python 2.7
- Python 3.5

Installation Method
-------------------

- PIP

Environment Management
----------------------

- pyenv
- pyenv-virtualenv

Style
-----

This should follow the PEP8 standard defined by the
`Python community <https://www.python.org/dev/peps/pep-0008/>`_. Also check out
the
`Google Python Style Guide <https://google.github.io/styleguide/pyguide.html>`_,
a quick and easy reference.

This should be enforced with the use of `pylint <https://www.pylint.org/>`_ to
ensure that we are matching the PEP8 coding standard.

In addition for every script that is written at the top of ALL python
scripts/modules should be the stub license agreement

Header
------
At the top of all scripts and modules there should be the minified license
version for the code. There should also be a full copy of the licence in with
the repo as part of the root _dir and a reStructuredText version as part of the
documentation.

As part of the head section is also the shebang (`#!`) line. This should only be
included if the script is an executable and refer to the form:

.. code-block:: none
   :linenos:

   #!/usr/bin/env python

If the file just contains classes and functions then no shebang is required.


Repository Structure
--------------------

This is based on python coding standards (PEP8) and the requirements for
installation (pip) and documentation (as detailed below). The base contents of a
git repository should include:

.. code-block:: none
   :linenos:

   <repo_name>/
      docs/
         conf.py
         index.rst
         install.rst
         license.rst
         ...
      <module>/
         __init__.py
         ...
      tests/
         data/
         test_<function_name>.py
      LICENSE
      README.md
      requirements.txt
      setup.cfg
      setup.py


Documentation
-------------

For this we use ReadTheDocs. This is based on the Sphinx annotation servers and
the reStructuredText format (
`Primer <http://www.sphinx-doc.org/en/stable/rest.html>`_ and
`RTD related docs <http://documentation-style-guide-sphinx.readthedocs.io/en/latest/style-guide.html>`_)

The code for a basic setup within a repo is as follows:

.. code-block:: none
   :linenos:

   cd <repo_root>

   pip install sphinx

   mkdir docs
   cd docs
   sphinx-quickstart

Once the `docs` folder has been generated the documentation can be built with:

.. code-block:: none
   :linenos:

   cd <repo_root>/docs
   make html

It is advisable to buid the repo locally to remove the majority of the bugs
before submitting to GitHub and letting the docs build on RTD.

Common extensions include:

.. code-block:: none
   :linenos:

   extensions = [
       'sphinx.ext.autodoc',
       'sphinx.ext.napoleon',
       'sphinx.ext.viewcode',
   ]

The current theme across all projects is `default`. This can be set like so:

.. code-block:: none
   :linenos:

   html_theme = 'default'

There is an issue with the display of code blocks, so there needs to be 2 extra
style files:

_static/style.css
^^^^^^^^^^^^^^^^^

.. code-block:: none
   :linenos:

   .rst-content .highlight > pre {
       line-height: 1.5;
   }

_templates/layout.html
^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: none
   :linenos:

   {% extends "!layout.html" %}
   {% block extrahead %}
       <link href="{{ pathto("_static/style.css", True) }}" rel="stylesheet" type="text/css">
   {% endblock %}


Classes and Functions
---------------------

All functions should have matching documentation describing the purpose of the
function, the inputs, outputs and where relevant an example piece of code
showing how to call the function:

.. code-block:: python
   :linenos:

   """
   Assembly Index Manager

   Manges the creation of indexes for a given genome assembly file. If the
   downloaded file has not been unzipped then it will get unzipped here.
   There are then 3 indexers that are available including BWA, Bowtie2 and
   GEM. If the indexes already exist for the given file then the indexing
   is not rerun.

   Parameters
   ----------
   file_name : str
      Location of the assembly FASTA file

   Returns
   -------
   dict
      bowtie : str
         Location of the Bowtie index file
      bwa : str
         Location of the BWA index file
      gem : str
         Location of the gem index file

   Example
   -------
   .. code-block:: python
     :linenos:

     from tool.common import common
     cf = common()

     indexes = cf.run_indexers('/<data_dir>/human_GRCh38.fa.gz')
     print(indexes)


   """


Architectural Design Record (ADR)
---------------------------------

For all repositories there should be a document called adr.rst. This should
record choices that have been made and summaries the reason for those
decisions. This is to provide an in-code record of the design process and
reasoning behind why technologies have been selected. In the case of python,
pytest, pyenv and pyenv-virtualenv this is the standard setup for use within the
pyCOMPSs environment. It is the selection of the key technology that is
important for the most part, but there will be times that one technology was
chosen over another due to the libraries that are used.


Testing
-------

pytest is the standard in the Python community and has been adopted for testing
within the MuG WP4 related code.

As with all python scripts these should have the licence stub and documentation
for all functions.

Runs of tests should also tidy up after themselves once they have completed so
that the environment is clean ready for the next test case to run. This could
mean that some files will get generated multiple times, but these should be
smalls sample datasets.

The following options should be used to test code:

.. code-block:: none
   :linenos:

   # Run only the tests
   pytest

   # Run only pylint as a test
   pytest --pylint --pylint-rcfile=pylintrc -m pylint

   # Run both
   pytest --pylint --pylint-rcfile=pylintrc

There will also be times when there are sections of code that are under
development or when a test needs to not be included as it is long running or has
a bug. To handle this pytest has decorators for this. It a test is to not be
used within the TravisCI environment then the following decorator should be
used:

.. code-block:: none
   :linenos:

   @pytest.mark.underdevelopment

pytest can then be run in the following manner:

.. code-block:: none
   :linenos:

   # Runs all tests
   pytest

   # Runs only those marked as underdevelopment
   pytest -m "underdevelopment"

   # Runs all tests except those underdevelopment
   pytest -m "not underdevelopment"


Sample Data
^^^^^^^^^^^

For all test cases there should be matching datasets that are packaged within
the repo.

All datasets should be in the directory `<repo>/tests/data` with a name patching
the pattern <script_name>.<species>.<assembly>.fasta for genome files and
<script_name>.<accession>.fastq for read files.

Only the raw files should be stored. For testing these should be small files
(~100kB).

Large files can be store, but in cases like that it might be best to have a
generation script that can calculate the relevant file with the data structure.
If this is part of a reader then it should be part of the DM API and stored
within the `dm_generator` directory. The script should be runnable from the
command line but should also be able to be run by the reader when the `user_id`
is `test`. The generated file should be saved to the `/tmp/` folder as
`sample_<reader-tag>.<file-tag>`.
