---
title: Deploy Windows Containers on Windows Server
description: Deploy Windows Containers on Windows Server
keywords: docker, containers
author: taylorb-microsoft
ms.date: 09/09/2019
ms.topic: article
ms.prod: windows-containers
ms.service: windows-containers
ms.assetid: ba4eb594-0cdb-4148-81ac-a83b4bc337bc
---
# Container host deployment: Windows Server

Deploying a Windows container host has different steps depending on the operating system and the host system type (physical or virtual). This document details deploying a Windows container host to either Windows Server 2016 or Windows Server Core 2016 on a physical or virtual system.

## Install Docker

Docker is required in order to work with Windows containers. Docker consists of the Docker Engine and the Docker client.

To install Docker, we'll use the [OneGet provider PowerShell module](https://github.com/OneGet/MicrosoftDockerProvider). The provider will enable the containers feature on your machine and install Docker, which will require a reboot.

Open an elevated PowerShell session and run the following cmdlets.

Install the OneGet PowerShell module.

```PowerShell
Install-Module -Name DockerMsftProvider -Repository PSGallery -Force
```

Use OneGet to install the latest version of Docker.

```PowerShell
Install-Package -Name docker -ProviderName DockerMsftProvider
```

When the installation is complete, reboot the computer.

```PowerShell
Restart-Computer -Force
```

## Install a specific version of Docker

There are currently two channels available for Docker EE for Windows Server:

* `17.06` - Use this version if you're using Docker Enterprise Edition (Docker Engine, UCP, DTR). `17.06` is the default.
* `18.03` - Use this version if you're running Docker EE Engine alone.

To install a specific version, use the `RequiredVersion` flag:

```PowerShell
Install-Package -Name docker -ProviderName DockerMsftProvider -Force -RequiredVersion 18.03
```

Installing specific Docker EE versions may require an update to previously installed DockerMsftProvider modules. To Update:

```PowerShell
Update-Module DockerMsftProvider
```

## Update Docker

If you need to update Docker EE Engine from an earlier channel to a later channel, use both the `-Update` and `-RequiredVersion` flags:

```PowerShell
Install-Package -Name docker -ProviderName DockerMsftProvider -Update -Force -RequiredVersion 18.03
```

## Install base container images

Before working with Windows containers, a base image needs to be installed. Base images are available with either Windows Server Core or Nano Server as the container operating system. For detailed information on Docker container images, see [Build your own images on docker.com](https://docs.docker.com/engine/tutorials/dockerimages/).

> [!TIP]
> With effect from May 2018, delivering a consistent and trustworthy acquisition experience, almost all of the Microsoft-sourced container images are served from the Microsoft Container Registry, _mcr.microsoft.com_, while maintaining the current discovery process via [_Docker Hub_](https://hub.docker.com/publishers/microsoftowner).

### Windows Server 2019 and newer

To install the 'Windows Server Core' base image run the following:

```PowerShell
docker pull mcr.microsoft.com/windows/servercore:ltsc2019
```

To install the 'Nano Server' base image run the following:

```PowerShell
docker pull mcr.microsoft.com/windows/nanoserver:1809
```

### Windows Server 2016 (versions 1607-1803)

To install the Windows Server Core base image run the following:

```PowerShell
docker pull mcr.microsoft.com/windows/servercore:1607
```

To install the Nano Server base image run the following:

```PowerShell
docker pull mcr.microsoft.com/windows/nanoserver:1803
```

> Please read the Windows containers OS image EULA, which can be found here – [EULA](../images-eula.md).

## Hyper-V isolation host

You must have the Hyper-V role to run Hyper-V isolation. If the Windows container host is itself a Hyper-V virtual machine, nested virtualization will need to be enabled before installing the Hyper-V role. For more information on nested virtualization, see [Nested Virtualization](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization).

### Nested virtualization

The following script will configure nested virtualization for the container host. This script is run on the parent Hyper-V machine. Ensure that the container host virtual machine is turned off when running this script.

```PowerShell
#replace with the virtual machine name
$vm = "<virtual-machine>"

#configure virtual processor
Set-VMProcessor -VMName $vm -ExposeVirtualizationExtensions $true -Count 2

#disable dynamic memory
Set-VMMemory -VMName $vm -DynamicMemoryEnabled $false

#enable mac spoofing
Get-VMNetworkAdapter -VMName $vm | Set-VMNetworkAdapter -MacAddressSpoofing On
```

### Enable the Hyper-V role

To enable the Hyper-V feature using PowerShell, run the following cmdlet in an elevated PowerShell session.

```PowerShell
Install-WindowsFeature hyper-v
```
