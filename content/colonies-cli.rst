Getting started
===============

Download the Colonies CLI binary `here <https://github.com/colonyos/colonies/releases>`_.

On Windows, it is recommended to use Unix alike shell, e.g. `Git BASH <https://gitforwindows.org>`_, 
`Linux for Windows with WSL <https://learn.microsoft.com/en-us/windows/wsl/install>`_, or
`Cygwin <https://www.cygwin.com>`_.


The following environmental variables must be set at a minimum.
Replace the sample values below with the actual ones you will use. 
Keep in mind that the private key is sensitive and should be kept confidential.

.. code-block:: console

    export COLONIES_TLS="true"
    export COLONIES_SERVER_TLS="true"
    export COLONIES_SERVER_HOST="server.colonyos.io"
    export COLONIES_SERVER_PORT="443"
    export COLONIES_COLONY_ID="bc05fea3354ad8b1740b8dcf1c39f076f0c1e18d5bdb5da3390ccf0e0bc57846"
    export COLONIES_USER_ID="4dcccd0ecf01106b3b5da6081933843dc37d1f776dd1eb79d2488df688e61925"
    export COLONIES_USER_PRVKEY="a499fddafe51fabb405dae13c33daa9d63fe262ee92e6fff52c82b4a72e36f6d"

To setup an Executor, you need to set an Executor Id and the corresponding private key.
      
.. code-block:: console

    export COLONIES_TLS="true"
    export COLONIES_SERVER_TLS="true"
    export COLONIES_SERVER_HOST="server.colonyos.io"
    export COLONIES_SERVER_PORT="443"
    export COLONIES_COLONY_ID="bc05fea3354ad8b1740b8dcf1c39f076f0c1e18d5bdb5da3390ccf0e0bc57846"
    export COLONIES_EXECUTOR_ID="4dcccd0ecf01106b3b5da6081933843dc37d1f776dd1eb79d2488df688e61925"
    export COLONIES_EXECUTOR_PRVKEY="a499fddafe51fabb405dae13c33daa9d63fe262ee92e6fff52c82b4a72e36f6d"

If you are a Colony administrator, you may also want to set: 

.. code-block:: console

    export COLONIES_COLONY_ID="90b3e03a61ef0e844ac04193db080fe6165e0c5f0ed3ff5308a455d820c18257"
    export COLONIES_COLONY_PRVKEY="29ddccc9e2825378b9055e2667c2fcf0ceffc4e8744ad0e97dd37f8a20750820"

To be able to create a new Colony, you need to be a Colonies server administrator and set the following variables:

.. code-block:: console

    export COLONIES_SERVER_ID="45c5adbdea61f11ffc0315090b1d3e748f8983e32e9482dbca7e126ff06f5248"
    export COLONIES_SERVER_PRVKEY="74b1e2168600ef0186b43515747ac0020698b1dab2d6eb868e329122727353c4"

Addititionally, to be able to use Colonies Filesystem, you need to provide AWS S3 configuration and credentials:

.. code-block:: console

    export AWS_S3_ENDPOINT=""
    export AWS_S3_ACCESSKEY=""
    export AWS_S3_SECRETKEY=""
    export AWS_S3_REGION_KEY=""
    export AWS_S3_BUCKET=""
    export AWS_S3_TLS=""
    export AWS_S3_SKIPVERIFY=""


Manage a Colony
===============

Create a new Colony
-------------------

 .. code-block:: console

      $ export OMP_NUM_THREADS=1
      $ python omp_test.py

      $ export OMP_NUM_THREADS=4
      $ python omp_test.py

ggg
 .. code-block:: json

      {
          "executorname": "ml_executor",
          "executortype": "ml",
          "location": {
              "long": 65.61204640586546,
              "lat": 22.132275667285477,
              "desc": "ICE Datacenter"
          },
          "capabilities": {
              "hardware": {
                  "model": "AMD Ryzen 9 5950X 16-Core Processor",
                  "cpu": "4000m",
                  "mem": "16Gi",
                  "storage": "100Ti",
                  "gpu": {
                      "name": "nvidia_3080ti",
                      "count": 1
                  }
              },
              "software": {
                  "name": "colonyos/ml:latest",
                  "type": "k8s",
                  "version": "latest"
              }
          }
      }

Add a new User
--------------
First, generate a new ECDSA private key. The user Id is cryptographically linked to this newly generated private key. In fact, the Colonies server will reconstruct the user Id from the signatures of messages sent to it.

It's also important to note that only the Colony owner can add a user. Therefore, ensure that the ``COLONIES_COLONY_PRVKEY`` environment variable is correctly set.

Next, you need to set the ``COLONIES_PRVKEY`` environment variable to interact with a Colonies server with this new user account. 


.. code-block:: console
    
    $ colonies keychain generate


.. code-block:: console
    
    INFO[0000] Generated new private key and stored in keychain  Id=b06e5e9445b2db98ec66a813a0fba923422163923c9b41096867961ec39a5ab5 PrvKey=4e7e012894601adb804061724757860f316e342146f3794f90ce14e527d7bac7


.. code-block:: console
    
    $ colonies user add \
    --username="johan" \
    --email="johan.kristiansson@ri.se" \
    --phone="+467011122233" \
    --userid="b06e5e9445b2db98ec66a813a0fba923422163923c9b41096867961ec39a5ab5"

.. code-block:: console
       
    INFO[0000] User added   ColonyName=dev Email=johan.kristiansson@ri.se Phone=+467011122233 UserId=b06e5e9445b2db98ec66a813a0fba923422163923c9b41096867961ec39a5ab5 Username=johan2


List Users 
----------
To list all users member of a Colony.

.. code-block:: console

   $ colonies user ls

.. code-block:: console

    +------------------------------------------------------------------+----------+--------------------------+---------------+
    |                              USERID                              | USERNAME |          EMAIL           |     PHONE     |
    +------------------------------------------------------------------+----------+--------------------------+---------------+
    | b06e5e9445b2db98ec66a813a0fba923422163923c9b41096867961ec39a5ab5 | johan    | johan.kristiansson@ri.se | +467011122233 |
    +------------------------------------------------------------------+----------+--------------------------+---------------+


Remove a User 
-------------

.. code-block:: console

   $ colonies user remove --username johan

.. code-block:: console

    INFO[0000] User removed   ColonyName=dev Username=johan



List Executors
--------------
 
.. code-block:: console
       
       colonies executor ls 

.. code-block:: console

       +-------------------------------------------+------------------------------+----------------+
       |                     ID                    |             TYPE             |    LOCATION    |
       +-------------------------------------------+------------------------------+----------------+
       | cc170701bab47ecb730c91a4209e45687359a7... | lumi-standard-hpcexecutor    | CSC, Finland   |
       | 8ba7a06ae12f351a63baae8792b8f98e26b78e... | lumi-small-hpcexecutor       | CSC, Finland   |
       | 972aaadefabcbf12aeffb5aacf76822a6b645a... | leonardo-booster-hpcexecutor | Cineca, Italy  |
       | 930af29477792d1dd2863d74645a69156569c8... | ice-kubeexecutor             | ICE Datacenter |
       +-------------------------------------------+------------------------------+----------------+


Manage processes
================
