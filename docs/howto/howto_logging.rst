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

HOWTO - Logging
===============

As the workflows and tools with the MuG VRE environment run wthout the terminal returning to the user it is important to have a way to communicate to the user that is an error with the workflow. As the code is run within a cluster the text that is printed to screen wont be returned to the user. Within the Tool API a logging interface has been implemented

Levels of Logging
-----------------

When there is an issue it can be pass back to the VRE. These are tracked and passed back to the VRE as the application finishes. There is the option to raise errors in 1 of 6 states:

- INFO
   Confirmation that Tool execution is working as expected.
- DEBUG
   Detailed information, typically of interest only when diagnosing problems.
- WARNING
   An indication that something unexpected happened, but that the Tool can continue working successfully.
- ERROR
   A more serious problem has occurred, and the Tool will not be able to perform some function.
- FATAL
   A serious error, indicating that the Tool may be unable to continue running.
- PROGRESS
   Provide the VRE with information about Tool execution progress, in the form of a percentage (0-100)

Using Logging
-------------

The code is present within the Tools API, so adding it into a tool or pipeline requires minimal effort. Improting the loggin functions requires the following code:

.. code-block:: python
   :linenos:

   from utils import logger

To add elements to the log can be implemented by:

.. code-block:: python
   :linenos:

   logger.info("Processing Text")

This logging has been implemented within the `mg-process-test <https://github.com/Multiscale-Genomics/mg-process-test>`_ repository within the process_test.py and within the testTool.py scripts. There is no logging within the @task as from this it is possible to return an actual object that can then be checked by the run() function to determine the correct error to return to the main workflow.