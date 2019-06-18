---
title: HB 시리즈 VM 개요-Azure Virtual Machines | Microsoft Docs
description: Azure에서 HB 시리즈 VM 크기에 대 한 미리 보기 지원에 알아봅니다.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/16/2019
ms.author: amverma
ms.openlocfilehash: bf143aa316a3d373a6303865cdc39ee0aaf27d87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66809919"
---
# <a name="hb-series-virtual-machines-overview"></a>HB-시리즈 virtual machines 개요

AMD EPYC에서 고성능 컴퓨팅 (HPC) 응용 프로그램 성능을 최대화 신중 하 게 접근 메모리 위치 및 프로세스 배치를 해야 합니다. 아래 AMD EPYC 아키텍처 및 구현은 Azure에서 HPC 응용 프로그램에 대해 설명합니다. 실제 NUMA 도메인 및 가상된 NUMA 도메인을 가리키도록 "vNUMA"를 참조 하려면 "pNUMA" 라는 용어가 사용 됩니다.

실제로 HB 시리즈는 2 * 64 실제 코어의 총 32 코어 EPYC 7551 Cpu입니다. 이러한 64 개의 코어가 각각은 4 개 코어 16 pNUMA 도메인 (소켓 당 8)으로 구분 되며를 "CPU 복잡 한" (또는 "CCX") 이라고 합니다. 각 CCX 자체 L3 캐시 하는 OS가 pNUMA/vNUMA 경계를 표시 하는 방법에 있습니다. 인접 한 CCXs 쌍 실제 DRAM (DRAM HB 시리즈 서버에서의 32GB)의 두 채널에 대 한 액세스를 공유합니다.

VM을 방해 하지 않고 작동 하도록 Azure 하이퍼바이저에 대 한 공간을 제공 하려면 실제 pNUMA 도메인 0 (첫 번째 CCX) 예약 했습니다. VM에 대 한 pNUMA 도메인 1-15 (나머지 CCX 단위)을 할당합니다. VM이 표시 됩니다.

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` VM 당 코어

VM 자체는 pNUMA 0을 지정 하지 않았습니다. 알 수 없습니다. VM에 0에서 8 vSocket vNUMA vSocket 1에서 7 vNUMA 사용 하 여 0-14 vNUMA로 pNUMA 1-15를 이해합니다. 비대칭 상태인 동안 OS 부팅 하 고 정상적으로 작동 해야 합니다. 이 가이드의 뒷부분에 나오는 하도록 지시 최상의 방법을이 비대칭 NUMA 레이아웃에서 MPI 응용 프로그램을 실행 합니다.

기본 실리콘으로 공개 하므로 HB 시리즈 Vm에서 작동 프로세스 고정-게스트 VM 하는 것입니다. 최적의 성능 및 일관성에 대 한 프로세스를 고정 하는 것이 좋습니다.

자세한 내용은의 참조 [AMD EPYC 아키텍처](https://bit.ly/2Epv3kC) 하 고 [다중 칩 아키텍처](https://bit.ly/2GpQIMb) LinkedIn에서. 자세한 내용은 참조는 [AMD EPYC 프로세서에 대 한 HPC 튜닝 가이드](https://bit.ly/2T3AWZ9)합니다.

다음 다이어그램 Azure 하이퍼바이저 및 HB 시리즈 VM에 대 한 예약 코어의 분리를 보여줍니다.

![Azure 하이퍼바이저 및 HB 시리즈 VM에 대 한 예약 코어의 분리](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>하드웨어 사양

| 하드웨어 사양                | HB 시리즈 VM                     |
|----------------------------------|----------------------------------|
| 코어 수                            | 60 (SMT 사용 하지 않도록 설정)                |
| CPU                              | AMD EPYC 7551*                   |
| CPU 주파수 (비 AVX)          | ~2.55 GHz (단일 + 모든 코어)   |
| 메모리                           | 4 GB/core (총 240)            |
| 로컬 디스크                       | 700GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5** |
| 네트워크                          | 50 10gb 이더넷 (40gb) 사용 가능한 Azure 두 번째 Gen SmartNIC * * * |

## <a name="software-specifications"></a>소프트웨어 사양

| SW 사양           |HB 시리즈 VM           |
|-----------------------------|-----------------------|
| 최대 MPI 작업 크기            | 6000 코어 (100 가상 머신 확장 집합) 12000 코어 (200 가상 머신 확장 집합)  |
| MPI 지원                 | MVAPICH2, OpenMPI MPICH MPI Intel MPI 플랫폼  |
| 추가 프레임 워크       | 통합된 통신 X libfabric, PGAS |
| Azure 저장소 지원       | 표준 + Premium (최대 4 개 디스크) |
| SRIOV RDMA에 대 한 OS 지원   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+  |
| Azure CycleCloud 지원    | 예                         |
| Azure Batch 지원         | 예                         |

## <a name="next-steps"></a>다음 단계

* 에 대 한 HPC VM 크기에 자세히 알아보려면 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) 하 고 [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) Azure에서.

* 에 대해 자세히 알아보세요 [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) Azure에서.
