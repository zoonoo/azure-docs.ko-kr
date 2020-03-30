---
title: HC 시리즈 VM 개요 - Azure 가상 머신| 마이크로 소프트 문서
description: Azure의 HC 시리즈 VM 크기에 대한 미리 보기 지원에 대해 알아봅니다.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: a4cd74c9c85ee7413cde9f0fb4cf3ffb54c9b3d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906752"
---
# <a name="hc-series-virtual-machine-overview"></a>HC 시리즈 가상 머신 개요

인텔 제온 확장형 프로세서에서 HPC 응용 프로그램 성능을 최대화하려면 이 새로운 아키텍처에 대한 배치를 처리하는 신중한 접근 방식이 필요합니다. 여기서는 HPC 응용 프로그램에 대한 Azure HC 시리즈 VM에서 구현을 간략하게 설명합니다. "pNUMA"라는 용어를 사용하여 실제 NUMA 도메인을 참조하고 "vNUMA"는 가상화된 NUMA 도메인을 참조합니다. 마찬가지로 실제 CPU 코어를 참조하기 위해 "pCore"라는 용어와 가상화된 CPU 코어를 참조하는 "vCore"를 사용합니다.

물리적으로 HC 서버는 2 * 24 코어 인텔 제온 플래티넘 8168 CPU총 48 물리적 코어입니다. 각 CPU는 단일 pNUMA 도메인이며 6개의 DRAM 채널에 대한 통합 액세스 권한을 가수 있습니다. 인텔 제온 플래티넘 CPU는 이전 세대(256KB/코어-> 1MB/코어)보다 4배 더 큰 L2 캐시를 갖추고 있으며, 이전 인텔 CPU(2.5MB/코어-> 1.375MB/코어)에 비해 L3 캐시를 줄였습니다.

위의 토폴로지도 HC 시리즈 하이퍼바이저 구성으로 이월됩니다. Azure 하이퍼바이저가 VM을 방해하지 않고 작동할 수 있는 공간을 제공하기 위해 pCores 0-1 및 24-25(즉, 각 소켓의 처음 2pCore)를 예약합니다. 그런 다음 pNUMA 도메인에 남아 있는 모든 코어를 VM에 할당합니다. 따라서 VM은 다음을 볼 수 있습니다.

`(2 vNUMA domains) * (22 cores/vNUMA) = 44`VM당 코어

VM은 pCores 0-1 및 24-25가 제공되지 않았다는 것을 알지 못합니다. 따라서 기본적으로 22 개의 코어를 가진 것처럼 각 vNUMA를 노출합니다.

인텔 제온 플래티넘, 골드, 실버 CPU는 또한 CPU 소켓 내부 및 외부 통신을 위한 온다이 2D 메쉬 네트워크를 소개합니다. 최적의 성능과 일관성을 위해 프로세스 고정을 강력히 권장합니다. 기본 실리콘이 게스트 VM에 있는 것처럼 노출되기 때문에 프로세스 고정은 HC 시리즈 VM에서 작동합니다. 자세한 내용은 [인텔 제온 SP 아키텍처를](https://bit.ly/2RCYkiE)참조하십시오.

다음 다이어그램은 Azure Hypervisor 및 HC 시리즈 VM용으로 예약된 코어의 분리를 보여 주며,

![Azure 하이퍼바이저 및 HC 시리즈 VM용으로 예약된 코어 분리](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>하드웨어 사양

| 하드웨어 사양          | HC 시리즈 VM                     |
|----------------------------------|----------------------------------|
| 코어 수                            | 44(HT 사용 안 함)                 |
| CPU                              | 인텔 제온 플래티넘 8168*        |
| CPU 주파수(AVX 가외)          | 3.7GHz(단일 코어), 2.7-3.4GHz(모든 코어) |
| 메모리                           | 8GB/코어(총 352개)            |
| 로컬 디스크                       | 700GB NVMe                      |
| Infiniband                       | 100 Gb EDR 멜라녹스 커넥트X-5** |
| 네트워크                          | 50Gb 이더넷(40Gb 사용 가능) Azure 2세대 SmartNIC*** |

## <a name="software-specifications"></a>소프트웨어 사양

| 소프트웨어 사양     | HC 시리즈 VM          |
|-----------------------------|-----------------------|
| 최대 MPI 작업 크기            | 13200코어(단일 배치그룹=true가 있는 단일 VMSS에서 300VM) |
| MPI 지원                 | MVAPICH2, 오픈MPI, MPICH, 플랫폼 MPI, 인텔 MPI  |
| 추가 프레임워크       | 통합 커뮤니케이션 X, 리패브릭, PGAS |
| Azure 저장소 지원       | Std + 프리미엄(최대 4개의 디스크) |
| SRIOV RDMA에 대한 OS 지원   | CentOS /RHEL 7.6+, SLES 12 SP4+, 윈서버 2016+ |
| Azure 사이클클라우드 지원    | yes                         |
| Azure 일괄 처리 지원         | yes                         |

## <a name="next-steps"></a>다음 단계

* Azure의 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) 및 [Windows용](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) HPC VM 크기에 대해 자세히 알아봅니다.

* Azure에서 [HPC에](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) 대해 자세히 알아보세요.
