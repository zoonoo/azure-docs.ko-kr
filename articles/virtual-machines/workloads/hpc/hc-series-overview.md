---
title: HC 시리즈 VM 개요-Azure Virtual Machines | Microsoft Docs
description: Azure에서 HC 시리즈 VM 크기에 대 한 미리 보기 지원에 알아봅니다.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: f96a1179c103dd9dfb4d358572f9a9adbe24b977
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66809874"
---
# <a name="hc-series-virtual-machine-overview"></a>HC 시리즈 가상 머신 개요

이 새로운 아키텍처에 배치 프로세스를 신중 하 게 접근을 해야 Intel Xeon 확장 가능한 프로세서에서 HPC 응용 프로그램 성능을 최대화 합니다. 여기에서 HPC 응용 프로그램에 대해 Azure HC 시리즈 Vm에서의 구현에 설명 합니다. 실제 NUMA 도메인 및 가상된 NUMA 도메인을 가리키도록 "vNUMA"를 참조 하려면 "pNUMA" 라는 용어가 사용 됩니다. 마찬가지로 실제 CPU 코어를 가리키도록 "pCore" 라는 용어를 사용할지 하 고 "vCore"를 가리키는 데 CPU 코어를 가상화 합니다.

실제로 HC 서버는 2 * 48 실제 코어의 총 24 코어 Intel Xeon 플래티넘 8168 Cpu입니다. 각 CPU 단일 pNUMA 도메인 이며 DRAM의 6 개 채널에 대 한 액세스를 통합 했습니다. 이전 세대 (KB/코어-코어 1 MB를 >), Intel Cpu를 이전에 비해 L3 캐시를 줄이는 동시에 4 보다 큰 L2 캐시가 x는 Intel Xeon 플래티넘 Cpu 기능 (2.5 MB/코어-1.375 MB/코어를 >).

위의 토폴로지 HC 시리즈 하이퍼바이저 구성을 전달합니다. VM을 방해 하지 않고 작동 하도록 Azure 하이퍼바이저에 대 한 공간을 제공 하려면 pCores 0-1 및 24-25 (즉, 각 소켓의 처음 2 pCores) 예약 했습니다. 에서는 다음 할당할 pNUMA 도메인 모든 나머지 코어 VM. 따라서 VM이 표시 됩니다.

`(2 vNUMA domains) * (22 cores/vNUMA) = 44` VM 당 코어

VM에 0-1 pCores 및 24-25 권한을 부여 하지 않은 알지 못합니다. 따라서 22 개 코어를 고유 하 게 되었을 것 처럼 각 vNUMA 노출 합니다.

Intel Xeon Platinum, Gold 및 Silver Cpu CPU 소켓 내 통신 및 외부 다 2D 메시 네트워크를 소개합니다. 최적의 성능 및 일관성에 대 한 프로세스를 고정 하는 것이 좋습니다. 기본 실리콘으로 노출 되기 때문에 HC 시리즈 Vm에서 작동 프로세스 고정-게스트 VM 하는 것입니다. Intel Xeon SP 아키텍처 방법은: https://bit.ly/2RCYkiE

다음 다이어그램 Azure 하이퍼바이저 및 HC 시리즈 VM에 대 한 예약 코어의 분리를 보여줍니다.

![Azure 하이퍼바이저 및 HC 시리즈 VM에 대 한 예약 코어의 분리](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>하드웨어 사양

| 하드웨어 사양          | HC 시리즈 VM                     |
|----------------------------------|----------------------------------|
| 코어 수                            | 40 (HT 사용 하지 않도록 설정)                 |
| CPU                              | Intel Xeon 플래티넘 8168 *        |
| CPU 주파수 (비 AVX)          | 3.7ghz (단일 코어) 2.7-3.4 GHz (모든 코어) |
| 메모리                           | 8 GB/core (총 352)            |
| 로컬 디스크                       | 700GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5** |
| 네트워크                          | 50 10gb 이더넷 (40gb) 사용 가능한 Azure 두 번째 Gen SmartNIC * * * |

## <a name="software-specifications"></a>소프트웨어 사양

| 소프트웨어 사양     | HC 시리즈 VM          |
|-----------------------------|-----------------------|
| 최대 MPI 작업 크기            | 4400 코어 (100 virtual machine scale sets) 8800 코어 (200 가상 머신 확장 집합) |
| MPI 지원                 | MVAPICH2, OpenMPI MPICH MPI Intel MPI 플랫폼  |
| 추가 프레임 워크       | 통합된 통신 X libfabric, PGAS |
| Azure 저장소 지원       | 표준 + Premium (최대 4 개 디스크) |
| SRIOV RDMA에 대 한 OS 지원   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+ |
| Azure CycleCloud 지원    | 예                         |
| Azure Batch 지원         | 예                         |

## <a name="next-steps"></a>다음 단계

* 에 대 한 HPC VM 크기에 자세히 알아보려면 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) 하 고 [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) Azure에서.

* 에 대해 자세히 알아보세요 [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) Azure에서.
