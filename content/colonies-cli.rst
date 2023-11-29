Getting started
===============

Installation
------------
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

Getting help
------------

To use the Colonies CLI tool, just type **colonies**. 

.. code-block:: console

    colonies


.. code-block:: console
    
    Colonies CLI tool
    
    Usage:
      colonies [command]
    
    Available Commands:
      attribute   Manage process attributes
      cluster     Manage clusters
      colony      Manage colonies
      completion  Generate the autocompletion script for the specified shell
      config      Show currently used configuration
      cron        Manage cron
      database    Manage internal database
      dev         Start a development server
      executors   Manage executors
      fs          Manage file storage
      function    Manage functions
      generator   Manage generators
      help        Help about any command
      key         Manage private keys
      log         Manage logging
      monitor     Manage Prometheus monitoring
      process     Manage processes
      server      Manage production server
      users       Manage users
      workflow    Manage workflows
    
    Flags:
      -h, --help              help for colonies
          --insecure          Disable TLS and use HTTP
          --skip-tls-verify   Skip TLS certificate verification
      -v, --verbose           Verbose (debugging)
    
    Use "colonies [command] --help" for more information about a command.

To get help about a certain subcommand, e.g. type: **colonies process --help**

.. code-block:: console

    Manage processes
    
    Usage:
      colonies process [command]
    
    Available Commands:
      assign      Assign a process to a executor
      close       Close a process as successful
      delete      Delete a process
      deleteall   Delete all processes
      fail        Close a process as failed
      get         Get info about a process
      ps          List all running processes
      psf         List all failed processes
      pss         List all successful processes
      psw         List all waiting processes
    
    Flags:
      -h, --help          help for process
          --host string   Server host (default "localhost")
          --port int      Server HTTP port (default -1)
    
    Global Flags:
          --insecure          Disable TLS and use HTTP
          --skip-tls-verify   Skip TLS certificate verification
      -v, --verbose           Verbose (debugging)
    
    Use "colonies process [command] --help" for more information about a command.
    

Show current configuration
--------------------------

To get info about current configuration type:

.. code-block:: console

    colonies config

.. code-block:: console

    Current configurations:
    +--------+-----------------+
    | Colony | dev             |
    | Server | localhost:50080 |
    | TLS    | false           |
    +--------+-----------------+

Manage a Colony
===============

Create a new Colony
-------------------
First generate a new ECDSA private key. To use the new Colony, you need to export the key as ``COLONIES_COLONY_PRVKEY``.

.. code-block:: console
    
    colonies key generate
   
.. code-block:: console

    INFO[0000] Generated new private key  

    Id=f5ce6d9c328b0750ea37cad504e5f64e2380836231e9389eb848f77250eb038f 
    PrvKey=4a8739fab821a394d5c4f215e525c8d908d010b9618a700e51739286869dc8e1

.. code-block:: console

    colonies colony add \ 
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

    colonies colony ls

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
    
    colonies colony remove --name test_colony  

Get statistics
--------------
All valid users and executors can get statistics on a Colony.

.. code-block:: console

    colonies colony stats --name dev

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
    
    colonies key generate


.. code-block:: console
    
    INFO[0000] Generated new private key
    
    Id=b06e5e9445b2db98ec66a813a0fba923422163923c9b41096867961ec39a5ab5
    PrvKey=4e7e012894601adb804061724757860f316e342146f3794f90ce14e527d7bac7

.. code-block:: console
    
    colonies users add \
    --name="johan" \
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

   colonies users ls

.. code-block:: console

    +----------+--------------------------+---------------+
    | USERNAME |          EMAIL           |     PHONE     |
    +----------+--------------------------+---------------+
    | johan    | johan.kristiansson@ri.se | +467011122233 |
    +----------+--------------------------+---------------+

Get info about a User 
---------------------

.. code-block:: console

    colonies users get --name johan

.. code-block:: console

    +------------+------------------------------------------------------------------+
    | Name       | johan                                                            |
    | ID         | b06e5e9445b2db98ec66a813a0fba923422163923c9b41096867961ec39a5ab5 |
    | ColonyName | dev                                                              |
    | Email      | johan.kristiansson@ri.se                                         |
    | Phone      | +467011122233                                                    |
    +------------+------------------------------------------------------------------+

Remove a User 
-------------

.. code-block:: console

    colonies users remove --name johan

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
        "executorname": "ml-executor",
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
        "executorname": "ml-executor",
        "executortype": "ml"
    }

.. code-block:: console 

    colonies executors add --spec examples/executors/executor.json  \
    --executorid 24bbbc074019734fc4676ec1641ca6f22c3ac943c48067ded3649602653a96c1 \ 
    --approve

It is also possible to override **executorname** and **executortype** fields.

.. code-block:: console

    colonies executors add --spec examples/executors/executor.json  \
    --executorid 24bbbc074019734fc4676ec1641ca6f22c3ac943c48067ded3649602653a96c1 \ 
    --name my_name \ 
    --type my_type \
    --approve

Or simply skip the **--spec** argument, but then **executorname** and **executortype** must be specified. 

.. code-block:: console

    colonies executors add --executorid 24bbbc074019734fc4676ec1641ca6f22c3ac943c48067ded3649602653a96c1 \ 
    --name my_name \ 
    --type my_type \
    --approve

If **--approve** is not specified, the Executor will be registered, but is not allowed to get process assignments.

Approve Executors
-----------------
Not approved Executors do not take part of process brokering and will not get any processassignments. 
The following command will approve an Executor:

.. code-block:: console
    
    colonies executors approve --name my_executor

.. code-block:: console

    INFO[0000] Executor approved

    ColonyName=dev ExecutorName=my_executor

Reject Executors
-----------------
The following command will reject an Executor and prevent it from taking part of process brokering:

.. code-block:: console
    
    colonies executors reject --name my_executor

.. code-block:: console

    INFO[0000] Executor rejected

    ColonyName=dev ExecutorName=my_executor


List Executors
--------------
 
.. code-block:: console
      
    colonies executors ls

.. code-block:: console

    +-------------+------+----------------+
    |    NAME     | TYPE |    LOCATION    |
    +-------------+------+----------------+
    | myexecutor  | cli  |                |
    | ml-executor | ml   | ICE Datacenter |
    +-------------+------+----------------+

Get info about an Executor
--------------------------

.. code-block:: console

    colonies executors get --name ml-executor

.. code-block:: console

     Executor:
     +-------------------------+------------------------------------------------------------------+
     | Name                    | ml-executor                                                      |
     | ID                      | 24bbbc074019734fc4676ec1641ca6f22c3ac943c48067ded3649602653a96c1 |
     | Type                    | ml                                                               |
     | ColonyName              | dev                                                              |
     | State                   | Approved                                                         |
     | RequireFuncRegistration | False                                                            |
     | CommissionTime          | 2023-11-29 15:05:25                                              |
     | LastHeardFrom           | 0001-01-01 00:53:28                                              |
     +-------------------------+------------------------------------------------------------------+
     
     Location:
     +-------------+----------------+
     | Longitude   | 65.612046      |
     | Latitude    | 22.132276      |
     | Description | ICE Datacenter |
     +-------------+----------------+
     
     Hardware:
     +-----------+--------------------------------+
     | Model     | AMD Ryzen 9 5950X 16-Core      |
     |           | Processor                      |
     | CPU       | 4000m                          |
     | Nodes     | 0                              |
     | Memory    | 16Gi                           |
     | Storage   | 100Ti                          |
     | GPU       | nvidia_3080ti                  |
     | GPUMem    |                                |
     | GPUs      | 1                              |
     | GPUs/Node | 0                              |
     +-----------+--------------------------------+
     
     Software:
     +---------+--------------------+
     | Name    | colonyos/ml:latest |
     | Type    | k8s                |
     | Version | latest             |
     +---------+--------------------+
     
     Functions:
     No functions found

Calling Functions on a Colony
=============================

Submitting Function Specs
-------------------------

ColonyOS operates on the principle of submitting **Functions Specifications** to a Colony, which are then executed by various Executors, members of that Colony. When a **Function Specification** is received by the Colonies server, it is wrapped into a **Process**, which is subsequently assigned to an Executor. Each Executor is responsible for implementing one or more of these functions and connects to the Colonies server to receive assignments. 

Let's submit a **Function Specification** for executing a function named **helloworld**, specifying **helloworld-executor** as the target Executor type. Note that a **Function Specification** can be submitted even if there are no matching Executors currently in the Colony. These functions will be executed in the future when an Executor of matching **executortype** becomes available.

.. code-block:: json

    {
        "conditions": {
            "executortype": "helloworld-executor"
        },
        "funcname": "helloworld"
    }

.. code-block:: console

    colonies function submit --spec ./examples/functions/helloworld.json

.. code-block:: console
    INFO[0000] Process submitted

    ProcessId=99962477b295c4058b0a54929b79dbc0d7f57699ca0a0385df1a41dfa473a04d

The command will block until the process is executed by an Executor. First, we need to generate a new ECDSA private key that will be used by the Executor. 

.. code-block:: console
    
    colonies key generate

.. code-block:: console

    INFO[0000] Generated new private key

    Id=ee58b16a187bb4467437cc068741118bf6ca0ba42e6589c7ea016550ac63e517
    PrvKey=8c32cdcea68600e05df8661eb0cb6679b9ba1d62c901b2a0a55c2eecd9bbbf58
    
.. code-block:: console

    colonies executors add --executorid ee58b16a187bb4467437cc068741118bf6ca0ba42e6589c7ea016550ac63e517 \ 
    --name helloworld-executor \ 
    --type helloworld-executor \
    --approve

.. code-block:: console

    INFO[0000] Executor added

    ColonyName=dev 
    ExecutorID=ee58b16a187bb4467437cc068741118bf6ca0ba42e6589c7ea016550ac63e517 
    ExecutorName=helloworld-executor ExecutorType=helloworld-executor

To assign a process to an Executor, the command **colonies process assign** can be used and also specify the **helloworld-executor** Executor's private key. Typically, the assign operation is carried out by specialized Executors developed using the ColonyOS SDKs, rather than through the Colonies CLI. The example below is primarily for educational purposes.

.. code-block:: console

    colonies process assign --prvkey 8c32cdcea68600e05df8661eb0cb6679b9ba1d62c901b2a0a55c2eecd9bbbf58 

.. code-block:: console

    INFO[0000] Assigned process to executor

    ExecutorId=ee58b16a187bb4467437cc068741118bf6ca0ba42e6589c7ea016550ac63e517
    ProcessId=0ddcc0b74ab1ec0cace153432fbf0bb3c7cdd3deffc0d0a69ad1f210f570962c

The Helloworld Executor is now assigned to the process, which means that it have exclusive access to it. No other Executors can hence be 
assigned this particular process. Only the assigned Executor can manipulate the process, such as closint it. 
Now close the process with a result string (Hej).  

.. code-block:: console

    colonies process close \
    -p 0ddcc0b74ab1ec0cace153432fbf0bb3c7cdd3deffc0d0a69ad1f210f570962c \ 
    --prvkey 8c32cdcea68600e05df8661eb0cb6679b9ba1d62c901b2a0a55c2eecd9bbbf58 \
    --out Hej

.. code-block:: console
    INFO[0000] Process closed as Successful

    ProcessId=055a9e1c93ea6e252cab7f2b45eaec78f4f87e540352d10636d88e944b6bfb85

Alternatively, we can close the process as *failed*.
    
.. code-block:: console
    
    colonies process close \
    -p 0ddcc0b74ab1ec0cace153432fbf0bb3c7cdd3deffc0d0a69ad1f210f570962c \ 
    --prvkey 8c32cdcea68600e05df8661eb0cb6679b9ba1d62c901b2a0a55c2eecd9bbbf58 \
    --out Hej

.. code-block:: console

    INFO[0000] Process closed as Failed

    ProcessId=46a0b33933a68ebfc0da722461b8c13bb1a170ee678ecd1ccede9ae1b01fcc91

Alternative method to execute a Function
----------------------------------------
It is possible to submit a Function Specification without specifying a JSON file, which can be suitable for simpler use cases.

.. code-block:: console

    colonies function exec --func fibonacci --args 10 --targettype cli 

Manage Processes
================
