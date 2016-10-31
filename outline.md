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

