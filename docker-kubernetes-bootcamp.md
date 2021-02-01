- `Kubernetes` is container orchestration tool.


- `Docker` is a tool for running applications in an isolated environment.
- It is very similar to Virtual Machine.
- App runs in same environment.
- Just works
- It is a standard for software deployment.

================================
Containers vs Virtual Machines:
================================

- `Containers` are an abstraction at the application layer that packages `code` and `dependencies` together.
- Multiple containers can run on the same machine and share the OS kernel with other containers, each running as isolated processes in user space.

A Container doesn't require a full OS. It simply shares the underlying OS but running in isolation of other containers.


- `Virtual Machines (VMs)` are an abstraction of physical hardware turning one server into many servers. The hypervisor allows multiple VMs to run on a single machine.
- Each VM includes a full copy of an OS, the application, necessary binaries and libraries - taking up 10s of GBs.
- VMs can also be slow to boot.

=========================
Installation of Docker:
=========================


1. Open link https://docs.docker.com/ and click the `Download and Install` option. There will be different options for download for different OS (Linux, MacOS, Windows).
2. Then click option `Download from Docker Hub`. You will notice something as `Docker Toolbox and Docker Desktop`. `Docker Toolbox` is the older way of working with Docker whereas `Docker
Desktop` is the newer way.
3. To check whether Docker is installed or not type command `docker --version`.
4. You can simply run command `docker` on terminal (CMD, Terminal, Git Bash) to get the list of different commands for docker.


You can stop the docker desktop by right-clicking on the icon and select quick `docker desktop` and after that when you run the command `docker ps` it will not able to connect with docker. On Windows the error will come like this: 

================================================================================================================================================================================================
"error during connect: Get http://%2F%2F.%2Fpipe%2Fdocker_engine/v1.40/containers/json: open //./pipe/docker_engine: The system cannot find the file specified. In the default daemon configuration on Windows, the docker client must be run elevated to connect. This error may also indicate that the docker daemon is not running."
================================================================================================================================================================================================
