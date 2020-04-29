---
title: HC 시리즈 VM 개요-Azure Virtual Machines | Microsoft Docs
description: Azure에서 HC 시리즈 VM 크기에 대 한 미리 보기 지원에 대해 알아봅니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76906752"
---
# <a name="hc-series-virtual-machine-overview"></a>HC 시리즈 가상 머신 개요

Intel Xeon 확장 가능한 프로세서에서 HPC 응용 프로그램 성능을 최대화 하려면이 새 아키텍처에서 배치를 처리 하는 방법을 신중 하 게 설정 해야 합니다. 여기서는 HPC 응용 프로그램에 대 한 Azure HC 시리즈 Vm의 구현에 대해 간략하게 설명 합니다. "PNUMA" 라는 용어를 사용 하 여 실제 NUMA 도메인을 참조 하 고 "vNUMA"를 사용 하 여 가상화 된 NUMA 도메인을 참조 합니다. 마찬가지로 "pCore" 라는 용어를 사용 하 여 실제 CPU 코어를 참조 하 고 "vCore"를 사용 하 여 가상화 된 CPU 코어를 참조 합니다.

물리적으로 HC 서버는 총 48 물리적 코어에 대해 2 * 24 코어 Intel Xeon Platinum 8168 Cpu입니다. 각 CPU는 단일 pNUMA 도메인 이며, 6 개의 DRAM 채널에 대 한 통합 액세스를 포함 합니다. Intel Xeon Platinum Cpu는 이전 세대 (256 k b/코어-> 1 m b/코어) 보다 더 큰 L2 캐시를 기능 하 고 이전 Intel Cpu (2.5 m b/코어-> 1.375 m b/코어)와 비교 하 여 L3 캐시를 줄일 수 있습니다.

위의 토폴로지가 HC 시리즈 하이퍼바이저 구성에도 전달 됩니다. VM을 방해 하지 않고 Azure 하이퍼바이저가 작동할 수 있는 공간을 제공 하기 위해 pCores 0-1 및 24-25 (즉, 각 소켓의 처음 2 개 코어)를 예약 합니다. 그런 다음, 나머지 코어를 모두 VM에 할당 합니다. 따라서 VM에 다음이 표시 됩니다.

`(2 vNUMA domains) * (22 cores/vNUMA) = 44`VM 당 코어 수

VM은 pCores 0-1 및 24-25이 제공 되지 않았다는 정보를 제공 하지 않습니다. 따라서 각 vNUMA를 기본적으로 22 개의 코어가 있는 것으로 노출 합니다.

Intel Xeon Platinum, Gold 및 은색 Cpu는 또한 CPU 소켓 내부 및 외부에서 통신에 대 한 다 대 다 2D 메시 네트워크를 도입 합니다. 최적의 성능과 일관성을 위해이를 고정 하는 것이 좋습니다. 기본 실리콘이 게스트 VM에 있는 그대로 노출 되기 때문에 HC 시리즈 Vm에서 프로세스 고정이 작동 합니다. 자세히 알아보려면 [Intel XEON SP 아키텍처](https://bit.ly/2RCYkiE)를 참조 하세요.

다음 다이어그램에서는 Azure 하이퍼바이저 및 HC 시리즈 VM 용으로 예약 된 코어의 분리 보여 줍니다.

![Azure 하이퍼바이저 및 HC 시리즈 VM 용으로 예약 된 코어 분리](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>하드웨어 사양

| 하드웨어 사양          | HC 시리즈 VM                     |
|----------------------------------|----------------------------------|
| 코어 수                            | 44 (HT 사용 안 함)                 |
| CPU                              | Intel Xeon 플래티넘 8168 *        |
| CPU 빈도 (비 AVX)          | 3.7 g h z (단일 코어), 2.7-3.4 GHz (모든 코어) |
| 메모리                           | 8gb/코어 (총 352)            |
| 로컬 디스크                       | 700 NVMe                      |
| Infiniband                       | 100 g b EDR Mellanox Connectx-3 * * |
| 네트워크                          | 50 g b 이더넷 (40 Gb 사용 가능) Azure second Gen SmartNIC * * * |

## <a name="software-specifications"></a>소프트웨어 사양

| 소프트웨어 사양     | HC 시리즈 VM          |
|-----------------------------|-----------------------|
| 최대 MPI 작업 크기            | 13200 코어 300 (단일 VMSS의 Vm Vm에서 Singleementgroup = true) |
| MPI 지원                 | MVAPICH2, OpenMPI, MPICH, 플랫폼 MPI, Intel MPI  |
| 추가 프레임 워크       | 통합 통신 X,을 (를) |
| 지원 Azure Storage       | 표준 + 프리미엄 (최대 4 개 디스크) |
| SRIOV RDMA에 대 한 OS 지원   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 + |
| Azure CycleCloud 지원    | 예                         |
| 지원 Azure Batch         | 예                         |

## <a name="next-steps"></a>다음 단계

* Azure의 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) 및 [WINDOWS](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) 용 HPC VM 크기에 대해 자세히 알아보세요.

* Azure의 [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) 에 대해 자세히 알아보세요.
