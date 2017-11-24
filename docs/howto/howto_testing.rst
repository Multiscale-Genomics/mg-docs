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

HOWTO - Testing Your Code
=========================

Running the Code
----------------
To run the code it needs a config.json file and an input_metadata.json file to provide the input.

Running the pipeline manually
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: none
   :linenos:

   python process_test.py --config config.json --in_metadata input_files.json --out_metadata output_metadata.json


Testing a Tools and Pipelines
-----------------------------

As defined in the coding standards documentation, it is important to generate scripts for testing the functionality of the tools and workflows. If there are then changes to the code, if it raises errors this is identified sooner rather than later. Within python the use of pytest provides the relevant framework around testing code functionality.

Scripts should be placed in the `<repo>/tests` directory.

An example pytest for the `test_writer <howto_tool.html>`_ tool:

.. code-block:: python
   :linenos:

   """
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
   """

   from __future__ import print_function

   import os.path

   from tool.testTool import testTool

   def test_testTool():
       """
       Test case to ensure that the GEM indexer works.
       """
       resource_path = os.path.dirname(__file__)
       text_file = resource_path + "/test.txt"

       input_files = {}

       output_files = {
           "output": text_file
       }

       metadata = {}

       print(input_files, output_files)

       tt_handle = testTool()
       tt_files, tt_meta = tt_handle.run(input_files, metadata, output_files)

       assert output_files['output'] == tt_files['output']
       assert os.path.isfile(text_file) is True
       assert os.path.getsize(text_file) > 0



Automated Testing
-----------------

Once you have defined your test functions it is handle to then hook up the repository with an automated testing framework that can notify you if there are unexpected changes to the behaviour of your code. This is often triggered whenever there is a push to the repository.


Running in COMPSs
-----------------

It is possible to use a local version of the `COMPS virtual machine <https://www.bsc.es/research-and-development/software-and-apps/software-list/comp-superscalar/>`_ as used by the MuG VRE. Within the VM is is possible to install any required software. To run the application the following command can then be used:

.. code-block:: none

   runcompss                                                         \\
      --lang=python                                                  \\
      --library_path=${HOME}/bin                                     \\
      --pythonpath=/<pyenv_virtenv_dir>/lib/python2.7/site-packages/ \\
      --log_level=debug                                              \\
      process_test.py                                                \\
         --config <repo>/tool_config/process_test.json               \\
         --in_metadata <repo>/tests/json/input_process_test.json     \\
         --out_metadata <repo>/tests/json/output_process_test.json
