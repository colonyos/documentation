Introduction
============

Function specs
--------------
A *function specification* defines a function that should be executed by an executor in the future. Below is a basic
example of a function specification. 

.. code-block:: json 

   {
       "conditions": {
           "colonyname": "mycolony",
           "executortype": "helloworld-executor"
       },
       "funcname": "helloworld"
   }

**funcname** defines the *name* of the function that should be executed.
**conditions** defines requirements that must be fullfilled by targeting executors. In the example, the 
**executortype** of an executor must be of the type *helloworld-executor*, only executors of that type will be eligible to be assigned this function. **colonyname** definies that name of the colony where this function should be executed. The **colonyname** is automatically set if using the Colonies CLI. 

Arguments
^^^^^^^^^

It is also possible to pass arguments to a function.

.. code-block:: json 

   {
       "conditions": {
           "colonyname": "mycolony",
           "executortype": "cli"
       },
       "funcname": "sleep",
       "args": [
           "10"
       ] 
   }

Keyword arguments
^^^^^^^^^^^^^^^^^

Alternatively, it is also possible to specify keyword arguments.

.. code-block:: json 

   {
        "conditions": {
            "colonyname": "mycolony",
            "executortype": "cli"
        },
       "funcname": "helloworld",
       "desc": "Hello world example",
       "kwargs": {
           "str1_kw": "str1",
           "str2_kw": "str2"
       } 
   }


Failure management
^^^^^^^^^^^^^^^^^^

**maxwaittime** sets the limit for how long a process can stay in the queue at the Colonies server before being marked as failed if not executed. **maxexectime** determines the maximum time an executor can spend executing a process before it is unassigned and returned to the queue. **maxretries** specifies the number of times a process can be reassigned for execution before being automatically set as failed. These parameters are crucial for efficient process management and failure management. For instance, if an executor crashes, **maxexectime** ensures the process is reassigned, maintaining workload continuity.

.. code-block:: json 

   {
       "conditions": {
           "executortype": "cli"
       },
       "funcname": "sleep",
       "args": [
           "20"
       ],
       "maxwaittime": 30,
       "maxexectime": 30,
       "maxretries": 3
   }

Labels
^^^^^^

A **label** can be set to more easily look up the process in the Colonies server database.

.. code-block:: json 

   {
       "conditions": {
           "colonyname": "mycolony",
           "executortype": "helloworld-executor"
       },
       "funcname": "helloworld",
       "label": "mylabel"
   }

Hardware requirements
^^^^^^^^^^^^^^^^^^^^^

The **conditions** may also include hardware requirements needed to execute the function.

.. code-block:: json 
    
   {
    "conditions": {
        "colonyname": "mycolony",
        "executortype": "helloworld-executor",
        "nodes": 10,
        "cpu": "1000m",
        "processes": 10,
        "processespernode": 1,
        "mem": "10G",
        "storage": "10G",
        "gpu": {
            "name": "test_name1",
            "mem": "11G",
            "count": 1,
            "nodecount": 0
        },
        "walltime": 1000
    },
       "funcname": "helloworld",
       "label": "mylabel"
   }

TODO

.. code-block:: json 

   {
    "nodename": "test_name",
    "funcname": "test_func",
    "args": [
        "test_arg"
    ],
    "kwargs": {
        "0": "test_arg"
    },
    "priority": 5,
    "maxwaittime": -1,
    "maxexectime": -1,
    "maxretries": 3,
    "conditions": {
        "colonyname": "e4b9f76c1d9488601c80c61e4cac860565087f568f7d6240dfac3bda215c30e7",
        "executorids": [
            "36d211b0e4665ffbb72847810d124b2123ea85726220967362046a373db35b9f",
            "b8d1b6b0721b248a5d2d66f1125a657a4b5f8a353279a7f857e9907a098ee4d2"
        ],
        "executortype": "test_executor_type",
        "dependencies": [
            "test_name2"
        ],
        "nodes": 10,
        "cpu": "1000m",
        "processes": 10,
        "processespernode": 1,
        "mem": "10G",
        "storage": "10G",
        "gpu": {
            "name": "test_name1",
            "mem": "11G",
            "count": 1,
            "nodecount": 0
        },
        "walltime": 1000
    },
    "label": "test_label",
    "fs": {
        "mount": "/cfs",
        "snapshots": [
            {
                "snapshotid": "test_snapshotid1",
                "label": "test_label1",
                "dir": "test_dir1",
                "keepfiles": false,
                "keepsnapshot": false
            },
            {
                "snapshotid": "test_snapshotid2",
                "label": "test_label2",
                "dir": "test_dir2",
                "keepfiles": true,
                "keepsnapshot": true
            }
        ],
        "dirs": [
            {
                "label": "test_label1",
                "dir": "test_dir1",
                "keepfiles": false,
                "onconflicts": {
                    "onstart": {
                        "keeplocal": false
                    },
                    "onclose": {
                        "keeplocal": false
                    }
                }
            },
            {
                "label": "test_label2",
                "dir": "test_dir2",
                "keepfiles": false,
                "onconflicts": {
                    "onstart": {
                        "keeplocal": false
                    },
                    "onclose": {
                        "keeplocal": false
                    }
                }
            }
        ]
    },
    "env": {
        "test_key": "test_value"
    }

Workflow specs
--------------

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
