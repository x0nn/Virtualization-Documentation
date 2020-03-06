---
title: Linux Containers on Windows 10
description: Learn about different ways you can use Hyper-V to run Linux containers on Windows 10 as if they're native.
keywords: LCOW, linux containers, docker, containers, windows 10
author: scooley
ms.date: 09/17/2019
ms.topic: article
ms.prod: windows-containers
ms.service: windows-containers
ms.assetid: edfd11c8-ee99-42d8-9878-efc126fe1826
---
# Linux containers on Windows 10

Linux containers make up a huge percent of the overall container ecosystem and are fundamental to both developer experiences and production environments.  Since containers share a kernel with the container host, however, running Linux containers directly on Windows isn't an option[*](linux-containers.md#other-options-we-considered).  This is where virtualization comes into the picture.

Right now there are two ways to run Linux containers with Docker for Windows and Hyper-V:

- Run Linux containers in a full Linux VM - this is what Docker typically does today.
- Run Linux containers with [Hyper-V isolation](../manage-containers/hyperv-container.md) (LCOW) - this is a new option in Docker for Windows.

> _Running Linux containers on a Windows Server OS is currently still in an experimental stage. Additional licensing for the Docker EE program will be needed to try this. **The remainder of this article will pertain only to Windows 10**._

This article outlines how each approach works, provides guidance about when to choose which solution, and shares work in progress.

## Linux containers in a Moby VM

To run Linux containers in a Linux VM, follow the instructions in [Docker's get-started guide](https://docs.docker.com/docker-for-windows/).

Docker has been able to run Linux containers on Windows desktop since it was first released in 2016 (before Hyper-V isolation or Linux containers on Windows were available) using a [LinuxKit](https://github.com/linuxkit/linuxkit) based virtual machine running on Hyper-V.

In this model, Docker Client runs on Windows desktop but calls into Docker Daemon on the Linux VM.

![Moby VM as the container host](media/MobyVM.png)

In this model, all Linux containers share a single Linux-based container host and all Linux containers:

* Share a kernel with each other and the Moby VM, but not with the Windows host.
* Have consistent storage and networking properties with Linux containers running on Linux (since they are running on a Linux VM).

It also means the Linux container host (Moby VM) needs to be running Docker Daemon and all of Docker Daemon's dependencies.

To see if you're running with Moby VM, check Hyper-V Manager for Moby VM using either the Hyper-V Manager UI or by running `Get-VM` in an elevated PowerShell window.

## Linux Containers with Hyper-V isolation

To try Linux containers on Windows 10 (LCOW10), follow the Linux container instructions in [Linux containers on Windows 10](../quick-start/quick-start-windows-10-linux.md). 

Linux containers with Hyper-V isolation run each Linux container in an optimized Linux VM with just enough OS to run containers. In contrast to the Moby VM approach, each Linux container has its own kernel and its own VM sandbox. They're also managed by Docker on Windows directly.

![Linux containers with Hyper-V isolation (LCOW)](media/lcow-approach.png)

Taking a closer look at how container management differs between the Moby VM approach and LCOW, in the LCOW model container management stays on Windows and each LCOW management happens via GRPC and containerd.  This means the Linux distro containers use for LCOW can have a much smaller inventory.  Right now, we're using LinuxKit for the optimized distro containers use, but other projects like Kata are building similar highly-tuned Linux distros (Clear Linux) as well.

Here's a closer look at each LCOW:

![LCOW architecture](media/lcow.png)

To see if you're running LCOW, navigate to `C:\Program Files\Linux Containers`. If Docker is configured to use LCOW, there will be a few files here containing the minimal LinuxKit distro that runs in each container running under Hyper-V isolation.  Notice the optimized VM components are less than 100 MB, much smaller than the LinuxKit image in Moby VM.

### Work in progress

LCOW is under active development. Track ongoing progress in the Moby project on [GitHub](https://github.com/moby/moby/issues/33850)

#### Bind mounts

Bind mounting volumes with `docker run -v ...` stores the files on the Windows NTFS filesystem, so some translation is needed for POSIX operations. Some filesystem operations are currently partially or not implemented, which may cause incompatibilities for some apps.

These operations are not currently working for bind-mounted volumes:

* MkNod
* XAttrWalk
* XAttrCreate
* Lock
* Getlock
* Auth
* Flush
* INotify

There are also a few that are not fully implemented:

* GetAttr – The Nlink count is always reported as 2
* Open – Only ReadWrite, WriteOnly, and ReadOnly flags are implemented

These applications all require volume mapping and will not start or run correctly.

* MySQL
* PostgreSQL
* WordPress
* Jenkins
* MariaDB
* RabbitMQ

### Extra information

[Docker blog describing LCOW](https://blog.docker.com/2017/11/docker-for-windows-17-11/)

[Linux Container Video](https://sec.ch9.ms/ch9/1e5a/08ff93f2-987e-4f8d-8036-2570dcac1e5a/LinuxContainer.mp4)

[LinuxKit LCOW-kernel plus build instructions](https://github.com/linuxkit/lcow)

## When to use Moby VM vs LCOW

### When to use Moby VM

Right now, we recommend the Moby VM method of running Linux containers to people who:

- Want a stable container environment.  This is the Docker for Windows default.
- Run Windows or Linux containers, but rarely both at the same time.
- Have complicated or custom networking requirements between Linux containers.
- Don't need kernel isolation (Hyper-V isolation) between Linux containers.

### When to use LCOW

Right now, we recommend LCOW to people who:

- Want to test our newest technology.
- Run Windows and Linux containers at the same time.
- Need kernel isolation (Hyper-V isolation) between Linux containers.

## Other options we considered

When we were looking at ways to run Linux containers on Windows, we considered WSL. Ultimately, we chose a virtualization-based approach so that Linux containers on Windows are consistent with Linux containers on Linux. Using Hyper-V also makes LCOW more secure. We may re-evaluate in the future, but for now, LCOW will continue to use Hyper-V.

If you have thoughts, please send feedback through GitHub or UserVoice.  We especially appreciate feedback about the specific experience you'd like to see.
