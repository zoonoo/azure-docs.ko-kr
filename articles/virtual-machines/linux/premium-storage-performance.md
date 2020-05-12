---
title: 'Azure Premium Storage: Linux Vm의 성능 설계 | Microsoft Docs'
description: Azure premium SSD 관리 디스크를 사용 하 여 고성능 응용 프로그램을 디자인 합니다. Premium Storage는 Azure Virtual Machines에서 실행되는 I/O 사용량이 많은 작업에 대해 대기 시간이 짧은 고성능 디스크 지원을 제공합니다.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 13430ff5f197560089ed184bc4a82f6a179ea3b7
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83117991"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium Storage: 고성능을 위한 설계
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> 경우에 따라 디스크 성능 문제로 보이는 것은 실제로 네트워크 병목 현상입니다. 이러한 상황에서 [네트워크 성능](../../virtual-network/virtual-network-optimize-network-bandwidth.md)을 최적화해야 합니다.
>
> 디스크 벤치 마크를 하려는 경우 [디스크 벤치마킹](disks-benchmarks.md)의 문서를 참조 하세요.
>
> VM에서 가속화된 네트워킹을 지원하는 경우 VM이 활성화되어 있는지 확인해야 합니다. 활성화되어 있지 않으면 [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) 및 [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) 모두에 이미 배포된 VM에서 활성화할 수 있습니다.

시작 하기 전에 Premium Storage을 처음 접하는 경우 먼저 [IaaS vm에 대 한 Azure 디스크 유형 선택](disks-types.md) 및 [프리미엄 페이지 blob 저장소 계정에 대 한 확장성 목표](../../storage/blobs/scalability-targets-premium-page-blobs.md)를 참조 하세요.


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

디스크 벤치 마크를 하려는 경우 [디스크 벤치마킹](disks-benchmarks.md)의 문서를 참조 하세요.

사용 가능한 디스크 유형에 대 한 자세한 정보: [디스크 유형 선택](disks-types.md)  

SQL Server 사용자의 경우 SQL Server에 대한 성능 모범 사례의 문서를 읽으세요.

* [Azure Virtual Machines에서 SQL Server에 대 한 성능 모범 사례](../windows/sql/virtual-machines-windows-sql-performance.md)
* [Azure Premium Storage는 Azure VM의 SQL Server에 대해 가장 높은 성능을 제공합니다](https://cloudblogs.microsoft.com/sqlserver/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm/)