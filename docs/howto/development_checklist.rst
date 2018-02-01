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

If you are adding new functionality to an already existing repository then you can skip ahead to step N.

0 - Copy mg-process-test from GitHub
------------------------------------

0.0 - Create an empty repsoitory
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In GitHub create a blank repository with no README, license or gitignore file. These files will be inherited from the mg-process-test file. For this example it will be called `mg-process-test1.git`.


0.1 - Copy the mg-process-test repository
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

From GitHub take a copy of the mg-process-test repsoitory:

.. code-block:: none

   git clone --depth 1 -b master https://github.com/Multiscale-Genomics/mg-process-test

   cd mg-process-test
   rm -rf .git

   cd ../

   mv mg-process-test mg-process-test1

   cd mg-process-test1

   git init
   git add .
   git commit -m 'Initial commit'

   git remote add origin https://github.com/USERNAME/mg-process-test1.git
   git remote -v

   git push origin master

From here you can then customise the `README.md`, copyright `NOTICE`, setup.py and __init__.py files to match your new repository.

1 - Create a Tool
-----------------

1.1 - Tool Development
^^^^^^^^^^^^^^^^^^^^^^

Using the `testTool.py` script as a template, create you new tool.

Checklist 1
^^^^^^^^^^^

#. There is a license at the header of the script
#. Documentation for each function.
#. Code matches the PEP8 standard (by running pylint).
#. Tool has been added to `docs/tool.rst`


2 - Create a Test to run the Tool
---------------------------------

2.1 - Test Dataset
^^^^^^^^^^^^^^^^^^

Create a small test dataset that can be used when testing the code. This should match the input file type required by the Tool.

When the tool has been run the output for the test datasets should provide a valid result. For example if wrapping a peak caller there should be enough of the genome selected and matching reads that when aligned and the peak caller analyses the alignments it should generate results similar to the original for that region.

Once the datasets have been generated the procedure for how the test sets were created should be documented in a new "NNN.rst" file. This should contain the source of the data, publications, where the files were downloaded from and how the data was handled so that this can be repeated if the datasets need to be regenerated or changed at a later stage. This file should then be linked into the rest of the documentation, this is usually done by linking the file in the table of contents block in the index page.

2.2 - Test Scripts
^^^^^^^^^^^^^^^^^^

Create a script that uses pytest to check that the required output files have been generated and are not empty. Other tests can be added here if there are other aspects that should be tested. Examples could include testing if a JSON object has the expected parameters.

Checklist 2
^^^^^^^^^^^

#. There is a test to run each single tool
#. There is a license header in each test script
#. All functions in the test script are fully documented with details about how to run the test or if other tests need to be run first
#. Test dataset generation has been fully documented and linked to the index.rst file
#. Any scripts developed to create the datasets are stored in `scripts/.` and have matching license headers and documentation
#. All code matches the PEP8 standard (by running pylint).
#. All tests are passing
#. Ensure that the output of running the tests matches what you would expect

3 - Create a Pipeline
---------------------

3.1 - Pipeline Development
^^^^^^^^^^^^^^^^^^^^^^^^^^

Using the `process_test.py` script as a template, create a pipeline to accept the configuration and input JSON files that describe the parameters and files to get passed into the pipeline. The pipeline should manage the passing of file locations and parameters to each of the tools.


3.2 - Create a Test to run the Pipeline
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Create a script that uses pytest to check that the required input files and configuration parameters are accepted by the pipeline and the relevant output files have been generated and are not empty. Other tests can be added to be more comprehensive.

The pipeline is running tools developed as part of part 1, so there should be no need for creating new datasets.

3.3 - Create test config and input JSON files
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

JSON files need to be created that duplicate what would be the expected input coming from the VRE and saved in the `tests/json/.` directory of the repository. Example files can be found in the `HOWTO on Configuration <howto_config.html>`_. There are also examples of these files in mg-process-test in the `test/json/.` These files allow a user to run the sample datasets from the command line either on their own computer or on one with (py)COMPSs installed.

Checklist 3
^^^^^^^^^^^

#. There is a license in the header of all pipelines and tests
#. There is a test to run each pipeline
#. There is documentation for all functions in the pipeline script and test script
#. All code matches the PEP8 standard (by running pylint).
#. All tests are passing
#. Ensure that the output of running the tests matches what you would expect
#. The script can be run from the command line

4 -
