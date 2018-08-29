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

HOWTO - Configuration Files
===========================

Tool Description
----------------

This configuration file is used to describe the Tool and inform the VRE about what arguments are required by the tool and a list of the file types that can be used as inputs and the matching names that should be used as input parameters.

Below is the example Tool config file for the process_test workflow. It is located in the tool_config directory within the repository. For a full description of all of the parameters please consult the `Tool Integration Document <https://docs.google.com/document/d/1Fid4RkNyt9-_0g_SrCw8J1k8MrOMZI4GLzzpdCAATZc/edit?usp=sharing>`_.

.. code-block:: none

   {
       "_id": "process_test",
       "name": "Process Test",
       "title": "Test Workflow",
       "short_description": "Generates file with some text",
       "owner": {
           "institution": "EMBL-EBI",
           "author": "Mark McDowall",
           "contact": "mcdowall@ebi.ac.uk",
           "url": "https://github.com/Multiscale-Genomics/mg-process-test"
       },
       "external": true,
       "has_custom_viewer": false,
       "keywords": [
           "dna"
       ],
       "infrastructure": {
           "memory": 12,
           "cpus": 4,
           "executable": "/home/pmes/code/mg-process-test/process_test.py",
           "clouds": {}
       },
       "input_files": [
           {
               "name": "input",
               "description": "Input file",
               "help": "path to the input file",
               "file_type": ["TXT"],
               "data_type": [
                   "text"
               ],
               "required": true,
               "allow_multiple": false
           }
       ],
       "input_files_combinations": [
           [
               "input"
           ]
       ],
       "arguments": [],
       "output_files": [
           {
               "name": "output",
               "required": true,
               "allow_multiple": false,
               "file": {
                   "file_type": "TXT",
                   "meta_data": {
                       "visible": true,
                       "tool": "process_test",
                       "description": "Output"
                   },
                   "file_path": "test.txt",
                   "data_type": "text",
                   "compressed": ""
               }
           }
       ]
   }

Input Files
^^^^^^^^^^^
The input_files section defines the types of files that are able to be processed. This can be one or many files. Each file object within the list needs to have the follwoing key-pairs:

- name
- description
- help
- file_type
- data_type
- required
- allow_multiple

file_type and data_type can have multiple values in. For example in the case of a DNA sequence this can have the type of "sequence_genomic" or "sequence_dna", so a tool that is able to accept both can have both in the list.

The input_files_combinations is a list of lists of the valid permutations of files that can be accepted by the tool. For example with aligners that are able to hande single or paried-end alignments would need to be able to accept 1 or 2 FASTQ files. These lists use the name value from the input_files file objects.

Arguements
^^^^^^^^^^
If extra arguements are required by a tool to perform its functions these are defined in the arguements section of the JSON. The arguements section is a list of key-value objects consisting of the following keys:

- name
- description
- help
- type
- required
- default

Examples that can be used within the list include:

.. code-block:: none

   {
       "name": "test_example_bool_param",
       "description": "Example boolean parameter",
       "help": "Example of a boolean selector",
       "type": "boolean",
       "required": false,
       "default": false
   },
   {
       "name": "test_example_integer_param",
       "description": "Example integer parameter",
       "help": "Example of an integer input",
       "type": "integer",
       "required": false,
       "default": 5
   },
   {
       "name": "test_example_string_param",
       "description": "Example string parameter",
       "help": "Example of a string input",
       "type": "string",
       "required": false,
       "default": "default_string_value"
   },
   {
       "name": "test_example_selector_param",
       "description": "Example selector parameter",
       "help": "Example of a selector input",
       "type": {
           "type": "string",
           "enum": ["abc", "def", "xyz"]
       }
       "required": false,
       "default": "xyz"
   }

Examples
^^^^^^^^

For larger examples of VRE JSON configuration files have a look at the `mg-process-fastq configuration files <https://github.com/Multiscale-Genomics/mg-process-fastq/tree/master/tool_config>`_ on GitHub.


Test Configuration Files
------------------------

There are 2 configuration JSON files as inputs for the test instance. These describe the input and output files and an required arguments that need to get passed to the workflow. These configuration files are those that would get passed to the workflow by the VRE.

config.json
^^^^^^^^^^^

Defines the configurations required for by the pipeline including parameters that need to be passed from the VRE submission form, file and the related metadata as well as the output files that need to be produced by the pipeline.

.. code-block:: none
   :linenos:

   {
       "input_files": [
           {
               "required": true,
               "allow_multiple": false,
               "name": "input",
               "value": "<unique_file_id>"
           }
       ],
       "arguments": [
           {
               "name": "project",
               "value": "run001"
           },
           {
               "name": "execution",
               "value": "/../run001"
           },
           {
               "name": "description",
               "value": null
           },
           {
               "name": "<tool_argument>"
               "value": "<value_from_form>"
           }
       ],
       "output_files": [
           {
               "required": true,
               "allow_multiple": false,
               "name": "output",
               "file": {
                   "file_type": "TXT",
                   "meta_data": {
                       "visible": true,
                       "tool": "testTool",
                       "description": "Output"
                   },
                   "file_path": "tests/data/test.txt",
                   "data_type": "text",
                   "compressed": ""
               }
           }
       ]
   }

In the arguments there are 2 sets (project and execution) that will always be present and are provided by the VRE at the point of submission of the to the tool. These are the name of the project that has been given in the VRE and is defined by the user. The second is the execution path, this is the location for where the input files are located and can be used as the working directory for the tool. The other parameters in the arguments list are from form elements based on what parameters the tool requires from the user at run time.


input_file_metadata.json
^^^^^^^^^^^^^^^^^^^^^^^^

Lists the file location that are used as input. The configuration names should match those that are in the config.json file defined above.

.. code-block:: none
   :linenos:

   [
       {
           "_id": "<unique_file_id>",
           "data_type": "text",
           "file_type": "TXT",
           "file_path": "tests/data/test_input.txt",
           "compressed": 0,
           "sources": [],
           "taxon_id": "0",
           "meta_data": {
               "visible": true,
               "validated": 1
           }
       }
   ]

Examples
^^^^^^^^

For larger examples of JSON configuration files that can be used to test pipelines have a look at the `mg-process-fastq test configuration files <https://github.com/Multiscale-Genomics/mg-process-fastq/tree/master/tests/json>`_ on GitHub.
