Introduction
============

Function specs
--------------

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
        "processes-per-node": 1,
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
