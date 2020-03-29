---
title: HB 시리즈 VM 개요 - Azure 가상 머신 | 마이크로 소프트 문서
description: Azure에서 HB 시리즈 VM 크기에 대한 미리 보기 지원에 대해 알아봅니다.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/16/2019
ms.author: amverma
ms.openlocfilehash: 62e4d3dbd7357f8c98df3307c1c8fe52cbed1c5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707769"
---
# <a name="hb-series-virtual-machines-overview"></a>HB 시리즈 가상 머신 개요

AMD EPYC에서 고성능 컴퓨팅(HPC) 애플리케이션 성능을 최대화하려면 신중한 접근 방식 메모리 위치 및 프로세스 배치가 필요합니다. 아래에서는 AMD EPYC 아키텍처와 HPC 용 Azure 응용 프로그램에 대한 아키텍처 구현에 대해 간략하게 설명합니다. "pNUMA"라는 용어를 사용하여 실제 NUMA 도메인을 참조하고 "vNUMA"는 가상화된 NUMA 도메인을 참조합니다.

물리적으로 HB 시리즈는 총 64개의 물리적 코어에 대해 2 * 32코어 EPYC 7551 CPU입니다. 이 64 개의 코어는 16 pNUMA 도메인 (소켓 당 8 개)으로 나뉘며, 각각 4 개의 코어이며 "CPU 컴플렉스"(또는 "CCX")로 알려져 있습니다. 각 CCX에는 자체 L3 캐시가 있으며, 이는 OS가 pNUMA/vNUMA 경계를 보는 방법입니다. 인접한 CCX 한 쌍은 물리적 DRAM의 두 채널(HB 시리즈 서버의 DRAM 32GB)에 대한 액세스를 공유합니다.

Azure 하이퍼바이저가 VM을 방해하지 않고 작동할 수 있는 공간을 제공하기 위해 물리적 pNUMA 도메인 0(첫 번째 CCX)을 예약합니다. 그런 다음 VM에 pNUMA 도메인 1-15(나머지 CCX 단위)를 할당합니다. VM은 다음을 볼 수 있습니다.

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`VM당 코어

VM 자체는 pNUMA 0이 제공되지 않았다는 것을 알지 못합니다. VM은 pNUMA 1-15를 vNUMA 0-14로 이해하고 vSocket 0에서는 7vNUMA, vSocket 1에서는 8vNUMA를 이해합니다. 비대칭이지만 OS는 정상적으로 부팅되고 작동해야 합니다. 이 가이드의 후반부에서는 이 비대칭 NUMA 레이아웃에서 MPI 응용 프로그램을 실행하는 것이 가장 좋은 방법을 설명합니다.

프로세스 고정은 기본 실리콘을 게스트 VM에 있는 것처럼 노출하기 때문에 HB 시리즈 VM에서 작동합니다. 최적의 성능과 일관성을 위해 프로세스 고정을 강력히 권장합니다.

링크드인에서 [AMD EPYC 아키텍처](https://bit.ly/2Epv3kC) 및 [멀티 칩 아키텍처에](https://bit.ly/2GpQIMb) 대한 자세한 내용은 확인할 수 있습니다. 자세한 내용은 [AMD EPYC 프로세서에 대한 HPC 튜닝 가이드를](https://bit.ly/2T3AWZ9)참조하십시오.

다음 다이어그램은 Azure Hypervisor 및 HB 시리즈 VM용으로 예약된 코어의 분리를 보여 주며,

![Azure 하이퍼바이저 및 HB 시리즈 VM용으로 예약된 코어 분리](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>하드웨어 사양

| HW 사양                | HB 시리즈 VM                     |
|----------------------------------|----------------------------------|
| 코어 수                            | 60(SMT 사용 안 함)                |
| CPU                              | AMD EPYC 7551*                   |
| CPU 주파수(AVX 가외)          | ~ 2.55 GHz의 (단일 + 모든 코어)   |
| 메모리                           | 4GB/코어(총 240개)            |
| 로컬 디스크                       | 700GB NVMe                      |
| Infiniband                       | 100 Gb EDR 멜라녹스 커넥트X-5** |
| 네트워크                          | 50Gb 이더넷(40Gb 사용 가능) Azure 2세대 SmartNIC*** |

## <a name="software-specifications"></a>소프트웨어 사양

| SW 사양           |HB 시리즈 VM           |
|-----------------------------|-----------------------|
| 최대 MPI 작업 크기            | 6000 코어(가상 머신 스케일 세트 100개) 12000코어(가상 머신 스케일 세트 200개)  |
| MPI 지원                 | MVAPICH2, 오픈MPI, MPICH, 플랫폼 MPI, 인텔 MPI  |
| 추가 프레임워크       | 통합 커뮤니케이션 X, 리패브릭, PGAS |
| Azure 저장소 지원       | Std + 프리미엄(최대 4개의 디스크) |
| SRIOV RDMA에 대한 OS 지원   | CentOS /RHEL 7.6+, SLES 12 SP4+, 윈서버 2016+  |
| Azure 사이클클라우드 지원    | yes                         |
| Azure 일괄 처리 지원         | yes                         |

## <a name="next-steps"></a>다음 단계

* Azure의 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) 및 [Windows용](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) HPC VM 크기에 대해 자세히 알아봅니다.

* Azure에서 [HPC에](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) 대해 자세히 알아보세요.
