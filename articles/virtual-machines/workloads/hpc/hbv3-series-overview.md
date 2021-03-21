---
title: HBv3 시리즈 VM 개요-Azure Virtual Machines | Microsoft Docs
description: Azure에서 HBv3 시리즈 VM 크기에 대해 알아봅니다.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: b1f2800c3787cd28437afa70b78ef8388461e413
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721180"
---
# <a name="hbv3-series-virtual-machine-overview"></a>HBv3 시리즈 가상 머신 개요 

[HBv3 시리즈](../../hbv3-series.md) 서버 기능 2 * 64-코어 Epyc 7V13 cpu는 총 128 물리적 "Zen3" 코어에 해당 합니다. HBv3에서는 SMT (동시 다중 스레딩)을 사용할 수 없습니다. 이러한 128 코어는 16 개 섹션 (소켓 당 8 개)으로 나뉘어 각 섹션에는 32 MB L3 캐시에 균일 하 게 액세스 하는 8 개의 프로세서 코어가 포함 되어 있습니다. Azure HBv3 서버는 다음 AMD BIOS 설정도 실행 합니다.

```bash
Nodes per Socket =2
L3 as NUMA = Disabled
```

따라서 서버는 32-코어 마다 4 개의 NUMA 도메인 (소켓 당 2)으로 부팅 합니다. 각 NUMA는 3200 MT/s에서 작동 하는 4 개의 물리적 DRAM 채널에 직접 액세스할 수 있습니다.

Azure 하이퍼바이저가 VM을 방해 하지 않고 작동할 수 있는 공간을 제공 하기 위해 서버당 8 개의 실제 코어를 예약 합니다. 

제약 된 코어 VM 크기는 VM에 노출 되는 실제 코어의 수를 줄입니다. 모든 전역 공유 자산 (RAM, 메모리 대역폭, L3 캐시, GMI 및 xGMI 연결, InfiniBand, Azure 이더넷 네트워크, 로컬 SSD)은 일정 하 게 유지 됩니다. 이를 통해 고객은 지정 된 워크 로드 또는 소프트웨어 라이선스 요구 사항 집합에 가장 적합 한 VM 크기를 선택할 수 있습니다.

## <a name="infiniband-networking"></a>InfiniBand 네트워킹
HBv3 Vm은 200 기가 비트/sec로 작동 하는 Nvidia Mellanox HDR InfiniBand 네트워크 어댑터 (Connectx-3)도 작동 합니다. NIC는 SRIOV을 통해 VM으로 전달 되어 네트워크 트래픽이 하이퍼바이저를 우회 하도록 합니다. 따라서 고객은 운영 체제 미 설치 환경 처럼 HBv3 Vm에서 표준 Mellanox OFED 드라이버를 로드 합니다.

HBv3 Vm은 적응 라우팅, 동적으로 연결 된 전송 (DCT, 표준 RC 및 UD 전송에 대 한 추가) 및 MPI collectives의 하드웨어 기반 오프 로드를 Connectx-3 어댑터의 온보드 프로세서로 지원 합니다. 이러한 기능은 응용 프로그램의 성능, 확장성 및 일관성을 향상 시키고 이러한 기능을 사용 하는 것이 좋습니다.

## <a name="temporary-storage"></a>임시 스토리지
HBv3 Vm은 물리적 로컬 SSD 장치를 세는 것입니다. 한 장치는 페이지 파일 역할을 하도록 미리 정의 되며, 일반 "SSD" 장치로 VM 내에 표시 됩니다.

NVMeDirect를 통해 형식이 지정 되지 않은 블록 NVMe 장치로 두 개의 더 큰 Ssd를 제공 합니다. 블록 NVMe 장치는 하이퍼바이저를 우회 하므로 더 높은 대역폭, 높은 IOPS 및 IOP 당 대기 시간이 줄어듭니다.

스트라이프 배열에서 쌍으로 연결 된 경우 NVMe SSD는 최대 7gb/s 읽기 및 3 g b/초를 제공 하 고 전체 큐 깊이에 대해 최대 186000 IOPS (읽기) 및 201000 IOPS (쓰기)를 제공 합니다.

## <a name="hardware-specifications"></a>하드웨어 사양 

| 하드웨어 사양          | HBv3 시리즈 Vm              |
|----------------------------------|----------------------------------|
| 코어                            | 120, 96, 64, 32 또는 16 (SMT 사용 안 함)               | 
| CPU                              | AMD EPYC 7V13                   | 
| CPU 빈도 (비 AVX)          | 3.1 g h z (모든 코어), 3.675 GHz (최대 10 개 코어)    | 
| 메모리                           | 448 GB (코어 당 RAM이 VM 크기에 따라 다름)         | 
| 로컬 디스크                       | 2 * 960 GB NVMe (차단), 480 GB SSD (페이지 파일) | 
| Infiniband                       | 200 g b/초 Mellanox Connectx-3-6 HDR InfiniBand | 
| 네트워크                          | 50 g b/s 이더넷 (40 g b/초 사용 가능) Azure second Gen SmartNIC | 

## <a name="software-specifications"></a>소프트웨어 사양 

| 소프트웨어 사양        | HBv3 시리즈 Vm                                            | 
|--------------------------------|-----------------------------------------------------------|
| 최대 MPI 작업 크기               | 36000 코어 (단일 가상 머신 확장 집합에서 단일 가상 머신 확장 집합의 300 Vm (Singleementgroup = true)) |
| MPI 지원                    | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH  |
| 추가 프레임 워크          | 로 x,을 (를)                  |
| 지원 Azure Storage          | Standard 및 Premium 디스크 (최대 32 디스크)              |
| SRIOV RDMA에 대 한 OS 지원      | CentOS/RHEL 7.6 +, Ubuntu 18.04 +, SLES 12 SP4 +, WinServer 2016 이상           |
| 성능에 권장 되는 OS | CentOS 8.1, Windows Server 2019 +
| Orchestrator 지원           | Azure CycleCloud, Azure Batch, AKS; [클러스터 구성 옵션](../../sizes-hpc.md#cluster-configuration-options)                      | 

> [!NOTE] 
> Windows Server 2012 r 2는 HBv3 및 64 (가상 또는 실제) 코어가 있는 다른 Vm에서 지원 되지 않습니다. 자세한 내용은 [여기](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항, HPC 워크 로드 예제 및 성능 결과에 대해 읽어 보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.