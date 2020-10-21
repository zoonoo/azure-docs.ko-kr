---
title: HBv2 시리즈 VM 개요-Azure Virtual Machines | Microsoft Docs
description: Azure에서 HBv2 시리즈 VM 크기에 대해 알아봅니다.
services: virtual-machines
author: vermagit
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 48366f205ed8eb2d179bdc39c8da3d673f066a69
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92332639"
---
# <a name="hbv2-series-virtual-machine-overview"></a>HBv2 시리즈 가상 머신 개요 

 
AMD EPYC에서 HPC (high performance compute) 응용 프로그램 성능을 최대화 하려면 메모리 위치 및 프로세스 배치를 신중 하 게 설정 해야 합니다. 아래에서는 Azure에서 HPC 응용 프로그램에 대 한 AMD EPYC 아키텍처 및 구현에 대해 간략하게 설명 합니다. 여기서는 **Pnuma** 라는 용어를 사용 하 여 실제 numa 도메인을 참조 하 고 **vnuma** 는 가상화 된 numa 도메인을 참조 합니다. 

물리적으로 [HBv2 시리즈](../../hbv2-series.md) 서버는 총 128 개의 실제 코어에 대해 2 * 64-코어 epyc 7742 cpu입니다. 이러한 128 코어는 32 pNUMA 도메인 (소켓 당 16 개)으로 구분 됩니다. 각각은 4 코어이 고 AMD는 **코어 복합** (또는 **CCX**)로 표현 됩니다. 각 CCX에는 자체 L3 캐시가 있으며,이는 OS에 pNUMA/vNUMA 경계가 표시 되는 방법입니다. 네 개의 인접 CCXs는 물리적 DRAM의 2 개 채널에 대 한 액세스를 공유 합니다. 

VM을 방해 하지 않고 Azure 하이퍼바이저가 작동할 수 있는 공간을 제공 하기 위해 실제 pNUMA 도메인 0 및 16 (즉, 각 CPU 소켓의 첫 번째 CCX)을 예약 합니다. 남아 있는 모든 30 개의 pNUMA 도메인은 VM에 할당 됩니다. 여기서 VM은 vNUMA가 됩니다. 따라서 VM에 다음이 표시 됩니다.

`(30 vNUMA domains) * (4 cores/vNUMA) = 120` VM 당 코어 수 

VM 자체는 pNUMA 0 및 16이 예약 된다는 것을 인식 하지 못합니다. 0-29로 표시 되는 vNUMA를 열거 합니다 .이는 소켓 당 15 개의 vNUMA, vnuma 0의 vNUMA 0-14, Vnuma 1의 vnuma 15-29 다음 섹션에는이 비대칭 NUMA 레이아웃에서 MPI 응용 프로그램을 실행 하는 최상의 방법에 대 한 지침이 있습니다. 

게스트 VM에 기본 실리콘를 그대로 노출 하기 때문에 프로세스 고정은 HBv2 시리즈 Vm에서 작동 합니다. 최적의 성능과 일관성을 위해이를 고정 하는 것이 좋습니다. 


## <a name="hardware-specifications"></a>하드웨어 사양 

| 하드웨어 사양          | HBv2 시리즈 VM                   | 
|----------------------------------|----------------------------------|
| 코어                            | 120 (SMT 사용 안 함)               | 
| CPU                              | AMD EPYC 7742                    | 
| CPU 빈도 (비 AVX)          | ~ 3.1 g h z (단일 + 모든 코어)    | 
| 메모리                           | 4gb/코어 (480 총 GB)         | 
| 로컬 디스크                       | 960 GB NVMe (블록), 480 GB SSD (페이지 파일) | 
| Infiniband                       | 200 g b/초 EDR Mellanox Connectx-3-6 | 
| 네트워크                          | 50 g b/s 이더넷 (40 g b/초 사용 가능) Azure second Gen SmartNIC | 


## <a name="software-specifications"></a>소프트웨어 사양 

| 소프트웨어 사양     | HBv2 시리즈 VM                                            | 
|-----------------------------|-----------------------------------------------------------|
| 최대 MPI 작업 크기            | 36000 코어 (단일 가상 머신 확장 집합에서 단일 가상 머신 확장 집합의 300 Vm (Singleementgroup = true)) |
| MPI 지원                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, Platform MPI  |
| 추가 프레임 워크       | 통합 통신 X,을 (를)                  |
| 지원 Azure Storage       | Standard 및 Premium 디스크 (최대 8 개 디스크)              |
| SRIOV RDMA에 대 한 OS 지원   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +           |
| Orchestrator 지원        | CycleCloud, Batch                                         | 


## <a name="next-steps"></a>다음 단계

- [AMD EPYC 아키텍처](https://bit.ly/2Epv3kC) 및 [다중 칩 아키텍처](https://bit.ly/2GpQIMb)에 대해 자세히 알아보세요. 자세한 내용은 [AMD EPYC 프로세서에 대 한 HPC 튜닝 가이드](https://bit.ly/2T3AWZ9)를 참조 하세요.
- [Azure Compute 기술 커뮤니티 블로그의](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)최신 발표 및 일부 HPC 예제에 대해 읽어 보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.