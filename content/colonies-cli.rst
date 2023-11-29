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
    export COLONIES_COLONY_NAME="dev"
    export COLONIES_PRVKEY="a499fddafe51fabb405dae13c33daa9d63fe262ee92e6fff52c82b4a72e36f6d"

If you are a Colony administrator, you may also want to set: 

.. code-block:: console

    export COLONIES_COLONY_PRVKEY="29ddccc9e2825378b9055e2667c2fcf0ceffc4e8744ad0e97dd37f8a20750820"

To be able to create a new Colony, you need to be a Colonies server administrator and set the following variables:

.. code-block:: console

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


Manage Colonies
===============

Create a new Colony
-------------------
First generate a new ECDSA private key. To use the new Colony, you need to export the key as ``COLONIES_COLONY_PRVKEY``.

.. code-block:: console
    
    $ colonies key generate
   
.. code-block:: console

    INFO[0000] Generated new private key  

    Id=f5ce6d9c328b0750ea37cad504e5f64e2380836231e9389eb848f77250eb038f 
    PrvKey=4a8739fab821a394d5c4f215e525c8d908d010b9618a700e51739286869dc8e1

.. code-block:: console

    $ colonies colony add \ 
    --name test_colony \ 
    --colonyid f5ce6d9c328b0750ea37cad504e5f64e2380836231e9389eb848f77250eb038f


.. code-block:: console

    INFO[0000] Colony added   

    ColonyID=f5ce6d9c328b0750ea37cad504e5f64e2380836231e9389eb848f77250eb038f
    ColonyName=test_colony


List all registered Colonies
----------------------------
To list all registered colonies, you must be server administrator and the ``COLONIES_SERVER_PRVKEY`` must be set.

.. code-block:: console

   $ colonies colony ls

.. code-block:: console

    +-------------+
    |    NAME     |
    +-------------+
    | dev         |
    | test_colony |
    +-------------+

Remove a Colony
---------------
Only a Colony owner can remove a Colony and you need to have a valid ``COLONIES_COLONY_PRVKEY`` private key.

.. code-block:: console
    
    $ colonies colony remove --name test_colony  

Get Colony statistics
---------------------
All valid users and executors can get statistics on a Colony.

.. code-block:: console

    $ colonies colony stats --name dev

.. code-block:: console

    Process statistics:
    +----------------------+-----+
    | Executors            | 3   |
    | Waiting processes    | 10  |
    | Running processes    | 3   |
    | Successful processes | 131 |
    | Failed processes     | 10  |
    | Waiting workflows    | 0   |
    | Running workflows    | 0   |
    | Successful workflows | 5   |
    | Failed workflows     | 1   |
    +----------------------+-----+

Manage Users
============

Add a new User
--------------
First, generate a new ECDSA private key. The user Id is cryptographically linked to this newly generated private key. In fact, the Colonies server will reconstruct the user Id from the signatures of messages sent to it.

It's also important to note that only the Colony owner can add a user. Therefore, ensure that the ``COLONIES_COLONY_PRVKEY`` environment variable is correctly set.

Next, you need to set the ``COLONIES_PRVKEY`` environment variable to interact with a Colonies server with this new user account. 


.. code-block:: console
    
    $ colonies key generate


.. code-block:: console
    
    INFO[0000] Generated new private key
    
    Id=b06e5e9445b2db98ec66a813a0fba923422163923c9b41096867961ec39a5ab5
    PrvKey=4e7e012894601adb804061724757860f316e342146f3794f90ce14e527d7bac7

.. code-block:: console
    
    $ colonies users add \
    --username="johan" \
    --email="johan.kristiansson@ri.se" \
    --phone="+467011122233" \
    --userid="b06e5e9445b2db98ec66a813a0fba923422163923c9b41096867961ec39a5ab5"

.. code-block:: console
       
    INFO[0000] User added   

    ColonyName=dev Email=johan.kristiansson@ri.se 
    Phone=+467011122233 
    UserId=b06e5e9445b2db98ec66a813a0fba923422163923c9b41096867961ec39a5ab5 
    Username=johan2

Note that both username and userid must be unique.

List Users 
----------
To list all users member of a Colony.

.. code-block:: console

   $ colonies users ls

.. code-block:: console

    +----------+--------------------------+---------------+
    | USERNAME |          EMAIL           |     PHONE     |
    +----------+--------------------------+---------------+
    | johan    | johan.kristiansson@ri.se | +467011122233 |
    +----------+--------------------------+---------------+

Remove a User 
-------------

.. code-block:: console

   $ colonies users remove --username johan

.. code-block:: console

    INFO[0000] User removed   

    ColonyName=dev 
    Username=johan

Manage Executors
================

Register an new Executor 
------------------------
Copy the JSON object below to a file, e.g. executor.json. Only **executorname** and **executortype** are mandatory fields. 
And only a Colony owner can register a new Executor.

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

Below is a minimal Executor spec.

.. code-block:: json

    { 
        "executorname": "ml_executor",
        "executortype": "ml"
    }

.. code-block:: console 

    $ executors add --spec examples/executors/executor.json  \
    --executorid 24bbbc074019734fc4676ec1641ca6f22c3ac943c48067ded3649602653a96c1 \ 
    --approve

It is also possible to override **executorname** and **executortype** fields.

.. code-block:: console

    $ executors add --spec examples/executors/executor.json  \
    --executorid 24bbbc074019734fc4676ec1641ca6f22c3ac943c48067ded3649602653a96c1 \ 
    --name my_name \ 
    --type my_type \
    --approve

Or simply skip the **--spec** argument, but then **executorname** and **executortype** must be specified. 

.. code-block:: console

    $ executors add --executorid 24bbbc074019734fc4676ec1641ca6f22c3ac943c48067ded3649602653a96c1 \ 
    --name my_name \ 
    --type my_type \
    --approve

If **--approve** is not specified, the Executor will be registered, but is not allowed to get process assignments.

Approve Executors
-----------------
Not approved Executors do not take part of process brokering and will not get any processassignments. 
The following command will approve an Executor:

.. code-block:: console
    
    executors approve --name my_executor

.. code-block:: console

    INFO[0000] Executor approved

    ColonyName=dev ExecutorName=my_executor

Reject Executors
-----------------
The following command will reject an Executor and prevent it from taking part of process brokering:

.. code-block:: console
    
    executors reject --name my_executor

.. code-block:: console

    INFO[0000] Executor rejected

    ColonyName=dev ExecutorName=my_executor


List Executors
--------------
 
.. code-block:: console
       
       colonies executors ls 

.. code-block:: console

       +-------------------------------------------+------------------------------+----------------+
       |                     ID                    |             TYPE             |    LOCATION    |
       +-------------------------------------------+------------------------------+----------------+
       | cc170701bab47ecb730c91a4209e45687359a7... | lumi-standard-hpcexecutor    | CSC, Finland   |
       | 8ba7a06ae12f351a63baae8792b8f98e26b78e... | lumi-small-hpcexecutor       | CSC, Finland   |
       | 972aaadefabcbf12aeffb5aacf76822a6b645a... | leonardo-booster-hpcexecutor | Cineca, Italy  |
       | 930af29477792d1dd2863d74645a69156569c8... | ice-kubeexecutor             | ICE Datacenter |
       +-------------------------------------------+------------------------------+----------------+


Manage Processes
================
