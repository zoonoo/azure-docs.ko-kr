---
title: HB-시리즈 VM 개요-Azure Virtual Machines | Microsoft Docs
description: Azure에서 HB 시리즈 VM 크기에 대 한 미리 보기 지원에 대해 알아봅니다.
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2020
ms.locfileid: "67707769"
---
# <a name="hb-series-virtual-machines-overview"></a>HB-시리즈 가상 머신 개요

AMD EPYC에서 HPC (high performance compute) 응용 프로그램 성능을 최대화 하려면 메모리 위치 및 프로세스 배치를 신중 하 게 설정 해야 합니다. 아래에서는 Azure에서 HPC 응용 프로그램에 대 한 AMD EPYC 아키텍처 및 구현에 대해 간략하게 설명 합니다. "PNUMA" 라는 용어를 사용 하 여 실제 NUMA 도메인을 참조 하 고 "vNUMA"를 사용 하 여 가상화 된 NUMA 도메인을 참조 합니다.

실제로 HB 시리즈는 2 * 32-코어 EPYC 7551 Cpu로, 총 64 개의 실제 코어에 해당 합니다. 이러한 64 코어는 각각 4 개의 코어 이며 "CPU 복합" (또는 "CCX") 이라고 하는 16 개의 pNUMA 도메인 (소켓 당 8)로 나뉩니다. 각 CCX에는 자체 L3 캐시가 있으며,이는 OS에 pNUMA/vNUMA 경계가 표시 되는 방법입니다. 인접 한 쌍의 CCXs는 두 개의 물리적 DRAM 채널 (HB 시리즈 서버에서 32 GB의 DRAM)에 대 한 액세스를 공유 합니다.

VM을 방해 하지 않고 Azure 하이퍼바이저가 작동할 수 있는 공간을 제공 하기 위해 실제 pNUMA 도메인 0 (첫 번째 CCX)을 예약 합니다. 그런 다음 VM에 대 한 pNUMA 도메인 1-15 (나머지 CCX 단위)을 할당 합니다. VM에 다음이 표시 됩니다.

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`VM 당 코어 수

VM 자체는 pNUMA 0이 지정 되지 않았음을 인식 하지 못합니다. VM은 vNUMA 0-14로 pNUMA 1-15을 인식 하 고 vnuma 0에서 vnuma 7 개, vnuma 1에서 8 vnuma를 사용 합니다. 비대칭 이기는 하지만 OS는 정상적으로 부팅 하 고 작동 해야 합니다. 이 가이드의 뒷부분에서는이 비대칭 NUMA 레이아웃에서 MPI 응용 프로그램을 실행 하는 최선의 방법을 설명 합니다.

게스트 VM에 기본 실리콘를 그대로 노출 하기 때문에 프로세스 고정은 HB 시리즈 Vm에서 작동 합니다. 최적의 성능과 일관성을 위해이를 고정 하는 것이 좋습니다.

LinkedIn의 [AMD EPYC 아키텍처](https://bit.ly/2Epv3kC) 및 [다중 칩 아키텍처](https://bit.ly/2GpQIMb) 에 대 한 자세한 내용을 참조 하세요. 자세한 내용은 [AMD EPYC 프로세서에 대 한 HPC 튜닝 가이드](https://bit.ly/2T3AWZ9)를 참조 하세요.

다음 다이어그램에서는 Azure 하이퍼바이저 및 HB 시리즈 VM 용으로 예약 된 코어의 분리 보여 줍니다.

![Azure 하이퍼바이저 및 HB 시리즈 VM 용으로 예약 된 코어의 분리](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>하드웨어 사양

| HW 사양                | HB-시리즈 VM                     |
|----------------------------------|----------------------------------|
| 코어 수                            | 60 (SMT 사용 안 함)                |
| CPU                              | AMD EPYC 7551 *                   |
| CPU 빈도 (비 AVX)          | ~ 2.55 g h z (단일 + 모든 코어)   |
| 메모리                           | 4gb/코어 (총 240)            |
| 로컬 디스크                       | 700 NVMe                      |
| Infiniband                       | 100 g b EDR Mellanox Connectx-3 * * |
| 네트워크                          | 50 g b 이더넷 (40 Gb 사용 가능) Azure second Gen SmartNIC * * * |

## <a name="software-specifications"></a>소프트웨어 사양

| SW 사양           |HB-시리즈 VM           |
|-----------------------------|-----------------------|
| 최대 MPI 작업 크기            | 6000 코어 (100 가상 머신 확장 집합) 12000 코어 (200 virtual machine scale sets)  |
| MPI 지원                 | MVAPICH2, OpenMPI, MPICH, 플랫폼 MPI, Intel MPI  |
| 추가 프레임 워크       | 통합 통신 X,을 (를) |
| 지원 Azure Storage       | 표준 + 프리미엄 (최대 4 개 디스크) |
| SRIOV RDMA에 대 한 OS 지원   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +  |
| Azure CycleCloud 지원    | 예                         |
| 지원 Azure Batch         | 예                         |

## <a name="next-steps"></a>다음 단계

* Azure의 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) 및 [WINDOWS](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) 용 HPC VM 크기에 대해 자세히 알아보세요.

* Azure의 [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) 에 대해 자세히 알아보세요.
