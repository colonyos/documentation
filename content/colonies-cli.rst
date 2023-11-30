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
      executor    Manage executors
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
      remove      Remove a process
      removeall   Remove all processes
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

Colony
======

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

User
====

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
    
    colonies user add \
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

   colonies user ls

.. code-block:: console

    +----------+--------------------------+---------------+
    | USERNAME |          EMAIL           |     PHONE     |
    +----------+--------------------------+---------------+
    | johan    | johan.kristiansson@ri.se | +467011122233 |
    +----------+--------------------------+---------------+

Get info about a User 
---------------------

.. code-block:: console

    colonies user get --name johan

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

    colonies user remove --name johan

.. code-block:: console

    INFO[0000] User removed   

    ColonyName=dev 
    Username=johan

Executors
=========

Register a new Executor 
-----------------------
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

    colonies executor add --spec examples/executors/executor.json  \
    --executorid 24bbbc074019734fc4676ec1641ca6f22c3ac943c48067ded3649602653a96c1 \ 
    --approve

It is also possible to override **executorname** and **executortype** fields.

.. code-block:: console

    colonies executor add --spec examples/executors/executor.json  \
    --executorid 24bbbc074019734fc4676ec1641ca6f22c3ac943c48067ded3649602653a96c1 \ 
    --name my_name \ 
    --type my_type \
    --approve

Or simply skip the **--spec** argument, but then **executorname** and **executortype** must be specified. 

.. code-block:: console

    colonies executor add --executorid 24bbbc074019734fc4676ec1641ca6f22c3ac943c48067ded3649602653a96c1 \ 
    --name my_name \ 
    --type my_type \
    --approve

If **--approve** is not specified, the Executor will be registered, but is not allowed to get process assignments.

Approve an Executor
-------------------
Not approved Executors do not take part of process brokering and will not get any processassignments. 
The following command will approve an Executor:

.. code-block:: console
    
    colonies executor approve --name my_executor

.. code-block:: console

    INFO[0000] Executor approved

    ColonyName=dev ExecutorName=my_executor

Reject an Executor
------------------
The following command will reject an Executor and prevent it from taking part of process brokering:

.. code-block:: console
    
    colonies executor reject --name my_executor

.. code-block:: console

    INFO[0000] Executor rejected

    ColonyName=dev ExecutorName=my_executor

Remove an Executor
------------------

.. code-block:: console
    
    colonies executor remove --name  ml-executor

.. code-block:: console

    INFO[0000] Executor removed

    ColonyName=dev ExecutorName=ml-executor


List Executors
--------------
 
.. code-block:: console
      
    colonies executor ls

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

    colonies executor get --name ml-executor

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

Functions
=========

Submitting Function Specs
-------------------------

ColonyOS operates on the principle of submitting **Functions Specifications** to a Colony, which are then executed by various Executors, members of that Colony. When a **Function Specification** is received by the Colonies server, it is wrapped into a **process**, which is subsequently assigned to an Executor. Each Executor is responsible for implementing one or more of these functions and connects to the Colonies server to receive assignments. 

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

    colonies executor add --executorid ee58b16a187bb4467437cc068741118bf6ca0ba42e6589c7ea016550ac63e517 \ 
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

    colonies function exec --func helloworld --targettype helloworld-executor

It also possible to provide *args*. Note that *kwargs* is currently not support by the **exec** function.

.. code-block:: console

    colonies function exec --func fibonacci --args 10 --targettype cli 

Registering a Function
----------------------
The primary role of Executors is to execute tasks. Executors can register a **Function**, thereby indicating to other Executors or users their ability to execute a specific function. Importantly, only the Executor itself can register Functions to itself; this task cannot be performed by Users or other Executors. To register a Function, access to the Executor's ECDSA private key is required.

.. code-block:: console

    colonies function register --name helloworld-executor \ 
    --func helloworld \
    --prvkey 8c32cdcea68600e05df8661eb0cb6679b9ba1d62c901b2a0a55c2eecd9bbbf58 

List all registered Functions
-----------------------------
To list all registered Function in a Colony and get some basic statistics, type: 

.. code-block:: console

    colonies function ls

.. code-block:: console

    Function:
    +-------------+-------------+
    | FuncName    | helloworld  |
    | Calls       | 1           |
    | Served by   | 1 executors |
    | MinWaitTime | 10.410566 s |
    | MaxWaitTime | 10.410566 s |
    | AvgWaitTime | 10.410566 s |
    | MinExecTime | 27.459162 s |
    | MaxExecTime | 27.459162 s |
    | AvgExecTime | 27.459162 s |
    +-------------+-------------+

If two or more Executors provide the same Function, they will compete for process assignments, effectively load-balancing requests between Executors.

Process
=======

List Waiting Processes
----------------------

.. code-block:: console

    colonies process psw
    
.. code-block:: console

     +------------------------------------------------------------------+------------+------+--------+---------------------+---------------------+
     |                                ID                                |    FUNC    | ARGS | KWARGS |   SUBMISSION TIME   |    EXECUTOR TYPE    |
     +------------------------------------------------------------------+------------+------+--------+---------------------+---------------------+
     | 70e75aa6cca5cd5821aeb19d0586e2ca76173cff7cae42e5e015037e387243be | helloworld |      |        | 2023-11-29 22:36:04 | helloworld-executor |
     | 85be2a0da5ffcc5fe92eb17882e88e5dadcdeb7d8a8364ba12989d6f3d78b343 | helloworld |      |        | 2023-11-29 22:36:05 | helloworld-executor |
     | 2ff94ccba5ae1fd3db3814866a55c79c889833767ae837ff8579c6e72ae32057 | helloworld |      |        | 2023-11-29 22:38:19 | helloworld-executor |
     | 5d0e4fc2341d162f08eefe805413c1125002e518613e56f5c31b0c1ee15048df | helloworld |      |        | 2023-11-29 22:38:19 | helloworld-executor |
     | 0bdd2a22b46f8319c4fcf344aeababa627788c890872281c7b49c868f7692435 | helloworld |      |        | 2023-11-29 22:38:19 | helloworld-executor |
     | 07bf994849e47afe4b8caeee87103fe911b1402d1440d7986c0e0c8130525baf | helloworld |      |        | 2023-11-29 22:38:20 | helloworld-executor |
     | 92fe9912d2b64d874437b95a8c4bb1beb35cbe63cc613e516a33fd1ba71a7b27 | helloworld |      |        | 2023-11-29 22:38:20 | helloworld-executor |
     +------------------------------------------------------------------+------------+------+--------+---------------------+---------------------+

List Running Processes
----------------------
.. code-block:: console

    colonies process ps

.. code-block:: console

     +------------------------------------------------------------------+------------+------+--------+---------------------+---------------------+
     |                                ID                                |  FUNCNAME  | ARGS | KWARGS |     START TIME      |    EXECUTOR TYPE    |
     +------------------------------------------------------------------+------------+------+--------+---------------------+---------------------+
     | cf525a815119b61c98219196982309c580285994d2efbde3ab86c9398e8912ec | helloworld |      |        | 2023-11-29 22:36:04 | helloworld-executor |
     +------------------------------------------------------------------+------------+------+--------+---------------------+---------------------+

List Successful Processes
-------------------------

.. code-block:: console

     colonies process pss

.. code-block:: console

    +------------------------------------------------------------------+------------+------+--------+---------------------+---------------------+
    |                                ID                                |  FUNCNAME  | ARGS | KWARGS |      END TIME       |    EXECUTOR TYPE    |
    +------------------------------------------------------------------+------------+------+--------+---------------------+---------------------+
    | 4adbfdd08bf6e97abe17773359a3821e526067779f076564e153d07f030802d4 | helloworld |      |        | 2023-11-29 22:36:03 | helloworld-executor |
    | 6e6e4eab234617943cf91b3a6eb6ae621530b61b4859d819e18f277bba178acb | helloworld |      |        | 2023-11-29 22:36:02 | helloworld-executor |
    +------------------------------------------------------------------+------------+------+--------+---------------------+---------------------+

List Failed Processes
---------------------

.. code-block:: console
    
    colonies process psf

.. code-block:: console

    +------------------------------------------------------------------+------------+------+--------+---------------------+---------------------+
    |                                ID                                |  FUNCNAME  | ARGS | KWARGS |      END TIME       |    EXECUTOR TYPE    |
    +------------------------------------------------------------------+------------+------+--------+---------------------+---------------------+
    | b9508fb7e12bf1a4c9bd27b2a8d4193a9652e53983a79d016360f17a540658c8 | helloworld |      |        | 2023-11-29 22:36:04 | helloworld-executor |
    +------------------------------------------------------------------+------------+------+--------+---------------------+---------------------+

Get info about a Process
-------------------------

.. code-block:: console

    colonies process get -p 4adbfdd08bf6e97abe17773359a3821e526067779f076564e153d07f030802d4


.. code-block:: console

     Process:
     +--------------------+------------------------------------------------------------------+
     | ID                 | 4adbfdd08bf6e97abe17773359a3821e526067779f076564e153d07f030802d4 |
     | IsAssigned         | True                                                             |
     | AssignedExecutorID | ee58b16a187bb4467437cc068741118bf6ca0ba42e6589c7ea016550ac63e517 |
     | State              | Successful                                                       |
     | PriorityTime       | 1701293763654005927                                              |
     | SubmissionTime     | 2023-11-29 22:36:03                                              |
     | StartTime          | 2023-11-29 22:37:23                                              |
     | EndTime            | 2023-11-29 22:37:41                                              |
     | WaitDeadline       | 0001-01-01 00:53:28                                              |
     | ExecDeadline       | 0001-01-01 00:53:28                                              |
     | WaitingTime        | 1m19.767467s                                                     |
     | ProcessingTime     | 18.021749s                                                       |
     | Retries            | 0                                                                |
     | Errors             |                                                                  |
     | Output             | Hej                                                              |
     +--------------------+------------------------------------------------------------------+
     
     FunctionSpec:
     +-------------+------------+
     | Func        | helloworld |
     | Args        | None       |
     | KwArgs      | None       |
     | MaxWaitTime | -1         |
     | MaxExecTime | -1         |
     | MaxRetries  | -1         |
     | Priority    | 0          |
     +-------------+------------+
     
     Conditions:
     +------------------+---------------------+
     | ColonyName       | dev                 |
     | ExecutorIDs      | None                |
     | ExecutorType     | helloworld-executor |
     | Dependencies     |                     |
     | Nodes            | 0                   |
     | CPU              |                     |
     | Memmory          |                     |
     | Processes        | 0                   |
     | ProcessesPerNode | 0                   |
     | Storage          |                     |
     | Walltime         | 0                   |
     | GPU              |                     |
     | GPUs             | 0                   |
     | GPUMemory        |                     |
     +------------------+---------------------+

     Attributes:
     No attributes found

Or as JSON instead of tables.

.. code-block:: console 

    colonies process get -p 4adbfdd08bf6e97abe17773359a3821e526067779f076564e153d07f030802d4 --json 

.. code-block:: json

    {
       "processid":"4adbfdd08bf6e97abe17773359a3821e526067779f076564e153d07f030802d4",
       "assignedexecutorid":"ee58b16a187bb4467437cc068741118bf6ca0ba42e6589c7ea016550ac63e517",
       "isassigned":true,
       "state":2,
       "prioritytime":1701293763654005927,
       "submissiontime":"2023-11-29T22:36:03.654006+01:00",
       "starttime":"2023-11-29T22:37:23.421473+01:00",
       "endtime":"2023-11-29T22:37:41.443222+01:00",
       "waitdeadline":"0001-01-01T00:53:28+00:53",
       "execdeadline":"0001-01-01T00:53:28+00:53",
       "retries":0,
       "attributes":[
          
       ],
       "spec":{
          "nodename":"",
          "funcname":"helloworld",
          "args":[
          ],
          "kwargs":{
          },
          "priority":0,
          "maxwaittime":-1,
          "maxexectime":-1,
          "maxretries":-1,
          "conditions":{
             "colonyname":"dev",
             "executorids":[
             ],
             "executortype":"helloworld-executor",
             "dependencies":[
             ],
             "nodes":0,
             "cpu":"",
             "processes":0,
             "processes-per-node":0,
             "mem":"",
             "storage":"",
             "gpu":{
                "name":"",
                "mem":"",
                "count":0,
                "nodecount":0
             },
             "walltime":0
          },
          "label":"",
          "fs":{
             "mount":"",
             "snapshots":null,
             "dirs":null
          },
          "env":{
             
          }
       },
       "waitforparents":false,
       "parents":[
          
       ],
       "children":[
          
       ],
       "processgraphid":"",
       "in":[
          
       ],
       "out":[
          "Hej"
       ],
       "errors":[
          
       ]
    }

Remove a Process
----------------

.. code-block:: console

    colonies process remove -p  0bcca3064a6619f91770b9e49c77f7537020a63d7c5b5d693756a2231aa2ad72

.. code-block:: console

    INFO[0000] Process removed

    ProcessId=0bcca3064a6619f91770b9e49c77f7537020a63d7c5b5d693756a2231aa2ad72

Note that it is not possible to remove a process if it is part of a workflows.

Remove all Processes
--------------------

.. code-block:: console

    colonies process removeall

.. code-block:: console

    WARNING!!! Are you sure you want to remove all all processes from Colony <dev>. 
    This operation cannot be undone! (YES,no): YES

    INFO[0002] Deleting all processes in Colony

    ColonyName=dev

To only remove *Waiting* processes, type:

.. code-block:: console

    colonies process removeall --waiting

Or only remove *Successful* processes, type:

.. code-block:: console

    colonies process removeall --successful

Or *Failed* processes, type:

.. code-block:: console

    colonies process removeall --failed

Note that it is not possible to remove processes if it is part of a workflows.

Logs
====
Colonies provides logging functionality, allowing Executors to add log messages to processes, e.g. stdout ot stderr logs. These logs are stored in a PostgreSQL database. If TimescaleDB is used, the logs will be stored in a timeseries hypertable; otherwise, they will be indexed and stored in a regular PostgreSQL table. If retention is enabled, log data will be automatically purged upon reaching its expiration date. This automated process ensures that logs are systematically removed from the server once they become outdated.

Adding Logs to a Process
------------------------
Let's demonstrate how to manage logs using the Colonies CLI. First submit a process.

.. code-block:: console

    colonies function submit --spec ./examples/functions/helloworld.json

To add a log to a process, the process be running and be assigned to an Executor. Only the assigned Executor can add logs. 
Let's register an Executor and assign the process we just submitted.

.. code-block:: console
    
    colonies key generate

.. code-block:: console

    INFO[0000] Generated new private key

    Id=ee58b16a187bb4467437cc068741118bf6ca0ba42e6589c7ea016550ac63e517
    PrvKey=8c32cdcea68600e05df8661eb0cb6679b9ba1d62c901b2a0a55c2eecd9bbbf58
    
.. code-block:: console

    colonies executor add --executorid ee58b16a187bb4467437cc068741118bf6ca0ba42e6589c7ea016550ac63e517 \ 
    --name helloworld-executor \ 
    --type helloworld-executor \
    --approve

.. code-block:: console

    INFO[0000] Executor added

    ColonyName=dev 
    ExecutorID=ee58b16a187bb4467437cc068741118bf6ca0ba42e6589c7ea016550ac63e517 
    ExecutorName=helloworld-executor ExecutorType=helloworld-executor

.. code-block:: console

    colonies process assign --prvkey 8c32cdcea68600e05df8661eb0cb6679b9ba1d62c901b2a0a55c2eecd9bbbf58 

.. code-block:: console

    INFO[0000] Assigned process to executor

    ExecutorId=ee58b16a187bb4467437cc068741118bf6ca0ba42e6589c7ea016550ac63e517
    ProcessId=0ddcc0b74ab1ec0cace153432fbf0bb3c7cdd3deffc0d0a69ad1f210f570962c

Now, the process is assign to the Executor. Let's add log to it.

.. code-block:: console

    colonies log add -p 511c09528b01a26d95bc4ed0899c65f2b95732aadb1221bd42d1c1e17d9daa34 \n
    -m "helloworld" \n
    --prvkey 8c32cdcea68600e05df8661eb0cb6679b9ba1d62c901b2a0a55c2eecd9bbbf58

.. code-block:: console

    INFO[0000] Adding log

    LogMsg=helloworld ProcessID=511c09528b01a26d95bc4ed0899c65f2b95732aadb1221bd42d1c1e17d9daa34

Getting Logs
------------

.. code-block:: console
   
    colonies log get -p 511c09528b01a26d95bc4ed0899c65f2b95732aadb1221bd42d1c1e17d9daa34

.. code-block:: console

    helloworld

It is possible to use flag **-follow** to follow a process and print all logs until the process is concludes. 

Attributes
==========

Add an Attribute to a Process
-----------------------------

.. code-block:: console

    colonies attribute add --key mykey \
   --value myvalue \
   -p 2dfc4d9348624f750151ad1eed24941676c30915d92af96c62bac155609c38c1 \
   --prvkey 8c32cdcea68600e05df8661eb0cb6679b9ba1d62c901b2a0a55c2eecd9bbbf58

.. code-block:: console

   INFO[0000] Attribute added

   AttributeID=77b767baed76180b98a3cf3f553f43dfeee5aad4d98c5107f59015fe04fcdef0

Lookup Attribute on a Process
-----------------------------

.. code-block:: console

    colonies attribute get --attributeid 77b767baed76180b98a3cf3f553f43dfeee5aad4d98c5107f59015fe04fcdef0

.. code-block:: console

    +---------------+------------------------------------------------------------------+
    | ID            | 77b767baed76180b98a3cf3f553f43dfeee5aad4d98c5107f59015fe04fcdef0 |
    | TargetID      | 2dfc4d9348624f750151ad1eed24941676c30915d92af96c62bac155609c38c1 |
    | AttributeType | Out                                                              |
    | Key           | mykey                                                            |
    | Value         | myvalue                                                          |
    +---------------+------------------------------------------------------------------+

Attributes can also viewed by looking up a Process.

.. code-block:: console

    colonies process get -p 2dfc4d9348624f750151ad1eed24941676c30915d92af96c62bac155609c38c1

.. code-block:: console

     Process:
     +--------------------+------------------------------------------------------------------+
     | ID                 | 2dfc4d9348624f750151ad1eed24941676c30915d92af96c62bac155609c38c1 |
     | IsAssigned         | True                                                             |
     | AssignedExecutorID | ee58b16a187bb4467437cc068741118bf6ca0ba42e6589c7ea016550ac63e517 |
     | State              | Running                                                          |
     | PriorityTime       | 1701297603277101941                                              |
     | SubmissionTime     | 2023-11-29 23:40:03                                              |
     | StartTime          | 2023-11-29 23:40:17                                              |
     | EndTime            | 0001-01-01 00:53:28                                              |
     | WaitDeadline       | 0001-01-01 00:53:28                                              |
     | ExecDeadline       | 0001-01-01 00:53:28                                              |
     | WaitingTime        | 14.272943s                                                       |
     | ProcessingTime     | 4m45.843362013s                                                  |
     | Retries            | 0                                                                |
     | Errors             |                                                                  |
     | Output             |                                                                  |
     +--------------------+------------------------------------------------------------------+
     
     FunctionSpec:
     +-------------+------------+
     | Func        | helloworld |
     | Args        | None       |
     | KwArgs      | None       |
     | MaxWaitTime | -1         |
     | MaxExecTime | -1         |
     | MaxRetries  | 0          |
     | Priority    | 0          |
     +-------------+------------+
     
     Conditions:
     +------------------+---------------------+
     | ColonyName       | dev                 |
     | ExecutorIDs      | None                |
     | ExecutorType     | helloworld-executor |
     | Dependencies     |                     |
     | Nodes            | 0                   |
     | CPU              |                     |
     | Memmory          |                     |
     | Processes        | 0                   |
     | ProcessesPerNode | 0                   |
     | Storage          |                     |
     | Walltime         | 0                   |
     | GPU              |                     |
     | GPUs             | 0                   |
     | GPUMemory        |                     |
     +------------------+---------------------+
     
     Attributes:
     +------------------------------------------------------------------+-------+---------+------+
     |                                ID                                |  KEY  |  VALUE  | TYPE |
     +------------------------------------------------------------------+-------+---------+------+
     | 77b767baed76180b98a3cf3f553f43dfeee5aad4d98c5107f59015fe04fcdef0 | mykey | myvalue | Out  |
     +------------------------------------------------------------------+-------+---------+------+

Workflows
=========

Submit a Workflow
-----------------

.. code-block:: json

    [
        {
            "nodename": "task_a",
            "funcname": "echo",
            "args": [
                "task_a"
            ],
            "conditions": {
                "executortype": "cli",
                "dependencies": []
            }
        },
        {
            "nodename": "task_b",
            "funcname": "echo",
            "args": [
                "task_b"
            ],
            "conditions": {
                "executortype": "cli",
                "dependencies": [
                    "task_a"
                ]
            }
        },
        {
            "nodename": "task_c",
            "funcname": "echo",
            "args": [
                "task_c"
            ],
            "conditions": {
                "executortype": "cli",
                "dependencies": [
                    "task_a"
                ]
            }
        },
        {
            "nodename": "task_d",
            "funcname": "echo",
            "args": [
                "task_d"
            ],
            "conditions": {
                "executortype": "cli",
                "dependencies": [
                    "task_b",
                    "task_c"
                ]
            }
        }
    ]

.. code-block:: text

    colonies workflow submit --spec examples/workflows/workflow.json

.. code-block:: text

    INFO[0000] Workflow submitted

    WorkflowID=3dd558fdd28cbc3ab01c5cf7e68ab8ca42e174aca9f520f193c9b98aca00696d

.. code-block:: console 

    colonies process psw

.. code-block:: console 

    +------------------------------------------------------------------+------+--------+--------+---------------------+---------------+
    |                                ID                                | FUNC |  ARGS  | KWARGS |   SUBMISSION TIME   | EXECUTOR TYPE |
    +------------------------------------------------------------------+------+--------+--------+---------------------+---------------+
    | 3c42610205e044b83040b8ce5b3efd1fb5a73f5f71ab755785c1fd12b5799ee5 | echo | task_a |        | 2023-11-30 13:43:16 | cli           |
    | fc58c224bd585868754b20a56b43b722c1387bebbe41124646c5ca9e3449caa2 | echo | task_b |        | 2023-11-30 13:43:16 | cli           |
    | b1f214365bb4c1dcfaf0d4a73c2cfed22f07fb8cfecdc89f99538f32f57d6ffa | echo | task_c |        | 2023-11-30 13:43:16 | cli           |
    | dc6f9f2ff1c2730db290f3c549a87ea5ee75629d905f286c04620f8615433594 | echo | task_d |        | 2023-11-30 13:43:16 | cli           |
    +------------------------------------------------------------------+------+--------+--------+---------------------+---------------+

List Waiting Workflows
----------------------

.. code-block:: console 

    colonies workflow psw

.. code-block:: console 

    +------------------------------------------------------------------+---------------------+
    |                                ID                                |   SUBMISSION TIME   |
    +------------------------------------------------------------------+---------------------+
    | 7fee39395bc839168efff707ed5ed23dcf713c7a87cb9e3f2e679f24bc3b79e3 | 2023-11-30 13:56:38 |
    +------------------------------------------------------------------+---------------------+

List Running Workflows
----------------------

.. code-block:: console 

    colonies workflow ps

.. code-block:: console

    WARN[0000] No running workflows found

List Successful Workflows
-------------------------

.. code-block:: console 

    colonies workflow pss

.. code-block:: console

    WARN[0000] No successful workflows found

List Failed Workflows
---------------------

.. code-block:: console 

    colonies workflow psf

.. code-block:: console

    WARN[0000] No successful workflows found


Get info about a Workflow
-------------------------

.. code-block:: console

    colonies workflow get --workflowid 7fee39395bc839168efff707ed5ed23dcf713c7a87cb9e3f2e679f24bc3b79e3

.. code-block:: console

    Workflow:
    +----------------+------------------------------------------------------------------+
    | WorkflowID     | 7fee39395bc839168efff707ed5ed23dcf713c7a87cb9e3f2e679f24bc3b79e3 |
    | ColonyName     | 7fee39395bc839168efff707ed5ed23dcf713c7a87cb9e3f2e679f24bc3b79e3 |
    | State          | Waiting                                                          |
    | SubmissionTime | 2023-11-30 13:56:38                                              |
    | StartTime      | 0001-01-01 00:53:28                                              |
    | EndTime        | 0001-01-01 00:53:28                                              |
    +----------------+------------------------------------------------------------------+
    
    Processes:
    +-------------------+------------------------------------------------------------------+
    | NodeName          | task_a                                                           |
    | ProcessID         | 515b789379219db59d16112fc006c0a00fd8194de9e2848ac9952d33da80ca74 |
    | ExecutorType      | cli                                                              |
    | FuncName          | echo                                                             |
    | Args              | task_a                                                           |
    | KwArgs            | None                                                             |
    | State             | Waiting                                                          |
    | WaitingForParents | false                                                            |
    | Dependencies      | None                                                             |
    +-------------------+------------------------------------------------------------------+
    
    +-------------------+------------------------------------------------------------------+
    | NodeName          | task_b                                                           |
    | ProcessID         | 16cd30c3d35b864d562c575e15bf169f3e78a44a42863787664a38c4fa2bcb7f |
    | ExecutorType      | cli                                                              |
    | FuncName          | echo                                                             |
    | Args              | task_b                                                           |
    | KwArgs            | None                                                             |
    | State             | Waiting                                                          |
    | WaitingForParents | true                                                             |
    | Dependencies      | task_a                                                           |
    +-------------------+------------------------------------------------------------------+
    
    +-------------------+------------------------------------------------------------------+
    | NodeName          | task_d                                                           |
    | ProcessID         | 2883d96adeaed704900f7001ccbb89e02b00d1d7b4718326de1ce19c65c4c69d |
    | ExecutorType      | cli                                                              |
    | FuncName          | echo                                                             |
    | Args              | task_d                                                           |
    | KwArgs            | None                                                             |
    | State             | Waiting                                                          |
    | WaitingForParents | true                                                             |
    | Dependencies      | task_b task_c                                                    |
    +-------------------+------------------------------------------------------------------+
    
    +-------------------+------------------------------------------------------------------+
    | NodeName          | task_c                                                           |
    | ProcessID         | 4c1c6b96d509b4b57291973d23195cf3a0a3134a53b0584f27d31101366c56ea |
    | ExecutorType      | cli                                                              |
    | FuncName          | echo                                                             |
    | Args              | task_c                                                           |
    | KwArgs            | None                                                             |
    | State             | Waiting                                                          |
    | WaitingForParents | true                                                             |
    | Dependencies      | task_a                                                           |
    +-------------------+------------------------------------------------------------------+
   
Remove a Workflow
-----------------
.. code-block:: text
    
    colonies workflow remove --workflowid 7fee39395bc839168efff707ed5ed23dcf713c7a87cb9e3f2e679f24bc3b79e3

.. code-block:: text

    INFO[0000] Workflow removed

    WorkflowID=7fee39395bc839168efff707ed5ed23dcf713c7a87cb9e3f2e679f24bc3b79e3

Remove all Workflows
--------------------

.. code-block:: console

    colonies workflow removeall

.. code-block:: console

    WARNING!!! Are you sure you want to remove all workflows in the Colony <dev>. This operation cannot be undone! (YES,no):
    This operation cannot be undone! (YES,no): YES

    INFO[0002] Deleting all workflows in Colony

    ColonyName=dev

To only remove *Waiting* processes, type:

.. code-block:: console

    colonies workflow removeall --waiting

Or only remove *Successful* processes, type:

.. code-block:: console

    colonies workflow removeall --successful

Or *Failed* processes, type:

.. code-block:: console

    colonies worklflow removeall --failed

Crons
=====
Cron expressions follow this format:

.. code-block:: text
   
    ┌───────────── second (0 - 59)
    │ ┌───────────── minute (0 - 59) 
    │ │ ┌───────────── hour (0 - 23)
    │ │ │ ┌───────────── day of the month (1 - 31)
    │ │ │ │ ┌───────────── month (1 - 12) 
    │ │ │ │ │ ┌───────────── day of the week
    │ │ │ │ │ │ 
    │ │ │ │ │ │ 
    * * * * * *

Spawn a workflow every second starting at 00 seconds: :code:`0/1 * * * * *`

Spawn a workflow every other second starting at 00 seconds: :code:`0/2 * * * * *`

Spawn a workflow every minute starting at 30 seconds: :code:`30 * * * * *`

Spawn a workflow every Monday at 15:03:59: :code:`59 3 15 * * MON`

Spawn a workflow every Christmas Eve at 15:00: :code:`0 0 15 24 12 *`

Add a Cron
----------
Let's add a **Cron** and run this workflow every 5 seconds.

.. code-block:: json 

     [
         {
             "nodename": "generate_date",
             "funcname": "date",
             "args": [
                 ">",
                 "/tmp/currentdate"
             ],
             "conditions": {
                 "executortype": "cli",
                 "dependencies": []
             }
         },
         {
             "nodename": "print_date",
             "funcname": "cat",
             "args": [
                 "/tmp/currentdate"
             ],
             "conditions": {
                 "executortype": "cli",
                 "dependencies": [
                     "generate_date"
                 ]
             }
         }
     ]

.. code-block:: console

    colonies cron add --name example_cron --cron "0/5 * * * * *" --spec examples/cron/cron_workflow.json

.. code-block:: console

    INFO[0000] Will not wait for previous processgraph to finish
    INFO[0000] Cron added

    CronID=733ec939a47ae4a499bdabcd3425e82b3c245613afe065ad6002dede8b98d5c2

We can now see that new Processes starting to appear every 5 seconds. Use the flag **--waitprevious** to only spawn a new process if the current 
process in the queue has finised or failed.

.. code-block:: console

    colonies process psw                                                                            23:58:09

.. code-block:: console

    +------------------------------------------------------------------+------+--------------------+--------+---------------------+---------------+
    |                                ID                                | FUNC |        ARGS        | KWARGS |   SUBMISSION TIME   | EXECUTOR TYPE |
    +------------------------------------------------------------------+------+--------------------+--------+---------------------+---------------+
    | 763e7fe136a4b58f749c343fc31b6172c45a3362e29dfc60b91cd3dbe15114e8 | date | > /tmp/currentdate |        | 2023-11-29 23:56:25 | cli           |
    | 47172a9f4033a954187b7987592fe8bd5e54095bcd430f6344387ab5d51e6cbb | cat  | /tmp/currentdate   |        | 2023-11-29 23:56:25 | cli           |
    | aeb966dfb3698c0fac9e1c32a0420a896445ac7944cca6cb5c0fe52c66a6f5e3 | date | > /tmp/currentdate |        | 2023-11-29 23:56:30 | cli           |
    | 0f457635a6582440f945b59b435ee9ab5a7c653c08f694e1ca1bc190785b8c87 | cat  | /tmp/currentdate   |        | 2023-11-29 23:56:30 | cli           |
    | ba621dfe492d4ca899c61bece97d43f2642bf83bdc345fcfb8bb448aa8ceb591 | date | > /tmp/currentdate |        | 2023-11-29 23:56:35 | cli           |
    | 914700a5309c8c9f609921d0d2764be58c2d2756331d780279c3bbda6777a680 | cat  | /tmp/currentdate   |        | 2023-11-29 23:56:35 | cli           |
    | 0a1ce698bb0f38163563aac7130e26eece5cba9afc7a913f16348544f4555910 | date | > /tmp/currentdate |        | 2023-11-29 23:56:40 | cli           |
    | d28f4fe18b9a710d5ab2701dc860bf07bdc801cdbb74cc1a0e5e5601deab4736 | cat  | /tmp/currentdate   |        | 2023-11-29 23:56:40 | cli           |
    | bb73ad7e491b00a4469f1a49bc251f6d1909d48ff24addfeba1bd225a08b2e05 | date | > /tmp/currentdate |        | 2023-11-29 23:56:45 | cli           |
    | 02076d5f70d9dcc464bb6079d61347895fd7d03c60aedcef163214bd8a67ffb5 | cat  | /tmp/currentdate   |        | 2023-11-29 23:56:45 | cli           |
    +------------------------------------------------------------------+------+--------------------+--------+---------------------+---------------+

Listing Crons
-------------
.. code-block:: console

    colonies cron ls

.. code-block:: console

    +------------------------------------------------------------------+--------------+
    |                              CRONID                              |     NAME     |
    +------------------------------------------------------------------+--------------+
    | 733ec939a47ae4a499bdabcd3425e82b3c245613afe065ad6002dede8b98d5c2 | example_cron |
    +------------------------------------------------------------------+--------------+

Getting info about a Cron
-------------------------
.. code-block:: console

    colonies cron get --cronid 733ec939a47ae4a499bdabcd3425e82b3c245613afe065ad6002dede8b98d5c2

.. code-block:: console

     Cron:
     +-------------------------+------------------------------------------------------------------+
     | Id                      | 733ec939a47ae4a499bdabcd3425e82b3c245613afe065ad6002dede8b98d5c2 |
     | ColonyName              | dev                                                              |
     | Name                    | example_cron                                                     |
     | Cron Expression         | 0/5 * * * * *                                                    |
     | Interval                | -1                                                               |
     | Random                  | false                                                            |
     | NextRun                 | 2023-11-30 08:40:20                                              |
     | LastRun                 | 2023-11-30 08:40:15                                              |
     | PrevProcessGraphID      | c02ced89f3e000bd5e0032b54672de377a58e4cb15c724261809a6e028ed6e75 |
     | WaitForPrevProcessGraph | false                                                            |
     | CheckerPeriod           | 1000                                                             |
     +-------------------------+------------------------------------------------------------------+
     
     WorkflowSpec:
     
     FunctionSpec 0:
     +-------------+---------------------+
     | Func        | date                |
     | Args        | > /tmp/currentdate  |
     | KwArgs      | None                |
     | MaxWaitTime | 0                   |
     | MaxExecTime | 0                   |
     | MaxRetries  | 0                   |
     | Priority    | 0                   |
     +-------------+---------------------+
     
     Conditions:
     +------------------+------+
     | ColonyName       |      |
     | ExecutorIDs      | None |
     | ExecutorType     | cli  |
     | Dependencies     |      |
     | Nodes            | 0    |
     | CPU              |      |
     | Memmory          |      |
     | Processes        | 0    |
     | ProcessesPerNode | 0    |
     | Storage          |      |
     | Walltime         | 0    |
     | GPU              |      |
     | GPUs             | 0    |
     | GPUMemory        |      |
     +------------------+------+
     
     FunctionSpec 1:
     +-------------+-------------------+
     | Func        | cat               |
     | Args        | /tmp/currentdate  |
     | KwArgs      | None              |
     | MaxWaitTime | 0                 |
     | MaxExecTime | 0                 |
     | MaxRetries  | 0                 |
     | Priority    | 0                 |
     +-------------+-------------------+
     
     Conditions:
     +------------------+---------------+
     | ColonyName       |               |
     | ExecutorIDs      | None          |
     | ExecutorType     | cli           |
     | Dependencies     | generate_date |
     | Nodes            | 0             |
     | CPU              |               |
     | Memmory          |               |
     | Processes        | 0             |
     | ProcessesPerNode | 0             |
     | Storage          |               |
     | Walltime         | 0             |
     | GPU              |               |
     | GPUs             | 0             |
     | GPUMemory        |               |
     +------------------+---------------+

Immediately run a Cron
----------------------

.. code-block:: console

    colonies cron run --cronid 733ec939a47ae4a499bdabcd3425e82b3c245613afe065ad6002dede8b98d5c2

.. code-block:: console

    INFO[0000] Running cron

    CronID=733ec939a47ae4a499bdabcd3425e82b3c245613afe065ad6002dede8b98d5c2

Use interval instead of a Cron expressions
------------------------------------------
An alternative way to spawn a cron is to specify an interval instead of a cron expression. In the example, below a workflow is spawned every 10 seconds.

.. code-block:: console
    
    colonies cron add --name another_example_cron \ 
    --interval 10 \ 
    --spec examples/cron/cron_workflow.json

.. code-block:: console

    INFO[0000] Will not wait for previous processgraph to finish
    INFO[0000] Cron added

    CronID=63aa987bb07444d16525dccf4f9936e563ca43f2ad0756a7aedb7a837ede5728

Random intervals
----------------
It is also possible to spawn a workflow at a random time within an interval. This can be very useful when testing a software (e.g. chaos engineering).

In the example, a workflow will be spawned randomly within 10 seconds.

.. code-block:: console
    
    colonies cron add --name random_example_cron \ 
    --interval 10 \ 
    --random 10 \ 
    --spec examples/cron/cron_workflow.json

.. code-block:: console
    INFO[0000] Will not wait for previous processgraph to finish
    INFO[0000] Cron added

    CronID=8f3c8db3a5f54c7726851166952453752e6381e4b1e44ab1d29f41bd4779ff11


Remove a Cron
-------------

.. code-block:: console

    colonies cron remove --cronid e5e25a98305ac11ff9292b584d4b119a48c99c5fa599d43e63cd9d57c53927d8

.. code-block:: console

    INFO[0000] Deleting cron

    CronId=e5e25a98305ac11ff9292b584d4b119a48c99c5fa599d43e63cd9d57c53927d8

Generators
==========
Generators automatically spawn workflows when number of **pack** calls exceeds a threshold. Pack **data** is then available as an argument to the process.

.. code-block:: console

    colonies generator add --spec ./examples/generators/generator_workflow.json --name testgenerator --trigger 5

.. code-block:: console

    INFO[0000] Generator added

    GeneratorID=1093f5b68dcc2583104250f3390db891fbe7b8467fd8095505714786ec9fe87d GeneratorName=testgenerator Timeout=-1 Trigger=5

Send data to Generator
----------------------
After 5 pack calls, the **Generator** should generate a workflow.

.. code-block:: console

    colonies generator pack --generatorid 1093f5b68dcc2583104250f3390db891fbe7b8467fd8095505714786ec9fe87d --arg hello1
    colonies generator pack --generatorid 1093f5b68dcc2583104250f3390db891fbe7b8467fd8095505714786ec9fe87d --arg hello2
    colonies generator pack --generatorid 1093f5b68dcc2583104250f3390db891fbe7b8467fd8095505714786ec9fe87d --arg hello3
    colonies generator pack --generatorid 1093f5b68dcc2583104250f3390db891fbe7b8467fd8095505714786ec9fe87d --arg hello4
    colonies generator pack --generatorid 1093f5b68dcc2583104250f3390db891fbe7b8467fd8095505714786ec9fe87d --arg hello5

Let's see if a Workflow was created.

.. code-block:: console

    colonies workflow psw

.. code-block:: console

    +------------------------------------------------------------------+---------------------+
    |                                ID                                |   SUBMISSION TIME   |
    +------------------------------------------------------------------+---------------------+
    | e470e6ad1ed92a0e9ccd3431223ba47be99c7f460b70d1f05439d7af238cea97 | 2023-11-30 14:28:22 |
    +------------------------------------------------------------------+---------------------+

Let's lookup the Workflow to see if the data is there.

.. code-block:: console

    colonies workflow get --workflowid  e470e6ad1ed92a0e9ccd3431223ba47be99c7f460b70d1f05439d7af238cea97

.. code-block:: console

    Workflow:
    +----------------+------------------------------------------------------------------+
    | WorkflowID     | e470e6ad1ed92a0e9ccd3431223ba47be99c7f460b70d1f05439d7af238cea97 |
    | ColonyName     | e470e6ad1ed92a0e9ccd3431223ba47be99c7f460b70d1f05439d7af238cea97 |
    | State          | Waiting                                                          |
    | SubmissionTime | 2023-11-30 14:28:22                                              |
    | StartTime      | 0001-01-01 00:53:28                                              |
    | EndTime        | 0001-01-01 00:53:28                                              |
    +----------------+------------------------------------------------------------------+
    
    Processes:
    +-------------------+------------------------------------------------------------------+
    | NodeName          | generator_example                                                |
    | ProcessID         | c4b589589e777ffbbaeb97494c0a43adf1bbead7ee24d711023712a1c0e36d61 |
    | ExecutorType      | cli                                                              |
    | FuncName          | echo                                                             |
    | Args              | hello1 hello2 hello3 hello4                                      |
    |                   | hello5                                                           |
    | KwArgs            | None                                                             |
    | State             | Waiting                                                          |
    | WaitingForParents | false                                                            |
    | Dependencies      | None                                                             |
    +-------------------+------------------------------------------------------------------+

List Generators
---------------
.. code-block:: console

    colonies generator ls

.. code-block:: console

    +------------------------------------------------------------------+---------------+
    |                           GENERATORID                            |     NAME      |
    +------------------------------------------------------------------+---------------+
    | 1093f5b68dcc2583104250f3390db891fbe7b8467fd8095505714786ec9fe87d | testgenerator |
    +------------------------------------------------------------------+---------------+

Get info about a Generator
--------------------------
.. code-block:: console

    colonies generator get --generatorid  1093f5b68dcc2583104250f3390db891fbe7b8467fd8095505714786ec9fe87d  

.. code-block:: console

     Generator:
     +---------------+------------------------------------------------------------------+
     | Id            | 1093f5b68dcc2583104250f3390db891fbe7b8467fd8095505714786ec9fe87d |
     | Name          | testgenerator                                                    |
     | Trigger       | 5                                                                |
     | Timeout       | -1                                                               |
     | Lastrun       | 2023-11-30 14:28:22                                              |
     | CheckerPeriod | 1000                                                             |
     | QueueSize     | 0                                                                |
     +---------------+------------------------------------------------------------------+
     
     WorkflowSpec:
     
     FunctionSpec 0:
     +-------------+------+
     | Func        | echo |
     | Args        | None |
     | KwArgs      | None |
     | MaxWaitTime | 0    |
     | MaxExecTime | 0    |
     | MaxRetries  | 0    |
     | Priority    | 0    |
     +-------------+------+
     
     Conditions:
     +------------------+------+
     | ColonyName       |      |
     | ExecutorIDs      | None |
     | ExecutorType     | cli  |
     | Dependencies     |      |
     | Nodes            | 0    |
     | CPU              |      |
     | Memmory          |      |
     | Processes        | 0    |
     | ProcessesPerNode | 0    |
     | Storage          |      |
     | Walltime         | 0    |
     | GPU              |      |
     | GPUs             | 0    |
     | GPUMemory        |      |
     +------------------+------+

Remove a Generator
------------------

.. code-block:: console

    colonies generator remove --generatorid 1093f5b68dcc2583104250f3390db891fbe7b8467fd8095505714786ec9fe87d

.. code-block:: console

    INFO[0000] Deleting generator

    GeneratorID=1093f5b68dcc2583104250f3390db891fbe7b8467fd8095505714786ec9fe87d

Filesystem
==========
ColonyOS features a built-in *Meta-Filesystem* designed to make data transfer between Executors easier. Unlike regular filesystems that store data directly, Colony FS only contains metadata on how to access files, for example data location, credentials or configuration settings. The data itself is stored in various other places like Amazon S3 or IPFS. 

Make sure the following environmental variable is set

.. code-block:: console

    export AWS_S3_ENDPOINT="s3.colonyos.io:443"
    export AWS_S3_ACCESSKEY=""
    export AWS_S3_SECRETKEY=""
    export AWS_S3_REGION_KEY=""
    export AWS_S3_BUCKET=""
    export AWS_S3_TLS=""
    export AWS_S3_SKIPVERIFY=""

Synchronizing files
-------------------

First create some files.

.. code-block:: console 

    mkdir myfiles  
    cd myfiles
    echo "Hello" > hello.txt
    mkdir subdir
    cd subdir
    echo "Hello 2" > hello2.txt  

Now, upload the *myfiles* directory and all its sub-directories to CFS under the **label** *myfiles*.

.. code-block:: console 

    colonies fs sync -l myfiles -d myfiles

.. code-block:: console 

    /myfiles:
    =========
    These files will be uploaded:
    +-----------+-------+---------+
    |   FILE    | SIZE  |  LABEL  |
    +-----------+-------+---------+
    | hello.txt | 0 KiB | myfiles |
    +-----------+-------+---------+
    No files will be downloaded
    
    /myfiles/subdir:
    ================
    These files will be uploaded:
    +------------+-------+---------+
    |    FILE    | SIZE  |  LABEL  |
    +------------+-------+---------+
    | hello2.txt | 0 KiB | myfiles |
    +------------+-------+---------+
    No files will be downloaded
    
    Are you sure you want to continue? (yes,no): yes
    Uploading hello.txt 100% [===============] (19 kB/s)
    Uploading hello2.txt 100% [===============] (32 kB/s)


We can not download the files from another computer, or just to another directory (*myfiles2*).

.. code-block:: console 

    colonies fs sync -l myfiles -d myfiles2

.. code-block:: console 

    /myfiles:
    =========
    No files will be uploaded
    
    These files will be downloaded to directory: myfiles2/
    +-----------+-------+---------+
    |   FILE    | SIZE  |  LABEL  |
    +-----------+-------+---------+
    | hello.txt | 0 KiB | myfiles |
    +-----------+-------+---------+
    
    /myfiles/subdir:
    ================
    No files will be uploaded
    
    These files will be downloaded to directory: myfiles2/
    +------------+-------+---------+
    |    FILE    | SIZE  |  LABEL  |
    +------------+-------+---------+
    | hello2.txt | 0 KiB | myfiles |
    +------------+-------+---------+
    
    Are you sure you want to continue? (yes,no): yes
     Downloading hello.txt 100% [===============] (196 B/s)
     Downloading hello2.txt 100% [===============] (224 B/s)

Use the flag **--keeplocal=true** to prevent the CLI from overwriting local files in case the files have changed remotely.

List all Labels
---------------

.. code-block:: console 

    colonies fs label ls

.. code-block:: console 

    +-----------------+-----------------+
    |      LABEL      | NUMBER OF FILES |
    +-----------------+-----------------+
    | /myfiles        | 1               |
    | /myfiles/subdir | 1               |
    +-----------------+-----------------+

.. code-block:: console 

    colonies fs ls -l /myfiles

List files in a Label
---------------------

.. code-block:: console 

    colonies fs ls -l /myfiles

.. code-block:: console 

    +-----------+-------+------------------------------------------------------------------+---------------------+-----------+
    | FILENAME  | SIZE  |                            LATEST ID                             |        ADDED        | REVISIONS |
    +-----------+-------+------------------------------------------------------------------+---------------------+-----------+
    | hello.txt | 0 KiB | f8bcf8878543d199e4b7d48209f2dbd81be69be9609018ea33c64ebd403df47c | 2023-11-29 23:06:29 | 1         |
    +-----------+-------+------------------------------------------------------------------+---------------------+-----------+

Get info about a File
---------------------

.. code-block:: console 

    colonies fs info -l /myfiles -n hello.txt

.. code-block:: console 

     +-----------------+------------------------------------------------------------------+
     | Filename        | hello.txt                                                        |
     | Id              | f8bcf8878543d199e4b7d48209f2dbd81be69be9609018ea33c64ebd403df47c |
     | ColonyName      | dev                                                              |
     | Added           | 2023-11-29 23:06:29                                              |
     | Sequence Number | 1                                                                |
     | Label           | /myfiles                                                         |
     | Size            | 0 KiB                                                            |
     | Checksum        | 66a045b452102c59d840ec097d59d9467e13a3f34f6494e539ffd32c1bb35f18 |
     | Checksum Alg    | SHA256                                                           |
     | Protocol        | s3                                                               |
     | S3 Endpoint     | s3.colonyos.io:443                                               |
     | S3 TLS          | true                                                             |
     | S3 Region       |                                                                  |
     | S3 Bucket       | colonies-prod                                                    |
     | S3 Object       | fdfc87d498950102a6129b7e09489e16c59e19f38310c2f93213e5f54656fea7 |
     | S3 Accesskey    | *********************************                                |
     | S3 Secretkey    | *********************************                                |
     | Encryption Key  | *********************************                                |
     | Encryption Alg  |                                                                  |
     +-----------------+------------------------------------------------------------------+

Download a specific File
------------------------
To download a specific File in a specific Label to a directory *newdir*:

.. code-block:: console 

    colonies fs get -l /myfiles -n hello.txt -d newdir

.. code-block:: console 

      Downloading hello.txt 100% [===============] (191 B/s)

Create a Snapshot
-----------------

.. code-block:: console 

    colonies fs snapshot create -l /myfiles -n mysnapshot

.. code-block:: console 

      INFO[0000] Snapshot created                              Label=/myfiles/ SnapshotName=mysnapshot
     +------------+------------------------------------------------------------------+
     | SnapshotId | 9e93f4017e0f1f9a9db7d0b987b07f0bb5389869ea731b1ddc1952f7adb6c234 |
     | ColonyName | dev                                                              |
     | Label      | /myfiles/                                                        |
     | Name       | mysnapshot                                                       |
     | Added      | 2023-11-29 22:32:07                                              |
     +------------+------------------------------------------------------------------+
     
     +------------+------------------------------------------------------------------+---------------------+
     |  FILENAME  |                              FILEID                              |        ADDED        |
     +------------+------------------------------------------------------------------+---------------------+
     | hello2.txt | 043fac24f80c4ec063bd038e65b8092030fa8388b51512cb00aff00c40c42d73 | 2023-11-29 23:06:29 |
     +------------+------------------------------------------------------------------+---------------------+

