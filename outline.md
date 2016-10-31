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

<img src="img/palmetto-structure.png" style="width:200px">

The Palmetto cluster


### Networking

Both the proxy and hub are run as services on
a special node of the cluster,
which is configured to submit and delete jobs
using the commands `qsub` and `qdel`.

### Authentication

We use the default PAM authenticator.

### Spawner

We use 

### Notebook environment

### Overview
