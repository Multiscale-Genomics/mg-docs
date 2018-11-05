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

Development Checklist
=====================

This document describes the standard work flow to help developers when creating a new tool or pipeline. The purpose is to aid the developer in the most efficient way for integrating a new tool or pipeline and ensure that all steps have been addressed so that they have a ready to deploy Tool and Pipeline within the MuG VRE.

.. note::  If you are adding a new tool and pipeline to an already existing repository then you can skip ahead and concentrate on **steps 1 to 6**.

.. note::  If you are adding just a new pipeline that just integrates already existing tools then you need to look at **steps 3 to 6**.

0 - Copy mg-process-test from GitHub
------------------------------------

0.0 - Create an empty repository
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In GitHub create a blank repository with no README, license or `.gitignore` file. These files will be inherited from the mg-process-test file. For this example it will be called `mg-process-test1.git`.


0.1 - Copy the mg-process-test repository
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

From GitHub take a copy of the mg-process-test repository:

.. code-block:: none

   git clone --depth 1 -b master https://github.com/Multiscale-Genomics/mg-process-test

   rm -rf mg-process-test/.git
   mv mg-process-test mg-process-test1
   cd mg-process-test1

   git init
   git add .
   git commit -m 'Initial commit'

   git remote add origin https://github.com/<USERNAME>/mg-process-test1.git
   git remote -v

   git push origin master

From here you can then customise the following files to match your new repository:

- `README.md`
- `NOTICE`
- setup.py
- `__init__.py`
- docs/conf.py

The files in `docs` contain boilerplate data that matches the processes and tools already in the repository, so should be updated as you add new pipelines and tools.

1 - Setup Your Python environment
---------------------------------

1.1 - pyenv
^^^^^^^^^^^
This is required for managing the version of Python and the installation
environment for the Python modules so that they can be installed in the user
space.

.. code-block:: none
   :linenos:

   git clone https://github.com/pyenv/pyenv.git ~/.pyenv
   echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bash_profile
   echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bash_profile
   echo 'eval "$(pyenv init -)"' >> ~/.bash_profile

   # Add the .bash_profile to your .bashrc file
   echo 'source ~/.bash_profile"' >> ~/.bashrc

   git clone https://github.com/pyenv/pyenv-virtualenv.git ${PYENV_ROOT}/plugins/pyenv-virtualenv

   pyenv install 2.7.14
   pyenv virtualenv 2.7.14 mg-process-test


1.2 - Install Tool API
^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: none
   :linenos:

   pyenv activate mg-process-test
   pip install git+https://github.com/Multiscale-Genomics/mg-tool-api.git


2 - Create a Tool
-----------------

See the `HOWTO - Tools <howto_tool.html>`_ for details about writing a tool and `HOWTO - Test Your Code <howto_testing.html>`_ about how to write relevant tests

2.1 - Tool Development
^^^^^^^^^^^^^^^^^^^^^^

Using the `testTool.py` script as a template, create you new tool.

Checklist
^^^^^^^^^

#. There is a license at the header of the script
#. Documentation for each function.
#. Code matches the PEP8 standard (by running pylint).
#. Tool has been added to `docs/tool.rst`


3 - Create a Test to run the Tool
---------------------------------

3.1 - Test Dataset
^^^^^^^^^^^^^^^^^^

Create a small test dataset that can be used when testing the code. This should match the input file type required by the Tool.

When the tool has been run the output for the test datasets should provide a valid result. For example if wrapping a peak caller there should be enough of the genome selected and matching reads that when aligned and the peak caller analyses the alignments it should generate results similar to the original for that region.

Once the datasets have been generated the procedure for how the test sets were created should be documented in a new "NNN.rst" file. This should contain the source of the data, publications, where the files were downloaded from and how the data was handled so that this can be repeated if the datasets need to be regenerated or changed at a later stage. This file should then be linked into the rest of the documentation, this is usually done by linking the file in the table of contents block in the index page.

3.2 - Test Scripts
^^^^^^^^^^^^^^^^^^

Create a script that uses pytest to check that the required output files have been generated and are not empty. Other tests can be added here if there are other aspects that should be tested. Examples could include testing if a JSON object has the expected parameters.

Checklist
^^^^^^^^^

#. There is a test to run each single tool
#. There is a license header in each test script
#. All functions in the test script are fully documented with details about how to run the test or if other tests need to be run first
#. Test dataset generation has been fully documented and linked to the index.rst file
#. Any scripts developed to create the datasets are stored in `scripts/.` and have matching license headers and documentation
#. All code matches the PEP8 standard (by running pylint).
#. All new tests have been added to TravisCI
#. All tests are passing
#. Ensure that the output of running the tests matches what you would expect


4 - Create a Pipeline
---------------------

See the `HOWTO - Pipelines <howto_pipeline.html>`_ for details about writing a pipeline and `HOWTO - Test Your Code <howto_testing.html>`_ about how to write relevant tests.

4.1 - Pipeline Development
^^^^^^^^^^^^^^^^^^^^^^^^^^

Using the `process_test.py` script as a template, create a pipeline to accept the configuration and input JSON files that describe the parameters and files to get passed into the pipeline. The pipeline should manage the passing of file locations and parameters to each of the tools.


4.2 - Create a Test to run the Pipeline
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Create a script that uses pytest to check that the required input files and configuration parameters are accepted by the pipeline and the relevant output files have been generated and are not empty. Other tests can be added to be more comprehensive.

The pipeline is running tools developed as part of part 1, so there should be no need for creating new datasets.

4.3 - Create test config and input JSON files
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

JSON files need to be created that duplicate what would be the expected input coming from the VRE and saved in the `tests/json/.` directory of the repository. Example files can be found in the `HOWTO on Configuration <howto_config.html>`_. There are also examples of these files in mg-process-test in the `test/json/.` These files allow a user to run the sample datasets from the command line either on their own computer or on one with (py)COMPSs installed.

Checklist
^^^^^^^^^

#. There is a license in the header of all pipelines and tests
#. There is a test to run each pipeline
#. There is documentation for all functions in the pipeline script and test script
#. Update docs/pipelines.rst to include documentation and links to the new pipeline to import all function documentation
#. All code matches the PEP8 standard (by running pylint).
#. All new tests have been added to TravisCI
#. All tests are passing
#. Ensure that the output of running the tests matches what you would expect
#. The script can be run from the command line


5 - VRE JSON Configuration
--------------------------

See the `HOWTO - Configuration Files <howto_config.html>`_ for details about writing a MuG VRE JSON configuration files.

Checklist
^^^^^^^^^

#. Ensure that there is a JSON configuration file present in the tool_json for each pipeline.


6 - Installation Documentation
------------------------------

Checklist
^^^^^^^^^

#. Make sure that setup.py, setup.cfg and requirements.txt are updated with any new packages required for installation
#. Update docs/install.rst if there is any external software that is required by tool or pipeline along with the required command to install that software


7 - COMPSs testing
------------------

Now that you have a functional pipeline and tool it now needs to be tested within a COMPSs environment. Download the latest version of the `COMPSs virtual machine <https://www.bsc.es/research-and-development/software-and-apps/software-list/comp-superscalar/>`_ from the BSC website.

Checklist
^^^^^^^^^

#. Was it possible to install everything based on the installation scripts and documentation?
#. Do all the test scripts pass when they are run?
#. When the test scripts have run do you get the expected results?
#. Can the pipeline be run using the "runcompss" command?


8 - Hook up your repository for continuous integration
------------------------------------------------------

Now that you have a fully documented pipeline, with tests it is possible to hook up your GitHub repository with ReadTheDocs.org, Travisci.org and Landscape.io. These services will automatically build you documentation, run the tests and check the compliance of the code with that of PEP8 respectively.

It is possible to login to each service using your GitHub account and link the repository.

Checklist
^^^^^^^^^

#. You have your documentation building on ReadTheDocs.org
#. You have your test scripts running on TravisCI and passing
#. Your code is being continually analysed by Landscape.io


9 - Congratulations
-------------------

You now have a pipeline that could be integrated into the MuG VRE.
