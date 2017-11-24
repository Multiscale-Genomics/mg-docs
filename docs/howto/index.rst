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

HOWTO
=====

.. toctree::
   :maxdepth: 2
   :caption: Contents:

   howto_tool
   howto_pipeline
   howto_config
   howto_logging
   howto_testing

The following is a walk through of developing a tool and pipeline wrapper to include new functionality within the MuG VRE. There are several stages covering the Tool development, using the tool within a pipeline and defining the configuration files required so that the final product can be smoothly integrated into the MuG VRE.

Common Coding Standards
-----------------------
When it comes to developing the code all the code should stick to a common standard. This has been defined within the `Coding Standards <http://multiscale-genomics.readthedocs.io/en/latest/coding_standards.html>`_ documentation as well as how to set up the licenses correctly so that your package can be integrated.

Adding a new function
---------------------
All of the examples in the following sections describe code that has been incorporated into a functional workflow and tool within a demonstration VRE Tool that is ready for deployment within the VRE. The code can be found in GitHub repository `mg-process-test <https://github.com/Multiscale-Genomics/mg-process-test>`_.

   `GitHub mg-process-test <https://github.com/Multiscale-Genomics/mg-process-test>`_

In the test process there are example workflows, tools, documentation, setup scripts unit tests and config files. This repository can be forked and used as the base for developing new workflows and tools.

`Wrapping a Tool <howto_tool.html>`_
   This section guides you through how to wrap an external tool, or create a tool that utilises the pyCOMPSs framework and should be capable of running within the MuG VRE environment.

`Adding a tool to a pipeline <howto_pipeline.html>`_
   Once you have created a tool you can now one or multiple tools into a pipeline. This will handle the passing of variables from the VRE to the tool and the tracking of outputs ready for handing back to the VRE. This document will also help in creating test input metadata and file location JSON files that are required to run the pipeline.

`Configuration <howto_config.html>`_
   This takes you through creating JSON configuration files for your tool. This should define all the inputs, outputs and any arguments that are required by the pipelines and tools.

`Logging <howto_logging.html>`_
   Takes you through adding logging to your workflows and tools to return messages to the user via the MuG VRE.

`Testing Your Code <howto_testing.html>`_
   A important part of making sure that a pipeline or tool is ready for integration is ensuring that the code has been tested. This covers testing the code is functional and that it is capable of running wihin the infrastructure used by the VRE.

Integrating a new tool into the VRE
-----------------------------------
The next step is the integration of the workflow/tool into the MuG VRE. To help with this a `GoogleDoc <https://docs.google.com/document/d/1Fid4RkNyt9-_0g_SrCw8J1k8MrOMZI4GLzzpdCAATZc/edit?usp=sharing>`_ has been prepared that details the requirements for correctly creating the Tool description JSON file and the requirements for parameters needed for an application.