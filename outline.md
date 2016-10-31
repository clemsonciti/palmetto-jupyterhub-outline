# JupyterHub on Palmetto Cluster

## Why JupyterHub?

Jupyter Notebook is a web application for
developing, documenting and executing code
in languages such as Python, R and MATLAB, among many others.
Jupyter Notebook is free and open-source software that anyone can
install on their own machines.
Once installed, users typically start a "notebook server" on their machine,
and use a web browser to access the Jupyter Notebook web application.

On a shared resource,
such as a high-performance computing cluster,
hosted servers on the cloud,
or even a lab computer shared by several users,
it can be beneficial to use JupyterHub to set up
a *multi-user* notebook server,
rather than have each user run an independent notebook server.
Some advantages of this approach are:

1. **No setup** or installation is required by the user
2. Notebook servers can be started **securely**
without any configuration or action on the users' part
3. A **consistent environment** for all users makes
it easier to support users and diagnose problems

## How does it work?

JupyterHub is a set of processes that together provide a
Single User Notebook Server for each person in a group:

1. *Proxy*: the public facing program that routes the HTTP request made by
the user's browser to the Hub or the Single User Notebook Sever
2. *Hub*: the program that authenticates users, manages accounts,
and spawns Single User Notebook Servers.
3. *Single User Notebook Servers*: which are started by the Hub
for each successfully authenticated user.

The user may remain oblivious to the above orchestration;
from their perspective:

1. The user accesses JupyterHub by typing in an IP address or domain name in their web browser
2. Typically, they are asked to provide a username and password - 
typically their credentials on whatever computing resource is being used
3. Once authenticated, the user is presented with the Jupyter Notebook dashboard

## JupyterHub configuration

JupyterHub can run out-of-the-box in most situations,
but needs to be configured to fit the needs of each site that deploys JupyterHub.
Following are some configuration options:

1. **Networking**: things like the IP addresses and ports for the Proxy and Hub
1. **Security**: including SSL encryption to enable HTTPS, and tokens
for encrypting browser cookies and securing communication between the Proxy and the Hub.
1. **Authentication**: the method used by the Hub for authenticating users.
By default, JupyterHub uses PAM Authentication - any user with a password
on the system will be able to authenticate with this password.
1. **Spawner**: the method used by the Hub to start Single User Notebook Servers
for authenticated users. For example,
*DockerSpawner* to start a Notebook Servers in separate Docker containers,
or *BatchSpawner* to start a Notebook Servers in batch scheduled systems
1. **Notebook environment**: the Jupyter Notebook features available to users,
such as the default kernels and extensions

## JupyterHub deployment on the Palmetto Cluster

### Overview of the cluster

The Palmetto cluster is the university's primary
high-performance computing resource,
consisting of over 2000 compute nodes, and
providing 100 GB of backed-up storage for each user.

<img src="img/palmetto-structure.png" style="width:200px">

The above image describes the basic
structure of the Palmetto cluster,
and the mechanism for scheduling jobs on the compute nodes of the cluster:
Traditionally, users log in to the `user001` node via SSH,
and use the `qsub` command to schedule jobs on the compute nodes
via the PBS Pro Scheduler.

### Networking

Both the proxy and hub are run as system services on
a special `webapp` node of the cluster - a special service node
for hosting web applications and cluster documentation.
Initially, the hub was run on the `user001` node,
while the proxy was run on the `webapp` node.
Later, the `webapp` node was configured with the `qsub`, `qstat` and `qdel` commands
for submitting, deleting and querying batch jobs,
enabling the hub also to be moved there.

The proxy running the `webapp` node
can be reached at `https://palmetto.clemson.edu/jupyterhub`.

### Authentication

Authentication by the Hub is done using the default PAM authenticator.

### Spawner

Because the Palmetto is a batch scheduled system,
the [`batchspawner`](https://github.com/jupyterhub/batchspawner)
Spawner is the most appropriate to use in our JupyterHub setup.

In traditional use of the cluster,
users log in to the cluster
and schedule computations on the cluster via "batch scipts".
The batch script specifies the resources required for the computations,
and the commands to be executed using these resources:

~~~
#!/bin/bash

#PBS -N hello
#PBS -l select=1:ncpus=8:mem=1gb:interconnect=1g
#PBS -l walltime=00:05:00
#PBS -j oe

module add gcc/4.8.1
export OMP_NUM_THREADS=1

./hello.x
~~~

The above script (`hello.pbs`) can be submitted to the "scheduler" using the `qsub command`:

~~~
$ qsub hello.pbs
~~~

Batchspawner simply works by submitting a batch script
on behalf of authenticated users.
As part of this batch script,
the `jupyterhub-singleuser` executable is run - which runs the
Single User Notebook Server for the user.
The scheduler views this batch script as any other user-submitted script.

We use a version of `batchspawner`
customized for our site-specific scheduler options,
and also customized to use a form for users to enter the required computing resources.
It is trivial to create such customized spawners
simply by subclassing the provided spawner classes such as `TorqueSpawner` or `SlurmSpawner`.

### Notebook environment

By default, we provide the following kernels:

* Python 2 and 3 (Anaconda v4.2.0)
* MATLAB (via [pymatbridge](https://anneurai.net/2015/11/12/matlab-based-ipython-notebooks/)
* R (via [IRKernel](https://irkernel.github.io/)).

Each kernel runs in a separate [conda](http://conda.pydata.org/) environment,
allowing us to easily configure the kernel's environments independent of each other.
In addition, we encourage users to install their own
kernels (e.g., for other languages) in their home directories.

### Overview
