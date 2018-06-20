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

HOWTO - Documentation
=====================

As part of the development of sustainable software it is important that code is well documentated to inform developers that need to implement, extend or replace the code about what it does, the inputs, outputs and any dependencies on other software or code. All classes and functions should have matching documentation.

There are 2 key parts of the documentation, the first is for the classes and functions. The documentation should match the PEP8 standard, an example of this is in the `MuG Coding Guidlines <http://multiscale-genomics.readthedocs.io/en/latest/coding_standards.html>`_. The second part is the Architetural Design Record. The ADR should record why key choices have been made, this is especially true if the choices do not match the borm or there has been a major change in a function (addition, removal or completely rewritten). The ADR provides the reasoning behind the code and the documentation string in the functions describe the code. Between them they provide a log of the development of the project.

An example function description should therefore match the following:

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

Building the Documentation
--------------------------

Full documentation for a repository can be built using `Sphinx <http://www.sphinx-doc.org>`_. If the pipeline has been developed based on a fork of the mg-process-test repository it can be done by:

.. code-block:: none
   :linenos:

   cd ${mg-process-test}
   pip install sphinx

   cd docs/
   make html


Updating the documentation
--------------------------

If new pipelines or tools are added to the repository then it is important that they are included in the documentation.

Updates for a new tool - docs/tool.rst
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A new section can be added to the docs/tool.rst file to reflect the new tool.

Before:

.. code-block:: none
   :linenos:

   .. automodule:: tool

      Test Tool
      -----------
      .. autoclass:: tool.testTool.testTool
         :members:

After:

.. code-block:: none
   :linenos:

   .. automodule:: tool

      Test Tool
      ---------
      .. autoclass:: tool.testTool.testTool
         :members:

      Test Tool 2
      -----------
      .. autoclass:: tool.testTool.testTool2
         :members:


Updates for a new pipeline - docs/pipelines.rst
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A new section can be added to the docs/pipelines.rst file to reflect the new pipeline. This requires providing a larger description about the input required for running the pipeline, what it returns and examples about how to run the code locally and within the COMPSs environment.

An example of a pipeline block is as follows:

.. code-block:: none
   :linenos:

   Test Tool
   ---------
   .. automodule:: process_test

      This is a demonstration pipeline using the testTool.

      Running from the command line
      =============================

      Parameters
      ----------
      config : file
         Location of the config file for the workflow
      in_metadata : file
         Location of the input list of files required by the process
      out_metadata : file
         Location of the output results.json file for returned files

      Returns
      -------
      output : file
         Text file with a single entry

      Example
      -------
      To run the script locally this can be done as follows:

      .. code-block:: none
         :linenos:

         cd ${mg-process-test}
         python mg_process_test/process_test.py --config mg_process_test/tests/json/process_test.json --in_metadata mg_process_test/tests/json/input_test.json --out_metadata mg_process_test/tests/results.json --local

      The `--local` parameter should be used if the script is being run within an environment where (py)COMPSs is not installed. It can also be used in an environment where (py)COMPSs is installed, but the script needs to be run locally for testing purposes.

      When using a local verion of the [COMPS virtual machine](http://www.bsc.es/computer-sciences/grid-computing/comp-superscalar/downloads-and-documentation):

      .. code-block:: none
         :linenos:

         cd /home/compss/code/mg-process-test
         runcompss --lang=python mg_process_test/process_test.py --config /home/compss/code/mg-process-test/mg_process_test/tests/json/process_test.json --in_metadata /home/compss/code/mg-process-test/mg_process_test/tests/json/input_test.json --out_metadata /home/compss/code/mg-process-test/mg_process_test/tests/results.json

      Methods
      =======
      .. autoclass:: process_test.process_test
         :members: