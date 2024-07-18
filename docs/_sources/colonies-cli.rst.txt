Getting started
===============

The Colonies is written in Golang and is compiled into a single binary, which simplifies deployment and eliminates external dependencies, ensuring consistent, 
cross-platform execution. The entire ColonyOS framework consists of the **colonies** binary.

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

If you are a colony administrator, you may also want to set: 

.. code-block:: console

    export COLONIES_COLONY_PRVKEY="29ddccc9e2825378b9055e2667c2fcf0ceffc4e8744ad0e97dd37f8a20750820"

To be able to create a new colony, you need to be a Colonies server administrator and set the following variables:

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

If you are using the Colonies CLI on Windows with Git Bash, you may need to set the following environment variable to prevent Git Bash to translates Unix-like paths into Windows-style paths.

.. code-block:: console

   export MSYS_NO_PATHCONV=1

To print table in ASCII format, set the following environment variable:

.. code-block:: console

   export COLONIES_CLI_ASCII="false"


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

Auto completion
---------------
Generate a completion file.

.. code-block:: console

    colonies completion fish > colonies_completion  

Then source the ``colonies_completion`` file in the shell config file, e.g. ``config.fish``.

.. code-block:: console

    source ~/.config/fish/colonies_completion

Show current configuration
--------------------------
To get info about current configuration type:

.. code-block:: console

    colonies config

.. code-block:: console

   ╭──────────────────────────────────╮
   │ Current configuration            │
   ├────────────────┬─────────────────┤
   │ ColoniesServer │ localhost:50080 │
   │ TLS            │ false           │
   │ Colony         │ dev             │
   ╰────────────────┴─────────────────╯

Building from source
--------------------
First install `Golang <https://go.dev>`_, then just type: ``go run cmd/main.go`` in root of the Colonies Github directory. 
Alternatively, there is Makefile, type: ``make``.

Colony
======

Create a new colony
-------------------
First generate a new ECDSA private key. To use the new colony, you need to export the key as ``COLONIES_COLONY_PRVKEY``.

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


List all registered colonies
----------------------------
To list all registered colonies, you must be server administrator and the ``COLONIES_SERVER_PRVKEY`` must be set.

.. code-block:: console

    colonies colony ls

.. code-block:: console

   ╭─────────────┬──────────────────────────────────────────────────────────────────╮
   │ NAME        │ COLONYID                                                         │
   ├─────────────┼──────────────────────────────────────────────────────────────────┤
   │ dev         │ 4787a5071856a4acf702b2ffcea422e3237a679c681314113d86139461290cf4 │
   │ test_colony │ f5ce6d9c328b0750ea37cad504e5f64e2380836231e9389eb848f77250eb038f │
   ╰─────────────┴──────────────────────────────────────────────────────────────────╯

Remove a colony
---------------
Only a colony owner can remove a colony and you need to have a valid ``COLONIES_COLONY_PRVKEY`` private key.

.. code-block:: console
    
    colonies colony remove --name test_colony  

Get statistics
--------------
All valid users and executors can get statistics on a colony. Set the ``COLONIES_PRVKEY`` environment variable to interact with a colony,
and the ``COLONIES_COLONY_NAME`` environment variable to specify the colony name.

.. code-block:: console

    colonies colony stats --name dev

.. code-block:: console

   ╭──────────────────────┬─────╮
   │ Colony               │ dev │
   │ Executors            │ 3   │
   │ Waiting processes    │ 10  │
   │ Running processes    │ 3   │
   │ Successful processes │ 131 │
   │ Failed processes     │ 10  │
   │ Waiting workflows    │ 0   │
   │ Running workflows    │ 0   │
   │ Successful workflows │ 5   │
   │ Failed workflows     │ 1   │
   ╰──────────────────────┴─────╯

Users
=====

Add a new user
--------------
First, generate a new ECDSA private key. The user Id is cryptographically linked to this newly generated private key. In fact, the Colonies server will reconstruct the user Id from the signatures of messages sent to it.

It's also important to note that only the colony owner can add a user. Therefore, ensure that the ``COLONIES_COLONY_PRVKEY`` environment variable is correctly set.

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

List users 
----------
To list all users member of a colony.

.. code-block:: console

   colonies user ls

.. code-block:: console

   ╭──────────┬──────────────────────────┬───────────────╮
   │ USERNAME │ EMAIL                    │ PHONE         │
   ├──────────┼──────────────────────────┼───────────────┤
   │ johan    │ johan.kristiansson@ri.se │ +467011122233 │
   ╰──────────┴──────────────────────────┴───────────────╯

Get info about a user 
---------------------

.. code-block:: console

    colonies user get --name johan

.. code-block:: console

   ╭──────────┬──────────────────────────────────────────────────────────────────╮
   │ Username │ johan                                                            │
   │ UserId   │ b06e5e9445b2db98ec66a813a0fba923422163923c9b41096867961ec39a5ab5 │
   │ Phone    │ +467011122233                                                    │
   │ Email    │ johan.kristiansson@ri.se                                         │
   │ Colony   │ dev                                                              │
   ╰──────────┴──────────────────────────────────────────────────────────────────╯

Remove a user 
-------------

.. code-block:: console

    colonies user remove --name johan

.. code-block:: console

    INFO[0000] User removed   

    ColonyName=dev 
    Username=johan

Executors
=========

Register a new executor 
-----------------------
Copy the JSON object below to a file, e.g. executor.json. Only **executorname** and **executortype** are mandatory fields. 
And only a colony owner can register a new executor.

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

Below is a minimal executor spec.

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

If **--approve** is not specified, the executor will be registered, but is not allowed to get process assignments.

Approve an executor
-------------------
Not approved executors do not take part of process brokering and will not get any processassignments. 
The following command will approve an executor:

.. code-block:: console
    
    colonies executor approve --name my_executor

.. code-block:: console

    INFO[0000] Executor approved

    ColonyName=dev ExecutorName=my_executor

Reject an executor
------------------
The following command will reject an executor and prevent it from taking part of process brokering:

.. code-block:: console
    
    colonies executor reject --name my_executor

.. code-block:: console

    INFO[0000] Executor rejected

    ColonyName=dev ExecutorName=my_executor

Remove an executor
------------------

.. code-block:: console
    
    colonies executor remove --name  ml-executor

.. code-block:: console

    INFO[0000] Executor removed

    ColonyName=dev ExecutorName=ml-executor


List executors
--------------
 
.. code-block:: console
      
    colonies executor ls

.. code-block:: console

   ╭─────────────┬──────┬────────────────╮
   │ NAME        │ TYPE │ LOCATION       │
   ├─────────────┼──────┼────────────────┤
   │ ml-executor │ ml   │ ICE Datacenter │
   │ myexecutor  │ cli  │                │
   ╰─────────────┴──────┴────────────────╯

Get info about an executor
--------------------------

.. code-block:: console

    colonies executor get --name ml-executor

.. code-block:: console

   ╭─────────────────────────┬──────────────────────────────────────────────────────────────────╮
   │ Name                    │ ml-executor                                                      │
   │ Id                      │ 24bbbc074019734fc4676ec1641ca6f22c3ac943c48067ded3649602653a96c1 │
   │ Type                    │ ml                                                               │
   │ Colony                  │ dev                                                              │
   │ State                   │ Approved                                                         │
   │ RequireFuncRegistration │ False                                                            │
   │ CommissionTime          │ 2024-01-02 18:54:06                                              │
   │ LastHeardFrom           │ 0001-01-01 00:53:28                                              │
   ╰─────────────────────────┴──────────────────────────────────────────────────────────────────╯
   ╭──────────────────────────────╮
   │ Location                     │
   ├─────────────┬────────────────┤
   │ Longitude   │ 65.612046      │
   │ Latitude    │ 22.132276      │
   │ Description │ ICE Datacenter │
   ╰─────────────┴────────────────╯
   ╭──────────────────────────────────────────────────╮
   │ Hardware                                         │
   ├────────────┬─────────────────────────────────────┤
   │ Nodes      │ 0                                   │
   │ Model      │ AMD Ryzen 9 5950X 16-Core Processor │
   │ CPU        │ 4000m                               │
   │ Memory     │ 16Gi                                │
   │ Storage    │ 100Ti                               │
   │ GPU        │ nvidia_3080ti                       │
   │ GPUs       │ 1                                   │
   │ GPU/Node   │ 0                                   │
   │ GPU Memory │                                     │
   ╰────────────┴─────────────────────────────────────╯
   ╭──────────────────────────────╮
   │ Software                     │
   ├─────────┬────────────────────┤
   │ Name    │ colonyos/ml:latest │
   │ Type    │ k8s                │
   │ Version │ latest             │
   ╰─────────┴────────────────────╯

Functions
=========

Submitting function specs
-------------------------

ColonyOS operates on the principle of submitting **Functions Specifications** to a colony, which are then executed by various executors, members of that colony. When a **Function Specification** is received by the Colonies server, it is wrapped into a **process**, which is subsequently assigned to an executor. Each executor is responsible for implementing one or more of these functions and connects to the Colonies server to receive assignments. 

Let's submit a **Function Specification** for executing a function named **helloworld**, specifying **helloworld-executor** as the target executor type. Note that a **Function Specification** can be submitted even if there are no matching executors currently available in the colony. These functions will be executed in the future when an executor of matching **executortype** becomes available.

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

The command will block until the process is executed by an executor. First, we need to generate a new ECDSA private key that will be used by the executor. 

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

Assigning functions to executors
--------------------------------
To assign a process to an executor, the command **colonies process assign** can be used and also specify the **helloworld-executor** executor's private key. Typically, the assign operation is carried out by specialized executors developed using the ColonyOS SDKs, rather than through the Colonies CLI. The example below is primarily for educational purposes.

.. code-block:: console

    colonies process assign --prvkey 8c32cdcea68600e05df8661eb0cb6679b9ba1d62c901b2a0a55c2eecd9bbbf58 

.. code-block:: console

    INFO[0000] Assigned process to executor

    ExecutorId=ee58b16a187bb4467437cc068741118bf6ca0ba42e6589c7ea016550ac63e517
    ProcessId=0ddcc0b74ab1ec0cace153432fbf0bb3c7cdd3deffc0d0a69ad1f210f570962c

The Helloworld executor is now assigned to the process, which means that it have exclusive access to it. No other executors can hence be 
assigned this particular process. Only the assigned executor can manipulate the process, such as closint it. 
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

Alternative method to execute a function
----------------------------------------
It is possible to submit a Function Specification without specifying a JSON file, which can be suitable for simpler use cases.

.. code-block:: console

    colonies function exec --func helloworld --targettype helloworld-executor

It also possible to provide *args*. Note that *kwargs* is currently not support by the **exec** function.

.. code-block:: console

    colonies function exec --func fibonacci --args 10 --targettype cli 

Registering a function
----------------------
The primary role of executors is to execute tasks. executors can register a **Function**, thereby indicating to other executors or users their ability to execute a specific function. Importantly, only the executor itself can register Functions to itself; this task cannot be performed by Users or other executors. To register a Function, access to the executor's ECDSA private key is required.

.. code-block:: console

    colonies function register --name helloworld-executor \ 
    --func helloworld \
    --prvkey 8c32cdcea68600e05df8661eb0cb6679b9ba1d62c901b2a0a55c2eecd9bbbf58 

List all registered functions
-----------------------------
To list all registered Function in a colony and get some basic statistics, type: 

.. code-block:: console

    colonies function ls

.. code-block:: console

   ╭────────────────────────────────────╮
   │ Function: helloworld               │
   ├──────────────┬─────────────────────┤
   │ ExecutorType │ helloworld-executor │
   │ FunctionName │ helloworld          │
   │ Call counter │ 0                   │
   │ MinWaitTime  │ 0.000000 s          │
   │ MaxWaitTime  │ 0.000000 s          │
   │ AvgWaitTime  │ 0.000000 s          │
   │ MinExecTime  │ 0.000000 s          │
   │ MaxExecTime  │ 0.000000 s          │
   │ AvgExecTime  │ 0.000000 s          │
   ╰──────────────┴─────────────────────╯

If two or more executors provide the same Function, they will compete for process assignments, effectively load-balancing requests between executors.

Processes
=========

List waiting processes
----------------------

.. code-block:: console

    colonies process psw
    
.. code-block:: console

   ╭────────────┬──────┬────────┬─────────────────────┬─────────────────────┬────────────╮
   │ FUNCNAME   │ ARGS │ KWARGS │ SUBMSSIONTIME       │ EXECUTORTYPE        │ INITIATOR  │
   ├────────────┼──────┼────────┼─────────────────────┼─────────────────────┼────────────┤
   │ helloworld │      │        │ 2024-01-02 18:57:36 │ helloworld-executor │ myexecutor │
   │ helloworld │      │        │ 2024-01-02 18:57:35 │ helloworld-executor │ myexecutor │
   │ helloworld │      │        │ 2024-01-02 18:57:35 │ helloworld-executor │ myexecutor │
   │ helloworld │      │        │ 2024-01-02 18:57:31 │ helloworld-executor │ myexecutor │
   ╰────────────┴──────┴────────┴─────────────────────┴─────────────────────┴────────────╯

Use the flag **-i** to also print process IDs.

.. code-block:: console

    colonies process psw -i

.. code-block:: console

   ╭──────────────────────────────────────────────────────────────────┬────────────┬──────┬────────┬─────────────────────┬─────────────────────┬────────────╮
   │ ID                                                               │ FUNCNAME   │ ARGS │ KWARGS │ SUBMSSIONTIME       │ EXECUTORTYPE        │ INITIATOR  │
   ├──────────────────────────────────────────────────────────────────┼────────────┼──────┼────────┼─────────────────────┼─────────────────────┼────────────┤
   │ 963c37ec90177cd2aae3aac0a5b3fe3ebf494f736fd6225b0d596da0f50850a7 │ helloworld │      │        │ 2024-01-02 18:57:36 │ helloworld-executor │ myexecutor │
   │ 36c1c32f44c477e5958b64b42487708ce3cb091936e4939e8289d3cb3a5b7463 │ helloworld │      │        │ 2024-01-02 18:57:35 │ helloworld-executor │ myexecutor │
   │ def42ec43e4add06bb803bdbd9d572df64eaed4479242093fe7d450b23d41b8f │ helloworld │      │        │ 2024-01-02 18:57:35 │ helloworld-executor │ myexecutor │
   │ e298c2ef655fada6ef752b11370c315bec9667406665475f5d64280889a44114 │ helloworld │      │        │ 2024-01-02 18:57:31 │ helloworld-executor │ myexecutor │
   ╰──────────────────────────────────────────────────────────────────┴────────────┴──────┴────────┴─────────────────────┴─────────────────────┴────────────╯

List running processes
----------------------
.. code-block:: console

    colonies process ps

.. code-block:: console

   ╭────────────┬──────┬────────┬─────────────────────┬─────────────────────┬────────────╮
   │ FUNCNAME   │ ARGS │ KWARGS │ STARTTIME           │ EXECUTORTYPE        │ INITIATOR  │
   ├────────────┼──────┼────────┼─────────────────────┼─────────────────────┼────────────┤
   │ helloworld │      │        │ 2024-01-02 18:59:47 │ helloworld-executor │ myexecutor │
   │ helloworld │      │        │ 2024-01-02 18:59:45 │ helloworld-executor │ myexecutor │
   ╰────────────┴──────┴────────┴─────────────────────┴─────────────────────┴────────────╯

List successful processes
-------------------------

.. code-block:: console

     colonies process pss

.. code-block:: console

   ╭────────────┬──────┬────────┬─────────────────────┬─────────────────────┬────────────╮
   │ FUNCNAME   │ ARGS │ KWARGS │ SUBMSSIONTIME       │ EXECUTORTYPE        │ INITIATOR  │
   ├────────────┼──────┼────────┼─────────────────────┼─────────────────────┼────────────┤
   │ helloworld │      │        │ 2024-01-02 18:57:35 │ helloworld-executor │ myexecutor │
   ╰────────────┴──────┴────────┴─────────────────────┴─────────────────────┴────────────╯

List failed processes
---------------------

.. code-block:: console
    
    colonies process psf

.. code-block:: console

   ╭────────────┬──────┬────────┬─────────────────────┬─────────────────────┬────────────╮
   │ FUNCNAME   │ ARGS │ KWARGS │ ENDTIME             │ EXECUTORTYPE        │ INITIATOR  │
   ├────────────┼──────┼────────┼─────────────────────┼─────────────────────┼────────────┤
   │ helloworld │      │        │ 2024-01-02 19:02:01 │ helloworld-executor │ myexecutor │
   ╰────────────┴──────┴────────┴─────────────────────┴─────────────────────┴────────────╯

Filtering processes
-------------------
It is possible to specify a label when submitting a process. This label can then be used to filter processes. The label can be either be specified in the JSON file or with the **--label** flag when submitting function specifications.

.. code-block:: console

    colonies process ps --func helloworld --label mylabel
    colonies function exec --func fibonacci --args 10 --targettype cli --label mylabel

.. code-block:: console

   {
       "conditions": {
           "executortype": "helloworld-executor"
       },
       "funcname": "helloworld",
           "label": "mylabel"
   }

To filter processes with a specific label, type:

.. code-block:: console

   colonies process ps --label mylabel

It is also possible to filter processes based on initiator name.

.. code-block:: console

   colonies process ps --initiator myuser

Get info about a process
-------------------------

.. code-block:: console

    colonies process get -p e298c2ef655fada6ef752b11370c315bec9667406665475f5d64280889a44114

.. code-block:: console

   ╭───────────────────────────────────────────────────────────────────────────────────────╮
   │ Process                                                                               │
   ├────────────────────┬──────────────────────────────────────────────────────────────────┤
   │ Id                 │ e298c2ef655fada6ef752b11370c315bec9667406665475f5d64280889a44114 │
   │ IsAssigned         │ True                                                             │
   │ InitiatorID        │ 3fc05cf3df4b494e95d6a3d297a34f19938f7daa7422ab0d4f794454133341ac │
   │ Initiator          │ myexecutor                                                       │
   │ AssignedExecutorID │ ee58b16a187bb4467437cc068741118bf6ca0ba42e6589c7ea016550ac63e517 │
   │ AssignedExecutorID │ Failed                                                           │
   │ PriorityTime       │ 1704218251124505155                                              │
   │ SubmissionTime     │ 2024-01-02 18:57:31                                              │
   │ StartTime          │ 2024-01-02 18:57:31                                              │
   │ EndTime            │ 2024-01-02 18:57:31                                              │
   │ WaitDeadline       │ 0001-01-01 00:53:28                                              │
   │ ExecDeadline       │ 0001-01-01 00:53:28                                              │
   │ WaitingTime        │ 2m14.682235s                                                     │
   │ ProcessingTime     │ 2m15.20196s                                                      │
   │ Retries            │ 0                                                                │
   │ Input              │                                                                  │
   │ Output             │                                                                  │
   │ Errors             │ No errors specified                                              │
   ╰────────────────────┴──────────────────────────────────────────────────────────────────╯
   ╭──────────────────────────╮
   │ Function Specification   │
   ├─────────────┬────────────┤
   │ Func        │ helloworld │
   │ Args        │ None       │
   │ KwArgs      │ None       │
   │ MaxWaitTime │ -1         │
   │ MaxExecTime │ -1         │
   │ MaxRetries  │ 0          │
   │ Priority    │ 0          │
   ╰─────────────┴────────────╯
   ╭──────────────────────────╮
   │ Function Specification   │
   ├─────────────┬────────────┤
   │ Func        │ helloworld │
   │ Args        │ None       │
   │ KwArgs      │ None       │
   │ MaxWaitTime │ -1         │
   │ MaxExecTime │ -1         │
   │ MaxRetries  │ 0          │
   │ Priority    │ 0          │
   │ Label       │            │
   ╰─────────────┴────────────╯
   ╭────────────────────────────────────────╮
   │ Conditions                             │
   ├──────────────────┬─────────────────────┤
   │ Colony           │ dev                 │
   │ ExecutorNames    │ None                │
   │ ExecutorType     │ helloworld-executor │
   │ Dependencies     │                     │
   │ Nodes            │ 0                   │
   │ CPU              │ 0m                  │
   │ Memory           │ 0Ki                 │
   │ Processes        │ 0                   │
   │ ProcessesPerNode │ 0                   │
   │ Storage          │ 0Ki                 │
   │ Walltime         │ 0                   │
   │ GPUName          │                     │
   │ GPUs             │ 0                   │
   │ GPUPerNode       │ 0                   │
   │ GPUMemory        │ 0Ki                 │
   ╰──────────────────┴─────────────────────╯
   
No attributes found


Or as JSON instead of tables.

.. code-block:: console 

    colonies process get -p e298c2ef655fada6ef752b11370c315bec9667406665475f5d64280889a44114 --json 

.. code-block:: json

    {
      "processid": "e298c2ef655fada6ef752b11370c315bec9667406665475f5d64280889a44114",
      "initiatorid": "3fc05cf3df4b494e95d6a3d297a34f19938f7daa7422ab0d4f794454133341ac",
      "initiatorname": "myexecutor",
      "assignedexecutorid": "ee58b16a187bb4467437cc068741118bf6ca0ba42e6589c7ea016550ac63e517",
      "isassigned": true,
      "state": 3,
      "prioritytime": 1704218251124505155,
      "submissiontime": "2024-01-02T18:57:31.124505+01:00",
      "starttime": "2024-01-02T18:59:45.80674+01:00",
      "endtime": "2024-01-02T19:02:01.0087+01:00",
      "waitdeadline": "0001-01-01T00:53:28+00:53",
      "execdeadline": "0001-01-01T00:53:28+00:53",
      "retries": 0,
      "attributes": [],
      "spec": {
        "nodename": "",
        "funcname": "helloworld",
        "args": [],
        "kwargs": {},
        "priority": 0,
        "maxwaittime": -1,
        "maxexectime": -1,
        "maxretries": 0,
        "conditions": {
          "colonyname": "dev",
          "executorNames": null,
          "executortype": "helloworld-executor",
          "dependencies": [],
          "nodes": 0,
          "cpu": "0m",
          "processes": 0,
          "processespernode": 0,
          "mem": "0Ki",
          "storage": "0Ki",
          "gpu": {
            "name": "",
            "mem": "0Ki",
            "count": 0,
            "nodecount": 0
          },
          "walltime": 0
        },
        "label": "",
        "fs": {
          "mount": "",
          "snapshots": null,
          "dirs": null
        },
        "env": {}
      },
      "waitforparents": false,
      "parents": [],
      "children": [],
      "processgraphid": "",
      "in": [],
      "out": [],
      "errors": [
        "No errors specified"
      ]
    }

Remove a process
----------------

.. code-block:: console

    colonies process remove -p  0bcca3064a6619f91770b9e49c77f7537020a63d7c5b5d693756a2231aa2ad72

.. code-block:: console

    INFO[0000] Process removed

    ProcessId=0bcca3064a6619f91770b9e49c77f7537020a63d7c5b5d693756a2231aa2ad72

Note that it is not possible to remove a process if it is part of a workflows.

Remove all processes
--------------------

.. code-block:: console

    colonies process removeall

.. code-block:: console

    WARNING!!! Are you sure you want to remove all all processes from Colony <dev>. 
    This operation cannot be undone! (YES,no): YES

    INFO[0002] Removing all processes in Colony

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
Colonies provides logging functionality, allowing executors to add log messages to processes, e.g. stdout ot stderr logs. These logs are stored in a PostgreSQL database. If TimescaleDB is used, the logs will be stored in a timeseries hypertable; otherwise, they will be indexed and stored in a regular PostgreSQL table. If retention is enabled, log data will be automatically purged upon reaching its expiration date. This automated process ensures that logs are systematically removed from the server once they become outdated.

Adding logs to a process
------------------------
Let's demonstrate how to manage logs using the Colonies CLI. First submit a process.

.. code-block:: console

    colonies function submit --spec ./examples/functions/helloworld.json

To add a log to a process, the process be running and be assigned to an executor. Only the assigned executor can add logs. 
Let's register an executor and assign the process we just submitted.

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

Now, the process is assign to the executor. Let's add log to it.

.. code-block:: console

    colonies log add -p 511c09528b01a26d95bc4ed0899c65f2b95732aadb1221bd42d1c1e17d9daa34 \
    -m "helloworld" \
    --prvkey 8c32cdcea68600e05df8661eb0cb6679b9ba1d62c901b2a0a55c2eecd9bbbf58

.. code-block:: console

    INFO[0000] Adding log

    LogMsg=helloworld ProcessID=511c09528b01a26d95bc4ed0899c65f2b95732aadb1221bd42d1c1e17d9daa34

Getting logs
------------

.. code-block:: console
   
    colonies log get -p 511c09528b01a26d95bc4ed0899c65f2b95732aadb1221bd42d1c1e17d9daa34

.. code-block:: console

    helloworld

It is possible to use flag **-follow** to follow a process and print all logs until the process is concludes. 

Searching logs
--------------
It is possible to search logs based on a text string. The example below searches for logs containing the string *ERROR*.

.. code-block:: console

    colonies log search --text ERROR

.. code-block:: console

    INFO[0000] Searching for logs
    Count=100 Days=1 Text=ERROR

   ╭──────────────┬──────────────────────────────────────────────────────────────────╮
   │ Timestamp    │ 2024-01-06 13:45:38                                              │
   │ ExecutorName │ helloworld-executor                                              │
   │ ProcessID    │ 0bb15f66e85d729b63d8bcd53f79c19e25ba15fce1b631bb559f560f9c724c51 │
   │ Text         │ ERROR: failed to say hello                                       │
   ╰──────────────┴──────────────────────────────────────────────────────────────────╯
   ╭──────────────┬──────────────────────────────────────────────────────────────────╮
   │ Timestamp    │ 2024-01-06 13:40:00                                              │
   │ ExecutorName │ helloworld-executor                                              │
   │ ProcessID    │ 0bb15f66e85d729b63d8bcd53f79c19e25ba15fce1b631bb559f560f9c724c51 │
   │ Text         │ ERROR: failed to print                                           │
   ╰──────────────┴──────────────────────────────────────────────────────────────────╯

The example above only searches for logs from the last 24 hours. To search for logs from the last 7 days, type:

.. code-block:: console

    colonies log search --text ERROR --days 7

Only show the last error log:

.. code-block:: console

    colonies log search --text ERROR --days 7 --print 

It is also possible print the logs and surrounding logs. The example below prints the last error log and 
the surrounding logs 1 second before the error string occured.

.. code-block:: console

   INFO[0000] Searching for logs
   Count=100 Days=1 
   Seconds=1 Text=ERROR

   Timestamp: 2024-01-06 13:45:38
   ProcessID: 0bb15f66e85d729b63d8bcd53f79c19e25ba15fce1b631bb559f560f9c724c51
   
   ExecutorName: helloworld-executor
   =================== LOGS ====================
   helloworld
   ERROR: failed to print 
   ================= END LOGS ==================
   
   Timestamp: 2024-01-06 13:40:00
   ProcessID: 0bb15f66e85d729b63d8bcd53f79c19e25ba15fce1b631bb559f560f9c724c51
   ExecutorName: helloworld-executor
   =================== LOGS ====================
   helloworld
   ERROR: failed to print 
   ================= END LOGS ==================

The print logs 10 seconds before the error string occured, type:

.. code-block:: console

    colonies log search --text ERROR --days 7 --print --seconds 10

Limit number of logs to print to 2, type:

.. code-block:: console

    colonies log search --text ERROR --days 7 --print --seconds 10 --count 2

Attributes
==========
Attributes are key-value pairs that can be used to store arbitrary data associated with a process. Attributes can be used to store information about the process, such as the input and output data, or to store information about the process execution. Attributes can only be added to process by an executor that has been assigned to that process. Let's submit a process and assign it to demonstrate how to use attributes.

Submit and assign a process
---------------------------

.. code-block:: console

    colonies function submit --spec ./examples/functions/helloworld.json

.. code-block:: console

    INFO[0000] Process submitted  
    ProcessId=462e4cee0641972a1e9f7d7303843606dfce8a8716612df6e7e458e1b327d234

.. code-block:: console

    colonies process assign --prvkey 8c32cdcea68600e05df8661eb0cb6679b9ba1d62c901b2a0a55c2eecd9bbbf58

.. code-block:: console

    INFO[0000] Assigned process to executor
    ExecutorId=ee58b16a187bb4467437cc068741118bf6ca0ba42e6589c7ea016550ac63e517
    ProcessId=def42ec43e4add06bb803bdbd9d572df64eaed4479242093fe7d450b23d41b8f

Add an attribute to a process
-----------------------------

.. code-block:: console

   colonies attribute add --key mykey \
                          --value myvalue \
                          -p def42ec43e4add06bb803bdbd9d572df64eaed4479242093fe7d450b23d41b8f \
                          --prvkey 8c32cdcea68600e05df8661eb0cb6679b9ba1d62c901b2a0a55c2eecd9bbbf58


    colonies attribute add --key mykey \
   --value myvalue \
   -p 2dfc4d9348624f750151ad1eed24941676c30915d92af96c62bac155609c38c1 \
   --prvkey 8c32cdcea68600e05df8661eb0cb6679b9ba1d62c901b2a0a55c2eecd9bbbf58

.. code-block:: console

   INFO[0000] Attribute added
   AttributeID=1c07e06dde48b7a25a44ed644ccfcc883fed420b0fdc1207f4e829e1617571db

Lookup an attribute on a process
--------------------------------

.. code-block:: console

    colonies attribute get --attributeid 1c07e06dde48b7a25a44ed644ccfcc883fed420b0fdc1207f4e829e1617571db

.. code-block:: console

   ╭──────────────────────────────────────────────────────────────────┬──────────────────────────────────────────────────────────────────┬───────┬─────────┬──────╮
   │ ATTRIBUTEID                                                      │ TARGETID                                                         │ KEY   │ VALUE   │ TYPE │
   ├──────────────────────────────────────────────────────────────────┼──────────────────────────────────────────────────────────────────┼───────┼─────────┼──────┤
   │ 1c07e06dde48b7a25a44ed644ccfcc883fed420b0fdc1207f4e829e1617571db │ def42ec43e4add06bb803bdbd9d572df64eaed4479242093fe7d450b23d41b8f │ mykey │ myvalue │ Out  │
   ╰──────────────────────────────────────────────────────────────────┴──────────────────────────────────────────────────────────────────┴───────┴─────────┴──────╯

Attributes can also viewed by looking up a process.

.. code-block:: console

    colonies process get -p def42ec43e4add06bb803bdbd9d572df64eaed4479242093fe7d450b23d41b8f 

.. code-block:: console

   ╭───────────────────────────────────────────────────────────────────────────────────────╮
   │ Process                                                                               │
   ├────────────────────┬──────────────────────────────────────────────────────────────────┤
   │ Id                 │ def42ec43e4add06bb803bdbd9d572df64eaed4479242093fe7d450b23d41b8f │
   │ IsAssigned         │ True                                                             │
   │ InitiatorID        │ 3fc05cf3df4b494e95d6a3d297a34f19938f7daa7422ab0d4f794454133341ac │
   │ Initiator          │ myexecutor                                                       │
   │ AssignedExecutorID │ ee58b16a187bb4467437cc068741118bf6ca0ba42e6589c7ea016550ac63e517 │
   │ AssignedExecutorID │ Running                                                          │
   │ PriorityTime       │ 1704218255844915410                                              │
   │ SubmissionTime     │ 2024-01-02 18:57:35                                              │
   │ StartTime          │ 2024-01-02 18:57:35                                              │
   │ EndTime            │ 2024-01-02 18:57:35                                              │
   │ WaitDeadline       │ 0001-01-01 00:53:28                                              │
   │ ExecDeadline       │ 0001-01-01 00:53:28                                              │
   │ WaitingTime        │ 10m51.249646s                                                    │
   │ ProcessingTime     │ 10m45.301510243s                                                 │
   │ Retries            │ 0                                                                │
   │ Input              │                                                                  │
   │ Output             │                                                                  │
   │ Errors             │                                                                  │
   ╰────────────────────┴──────────────────────────────────────────────────────────────────╯
   ╭──────────────────────────╮
   │ Function Specification   │
   ├─────────────┬────────────┤
   │ Func        │ helloworld │
   │ Args        │ None       │
   │ KwArgs      │ None       │
   │ MaxWaitTime │ -1         │
   │ MaxExecTime │ -1         │
   │ MaxRetries  │ 0          │
   │ Priority    │ 0          │
   ╰─────────────┴────────────╯
   ╭──────────────────────────╮
   │ Function Specification   │
   ├─────────────┬────────────┤
   │ Func        │ helloworld │
   │ Args        │ None       │
   │ KwArgs      │ None       │
   │ MaxWaitTime │ -1         │
   │ MaxExecTime │ -1         │
   │ MaxRetries  │ 0          │
   │ Priority    │ 0          │
   │ Label       │            │
   ╰─────────────┴────────────╯
   ╭────────────────────────────────────────╮
   │ Conditions                             │
   ├──────────────────┬─────────────────────┤
   │ Colony           │ dev                 │
   │ ExecutorNames    │ None                │
   │ ExecutorType     │ helloworld-executor │
   │ Dependencies     │                     │
   │ Nodes            │ 0                   │
   │ CPU              │ 0m                  │
   │ Memory           │ 0Ki                 │
   │ Processes        │ 0                   │
   │ ProcessesPerNode │ 0                   │
   │ Storage          │ 0Ki                 │
   │ Walltime         │ 0                   │
   │ GPUName          │                     │
   │ GPUs             │ 0                   │
   │ GPUPerNode       │ 0                   │
   │ GPUMemory        │ 0Ki                 │
   ╰──────────────────┴─────────────────────╯
   ╭──────────────────────────────────────────────────────────────────────────────────────────╮
   │ Attributes                                                                               │
   ├──────────────────────────────────────────────────────────────────┬───────┬─────────┬─────┤
   │ ATTRIBUTEID                                                      │ KEY   │ TYPE    │     │
   ├──────────────────────────────────────────────────────────────────┼───────┼─────────┼─────┤
   │ 1c07e06dde48b7a25a44ed644ccfcc883fed420b0fdc1207f4e829e1617571db │ mykey │ myvalue │ Out │
   ╰──────────────────────────────────────────────────────────────────┴───────┴─────────┴─────╯

Workflows
=========

Submit a workflow
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
    WorkflowID=dacee3f1a54074f77e1682df20cedcae550a796ff831123793e491d7acfcfaf7

.. code-block:: console 

    colonies process psw

.. code-block:: console 

   ╭────────────┬────────┬────────┬─────────────────────┬─────────────────────┬────────────╮
   │ FUNCNAME   │ ARGS   │ KWARGS │ SUBMSSIONTIME       │ EXECUTORTYPE        │ INITIATOR  │
   ├────────────┼────────┼────────┼─────────────────────┼─────────────────────┼────────────┤
   │ echo       │ task_b │        │ 2024-01-02 19:21:43 │ cli                 │ myexecutor │
   │ echo       │ task_c │        │ 2024-01-02 19:21:43 │ cli                 │ myexecutor │
   │ echo       │ task_d │        │ 2024-01-02 19:21:43 │ cli                 │ myexecutor │
   │ echo       │ task_a │        │ 2024-01-02 19:21:43 │ cli                 │ myexecutor │
   ╰────────────┴────────┴────────┴─────────────────────┴─────────────────────┴────────────╯


List waiting workflows
----------------------

.. code-block:: console 

    colonies workflow psw

.. code-block:: console 

   ╭──────────────────────────────────────────────────────────────────┬─────────────────────┬────────────╮
   │ WORKFLOWID                                                       │ SUBMSSIONTIME       │ INITIATOR  │
   ├──────────────────────────────────────────────────────────────────┼─────────────────────┼────────────┤
   │ dacee3f1a54074f77e1682df20cedcae550a796ff831123793e491d7acfcfaf7 │ 2024-01-02 19:21:43 │ myexecutor │
   ╰──────────────────────────────────────────────────────────────────┴─────────────────────┴────────────╯

List running workflows
----------------------

.. code-block:: console 

    colonies workflow ps

.. code-block:: console

    WARN[0000] No running workflows found

List successful workflows
-------------------------

.. code-block:: console 

    colonies workflow pss

.. code-block:: console

    WARN[0000] No successful workflows found

List failed workflows
---------------------

.. code-block:: console 

    colonies workflow psf

.. code-block:: console

    WARN[0000] No successful workflows found


Get info about a workflow
-------------------------

.. code-block:: console

    colonies workflow get --workflowid dacee3f1a54074f77e1682df20cedcae550a796ff831123793e491d7acfcfaf7

.. code-block:: console

   ╭───────────────────────────────────────────────────────────────────────────────────╮
   │ Workflow                                                                          │
   ├────────────────┬──────────────────────────────────────────────────────────────────┤
   │ WorkflowId     │ dacee3f1a54074f77e1682df20cedcae550a796ff831123793e491d7acfcfaf7 │
   │ SubmissionTime │ 2024-01-02 19:21:43                                              │
   │ State          │ Waiting                                                          │
   │ StartTime      │ 0001-01-01 00:53:28                                              │
   │ InitiatorName  │ myexecutor                                                       │
   │ InitiatorId    │ 3fc05cf3df4b494e95d6a3d297a34f19938f7daa7422ab0d4f794454133341ac │
   │ EndTime        │ 0001-01-01 00:53:28                                              │
   ╰────────────────┴──────────────────────────────────────────────────────────────────╯
   
   Processes:
   ╭───────────────────┬──────────────────────────────────────────────────────────────────╮
   │ WaitingForParents │ false                                                            │
   │ State             │ Waiting                                                          │
   │ ProcessId         │ 025ee8eaf3fd031b7188687f8ef877b10f2b803036d9c1aea524769d2fe94ade │
   │ NodeName          │ task_a                                                           │
   │ KwArgs            │ None                                                             │
   │ InitiatorId       │ 3fc05cf3df4b494e95d6a3d297a34f19938f7daa7422ab0d4f794454133341ac │
   │ Initiator         │ myexecutor                                                       │
   │ FuncName          │ echo                                                             │
   │ ExecutorType      │ cli                                                              │
   │ Dependencies      │ None                                                             │
   │ Args              │ task_a                                                           │
   ╰───────────────────┴──────────────────────────────────────────────────────────────────╯
   ╭───────────────────┬──────────────────────────────────────────────────────────────────╮
   │ WaitingForParents │ true                                                             │
   │ State             │ Waiting                                                          │
   │ ProcessId         │ 323082529ba9d3c1888fc049ca6757a2093828b5e42c946e4c8204d4bc4b4973 │
   │ NodeName          │ task_b                                                           │
   │ KwArgs            │ None                                                             │
   │ InitiatorId       │ 3fc05cf3df4b494e95d6a3d297a34f19938f7daa7422ab0d4f794454133341ac │
   │ Initiator         │ myexecutor                                                       │
   │ FuncName          │ echo                                                             │
   │ ExecutorType      │ cli                                                              │
   │ Dependencies      │ task_a                                                           │
   │ Args              │ task_b                                                           │
   ╰───────────────────┴──────────────────────────────────────────────────────────────────╯
   ╭───────────────────┬──────────────────────────────────────────────────────────────────╮
   │ WaitingForParents │ true                                                             │
   │ State             │ Waiting                                                          │
   │ ProcessId         │ b3be222780252f9f334671b8f35a90b7267ff34878cee054cd084fad9e916853 │
   │ NodeName          │ task_d                                                           │
   │ KwArgs            │ None                                                             │
   │ InitiatorId       │ 3fc05cf3df4b494e95d6a3d297a34f19938f7daa7422ab0d4f794454133341ac │
   │ Initiator         │ myexecutor                                                       │
   │ FuncName          │ echo                                                             │
   │ ExecutorType      │ cli                                                              │
   │ Dependencies      │ task_b task_c                                                    │
   │ Args              │ task_d                                                           │
   ╰───────────────────┴──────────────────────────────────────────────────────────────────╯
   ╭───────────────────┬──────────────────────────────────────────────────────────────────╮
   │ WaitingForParents │ true                                                             │
   │ State             │ Waiting                                                          │
   │ ProcessId         │ 45b650eb0dd8e61e1ad338f40efe4734dd3d2cf3ad5b179f33f9dce4ddf3ec99 │
   │ NodeName          │ task_c                                                           │
   │ KwArgs            │ None                                                             │
   │ InitiatorId       │ 3fc05cf3df4b494e95d6a3d297a34f19938f7daa7422ab0d4f794454133341ac │
   │ Initiator         │ myexecutor                                                       │
   │ FuncName          │ echo                                                             │
   │ ExecutorType      │ cli                                                              │
   │ Dependencies      │ task_a                                                           │
   │ Args              │ task_c                                                           │
   ╰───────────────────┴──────────────────────────────────────────────────────────────────╯

Remove a workflow
-----------------
.. code-block:: text
    
    colonies workflow remove --workflowid 7fee39395bc839168efff707ed5ed23dcf713c7a87cb9e3f2e679f24bc3b79e3

.. code-block:: text

    INFO[0000] Workflow removed

    WorkflowID=7fee39395bc839168efff707ed5ed23dcf713c7a87cb9e3f2e679f24bc3b79e3

Remove all workflows
--------------------

.. code-block:: console

    colonies workflow removeall

.. code-block:: console

    WARNING!!! Are you sure you want to remove all workflows in the Colony <dev>. This operation cannot be undone! (YES,no):
    This operation cannot be undone! (YES,no): YES

    INFO[0002] Removing all workflows in Colony

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

Add a cron
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

We can now see that new processes starting to appear every 5 seconds. Use the flag **--waitprevious** to only spawn a new process if the current 
process in the queue has finised or failed.

.. code-block:: console

    colonies process psw

.. code-block:: console

   ╭────────────┬────────────────────┬────────┬─────────────────────┬─────────────────────┬────────────╮
   │ FUNCNAME   │ ARGS               │ KWARGS │ SUBMSSIONTIME       │ EXECUTORTYPE        │ INITIATOR  │
   ├────────────┼────────────────────┼────────┼─────────────────────┼─────────────────────┼────────────┤
   │ cat        │ /tmp/currentdate   │        │ 2024-01-02 19:25:55 │ cli                 │ myexecutor │
   │ date       │ > /tmp/currentdate │        │ 2024-01-02 19:25:55 │ cli                 │ myexecutor │
   │ cat        │ /tmp/currentdate   │        │ 2024-01-02 19:25:50 │ cli                 │ myexecutor │
   │ date       │ > /tmp/currentdate │        │ 2024-01-02 19:25:50 │ cli                 │ myexecutor │
   │ date       │ > /tmp/currentdate │        │ 2024-01-02 19:25:45 │ cli                 │ myexecutor │
   │ cat        │ /tmp/currentdate   │        │ 2024-01-02 19:25:45 │ cli                 │ myexecutor │
   │ date       │ > /tmp/currentdate │        │ 2024-01-02 19:25:40 │ cli                 │ myexecutor │
   │ cat        │ /tmp/currentdate   │        │ 2024-01-02 19:25:40 │ cli                 │ myexecutor │
   ╰────────────┴────────────────────┴────────┴─────────────────────┴─────────────────────┴────────────╯

Listing crons
-------------
.. code-block:: console

    colonies cron ls

.. code-block:: console

   ╭──────────────────────────────────────────────────────────────────┬──────────────┬────────────╮
   │ CRONID                                                           │ NAME         │ INITIATOR  │
   ├──────────────────────────────────────────────────────────────────┼──────────────┼────────────┤
   │ 9005f3f4ea3ce9ff6fdd1aae14e67f6c6c02a72a280c2b359e28bdc3f23c7ee7 │ example_cron │ myexecutor │
   ╰──────────────────────────────────────────────────────────────────┴──────────────┴────────────╯

Getting info about a cron
-------------------------
.. code-block:: console

    colonies cron get --cronid 9005f3f4ea3ce9ff6fdd1aae14e67f6c6c02a72a280c2b359e28bdc3f23c7ee7

.. code-block:: console

   ╭────────────────────────────────────────────────────────────────────────────────────────────╮
   │ Cron                                                                                       │
   ├─────────────────────────┬──────────────────────────────────────────────────────────────────┤
   │ CronId                  │ 9005f3f4ea3ce9ff6fdd1aae14e67f6c6c02a72a280c2b359e28bdc3f23c7ee7 │
   │ Name                    │ example_cron                                                     │
   │ Colony                  │ dev                                                              │
   │ InitiatorID             │ 3fc05cf3df4b494e95d6a3d297a34f19938f7daa7422ab0d4f794454133341ac │
   │ Initiator               │ myexecutor                                                       │
   │ Cron Expression         │ 0/5 * * * * *                                                    │
   │ Interval                │ -1                                                               │
   │ Random                  │ false                                                            │
   │ NextRun                 │ 2024-01-02 19:27:35                                              │
   │ LastRun                 │ 2024-01-02 19:27:35                                              │
   │ PrevProcessGraphID      │ 8638064388532a2e2a082e4200579990b66243d01f2d32769e9fbc70ef206427 │
   │ WaitForPrevProcessGraph │ false                                                            │
   │ CheckerPeriod           │ 1000                                                             │
   ╰─────────────────────────┴──────────────────────────────────────────────────────────────────╯
   ╭───────────────────────────────────╮
   │ Function Specification            │
   ├─────────────┬─────────────────────┤
   │ Func        │ date                │
   │ Args        │ > /tmp/currentdate  │
   │ KwArgs      │ None                │
   │ MaxWaitTime │ 0                   │
   │ MaxExecTime │ 0                   │
   │ MaxRetries  │ 0                   │
   │ Priority    │ 0                   │
   ╰─────────────┴─────────────────────╯
   ╭───────────────────────────────────╮
   │ Function Specification            │
   ├─────────────┬─────────────────────┤
   │ Func        │ date                │
   │ Args        │ > /tmp/currentdate  │
   │ KwArgs      │ None                │
   │ MaxWaitTime │ 0                   │
   │ MaxExecTime │ 0                   │
   │ MaxRetries  │ 0                   │
   │ Priority    │ 0                   │
   │ Label       │                     │
   ╰─────────────┴─────────────────────╯
   ╭─────────────────────────────────╮
   │ Function Specification          │
   ├─────────────┬───────────────────┤
   │ Func        │ cat               │
   │ Args        │ /tmp/currentdate  │
   │ KwArgs      │ None              │
   │ MaxWaitTime │ 0                 │
   │ MaxExecTime │ 0                 │
   │ MaxRetries  │ 0                 │
   │ Priority    │ 0                 │
   ╰─────────────┴───────────────────╯
   ╭─────────────────────────────────╮
   │ Function Specification          │
   ├─────────────┬───────────────────┤
   │ Func        │ cat               │
   │ Args        │ /tmp/currentdate  │
   │ KwArgs      │ None              │
   │ MaxWaitTime │ 0                 │
   │ MaxExecTime │ 0                 │
   │ MaxRetries  │ 0                 │
   │ Priority    │ 0                 │
   │ Label       │                   │
   ╰─────────────┴───────────────────╯

Immediately run a cron
----------------------

.. code-block:: console

    colonies cron run --cronid 180828461a5b76b4e82b1cb5275a5995f7a690cd2ba4e07566b9e438e0cfdb3d

.. code-block:: console

    INFO[0000] Running cron

    CronID=180828461a5b76b4e82b1cb5275a5995f7a690cd2ba4e07566b9e438e0cfdb3d

Use interval instead of a cron expressions
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


Remove a cron
-------------

.. code-block:: console

    colonies cron remove --cronid e5e25a98305ac11ff9292b584d4b119a48c99c5fa599d43e63cd9d57c53927d8

.. code-block:: console

    INFO[0000] Removing cron

    CronId=e5e25a98305ac11ff9292b584d4b119a48c99c5fa599d43e63cd9d57c53927d8

Generators
==========
Generators is a built-in feature in ColonyOS, which spawns workflows when certain conditions are met. Generators automatically spawn workflows when number of **pack** calls exceeds a threshold. Pack **data** is then available as an argument to the process.

Add a generator
---------------

.. code-block:: console

    colonies generator add --spec ./examples/generators/generator_workflow.json --name testgenerator --trigger 5

.. code-block:: console

    INFO[0000] Generator added

    GeneratorID=112480ed9cfc25f69ad87009d38946fd024c8d0320f7295838eda368e0cb7880 
    GeneratorName=testgenerator 
    Timeout=-1 
    Trigger=5

Send data to a generator
------------------------
After 5 pack calls, the **Generator** should generate a workflow.

.. code-block:: console

    colonies generator pack --arg hello1 --generatorid 112480ed9cfc25f69ad87009d38946fd024c8d0320f7295838eda368e0cb7880

Make 4 more pack calls.

.. code-block:: console

    colonies generator pack --arg hello2 --generatorid 3ac74d27f132007d5fb2e7cbedb279f26c78f0c9df98ba73357c0efe91c9c1ef;
    colonies generator pack --arg hello3 --generatorid 3ac74d27f132007d5fb2e7cbedb279f26c78f0c9df98ba73357c0efe91c9c1ef;
    colonies generator pack --arg hello4 --generatorid 3ac74d27f132007d5fb2e7cbedb279f26c78f0c9df98ba73357c0efe91c9c1ef;
    colonies generator pack --arg hello5 --generatorid 3ac74d27f132007d5fb2e7cbedb279f26c78f0c9df98ba73357c0efe91c9c1ef;

Let's see if a workflow was created.

.. code-block:: console

    colonies workflow psw

.. code-block:: console

   ╭──────────────────────────────────────────────────────────────────┬─────────────────────┬────────────╮
   │ WORKFLOWID                                                       │ SUBMSSIONTIME       │ INITIATOR  │
   ├──────────────────────────────────────────────────────────────────┼─────────────────────┼────────────┤
   │ b1706752af86ad9da0ea5cd194009188c8391a837fbb80e478e8df672742230b │ 2024-01-02 19:31:23 │ myexecutor │
   ╰──────────────────────────────────────────────────────────────────┴─────────────────────┴────────────╯

Let's lookup the Workflow to see if the data is there.

.. code-block:: console

    colonies workflow get --workflowid b1706752af86ad9da0ea5cd194009188c8391a837fbb80e478e8df672742230b 

.. code-block:: console

   ╭───────────────────────────────────────────────────────────────────────────────────╮
   │ Workflow                                                                          │
   ├────────────────┬──────────────────────────────────────────────────────────────────┤
   │ WorkflowId     │ b1706752af86ad9da0ea5cd194009188c8391a837fbb80e478e8df672742230b │
   │ SubmissionTime │ 2024-01-02 19:31:23                                              │
   │ State          │ Waiting                                                          │
   │ StartTime      │ 0001-01-01 00:53:28                                              │
   │ InitiatorName  │ myexecutor                                                       │
   │ InitiatorId    │ 3fc05cf3df4b494e95d6a3d297a34f19938f7daa7422ab0d4f794454133341ac │
   │ EndTime        │ 0001-01-01 00:53:28                                              │
   ╰────────────────┴──────────────────────────────────────────────────────────────────╯
   
   Processes:
   ╭───────────────────┬──────────────────────────────────────────────────────────────────╮
   │ WaitingForParents │ false                                                            │
   │ State             │ Waiting                                                          │
   │ ProcessId         │ 3fc9d091bd6684c86215d4284dce1836cb477eab4a7a44771ae4d008ff3d3e4f │
   │ NodeName          │ generator_example                                                │
   │ KwArgs            │ None                                                             │
   │ InitiatorId       │ 3fc05cf3df4b494e95d6a3d297a34f19938f7daa7422ab0d4f794454133341ac │
   │ Initiator         │ myexecutor                                                       │
   │ FuncName          │ echo                                                             │
   │ ExecutorType      │ cli                                                              │
   │ Dependencies      │ None                                                             │
   │ Args              │ hello3 hello4 hello5 hello2 hello3                               │
   ╰───────────────────┴──────────────────────────────────────────────────────────────────╯

List generators
---------------
.. code-block:: console

    colonies generator ls

.. code-block:: console

   ╭──────────────────────────────────────────────────────────────────┬───────────────┬────────────╮
   │ GENERATORID                                                      │ NAME          │ INITIATOR  │
   ├──────────────────────────────────────────────────────────────────┼───────────────┼────────────┤
   │ 3ac74d27f132007d5fb2e7cbedb279f26c78f0c9df98ba73357c0efe91c9c1ef │ testgenerator │ myexecutor │
   ╰──────────────────────────────────────────────────────────────────┴───────────────┴────────────╯

Get info about a generator
--------------------------
.. code-block:: console

    colonies generator get --generatorid 3ac74d27f132007d5fb2e7cbedb279f26c78f0c9df98ba73357c0efe91c9c1ef

.. code-block:: console

   ╭───────────────┬──────────────────────────────────────────────────────────────────╮
   │ Trigger       │ 5                                                                │
   │ Timeout       │ -1                                                               │
   │ QueueSize     │ 2                                                                │
   │ Name          │ testgenerator                                                    │
   │ Lastrun       │ 2024-01-02 19:31:23                                              │
   │ Initiator     │ myexecutor                                                       │
   │ GeneratorId   │ 3ac74d27f132007d5fb2e7cbedb279f26c78f0c9df98ba73357c0efe91c9c1ef │
   │ Colony        │ dev                                                              │
   │ CheckerPeriod │ 1000                                                             │
   ╰───────────────┴──────────────────────────────────────────────────────────────────╯
   ╭────────────────────╮
   │ Function Specifica │
   │ tion               │
   ├─────────────┬──────┤
   │ Func        │ echo │
   │ Args        │ None │
   │ KwArgs      │ None │
   │ MaxWaitTime │ 0    │
   │ MaxExecTime │ 0    │
   │ MaxRetries  │ 0    │
   │ Priority    │ 0    │
   ╰─────────────┴──────╯
   ╭────────────────────╮
   │ Function Specifica │
   │ tion               │
   ├─────────────┬──────┤
   │ Func        │ echo │
   │ Args        │ None │
   │ KwArgs      │ None │
   │ MaxWaitTime │ 0    │
   │ MaxExecTime │ 0    │
   │ MaxRetries  │ 0    │
   │ Priority    │ 0    │
   │ Label       │      │
   ╰─────────────┴──────╯

Remove a generator
------------------

.. code-block:: console

    colonies generator remove --generatorid 112480ed9cfc25f69ad87009d38946fd024c8d0320f7295838eda368e0cb7880

.. code-block:: console

    INFO[0000] Removing generator

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

    mkdir myfiles; 
    cd myfiles;
    echo "Hello" > hello.txt;
    mkdir subdir;
    cd subdir;
    echo "Hello 2" > hello2.txt;
    cd ../..

Now, upload the *myfiles* directory and all its sub-directories to CFS under the **label** *myfiles*.

.. code-block:: console 

    colonies fs sync -l myfiles -d myfiles --yes

.. code-block:: console 

   INFO[0000] Calculating sync plans
   Analyzing /home/johan/dev/github/colony~ ... done!
   Analyzing /home/johan/dev/github/colony~ ... done!
   Uploading /myfiles/subdir                ... done! [8B]
   Uploading /myfiles                       ... done! [6B]

We can not download the files from another computer, or just to another directory (*myfiles2*).

.. code-block:: console 

    colonies fs sync -l myfiles -d myfiles2 --yes

.. code-block:: console 

   INFO[0000] Calculating sync plans
   Downloading /home/johan/dev/github/colo~ ... done! [8B]
   Downloading /home/johan/dev/github/colo~ ... done! [6B]

Use the flag **--keeplocal=true** to prevent the CLI from overwriting local files in case the files have changed remotely.

List all labels
---------------
To list all labels, type:

.. code-block:: console 

    colonies fs label ls

.. code-block:: console 

   ╭─────────────────┬───────╮
   │ LABEL           │ FILES │
   ├─────────────────┼───────┤
   │ /myfiles/subdir │ 1     │
   │ /myfiles        │ 1     │
   ╰─────────────────┴───────╯

.. code-block:: console 

    colonies fs ls -l /myfiles

List files in a label
---------------------

.. code-block:: console 

    colonies fs ls -l /myfiles

.. code-block:: console 

   ╭───────────┬───────┬──────────────────────────────────────────────────────────────────┬─────────────────────┬───────────╮
   │ FILENAME  │ SIZE  │ LATEST ID                                                        │ ADDED               │ REVISIONS │
   ├───────────┼───────┼──────────────────────────────────────────────────────────────────┼─────────────────────┼───────────┤
   │ hello.txt │ 0 KiB │ e7036e2a0da8bc9d4e8cb260e340e21ff2c493122561fd05f7fb76da4b191284 │ 2024-01-02 19:38:55 │ 1         │
   ╰───────────┴───────┴──────────────────────────────────────────────────────────────────┴─────────────────────┴───────────╯

Get info about a file
---------------------

.. code-block:: console 

    colonies fs info -l /myfiles -n hello.txt

.. code-block:: console 

   ╭─────────────────┬──────────────────────────────────────────────────────────────────╮
   │ Filename        │ hello.txt                                                        │
   │ FileId          │ e7036e2a0da8bc9d4e8cb260e340e21ff2c493122561fd05f7fb76da4b191284 │
   │ Added           │ 2024-01-02 19:38:55                                              │
   │ Sequence Number │ 2                                                                │
   │ Label           │ /myfiles                                                         │
   │ Colony          │ dev                                                              │
   │ Size            │ 0 KiB                                                            │
   │ Checksum        │ 66a045b452102c59d840ec097d59d9467e13a3f34f6494e539ffd32c1bb35f18 │
   │ Checksum Alg    │ SHA256                                                           │
   │ Protocol        │ s3                                                               │
   │ S3 Endpoint     │ localhost:9000                                                   │
   │ S3 TLS          │ false                                                            │
   │ S3 Region       │                                                                  │
   │ S3 Bucket       │ colonies-prod                                                    │
   │ S3 Object       │ e85385e4fcc4a4de5ff6d9921288df79d53ed33ebfde9625ec65bc234bc70b8b │
   │ S3 Accesskey    │ ******************************                                   │
   │ S3 Secretkey    │ ******************************                                   │
   │ Encryption Key  │ ******************************                                   │
   │ Encryption Alg  │                                                                  │
   ╰─────────────────┴──────────────────────────────────────────────────────────────────╯

Download a specific file
------------------------
To download a specific File in a specific Label to a directory *newdir*:

.. code-block:: console 

    colonies fs get -l /myfiles -n hello.txt -d newdir

.. code-block:: console 

   Downloading hello.txt                    ... done! [6B]

Remove a file
-------------

.. code-block:: console 

    colonies fs remove -l /myfiles -n hello.txt

.. code-block:: console 

    INFO[0000] Removed file (local file is not removed)

    FileID= Label=/myfiles Name=hello.txt

Warning! In the current version of ColonyOS, this will break snapshots where the file is included.

Remove a label
--------------

.. code-block:: console 

    colonies fs label remove -l /myfiles/subdir

.. code-block:: console 

    All files with label </myfiles/subdir/*> will be removed. Local files are not removed.

    Are you sure you want to continue?  (yes,no): yes

Create a snapshot
-----------------
A snapshot is a read-only copy of a label. Snapshots are useful for ensuring that a label remains unchanged. For instance, you can create a snapshot of a label, then download it to another computer, ensuring you retrieve the same files part of the snapshots even if the label has been modified. Once created, a snapshot cannot be altered.

.. code-block:: console 

    colonies fs snapshot create -l /myfiles -n mysnapshot

.. code-block:: console 

   INFO[0000] Snapshot created
   Label=/myfiles/ SnapshotName=mysnapshot
   ╭────────────┬──────────────────────────────────────────────────────────────────╮
   │ SnapshotId │ 8a66fe1bd92ee5af4f739f8c5ded116251d12db95db248a77b0f41fd3e41ff39 │
   │ Name       │ mysnapshot                                                       │
   │ Label      │ /myfiles                                                         │
   │ Colony     │ dev                                                              │
   │ Added      │ 2024-01-02 18:43:45                                              │
   ╰────────────┴──────────────────────────────────────────────────────────────────╯
   ╭────────────┬──────────────────────────────────────────────────────────────────┬─────────────────────╮
   │ FILENAME   │ FILEID                                                           │ ADDED               │
   ├────────────┼──────────────────────────────────────────────────────────────────┼─────────────────────┤
   │ hello.txt  │ e7036e2a0da8bc9d4e8cb260e340e21ff2c493122561fd05f7fb76da4b191284 │ 2024-01-02 19:38:55 │
   │ hello2.txt │ 84c424fc6e7ae6ce6dfe7a88c5877e5af44c56fc65f60824f4282b22a73d0cff │ 2024-01-02 19:38:55 │
   ╰────────────┴──────────────────────────────────────────────────────────────────┴─────────────────────╯

Download a snapshot
-------------------
To download a snapshot to a directory *downloaded_snapshot*:

.. code-block:: console 

    colonies fs snapshot download -n mysnapshot -d downloaded_snasphot

.. code-block:: console 
    
    Downloading hello.txt 100% [===============] (195 B/s)
    Downloading hello2.txt 100% [===============] (258 B/s)

Show info about a snapshot
--------------------------
To show info about a snapshot:

.. code-block:: console 

    colonies fs snapshot info -n mysnapshot

.. code-block:: console 

   ╭────────────┬──────────────────────────────────────────────────────────────────╮
   │ SnapshotId │ 8a66fe1bd92ee5af4f739f8c5ded116251d12db95db248a77b0f41fd3e41ff39 │
   │ Name       │ mysnapshot                                                       │
   │ Label      │ /myfiles                                                         │
   │ Colony     │ dev                                                              │
   │ Added      │ 2024-01-02 19:43:45                                              │
   ╰────────────┴──────────────────────────────────────────────────────────────────╯
   ╭────────────┬──────────────────────────────────────────────────────────────────┬─────────────────────╮
   │ FILENAME   │ FILEID                                                           │ ADDED               │
   ├────────────┼──────────────────────────────────────────────────────────────────┼─────────────────────┤
   │ hello.txt  │ e7036e2a0da8bc9d4e8cb260e340e21ff2c493122561fd05f7fb76da4b191284 │ 2024-01-02 19:38:55 │
   │ hello2.txt │ 84c424fc6e7ae6ce6dfe7a88c5877e5af44c56fc65f60824f4282b22a73d0cff │ 2024-01-02 19:38:55 │
   ╰────────────┴──────────────────────────────────────────────────────────────────┴─────────────────────╯

List all snapshots
------------------

.. code-block:: console 

    colonies fs snapshot ls 

.. code-block:: console

   ╭─────────────┬──────────────────────────────────────────────────────────────────┬──────────┬───────┬─────────────────────╮
   │ NAME        │ SNAPSHOTID                                                       │ LABEL    │ FILES │ ADDED               │
   ├─────────────┼──────────────────────────────────────────────────────────────────┼──────────┼───────┼─────────────────────┤
   │ mysnapshot  │ 99fb744919d8593940ad3e49354b307842f364d2b919b722b7e760923cb0e1b5 │ /myfiles │ 2     │ 2024-01-04 12:31:12 │
   │ mysnapshot2 │ eaad357f164c330c8922756c9ae74e25d12cd99b98f6617d3bc2284464d01df3 │ /myfiles │ 2     │ 2024-01-04 12:31:11 │
   │ mysnapshot1 │ c94e7d2a347da9c9b29ed46ff6b9fdd28aea52fd032287f2dbca23a8829d6ae5 │ /myfiles │ 2     │ 2024-01-04 12:31:10 │
   │ mysnapshot3 │ 479c3a0b50fb1d9e3963462ecad1d9934db0d10d7bfcbe42e8eb45a3c38d0c65 │ /myfiles │ 2     │ 2024-01-04 12:31:08 │
   ╰─────────────┴──────────────────────────────────────────────────────────────────┴──────────┴───────┴─────────────────────╯

Remove a snaphot
----------------

.. code-block:: console 

    colonies fs snapshot remove -n mysnapshot

.. code-block:: console 

    INFO[0000] Snapshot removed

    SnapshotName=mysnapshot

Remove all snaphots
-------------------

.. code-block:: console 

   colonies fs snapshot removeall 

.. code-block:: console

   WARNING!!! Are you sure you want to remove all snapshots in colony <dev>. This operation cannot be undone! (YES,no): YES
   INFO[0001] All snapshots removed

Security
========

Change user Id
--------------
To change the user Id, you first need to generate a new private key. The private key is used to sign all requests to the ColonyOS API. 

.. code-block:: console

    colonies key generate

.. code-block:: console

    INFO[0000] Generated new private key
    Id=af27015d13c896829c7f670d9cc3804a99b95f6ff6742090c99255a15e9a9079 
    PrvKey=09ca203b3b033a58118ee786e779020a50bdc47e81d6dd214ccd3f9b802d659f

To change the user Id, type:

.. code-block:: console
   
    colonies user chid --userid af27015d13c896829c7f670d9cc3804a99b95f6ff6742090c99255a15e9a9079

.. code-block:: console

    INFO[0000] Changed user Id
    ColonyName=dev 
    UserId=af27015d13c896829c7f670d9cc3804a99b95f6ff6742090c99255a15e9a9079

To use the new user Id, you also need to update the environmental variable **COLONIES_PRVKEY**

.. code-block:: console

    export COLONIES_PRVKEY=09ca203b3b033a58118ee786e779020a50bdc47e81d6dd214ccd3f9b802d659f

Change executor Id
------------------
Let's first add a test executor.

.. code-block:: console

    colonies key generate 

.. code-block:: console

    INFO[0000] Generated new private key
    Id=e199f392d5ed676fdebfac8f71d69684f90c80e7776d390b1ef495f0ea3950a9
    PrvKey=a410e7471a2d43228c90d6b6de6df299fe5d2863eb20dda1964e1f169e4e6dcd

.. code-block:: console

   colonies executor add --executorid e199f392d5ed676fdebfac8f71d69684f90c80e7776d390b1ef495f0ea3950a9 \
   --name my_name \
   --type my_type \
   --approve

Export the private key to an environmental variable to use exector when interacting with the ColonyOS API.

.. code-block:: console

   export COLONIES_PRVKEY=a410e7471a2d43228c90d6b6de6df299fe5d2863eb20dda1964e1f169e4e6dcd

Now, let's change the executor Id.

.. code-block:: console

    colonies key generate 

.. code-block:: console

   INFO[0000] Generated new private key
   Id=68fd35cdb87cc00825a86cdb0accae67565ea68fede3a1694864cd57104844cd
   PrvKey=6131943cee3c123a5eb6d4c28ba107f60e215ea4fc2013c009dd6cacf1e964cd

.. code-block:: console

    colonies executor chid --executorid 68fd35cdb87cc00825a86cdb0accae67565ea68fede3a1694864cd57104844cd

.. code-block:: console

    INFO[0000] Changed executor Id
    ColonyName=dev 
    ExecutorId=68fd35cdb87cc00825a86cdb0accae67565ea68fede3a1694864cd57104844cd

Update the environmental variable **COLONIES_PRVKEY** to use the new executor Id.

.. code-block:: console

   export COLONIES_PRVKEY=6131943cee3c123a5eb6d4c28ba107f60e215ea4fc2013c009dd6cacf1e964cd

Change colony Id
----------------

To generate a new colony Id, first generate a new private key.

.. code-block:: console

   colonies key generate 

.. code-block:: console
   
   INFO[0000] Generated new private key
   Id=07079e6e0d6737c688204ec2fcb68db1b00a903e9f059a76f1fcb185947de7fa
   PrvKey=da041bd75fe6e2c0bfdf95873ab12d2dcd11aaf46ef6f9c5cac28b4fa9386ff0

.. code-block:: console

   colonies colony chid --colonyid 07079e6e0d6737c688204ec2fcb68db1b00a903e9f059a76f1fcb185947de7fa
 
.. code-block:: console

    INFO[0000] Changed colony Id
    ColonyId=07079e6e0d6737c688204ec2fcb68db1b00a903e9f059a76f1fcb185947de7fa
    ColonyName=dev

To use the new colony Id, you also need to update the environmental variable **COLONIES_COLONY_PRVKEY**.

.. code-block:: console
   
    export COLONIES_COLONY_PRVKEY=da041bd75fe6e2c0bfdf95873ab12d2dcd11aaf46ef6f9c5cac28b4fa9386ff0

Change server Id
----------------

To generate a new server Id, first generate a new private key.

.. code-block:: console

   colonies key generate

.. code-block:: console

   INFO[0000] Generated new private key
   Id=fc99670de6c091c99b5ea8e86f230e238f1cc67b29d0d4e7204e38b6d366710b
   PrvKey=37837d364ad0df1d62cc48eb4c58aeba267196b60c9d09005c3fcad53195ef18

.. code-block:: console

   colonies server chid --serverid fc99670de6c091c99b5ea8e86f230e238f1cc67b29d0d4e7204e38b6d366710b

To use the new server Id, you also need to update the environmental variable **COLONIES_SERVER_PRVKEY**. 

.. code-block:: console

   export COLONIES_SERVER_PRVKEY=37837d364ad0df1d62cc48eb4c58aeba267196b60c9d09005c3fcad53195ef18

Find out Id given a private key
-------------------------------

.. code-block:: console

    colonies key id --prvkey a410e7471a2d43228c90d6b6de6df299fe5d2863eb20dda1964e1f169e4e6dcd                                                             16:58:56

.. code-block:: console

    INFO[0000] Corresponding Id for the given private key
    Id=e199f392d5ed676fdebfac8f71d69684f90c80e7776d390b1ef495f0ea3950a9
