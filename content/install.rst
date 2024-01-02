Docker-compose
==============

Docker-compose is the easiest way to setup Colonies on a local server.
Start by cloning the `Colonies Github repo <https://github.com/colonyos/colonies>`_. 

.. code-block:: console

    git clone https://github.com/colonyos/colonies.git

All settings are located in the ``docker-compose.env`` file located in the repos root directory.
Note that all keys are published on the Internet. Use ``colonies key generate`` to create new key pairs.

.. code-block:: console

   export LANG=en_US.UTF-8
   export LANGUAGE=en_US.UTF-8
   export LC_ALL=en_US.UTF-8
   export LC_CTYPE=UTF-8
   export TZ=Europe/Stockholm
   export COLONIES_SERVER_TLS="false"
   export COLONIES_SERVER_HOST="localhost"
   export COLONIES_SERVER_PORT="50080"
   export COLONIES_MONITOR_PORT="21120"
   export COLONIES_MONITOR_INTERVAL="1"
   export COLONIES_SERVER_ID="039231c7644e04b6895471dd5335cf332681c54e27f81fac54f9067b3f2c0103"
   export COLONIES_SERVER_PRVKEY="fcc79953d8a751bf41db661592dc34d30004b1a651ffa0725b03ac227641499d"
   export COLONIES_DB_HOST="timescaledb"
   export COLONIES_DB_USER="postgres"
   export COLONIES_DB_PASSWORD="rFcLGNkgsNtksg6Pgtn9CumL4xXBQ7"
   export COLONIES_COLONY_NAME="dev"
   export COLONIES_COLONY_ID="4787a5071856a4acf702b2ffcea422e3237a679c681314113d86139461290cf4"
   export COLONIES_COLONY_PRVKEY="ba949fa134981372d6da62b6a56f336ab4d843b22c02a4257dcf7d0d73097514"
   export COLONIES_ID="3fc05cf3df4b494e95d6a3d297a34f19938f7daa7422ab0d4f794454133341ac"
   export COLONIES_PRVKEY="ddf7f7791208083b6a9ed975a72684f6406a269cfa36f1b1c32045c0a71fff05"
   export COLONIES_EXECUTOR_TYPE="cli"
   export COLONIES_CRON_CHECKER_PERIOD="1000"
   export COLONIES_GENERATOR_CHECKER_PERIOD="1000"
   export COLONIES_EXCLUSIVE_ASSIGN="true"
   export COLONIES_ALLOW_EXECUTOR_REREGISTER="false"
   export COLONIES_RETENTION="false"
   export COLONIES_RETENTION_POLICY="200"
   export COLONIES_SERVER_PROFILER="false"
   export COLONIES_SERVER_PROFILER_PORT="6060"
   export MINIO_USER="admin"
   export MINIO_PASSWORD="admin12345"
   export AWS_S3_ENDPOINT="localhost:9000"
   export AWS_S3_ACCESSKEY="RrXN2vcLeHjBptG8a3Ay"
   export AWS_S3_SECRETKEY="ivwLB0Luqomq65nNVmoo8fTBgxXgNvqYGC50VQN6"
   export AWS_S3_REGION_KEY=""
   export AWS_S3_BUCKET="colonies-prod"
   export AWS_S3_TLS="false"
   export AWS_S3_SKIPVERIFY="false"
   
.. code-block:: console
    
    source docker-compose.env

Docker-compose will setup a TimescaleDB server, a Minio/S3 server and a single node Colonies server.

.. code-block:: console

    docker-compose up

.. code-block:: console

   colonies-server_1  | [GIN-debug] POST   /api --> github.com/colonyos/colonies/pkg/server.(*ColoniesServer).handleAPIRequest-fm (4 handlers)
   colonies-server_1  | time="2023-12-18T20:53:30Z" level=info msg="EtcdServer is ready" DataPath=/var/colonies/etcd EtcdClientPort=23100 EtcdPeerPort=24100 Host=colonies-server Name=server1
   colonies-server_1  | time="2023-12-18T20:53:30Z" level=debug msg="ColoniesServer became leader" EtcdNode=server1
   colonies-server_1  | time="2023-12-18T20:53:30Z" level=info msg="Starting Colonies server" APIPort=50080 AllowExecutorReregister=false CronPeriod=1000 EtcdClientPort=23100 EtcdDataPath=/var/colonies/etcd EtcdPeerPort=24100 ExclusiveAssign=true GeneratorPeriod=500 Host=colonies-server Name=server1 Port=50080 RelayPort=25100 Retention=false RetentionPolicy=604800 ServerID=039231c7644e04b6895471dd5335cf332681c54e27f81fac54f9067b3f2c0103 TLS=false TLSCertPath= TLSPrivateKeyPath=
   
To remove all data, type:

.. code-block:: console

    docker-compose down --volumes

Setup Minio/S3
--------------
To use Colony FS, you alse need to set up Minio/S3.

.. code-block:: console

    mc alias set myminio http://localhost:9000 $MINIO_USER $MINIO_PASSWORD;
    mc admin user add myminio $AWS_S3_ACCESSKEY $AWS_S3_SECRETKEY;
    mc admin policy attach myminio readwrite --user=$AWS_S3_ACCESSKEY;
    mc mb myminio/$AWS_S3_BUCKET;


Create a Colony
---------------
Create a new colony and a new user.

.. code-block:: console

    colonies colony add --name $COLONIES_COLONY_NAME --colonyid $COLONIES_COLONY_ID;
    colonies user add --name="myuser" --email="" --phone="" --userid=$COLONIES_ID


Dev server
==========

The Colonies CLI features a built-in development server that is easy to set up.
Clone the `Colonies Github repo <https://github.com/colonyos/colonies>`_. 

It will also automatically set up a test colony and a test user. Note that all data is lost if the dev server is restarted.

.. code-block:: console

    git clone git@github.com:colonyos/colonies.git

Source the development environment test credentials. Note that the keys are publicly known and should NOT be used in a production system.

.. code-block:: console

    source devenv

Download the **colonies** binary and start the server:

.. code-block:: console

    colonies dev

.. code-block:: console

    INFO[0001] Connecting to PostgreSQL server               DBHost=localhost DBName=postgres DBPassword=rFcLGNkgsNtksg6Pgtn9CumL4xXBQ7 DBPort=50070 DBUser=postgres
    INFO[0001] Initialize a Colonies PostgreSQL database
    INFO[0001] Starting a Colonies server                    Port=50080
    INFO[0001] EtcdServer is ready                           DataPath=/tmp/coloniesdev/dev/etcd EtcdClientPort=23790 EtcdPeerPort=23800 Host=localhost Name=dev
    INFO[0001] Starting Colonies server                      APIPort=50080 AllowExecutorReregister=false CronPeriod=1000 EtcdClientPort=23790 EtcdDataPath=/tmp/coloniesdev/dev/etcd EtcdPeerPort=23800 ExclusiveAssign=true GeneratorPeriod=1000 Host=localhost Name=dev Port=50080 RelayPort=2381 Retention=false RetentionPolicy=200 ServerID=039231c7644e04b6895471dd5335cf332681c54e27f81fac54f9067b3f2c0103 TLS=false TLSCertPath= TLSPrivateKeyPath=
    INFO[0001] Connecting to Colonies server                 ServerHost=localhost ServerPort=50080
    INFO[0001] Registering a new Colony                      ColonyID=4787a5071856a4acf702b2ffcea422e3237a679c681314113d86139461290cf4 ColonyName=dev ServerPrvKey=fcc79953d8a751bf41db661592dc34d30004b1a651ffa0725b03ac227641499d
    INFO[0001] Registering a new executor                    ColonyPrvKey=ba949fa134981372d6da62b6a56f336ab4d843b22c02a4257dcf7d0d73097514 ExecutorID=3fc05cf3df4b494e95d6a3d297a34f19938f7daa7422ab0d4f794454133341ac ExecutorName=myexecutor ExecutorType=cli
    INFO[0001] Approving executor                            ExecutorID=3fc05cf3df4b494e95d6a3d297a34f19938f7daa7422ab0d4f794454133341ac
    INFO[0001] Approving CLI executor
    INFO[0001] Starting Prometheus monitoring server         MonitorInterval=1 Port=21120 ServerHost=localhost ServerPort=50080
    INFO[0001] Successfully started Colonies development server
    INFO[0001] Press ctrl+c to exit

Verify the installation. There should be one registered executor.

.. code-block:: console

   colonies executor ls

.. code-block:: console

   ╭────────────┬──────┬──────────╮
   │ NAME       │ TYPE │ LOCATION │
   ├────────────┼──────┼──────────┤
   │ myexecutor │ cli  │          │
   ╰────────────┴──────┴──────────╯


Setup Minio/S3
--------------
To use Colony FS, you alse need to set up Minio/S3.

.. code-block:: console

    mc alias set myminio http://localhost:9000 $MINIO_USER $MINIO_PASSWORD
    mc admin user add myminio $AWS_S3_ACCESSKEY $AWS_S3_SECRETKEY
    mc admin policy attach myminio readwrite --user=$AWS_S3_ACCESSKEY
    mc mb myminio/$AWS_S3_BUCKET

Troubleshooting
^^^^^^^^^^^^^^^
To simplify deployment, the Colonies development server uses internally an `Embedded PostgreSQL database <https://github.com/fergusstrange/embedded-postgres>`_ 
instead of real PostgreSQL server. However, in some situations, the Embedded PostgreSQL server may not shut down correctly, leaving a zombie process and 
this error:

.. code-block:: console

   INFO[0000] Starting a Colonies development server
   INFO[0000] Creating Colonies data directory, this directory will be deleted every time the development server is restarted  Path=/tmp/coloniesdev/
   INFO[0000] Starting embedded PostgreSQL server           DBHost=localhost DBName=postgres DBPassword=rFcLGNkgsNtksg6Pgtn9CumL4xXBQ7 DBPort=50070 DBUser=postgres
   ERRO[0000] process already listening on port 50070       BuildTime="2023-12-04T07:11:56Z" BuildVersion=154ad05

To kill the Embedded PostgreSQL server running in background:

.. code-block:: console

    ps ax -o pid,cmd | grep embedded-postgres-go

.. code-block:: console

    3045089 /tmp/coloniesdev/embedded-postgres-go/extracted/bin/postgres -D /tmp/coloniesdev/embedded-postgres-go/extracted/data -p 50070

.. code-block:: console

   kill -9 3045089 

Building from source
====================
First install `Golang <https://go.dev>`_, then just type: ``source devenv`` and then ``go run cmd/main.go`` in root of the Colonies Github directory. Alternatively, there is Makefile, type: ``make``.

Production server
=================
Kubernetes is recommended for setting up a production server.


Prerequisites
-----------
Install `K3s <https://k3s.io>`_ (or some other Kubernetes distribution). 

Let's encrypt
^^^^^^^^^^^^^
You need a valid domain name to setup TLS and use Let's encrypt.

If you are using Nginx:

.. code-block:: console

    kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.10.0/cert-manager.yaml
    kubectl create -f letsencrypt.yaml

If you are using Traefik:

.. code-block:: console

    kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.10.0/cert-manager.yaml
    kubectl create -f letsencrypt_k3s.yaml

Verify the installation:

.. code-block:: console

    kubectl get pods --namespace cert-manager

.. code-block:: console
 
    NAME                                     READY   STATUS    RESTARTS   AGE
    cert-manager-646dddd544-rrmx4            1/1     Running   0          19h
    cert-manager-cainjector-8676c4b7-dxsjw   1/1     Running   0          19h
 
Longhorn
^^^^^^^^
You may also want to install Longhorn. 

.. code-block:: console

    kubectl apply -f https://raw.githubusercontent.com/longhorn/longhorn/v1.5.1/deploy/longhorn.yaml

Helm
----

.. code-block:: console

   git clone https://github.com/colonyos/helm.git

Follow instructions at `Colones Helm Chart Github page <https://github.com/colonyos/helm/tree/main/colonies>`_, and edit the ``values.yaml`` files accordingly.

.. code-block:: console

   cd colonies; ./install.sh
