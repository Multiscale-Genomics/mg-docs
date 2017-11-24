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

This configuration file is used to describe the Tool and inform the VRE about what aruments are requiredby the tool and a list of the file types that can be used as inputs and the matching names that should be used as input paramaeters.

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
       "input_files": [],
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
               "name": "genome",
               "value": "<unique_file_id>"
           }
       ],
       "arguments": [
           {
               "name": "project",
               "value": "run001"
           },
           {
               "name": "description",
               "value": null
           }
       ],
       "output_files": [
           {
               "required": true,
               "allow_multiple": false,
               "name": "bwa_index",
               "file": {
                   "file_type": "TAR",
                   "meta_data": {
                       "visible": true,
                       "tool": "bwq_indexer",
                       "description": "Output"
                   },
                   "file_path": "tests/data/macs2.Human.GCA_000001405.22.fasta.bwa.tar.gz",
                   "data_type": "sequence_mapping_index_bwa",
                   "compressed": "gzip"
               }
           }
       ]
   }


input_file_metadata.json
^^^^^^^^^^^^^^^^^^^^^^^^

Lists the file location that are used as input. The configuration names should match those that are in the config.json file defined above.

.. code-block:: none
   :linenos:

   [
       {
           "_id": "<unique_file_id>",
           "data_type": "sequence_dna",
           "file_type": "FASTA",
           "file_path": "tests/data/macs2.Human.GCA_000001405.22.fasta",
           "compressed": 0,
           "sources": [],
           "creation_time": {
               "sec": 1503567524,
               "usec": 0
           },
           "taxon_id": "0",
           "meta_data": {
               "visible": true,
               "validated": 1,
               "assembly": "GCA_000001405.22"
           }
       }
   ]