---
title: 'Azure Premium Storage: Windows VM의 성능을 위한 설계 | Microsoft Docs'
description: Azure Premium Storage를 사용하여 고성능 애플리케이션을 설계합니다. Premium Storage는 Azure Virtual Machines에서 실행되는 I/O 사용량이 많은 작업에 대해 대기 시간이 짧은 고성능 디스크 지원을 제공합니다.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 3979920cac8e3ef856b3211733f08c21afac3e26
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693690"
---
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> 경우에 따라 디스크 성능 문제로 보이는 것은 실제로 네트워크 병목 현상입니다. 이러한 상황에서 [네트워크 성능](../../virtual-network/virtual-network-optimize-network-bandwidth.md)을 최적화해야 합니다.
>
> 디스크 벤치 마크를 하려는 경우 [디스크 벤치마킹](disks-benchmarks.md)의 문서를 참조 하세요.
>
> VM에서 가속화된 네트워킹을 지원하는 경우 VM이 활성화되어 있는지 확인해야 합니다. 활성화되어 있지 않으면 [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) 및 [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) 모두에 이미 배포된 VM에서 활성화할 수 있습니다.

시작 하기 전에 Premium Storage을 처음 접하는 경우 먼저 [IaaS vm에 대 한 Azure 디스크 유형 선택](disks-types.md) 및 [저장소 계정에 대 한 확장성 및 성능 목표 Azure Storage](../../storage/common/storage-scalability-targets.md)를 참조 하세요.

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]
