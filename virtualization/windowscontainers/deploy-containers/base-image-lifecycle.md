---
title: Base image servicing lifecycles
description: Information about the Windows container base image lifecycle.
keywords: windows containers, containers, lifecycle, release information, base image, container base image
author: Heidilohr
ms.author: helohr
ms.date: 06/17/2019
ms.topic: article
ms.prod: windows-containers
ms.service: windows-containers
---
# Base image servicing lifecycles

Windows container base images are based on either Semi-Annual Channel releases or Long-Term Servicing Channel releases of Windows Server. This article will tell you how long support will last for different versions of base images from both channels.

The Semi-Annual Channel is a twice-per-year feature update release with eighteen-month servicing timelines for each release. This lets customers take advantage of new operating system capabilities at a faster pace, both in applications (particularly those built on containers and microservices) and in the software-defined hybrid datacenter. For more information, see the [Windows Server Semi-Annual Channel overview](https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview).

For Server Core images, customers can also use the Long-Term Servicing Channel that releases a new major version of Windows Server every two to three years. Long-Term Servicing Channel releases receive five years of mainstream support and five years of extended support. This channel works with systems that require a longer servicing option and functional stability.

The following table lists each type of base image, its servicing channel, and how long its support will last.

|Base image                       |Servicing channel|Version|OS build|Availability|Mainstream support end date|Extended support date|
|---------------------------------|-----------------|-------|--------|------------|---------------------------|---------------------|
|Server Core, Nano Server, Windows|Semi-Annual      |1909   |18363   |11/12/2019  |05/11/2021                 |N/A                  |
|Server Core, Nano Server, Windows|Semi-Annual      |1903   |18362   |05/21/2019  |12/08/2020                 |N/A                  |
|Server Core                      |Long-Term        |2019   |17763   |11/13/2018  |01/09/2024                 |01/09/2029           |
|Server Core, Nano Server, Windows|Semi-Annual      |1809   |17763   |11/13/2018  |05/12/2020                 |N/A                  |
|Server Core, Nano Server         |Semi-Annual      |1803   |17134   |04/30/2018  |11/12/2019                 |N/A                  |
|Server Core, Nano Server         |Semi-Annual      |1709   |16299   |10/17/2017  |04/09/2019                 |N/A                  |
|Server Core                      |Long-Term        |1607   |14393   |10/15/2016  |01/11/2022                 |01/11/2027           |
|Nano Server                      |Semi-Annual      |1607   |14393   |10/15/2016  |10/09/2018                 |N/A                  |

For servicing requirements and other additional information, see the [Windows Lifecycle FAQ](https://support.microsoft.com/help/18581/lifecycle-faq-windows-products), [Windows Server release information](https://docs.microsoft.com/windows-server/get-started/windows-server-release-info), and the [Windows base OS images Docker hub repo](https://hub.docker.com/_/microsoft-windows-base-os-images).
