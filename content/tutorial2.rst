Nvidia systems
==============

This is a short guide how to use Nvidia GPUs. 

Build a container
-----------------

.. code-block:: docker

   FROM docker.io/tensorflow/tensorflow:2.13.0-gpu
   
   USER root
   
   RUN apt-get update && apt-get install -y python3 python3-pip wget vim git fish libgl1-mesa-glx libglib2.0-0 libc6
   RUN python3 -m pip install --upgrade pip
   RUN pip3 install --target=/usr/local/lib/python3.8/dist-packages pycolonies opencv-python tqdm Pillow scikit-learn keras matplotlib numpy

Note the ``target`` flag to ``pip3``. Without it, ``pip3`` will installs to modules to ``~/.local/lib/python3.9/dist-packages``. 
However, when using Singularity, the home directory is normally mounted from the host system, which means that installed Python 
libraries will not be available in the container as intended.

.. code-block:: console 
    
    docker build -t johan/tensorflow .
    docker push johan/tensorflow

Simple Python example
---------------------

.. code-block:: console 
    
    pollinator new -e ice-kubeexecutor

Update ``project.yaml`` file:

.. code-block:: yaml

   projectname: gputest
   conditions:
     executorType: ice-kubeexecutor
     nodes: 1
     processesPerNode: 1
     cpu: 5000m
     mem: 10000Mi
     walltime: 600
     gpu:
       count: 1
       name: "nvidia-gtx-2080ti"
   environment:
     docker: johan/tensorflow
     rebuildImage: false
     cmd: python3
     source: main.py

.. code-block:: console 

    import tensorflow as tf
    print("Num GPUs Available: ", len(tf.config.experimental.list_physical_devices('GPU')))

Now, run the code:

.. code-block:: python 

   pollinator run --follow

.. code-block:: console 

   Uploading main.py 100% [===============] (590 kB/s)
   Uploading hello.txt 100% [===============] (66 kB/s)
   INFO[0000] Process submitted                             ProcessID=591c063721f408419e571a65d451187488ceb2624cffa2262e7ad44d80744b37
   INFO[0000] Follow process at https://dashboard.colonyos.io/process?processid=591c063721f408419e571a65d451187488ceb2624cffa2262e7ad44d80744b37
   Num GPUs Available:  1
   INFO[0012] Process finished successfully                 ProcessID=591c063721f408419e571a65d451187488ceb2624cffa2262e7ad44d80744b37

Running nvidia-smi
------------------

.. code-block:: json 

    {
        "conditions": {
            "executortype": "ice-kubeexecutor",
            "nodes": 2,
            "processes-per-node": 2,
            "mem": "2000Mi",
            "cpu": "500m",
            "gpu": {
                "name": "nvidia-gtx-2080ti",
                "count": 2
            },
            "walltime": 600
        },
        "funcname": "execute",
        "kwargs": {
            "cmd": "nvidia-smi",
            "args": [],
            "docker-image": "tensorflow/tensorflow:2.14.0rc1-gpu",
            "rebuild-image": false
        },
        "maxexectime": 600,
        "maxretries": 3
    }

.. code-block:: console 

   colonies function submit --spec nvidia-smi.json

.. code-block:: console 

   INFO[0000] Process submitted                             ProcessId=1b93b14c1eb83c4b91bbe33c7f0b1bf35845ac20e1fe371aae0c9bedf3b638df
   INFO[0000] Printing logs from process                    ProcessId=1b93b14c1eb83c4b91bbe33c7f0b1bf35845ac20e1fe371aae0c9bedf3b638df
   Sun Dec 17 14:34:23 2023
   +---------------------------------------------------------------------------------------+
   | NVIDIA-SMI 545.29.02              Driver Version: 545.29.02    CUDA Version: 12.3     |
   |-----------------------------------------+----------------------+----------------------+
   | GPU  Name                 Persistence-M | Bus-Id        Disp.A | Volatile Uncorr. ECC |
   | Fan  Temp   Perf          Pwr:Usage/Cap |         Memory-Usage | GPU-Util  Compute M. |
   |                                         |                      |               MIG M. |
   |=========================================+======================+======================|
   |   0  NVIDIA GeForce RTX 2080 Ti     Off | 00000000:24:00.0 Off |                  N/A |
   | 32%   32C    P0              35W / 250W |      0MiB / 11264MiB |      0%      Default |
   |                                         |                      |                  N/A |
   +-----------------------------------------+----------------------+----------------------+
   
   +---------------------------------------------------------------------------------------+
   | Processes:                                                                            |
   |  GPU   GI   CI        PID   Type   Process name                            GPU Memory |
   |        ID   ID                                                             Usage      |
   |=======================================================================================|
   |  No running processes found                                                           |
   +---------------------------------------------------------------------------------------+
   INFO[0010] Process finished successfully                 ProcessId=1b93b14c1eb83c4b91bbe33c7f0b1bf35845ac20e1fe371aae0c9bedf3b638df
   
AMD/ROCm systems
================

This is a short guide how to use AMD/ROCm GPUs. 

Build a container
-----------------

.. code-block:: docker 

   FROM docker.io/rocm/tensorflow:rocm5.2.0-tf2.9-dev
   
   USER root
   
   RUN apt-get update && DEBIAN_FRONTEND="noninteractive" TZ="Europe/Stockholm" apt-get install -y python3 python3-pip wget git fish libgl1-mesa-glx libglib2.0-0
   RUN python3 -m pip install --upgrade pip
   RUN pip3 install --target=/usr/local/lib/python3.9/dist-packages opencv-python tqdm Pillow scikit-learn keras matplotlib numpy google wrapt typing_extensions packaging opt_einsum gast astunparse termcolor flatbuffers 
   RUN pip3 install --target=/usr/local/lib/python3.9/dist-packages protobuf==3.20.0


.. code-block:: console 
    
    docker build -t johan/rocmtensorflow .
    docker push johan/rocmhackaton52g
  
Note that the Docker image becomes almost 9Gb!

Simple Python example
---------------------

.. code-block:: console 
    
    pollinator new -e lumi-standard-gpu-hpcexecutor

Update ``project.yaml`` file:

.. code-block:: yaml

   projectname: gputest
   conditions:
     executorType: lumi-standard-gpu-hpcexecutor
     nodes: 1
     processesPerNode: 1
     cpu: 5000m
     mem: 10000Mi
     walltime: 600
     gpu:
       count: 1
       name: ""
   environment:
     docker: johan/rocmtensorflow
     rebuildImage: false
     cmd: python3
     source: main.py

Replace the ``main.py`` with this code:

.. code-block:: console 

    import tensorflow as tf
    print("Num GPUs Available: ", len(tf.config.experimental.list_physical_devices('GPU')))

Now, run the code:

.. code-block:: python 

    pollinator run --follow

.. code-block:: console 

   INFO[0000] Process submitted                  ProcessID=192f0c29a89a3f7bc3c620a2306e2cab92709d5af5693736b5cb774536a07070
   INFO[0000] Follow process at https://dashboard.colonyos.io/process?processid=192f0c29a89a3f7bc3c620a2306e2cab92709d5af5693736b5cb774536a07070
   Num GPUs Available:  1
   INFO[0036] Process finished successfully      ProcessID=192f0c29a89a3f7bc3c620a2306e2cab92709d5af5693736b5cb774536a07070

Running rocm-smi
----------------

.. code-block:: json 

   {
       "conditions": {
           "executortype": "lumi-standard-gpu-hpcexecutor",
           "nodes": 1,
           "processes-per-node": 1,
           "mem": "10Gi",
           "cpu": "1000m",
           "gpu": {
               "count": 8
           },
           "walltime": 60
       },
       "funcname": "execute",
       "kwargs": {
           "cmd": "rocm-smi",
           "args": [
               ""
           ],
           "docker-image": "johan/rocmtensorflow"
       },
       "maxexectime": 55,
       "maxretries": 3
   }

.. code-block:: console 

   colonies function submit --spec rocm-smi.json

.. code-block:: console 

   ======================= ROCm System Management Interface =======================
   ================================= Concise Info =================================
   GPU  Temp   AvgPwr  SCLK    MCLK     Fan  Perf    PwrCap  VRAM%  GPU%
   0    44.0c  92.0W   800Mhz  1600Mhz  0%   manual  500.0W    0%   0%
   1    49.0c  N/A     800Mhz  1600Mhz  0%   manual  0.0W      0%   0%
   2    43.0c  88.0W   800Mhz  1600Mhz  0%   manual  500.0W    0%   0%
   3    45.0c  N/A     800Mhz  1600Mhz  0%   manual  0.0W      0%   0%
   4    48.0c  87.0W   800Mhz  1600Mhz  0%   manual  500.0W    0%   0%
   5    48.0c  N/A     800Mhz  1600Mhz  0%   manual  0.0W      0%   0%
   6    40.0c  92.0W   800Mhz  1600Mhz  0%   manual  500.0W    0%   0%
   7    44.0c  N/A     800Mhz  1600Mhz  0%   manual  0.0W      0%   0%
   ================================================================================
   ============================= End of ROCm SMI Log ==============================
