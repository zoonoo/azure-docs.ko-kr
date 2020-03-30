---
title: 'Azure 프리미엄 스토리지: Linux VM에서 성능을 위한 디자인 | 마이크로 소프트 문서'
description: Azure 프리미엄 SSD 관리 디스크를 사용하여 고성능 응용 프로그램을 디자인합니다. Premium Storage는 Azure Virtual Machines에서 실행되는 I/O 사용량이 많은 작업에 대해 대기 시간이 짧은 고성능 디스크 지원을 제공합니다.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 9940ee4cfce9721ac65f2b3cf1469e180adfa098
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267145"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium Storage: 고성능을 위한 설계
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> 경우에 따라 디스크 성능 문제로 보이는 것은 실제로 네트워크 병목 현상입니다. 이러한 상황에서 [네트워크 성능](../../virtual-network/virtual-network-optimize-network-bandwidth.md)을 최적화해야 합니다.
>
> 디스크벤치마킹을 원할 경우 디스크 [벤치마킹에](disks-benchmarks.md)대한 도움말을 참조하십시오.
>
> VM에서 가속화된 네트워킹을 지원하는 경우 VM이 활성화되어 있는지 확인해야 합니다. 활성화되어 있지 않으면 [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) 및 [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) 모두에 이미 배포된 VM에서 활성화할 수 있습니다.

시작하기 전에 프리미엄 저장소를 처음 사용하는 경우 먼저 프리미엄 페이지 Blob 저장소 계정에 [대한 IaaS VM](disks-types.md) 및 [확장성 대상에 대한](../../storage/blobs/scalability-targets-premium-page-blobs.md)Azure 디스크 유형 선택을 읽어보십시오.


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

디스크벤치마킹을 원할 경우 디스크 [벤치마킹에](disks-benchmarks.md)대한 도움말을 참조하십시오.

사용 가능한 디스크 유형에 대해 자세히 알아보기: [디스크 유형 선택](disks-types.md)  

SQL Server 사용자의 경우 SQL Server에 대한 성능 모범 사례의 문서를 읽으세요.

* [Azure 가상 시스템의 SQL 서버에 대한 성능 모범 사례](../windows/sql/virtual-machines-windows-sql-performance.md)
* [Azure Premium Storage는 Azure VM의 SQL Server에 대해 가장 높은 성능을 제공합니다](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)