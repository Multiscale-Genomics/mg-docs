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

Licensing
=========

All software developed as part of the VRE by the MuG consortium should be openly
licensed using the Apache 2.0 software license. This should encompass the APIs,
Tool wrappers and pipelines that have been developed.

Implementing the Apache 2.0 license
-----------------------------------

There are 3 parts to the license


LICENSE file
^^^^^^^^^^^^

This is the full Apache license. This should be an unmodified version of the
Apache LICENSE file which can be downloaded from:

.. code-block:: none

   wget http://www.apache.org/licenses/LICENSE-2.0.txt -O LICENSE

Often when starting a new project on GitHub this is automatically generated and
in included in the repository by default.


File headers
^^^^^^^^^^^^

At the top of all code and documentation there needs to be a header including
the license agreement:

.. code-block:: none

   See the NOTICE file distributed with this work for additional information
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

This should be surrounded by the appropriate commenting depending on the
language


NOTICE file
^^^^^^^^^^^

Lists those that own the Copyright  on the software in the repository and the
dates that they have been involved with the development of the software. There
is then a a second list of institutes that have contributed. Often these will be
the same.

.. code-block:: none

   Multiscale Genomics (MuG)
   Copyright 2015-2017 Institute A
   Copyright 2015-2016 Institute B
   Copyright 2016-2017 Institute C


   This product includes software developed at:
   - Institute A
   - Institute B
   - Institute C


Benefits for developers
-----------------------

For those that are developing software this format means that there is only a
single file that needs to be updated at the start of each year to reflect the
involvement of those in the development of the software.

If there are additional licenses that need to match specific sections of code
then these can be added at the end of the LICENSE file along with the files that
they relate to and who owns the Copyright.

There is a single header for all files referring the reader to the NOTICE file
for details about the developers and those that have contributed to the code.
