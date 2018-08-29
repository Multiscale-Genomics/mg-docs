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

HOWTO - Tools
=============

This document provides a tutorial for the creation of a tool that can be used within a pipeline within the MuG VRE. All functions should be wrapped up as a tool, this then allows for the tools to be easily reused by other pipelines and also deployed onto the compute cluster.

The Tool is the core element when it comes to running a program of function within the COMPSs environment. It defines the procedures that need to happen to prepare the data along with the function that is parallelised to run over the chunks of data provided. A function can be either a piece of code that is written in python or an external package that is run with given chunks of data or defined parameters. The results are then returned to the calling function for merging.

All functions contain at least a `run(self)` function which is called by the pipeline. The run function takes the input files (list), defined output files (list) and relevant metadata (dict). Returned by the run function is a list containing a list of the output files as the first object and a list of metadata dict objects as the second element.


Repository Structure
--------------------

All tools should be placed within the `tools` directory within the package.


Basic Tool
----------

This is a test tool that takes an input file, then counts the number of characters in that file and then prints the result to a second file. The matching code can be found in the GitHub repository `mg-process-test <https://github.com/Multiscale-Genomics/mg-process-test>`_.  The file is called `testTool.py`.

.. code-block:: python
   :linenos:

   """
   .. License and copyright agreement statement
   """
   from __future__ import print_function

   from utils import logger

   try:
       from pycompss.api.parameter import FILE_IN, FILE_OUT
       from pycompss.api.task import task
       from pycompss.api.api import compss_wait_on
   except ImportError:
       logger.warn("[Warning] Cannot import \"pycompss\" API packages.")
       logger.warn("          Using mock decorators.")

       from utils.dummy_pycompss import FILE_IN, FILE_OUT  # pylint: disable=ungrouped-imports
       from utils.dummy_pycompss import task  # pylint: disable=ungrouped-imports
       from utils.dummy_pycompss import compss_wait_on  # pylint: disable=ungrouped-imports

   from basic_modules.tool import Tool
   from basic_modules.metadata import Metadata

   # ------------------------------------------------------------------------------

   class testTool(Tool):
       """
       Tool for writing to a file
       """

       def __init__(self, configuration=None):
           """
           Init function
           """
           print("Test writer")
           Tool.__init__(self)

           if configuration is None:
               configuration = {}

           self.configuration.update(configuration)

       @task(returns=bool, file_in_loc=FILE_IN, file_out_loc=FILE_OUT, isModifier=False)
       def test_writer(self, file_loc):
           """
           Count the number of characters in a file and return a file with the count

           Parameters
           ----------
           file_in_loc : str
               Location of the input file
           file_out_loc : str
               Location of an output file

           Returns
           -------
           bool
               Writes to the file, which is returned by pyCOMPSs to the defined location
           """
           try:
               with open(file_loc, "w") as file_handle:
                   file_handle.write("This is the test writer")
           except IOError as error:
               logger.fatal("I/O error({0}): {1}".format(error.errno, error.strerror))
               return False

           return True

       def run(self, input_files, input_metadata, output_files):
           """
           The main function to run the test_writer tool

           Parameters
           ----------
           input_files : dict
               List of input files - In this case there are no input files required
           input_metadata: dict
               Matching metadata for each of the files, plus any additional data
           output_files : dict
               List of the output files that are to be generated

           Returns
           -------
           output_files : dict
               List of files with a single entry.
           output_metadata : dict
               List of matching metadata for the returned files
           """

           results = self.test_writer(
               input_files["input_file_location"],
               output_files["output_file_location"]
           )

           results = compss_wait_on(results)

           if results is False:
               logger.fatal("Test Writer: run failed")
               return {}, {}

           output_metadata = {
               "test": Metadata(
                   data_type="<data_type>",
                   file_type="txt",
                   file_path=output_files["test"],
                   sources=[input_metadata["input_file_location"].file_path],
                   taxon_id=input_metadata["input_file_location"].taxon_id,
                   meta_data={
                       "tool": "testTool"
                   }
               )
           }

           return (output_files, output_metadata)

This is this simplest case of a Tool that will run a function within the COMPSS environment. The run function takes the input files, if the output files are defined it can use those as the output locations and any relevant metadata. The locations of the output files can also be defined within the run function as sometimes functions can generate a large number of files that are not always easy to define up front if the Tool is being run as part of the VRE or as part of a larger pipeline.

The run function then calls the `test_writer` function. This uses the python decorator syntax to highlight that it is a function that can be run in parallel to pyCOMPSs library. The `task` decorator is used to define the list of files and parameters that need to be passed to the function. It also requires a list of the files a that are to be returned. As such the most common types will be `FILE_IN`, `FILE_OUT`, `FILE_INOUT`.

The `__init__` function is important as it loads the configuration parameters into the class from the VRE. In this case there are no parameters used, but these can be parameters required for the tool that has been wrapped by the code.

Decorators can also be used to define the resources that are required by function. They can be used to define a set of machines that the task should be run on, required CPU capacity  or the amount of RAM that is required by the task. Defining these parameters helps the COMPSS infrastructure correctly allocate jobs so that they are able to run as soon as the resources allow and prevent the job failing by being run on a machine that does not have the correct resources.

Further details about COMPSS and pyCOMPSs can be found at the BSC website along with specific tutorials about how to write functions that can utilise the full power of COMPSS.


pyCOMPSs within the Tool
------------------------

When importing the pyCOMPSs modules it is important to provide access to the dummy_pycompss decorators as well. This will allow scripts to be run on computers where COMPSs has not been installed.


Practical Example
-----------------

Now that we know the basics it is possible to apply this to writing a tool that can run and perform a real operation within the cluster.

Here is a tool that uses BWA to index a genome sequence file that has been saved in FASTA format.

The run function takes the input FASTA file, from this it generates a list of the locations of the output files. The input file and output files are passed to the bwa_indexer function. The files do not need to be listed in the return call so True is fine. COMPSS handles the passing back of the files to the run function. The run function then returns the output files to the pipeline or the VRE.

.. code-block:: python
   :linenos:

   from __future__ import print_function

   import os
   import shlex
   import shutil
   import subprocess
   import sys
   import tarfile

   from utils import logger

   try:
       if hasattr(sys, '_run_from_cmdl') is True:
           raise ImportError
       from pycompss.api.parameter import FILE_IN, FILE_OUT
       from pycompss.api.task import task
       from pycompss.api.api import compss_wait_on
   except ImportError:
       logger.warn("[Warning] Cannot import \"pycompss\" API packages.")
       logger.warn("          Using mock decorators.")

    from utils.dummy_pycompss import FILE_IN, FILE_OUT # pylint: disable=ungrouped-imports
    from utils.dummy_pycompss import task # pylint: disable=ungrouped-imports
    from utils.dummy_pycompss import compss_wait_on # pylint: disable=ungrouped-imports

   from basic_modules.tool import Tool
   from basic_modules.metadata import Metadata

   # ------------------------------------------------------------------------------

   class bwaIndexerTool(Tool):
       """
       Tool for running indexers over a genome FASTA file
       """

       def __init__(self, configuration=None):
           """
           Init function
           """
           print("BWA Indexer")
           Tool.__init__(self)

           if configuration is None:
               configuration = {}

           self.configuration.update(configuration)

       def bwa_index_genome(self, genome_file):
           """
           Create an index of the genome FASTA file with BWA. These are saved
           alongside the assembly file. If the index has already been generated
           then the locations of the files are returned

           Parameters
           ----------
           genome_file : str
               Location of the assembly file in the file system

           Returns
           -------
           amb_file : str
               Location of the amb file
           ann_file : str
               Location of the ann file
           bwt_file : str
               Location of the bwt file
           pac_file : str
               Location of the pac file
           sa_file : str
               Location of the sa file

           """
           command_line = 'bwa index ' + genome_file

           amb_name = genome_file + '.amb'
           ann_name = genome_file + '.ann'
           bwt_name = genome_file + '.bwt'
           pac_name = genome_file + '.pac'
           sa_name = genome_file + '.sa'

           if os.path.isfile(bwt_name) is False:
               args = shlex.split(command_line)
               process = subprocess.Popen(args)
               process.wait()

           return (amb_name, ann_name, bwt_name, pac_name, sa_name)

       @task(file_loc=FILE_IN, idx_out=FILE_OUT)
       def bwa_indexer(self, file_loc, idx_out): # pylint: disable=unused-argument
           """
           BWA Indexer

           Parameters
           ----------
           file_loc : str
               Location of the genome assebly FASTA file
           idx_out : str
               Location of the output index file

           Returns
           -------
           bool
           """
           amb_loc, ann_loc, bwt_loc, pac_loc, sa_loc = self.bwa_index_genome(file_loc)

           # tar.gz the index
           print("BS - idx_out", idx_out, idx_out.replace('.tar.gz', ''))
           idx_out_pregz = idx_out.replace('.tar.gz', '.tar')

           index_dir = idx_out.replace('.tar.gz', '')
           os.mkdir(index_dir)

           idx_split = index_dir.split("/")

           shutil.move(amb_loc, index_dir)
           shutil.move(ann_loc, index_dir)
           shutil.move(bwt_loc, index_dir)
           shutil.move(pac_loc, index_dir)
           shutil.move(sa_loc, index_dir)

           index_folder = idx_split[-1]

           tar = tarfile.open(idx_out_pregz, "w")
           tar.add(index_dir, arcname=index_folder)
           tar.close()

           command_line = 'pigz ' + idx_out_pregz
           args = shlex.split(command_line)
           process = subprocess.Popen(args)
           process.wait()

           return True

       def run(self, input_files, metadata, output_files):
           """
           Function to run the BWA over a genome assembly FASTA file to generate
           the matching index for use with the aligner

           Parameters
           ----------
           input_files : dict
               List containing the location of the genome assembly FASTA file
           meta_data : dict
           output_files : dict
               List of outpout files generated

           Returns
           -------
           output_files : dict
               index : str
                   Location of the index file defined in the input parameters
           output_metadata : dict
               index : Metadata
                   Metadata relating to the index file
           """
           results = self.bwa_indexer(
               input_files["genome"],
               output_files["index"]
           )
           results = compss_wait_on(results)

           if results is False:
               logger.fatal("BWA Indexer: run failed")
               return {}, {}

           output_metadata = {
               "index": Metadata(
                   data_type="sequence_mapping_index_bwa",
                   file_type="TAR",
                   file_path=output_files["index"],
                   sources=[metadata["genome"].file_path],
                   taxon_id=metadata["genome"].taxon_id,
                   meta_data={
                       "assembly": metadata["genome"].meta_data["assembly"],
                       "tool": "bwa_indexer"
                   }
               )
           }

           return (output_files, output_metadata)

   # ------------------------------------------------------------------------------


Troubleshooting Common Issues
-----------------------------

Program is installed but fails to run
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
There are several points that need to be checked in this instance:

#. **Is the program available on your $PATH?** -
   If not either add it, or place a symlink in a directory that is.
#. **Check that the command that you are running matches the command run by subprocess** -
   Use the logger.info() to print the command and check that it works.
#. **Subprocess runs commands in a sandbox** -
   The normal way to run subprocess() is to use `subprocess.Popen(args)` and pass it a list of arguments that represent the command to be run (as shown in the practical example above). Sometimes this fails as extra environment parameters may be required by the program, in this case it is possible to run the whole command as a single string and tell the subprocess to use a shell:

   .. code-block:: python
      :linenos:

      command_line = "python --version"
      process = subprocess.Popen(command_line, shell=True)
      process.wait()
