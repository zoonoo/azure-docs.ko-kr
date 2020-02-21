---
title: Azure VM 크기-저장소 | Microsoft Docs
description: Azure의 가상 컴퓨터에 사용할 수 있는 다양 한 저장소 최적화 크기를 나열 합니다. 이 시리즈의 크기에 대 한 저장소 처리량 및 네트워크 대역폭 뿐만 아니라 vCPUs, 데이터 디스크 및 Nic의 수에 대 한 정보를 나열 합니다.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 17cceb04d3d6e559cd6f2b49674f6d408a0650a4
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492995"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>스토리지 최적화 가상 컴퓨터 크기

저장소에 최적화 된 VM 크기는 높은 디스크 처리량 및 IO를 제공 하며 빅 데이터, SQL, NoSQL 데이터베이스, 데이터 웨어하우징 및 대규모 트랜잭션 데이터베이스에 적합 합니다.  예로는 Cassandra, MongoDB, Cloudera 및 Redis가 있습니다. 이 문서에서는 각 최적화 된 크기에 대 한 로컬 저장소 처리량 및 네트워크 대역폭 뿐만 아니라 vCPUs, 데이터 디스크 및 Nic의 수에 대 한 정보를 제공 합니다.

[Lsv2 시리즈](lsv2-series.md) 는 높은 처리량, 짧은 대기 시간, [AMD epyc<sup>TM</sup> 7551 프로세서](https://www.amd.com/en/products/epyc-7000-series) 에서 실행 되는 로컬 NVMe 저장소를 2.55 ghz의 모든 코어 부스트와 3.0 g h z의 최대 상승 기능으로 제공 합니다. Lsv2 시리즈 VM은 동시 다중 스레딩 구성을 통해 8~80vCPU 크기로 제공됩니다.  vCPU당 8GiB 메모리, 8vCPU당 1.92TB NVMe SSD M.2 디바이스 한 대가 있으며, L80s v2에서 최대 19.2TB(10x1.92TB)를 사용할 수 있습니다.

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [컴퓨팅 최적화](sizes-compute.md)
- [메모리에 최적화](sizes-memory.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.

[Windows](windows/storage-performance.md) 또는 [Linux](linux/storage-performance.md)용 Lsv2 시리즈 가상 머신에서 성능을 최적화 하는 방법에 대해 알아봅니다.