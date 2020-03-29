---
title: Azure VM 크기 - 저장소 | 마이크로 소프트 문서
description: Azure의 가상 컴퓨터에 사용할 수 있는 다양한 저장소 최적화 크기를 나열합니다. 이 시리즈의 크기에 대한 vCPU, 데이터 디스크 및 NIC 수와 스토리지 처리량 및 네트워크 대역폭에 대한 정보를 나열합니다.
services: virtual-machines
documentationcenter: ''
author: sasha-melamed
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
ms.openlocfilehash: 5a611dc288fc18a14f6000689f9f9b49d4b3feb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913339"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>스토리지 최적화 가상 컴퓨터 크기

스토리지에 최적화된 VM 크기는 높은 디스크 처리량과 IO를 제공하며 빅 데이터, SQL, NoSQL 데이터베이스, 데이터 웨어하우징 및 대규모 트랜잭션 데이터베이스에 이상적입니다.  예를 들어 카산드라, 몽고DB, 클라우데라 및 레디스가 있습니다. 이 문서에서는 vCPU, 데이터 디스크 및 NIC 수와 최적화된 각 크기에 대한 로컬 저장소 처리량 및 네트워크 대역폭에 대한 정보를 제공합니다.

[Lsv2 시리즈는](lsv2-series.md) 높은 처리량, 낮은 대기 시간, [AMD EPYC<sup>TM</sup> 7551 프로세서에서](https://www.amd.com/en/products/epyc-7000-series) 실행되는 로컬 NVMe 스토리지를 직접 매핑하여 2.55GHz의 모든 코어 부스트와 3.0GHz의 최대 부스트를 제공합니다. Lsv2 시리즈 VM은 동시 다중 스레딩 구성을 통해 8~80vCPU 크기로 제공됩니다.  vCPU당 8GiB 메모리, 8vCPU당 1.92TB NVMe SSD M.2 디바이스 한 대가 있으며, L80s v2에서 최대 19.2TB(10x1.92TB)를 사용할 수 있습니다.

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [컴퓨팅 최적화](sizes-compute.md)
- [메모리 최적화](sizes-memory.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.

[Windows](windows/storage-performance.md) 또는 [Linux용](linux/storage-performance.md)Lsv2 시리즈 가상 컴퓨터에서 성능을 최적화하는 방법에 대해 알아봅니다.
