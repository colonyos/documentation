What is a container?
====================
A container is a lightweight, standalone, executable package that includes everything needed to run a piece of software, such as the code, a runtime, binaries, libraries, environment variables, and config files. Numerous container technologies have been developed to meet different requirements.

`Docker <https://www.docker.com>`_ containers are extensively used in a variety of computing environments. `Kubernetes <https://kubernetes.io>`_ a de facto standard container orchestration system, uses a container technology called `Containerd <https://containerd.io>`_ as a low-level interface for container management. Containerd is also utilized internally by Docker. In High-Performance Computing (HPC) environments, `Singularity <https://sylabs.io>`_ containers are often preferred for their specific features that is designed to meet specific HPC needs. Generally, different container technologies offer distinct toolsets and APIs.

Colony OS offers a way to run containers in a unified way across platforms, independent of the underlying container technology, using a consistent API. This is achieved by submitting *function specifications* to a Colonies server, which then wraps the *specifications* into processes. A *process* is then assigned to a suitable executor, which subsequently launches a container on the underlying container platform where the executor is operating.

Within ColonyOS, there is a family of executors known as *container executors*. These implement a function called ``execute`` that spawns containers. As the format of the *function specification* is identical, it becomes possible to seamless switch between different platforms.

There are currently three types of *container executors*:

1. **Kube executor** spawns containers as Kubernetes batch jobs.  
2. **Docker executor** spawns containers as Docker containers on a baremetal servers or VMs.
3. **HPC executor** spawns containers as Singularity containers on HPC systems, managing them as Slurm jobs.

We are now going to explore how we can launch containers on various platforms.

Execute containers
==================

Follow instructions at :doc:`colonies-cli` and install the ``colonies`` and ``pollinator`` CLI tool.

In this tutorial, we assume that at least one *container executor* is available in the colony. 

Let's check which executors are currently available.

.. code-block:: console

   colonies executor ls

.. code-block:: console

    ╭──────────────────┬────────────────────┬────────────────┬─────────────────────╮
    │ NAME             │ TYPE               │ LOCATION       │ LAST HEARD FROM     │
    ├──────────────────┼────────────────────┼────────────────┼─────────────────────┤
    │ icekube          │ container-executor │ RISE, Sweden   │ 2024-02-22 18:27:15 │
    │ leonardo-booster │ container-executor │ Cineca, Italy  │ 2024-02-22 18:27:43 │
    │ dev              │ container-executor │ Rutvik, Sweden │ 2024-02-22 18:27:43 │
    │ lumi-small       │ container-executor │ CSC, Finland   │ 2024-02-22 18:28:28 │
    ╰──────────────────┴────────────────────┴────────────────┴─────────────────────╯

A *container executor* takes a Unix command, a list of arguments, and a Docker image as input. It then launches a container that executes the specified command. 
For example, to run the command ``echo "hello", "world"`` on an Ubuntu container we need to specify the following information:

.. code-block:: json 

    {
        "funcname": "execute",
        "kwargs": {
            "cmd": "echo",
            "docker-image": "ubuntu:20.04"
            "rebuild-image": false,
            "args": [
                "hello", "world"
            ]
        }
    }

To submit a *function specification*, we also need to specify requirements, so-called ``conditions``, on the executors that will execute the function. 
Additionally, we also need to define constraints on the execution, such as the expected execution time of the container. 
This aspect is particularly important for managing failures effectively. If the ``maxexectime`` is exceeded, meaning the process takes 
longer than anticipated, it will be unassigned and potentially reassigned to another executor. 
The ``maxretries`` parameter determines the number of times a process can be reassigned. 
The ``maxwaittime`` parameter specifies how long time process can wait in the queue before it is assigned and automatically failed. 
This approach ensures execution continuity and that processes runs to completion, even in cases of 
unexpected delays or failures. 

A process can have the following *states*:

* **Waiting** The process is submitted and enqueued at the Colonies server, waiting for an executor to be assigned and execute the process.
* **Running** The process is assigned to an executor.
* **Successful** The process has successfully been executed by an executor.
* **Failed** The process has failed when executed by one or several executors.

Now, let's execute the *echo* command specifed above. 

.. code-block:: json 

   {
       "conditions": {
           "executortype": "container-executor",
	        "executornames": [
                "lumi-small"
            ],
           "nodes": 1,
           "processespernode": 1,
           "mem": "1Gi",
           "cpu": "500m",
           "walltime": 200,
           "gpu": {
               "count": 0
           }
       },
       "funcname": "execute",
       "kwargs": {
           "cmd": "echo",
           "docker-image": "ubuntu:20.04",
           "args": [
               "hello", "world"
           ]
       },
       "maxwaittime": -1,
       "maxexectime": 100,
       "maxretries": 3
   }

.. code-block:: console

   colonies function submit --spec echo.json --follow

The function will be execute by ``container-executor`` with the name ``lumi-small`` running on the LUMI supercomputer in Finland. If we change the ``executortype`` to
``ice-kubeexecutor`` it would instead run on a Kubernetes cluster at the ICE Datacenter in Sweden. 

.. code-block:: console

    INFO[0000] Process submitted                             ProcessId=326a94608eba9a113ab875bab1a91db96156ab5abb0f6b556d9317ac81146fdb
    INFO[0000] Printing logs from process                    ProcessId=326a94608eba9a113ab875bab1a91db96156ab5abb0f6b556d9317ac81146fdb
    hello world
    INFO[0252] Process finished successfully                 ProcessId=326a94608eba9a113ab875bab1a91db96156ab5abb0f6b556d9317ac81146fdb


We can also lookup the process by typing the following command: 

.. code-block:: console

   colonies process get -p 326a94608eba9a113ab875bab1a91db96156ab5abb0f6b556d9317ac81146fdb                                                    18:41:51

.. code-block:: console

    ╭───────────────────────────────────────────────────────────────────────────────────────╮
    │ Process                                                                               │
    ├────────────────────┬──────────────────────────────────────────────────────────────────┤
    │ Id                 │ 326a94608eba9a113ab875bab1a91db96156ab5abb0f6b556d9317ac81146fdb │
    │ IsAssigned         │ True                                                             │
    │ InitiatorID        │ bcaeac1a507036f7fed0be9d38c43ba973be7c0064d1b0b010ede2f088093b3f │
    │ Initiator          │ johan                                                            │
    │ AssignedExecutorID │ 13233dbd76811bc1c0d1f1118a90e5d42aa6cf8b23ee51fea915136127221aa1 │
    │ AssignedExecutorID │ Successful                                                       │
    │ PriorityTime       │ 1708623299332590772                                              │
    │ SubmissionTime     │ 2024-02-22 18:34:59                                              │
    │ StartTime          │ 2024-02-22 18:34:59                                              │
    │ EndTime            │ 2024-02-22 18:34:59                                              │
    │ WaitDeadline       │ 0001-01-01 00:53:28                                              │
    │ ExecDeadline       │ 2024-02-22 18:40:01                                              │
    │ WaitingTime        │ 3m21.80712s                                                      │
    │ ProcessingTime     │ 53.002228s                                                       │
    │ Retries            │ 2                                                                │
    │ Input              │                                                                  │
    │ Output             │                                                                  │
    │ Errors             │                                                                  │
    ╰────────────────────┴──────────────────────────────────────────────────────────────────╯
    ╭─────────────────────────────────────────────────────────────────────╮
    │ Function Specification                                              │
    ├─────────────┬───────────────────────────────────────────────────────┤
    │ Func        │ execute                                               │
    │ Args        │ None                                                  │
    │ KwArgs      │ args:[hello world] cmd:echo docker-image:ubuntu:20... │
    │ MaxWaitTime │ -1                                                    │
    │ MaxExecTime │ 100                                                   │
    │ MaxRetries  │ 3                                                     │
    │ Priority    │ 0                                                     │
    ╰─────────────┴───────────────────────────────────────────────────────╯
    ╭───────────────────────────────────────╮
    │ Conditions                            │
    ├──────────────────┬────────────────────┤
    │ Colony           │ hpc                │
    │ ExecutorNames    │ lumi-small         │
    │ ExecutorType     │ container-executor │
    │ Dependencies     │                    │
    │ Nodes            │ 1                  │
    │ CPU              │ 500m               │
    │ Memory           │ 1024Mi             │
    │ Processes        │ 0                  │
    │ ProcessesPerNode │ 1                  │
    │ Storage          │ 0Mi                │
    │ Walltime         │ 200                │
    │ GPUName          │                    │
    │ GPUs             │ 0                  │
    │ GPUPerNode       │ 0                  │
    │ GPUMemory        │ 0Mi                │
    ╰──────────────────┴────────────────────╯


As ColonyOS stores process execution history in a database, we can also fetch the logs after process has finished.

.. code-block:: console

    colonies log get -p 326a94608eba9a113ab875bab1a91db96156ab5abb0f6b556d9317ac81146fdb 

.. code-block:: console

    hello world

Or we could look up the process in the ColonyOS dashboard:

.. image:: img/tutorial1-dashboard1.png

Is can also be useful to get information about the execution history or list the queue. This is done using the ``colonies process`` command.
For example. the command below list the last 10 successful processes:

.. code-block:: console

    colonies process pss --count 10

.. code-block:: console

    ╭──────────┬──────┬─────────────────────────┬─────────────────────┬──────────────────┬────────────────────┬───────────┬────────────╮
    │ FUNCNAME │ ARGS │ KWARGS                  │ SUBMSSION TIME      │ EXECUTOR NAME    │ EXECUTOR TYPE      │ INITIATOR │ LABEL      │
    ├──────────┼──────┼─────────────────────────┼─────────────────────┼──────────────────┼────────────────────┼───────────┼────────────┤
    │ execute  │      │ docker-image:ubuntu:... │ 2024-02-22 18:34:59 │ lumi-small       │ container-executor │ johan     │            │
    │ execute  │      │ docker-image:tensorf... │ 2024-02-20 11:30:47 │ leonardo-booster │ container-executor │ johan     │            │
    │ execute  │      │ args:[] cmd:nvidia-s... │ 2024-02-20 11:30:42 │ leonardo-booster │ container-executor │ johan     │            │
    │ execute  │      │ args:[/cfs/03ca98d67... │ 2024-02-20 11:28:00 │ leonardo-booster │ container-executor │ johan     │ test_label │
    │ execute  │      │ cmd:python3 docker-i... │ 2024-02-20 09:37:21 │ lumi-small       │ container-executor │ johan     │ test_label │
    │ execute  │      │ args:[/cfs/e20ebf4b2... │ 2024-02-20 09:34:53 │ icekube          │ container-executor │ johan     │ test_label │
    │ execute  │      │ cmd:sleep 8 docker-i... │ 2024-02-20 09:27:38 │ lumi-small-g     │ container-executor │ johan     │            │
    │ execute  │      │ args:[/cfs/e20ebf4b2... │ 2024-02-20 09:31:58 │ icekube          │ container-executor │ johan     │ test_label │
    │ execute  │      │ cmd:sleep 8 docker-i... │ 2024-02-20 09:27:38 │ lumi-small-g     │ container-executor │ johan     │            │
    │ execute  │      │ cmd:sleep 8 docker-i... │ 2024-02-20 09:27:38 │ lumi-small-g     │ container-executor │ johan     │            │
    ╰──────────┴──────┴─────────────────────────┴─────────────────────┴──────────────────┴────────────────────┴───────────┴────────────╯

Alternativly, ``colonies process ps`` lists running processes, and ``colonies process psw`` lists waiting processes, and finally
``colonies process psf`` lists failed processes.

Now that you may have acquired some fundamental knowledge about running containers on ColonyOS, let's proceed to explore how to share data effectively across different container executors.

Init command
------------

Managing data
=============

Upload data
-----------

Let's create a empty directory, and upload the directory to CFS. 

.. code-block:: console

    mkdir myfiles

.. code-block:: console

    echo "Hello world" > myfiles/hello.txt

The command below uploads all files in the ``myfiles`` directory to CFS under the label ``myfiles``.

.. code-block:: console

    colonies fs sync -l /myfiles -d ./myfiles

.. code-block:: console

    INFO[0000] Calculating sync plans
    Analyzing /home/johan/b/myfiles          ... done!
    INFO[0000] Sync plans completed                          Conflict resolution=replace-remote Conflicts=0 Download=0 Upload=1
    INFO[0000] Add --syncplan flag to view the sync plan in more detail

    Are you sure you want to continue? (yes,no):


Let's list all labels on CFS.

.. code-block:: console

    colonies fs label ls

.. code-block:: console

    ╭─────────────────────────────────────────────────────────────────────────────────────┬───────╮
    │ LABEL                                                                               │ FILES │
    ├─────────────────────────────────────────────────────────────────────────────────────┼───────┤
    │ /myfiles                                                                            │ 1     │
    ╰─────────────────────────────────────────────────────────────────────────────────────┴───────╯

To download the ``/myfiles`` label, for example on another computer: 
    
.. code-block:: console
    
    colonies fs sync -l /myfiles -d ./myfiles2

We are now able to submit a *function specification* that synchronizes the ``/myfiles`` directory, ensuring the files becomes available on a shared file system accessible by the container executing the corresponding process.

The ``/myfiles`` label will be synchronized to ``/cfs/myfiles``. This synchronization occurs twice: the first time before the container executes, and a second time after the container has completed its execution. This dual synchronization process enables the fetching of data needed by the container and pushing of data generated by the container.

.. code-block:: json 

   {
       "conditions": {
            "executortype": "container-executor",
    	    "executornames": [
            	"lumi-small"
            ],
           "nodes": 1,
           "processespernode": 1,
           "mem": "1Gi",
           "cpu": "500m",
           "walltime": 200,
           "gpu": {
               "count": 0
           }
       },
       "funcname": "execute",
       "kwargs": {
           "cmd": "cat",
           "docker-image": "ubuntu:20.04",
           "args": [
               "/cfs/myfiles/hello.txt"
           ]
       },
       "fs": {
           "mount": "/cfs",
           "dirs": [
               {
                   "label": "/myfiles",
                   "dir": "/myfiles",
                   "keepfiles": false,
                   "onconflicts": {
                       "onstart": {
                           "keeplocal": false
                       },
                       "onclose": {
                           "keeplocal": true
                       }
                   }
                }
           ]
       },
       "maxwaittime": -1,
       "maxexectime": 100,
       "maxretries": 3
   }

.. code-block:: console

   colonies function submit --spec ./myfiles.json  --follow 

.. code-block:: console 

   INFO[0000] Process submitted                             ProcessId=b3a15b7822651cbbd34f7299d266f78de806505a0836a89033d513ade038ab13
   INFO[0000] Printing logs from process                    ProcessId=b3a15b7822651cbbd34f7299d266f78de806505a0836a89033d513ade038ab13
   Hello world
   INFO[0003] Process finished successfully                 ProcessId=b3a15b7822651cbbd34f7299d266f78de806505a0836a89033d513ade038ab13
