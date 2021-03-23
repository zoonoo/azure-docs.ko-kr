---
title: Azure VM 크기-HPC | Microsoft Docs
description: Azure에서 고성능 컴퓨팅 가상 머신에 사용할 수 있는 다양 한 크기를 나열 합니다. 이 시리즈의 크기에 대한 스토리지 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/19/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: a41dce28427db40dfd19879e4ada95add64009c3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772436"
---
# <a name="high-performance-computing-vm-sizes"></a>고성능 컴퓨팅 VM 크기

Azure H 시리즈 Vm (가상 머신)은 다양 한 실제 HPC 워크 로드에 대 한 리더십 클래스 성능, 확장성 및 비용 효율성을 제공 하도록 설계 되었습니다.

[HBv3 시리즈](hbv3-series.md) Vm은 유체 dynamics, 명시적 및 암시적 유한 요소 분석, 날씨 모델링, 지진 처리, 저수지 시뮬레이션 및 RTL 시뮬레이션 같은 HPC 응용 프로그램에 최적화 되어 있습니다. HBv3 Vm은 최대 120 AMD EPYC™ 7003 시리즈 (Milan) CPU 코어, 448 g b RAM, 하이퍼스레딩을 갖지 않습니다. HBv3 시리즈 Vm은 메모리 대역폭의 350 g b/초를 제공 합니다. 코어 당 최대 32 MB의 L3 캐시, 블록 장치 SSD 성능의 최대 7GB/s, 최대 3.675 GHz의 클록 주파수를 제공 합니다. 

모든 HBv3 시리즈 Vm은 NVIDIA 네트워킹에서 200 g b/sec HDR InfiniBand를 사용 하 여 고성능 컴퓨터 규모의 MPI 작업을 지원 합니다. 이러한 Vm은 최적화 되 고 일관 된 RDMA 성능을 위해 차단 되지 않는 fat 트리에 연결 됩니다. 또한 HDR InfiniBand fabric은 적응 라우팅과 표준 RC 및 UD 전송 뿐만 아니라 DCT에 대 한 동적 연결 전송도 지원 합니다. 이러한 기능을 사용 하면 응용 프로그램 성능, 확장성 및 일관성을 향상 시킬 수 있으며, 이러한 기능을 사용 하는 것이 좋습니다.

[HBv2 시리즈](hbv2-series.md) Vm은 유체 dynamics, 유한 요소 분석 및 저수지 시뮬레이션 등 메모리 대역폭을 기반으로 하는 응용 프로그램에 최적화 되어 있습니다. HBv2 Vm 기능 120 AMD EPYC 7742 프로세서 코어, CPU 코어 당 4gb RAM 및 동시 다중 스레딩은 없습니다. 각 HBv2 VM은 최대 340 g b/초 메모리 대역폭을 제공 하 고 최대 4 테라플롭 FP64 compute를 제공 합니다.

HBv2 Vm 기능 200 g b/초 Mellanox HDR InfiniBand이 고, HB 및 HC 시리즈 Vm은 100 g b/초 Mellanox EDR InfiniBand 기능을 모두가지고 있습니다. 이러한 각 VM 유형은 최적화 되 고 일관 된 RDMA 성능을 위해 차단 되지 않는 fat 트리에 연결 됩니다. HBv2 Vm은 적응 라우팅과 표준 RC 및 UD 전송 뿐만 아니라 DCT (동적 연결 전송)를 지원 합니다. 이러한 기능을 사용 하면 응용 프로그램 성능, 확장성 및 일관성을 향상 시킬 수 있으며, 이러한 기능을 사용 하는 것이 좋습니다.

[Hb-시리즈](hb-series.md) Vm은 유체 dynamics, 명시적 유한 요소 분석, 날씨 모델링 등 메모리 대역폭을 기반으로 하는 응용 프로그램에 최적화 되어 있습니다. HB Vm 기능 60 AMD EPYC 7551 프로세서 코어, CPU 코어 당 4gb RAM, 하이퍼스레딩을 없음 AMD EPYC 플랫폼은 260 g b/초 이상의 메모리 대역폭을 제공 합니다.

[HC 시리즈](hc-series.md) Vm은 암시적 유한 요소 분석, 분자 dynamics 및 계산 연금술과 같이 조밀한 계산으로 구동 되는 응용 프로그램에 최적화 되어 있습니다. HC Vm 기능 44 Intel Xeon 플래티넘 8168 프로세서 코어, CPU 코어 당 8gb RAM, 하이퍼스레딩을 없음 Intel Xeon Platinum 플랫폼은 intel 수학 커널 라이브러리와 같은 소프트웨어 도구에 대 한 다양 한 소프트웨어 에코 시스템을 지원 합니다.

[H 시리즈](h-series.md) Vm은 높은 CPU 주파수 또는 핵심 요구 사항 당 대용량 메모리를 기반으로 하는 응용 프로그램에 최적화 되어 있습니다. H 시리즈 Vm은 8 또는 16 Intel Xeon E5 2667 v3 프로세서 코어, 7 개 또는 14gb의 RAM 및 CPU 코어 당 RAM이 없으며 하이퍼스레딩을 갖지 않습니다. H 시리즈 기능은 일관 된 RDMA 성능을 위해 비 블로킹 된 fat 트리 구성에서 56 g b/초 Mellanox Mellanox DR InfiniBand을 제공 합니다. H 시리즈 Vm은 Intel MPI 5.x 및 MS MPI를 지원 합니다.

> [!NOTE]
> 모든 HBv3, HBv2, HB 및 HC 시리즈 Vm은 물리적 서버에 단독으로 액세스할 수 있습니다. 물리적 서버당 VM은 1 개만 있으며 이러한 VM 크기에 대 한 다른 vm과 공유 되는 다중 테 넌 트가 없습니다.

> [!NOTE]
> [A8 – A11 vm](./sizes-previous-gen.md#a-series---compute-intensive-instances) 은 3/2021부터 사용이 중지 됩니다. 이제 이러한 크기의 새로운 VM 배포가 가능 하지 않습니다. 기존 Vm이 있는 경우 [HPC 마이그레이션 가이드](https://azure.microsoft.com/resources/hpc-migration-guide/)의 다른 VM 크기로 마이그레이션을 포함 하는 다음 단계에 대 한 전자 메일 알림을 참조 하세요.

## <a name="rdma-capable-instances"></a>RDMA 지원 인스턴스

대부분의 HPC VM 크기는 RDMA (원격 직접 메모리 액세스) 연결을 위한 네트워크 인터페이스를 특징으로 합니다. ' R '로 지정 된 [N 시리즈](./nc-series.md) 크기를 선택 하면 RDMA도 가능 합니다. 이 인터페이스는 다른 VM 크기에서 사용할 수 있는 표준 Azure 이더넷 네트워크 인터페이스에 추가 됩니다.

이 보조 인터페이스를 사용 하면 RDMA 지원 인스턴스가 InfiniBand (IB) 네트워크를 통해 통신할 수 있으며, HBv3, HBv2, HC, NDv2, H16r, H16mr, 및 기타 RDMA 지원 N 시리즈 가상 컴퓨터의 경우에는 FDR 요금에 대 한,, EDR 요금으로 작동 합니다. 이러한 RDMA 기능은 MPI (메시지 전달 인터페이스) 기반 응용 프로그램의 확장성과 성능을 향상 시킬 수 있습니다.

> [!NOTE]
> **Sr-iov 지원**: Azure HPC에는 현재 InfiniBand에 대해 sr-iov를 사용 하는지 여부에 따라 두 개의 vm 클래스가 있습니다. 현재 Azure에서 모든 최신 세대, RDMA 지원 또는 InfiniBand 사용이 가능한 Vm은 H16r, H16mr 및 NC24r를 제외 하 고 sr-iov를 사용 하도록 설정 되어 있습니다.
> RDMA는 InfiniBand (IB) 네트워크를 통해 사용 하도록 설정 되며 모든 RDMA 지원 Vm에 대해 지원 됩니다.
> IP over IB는 SR-IOV 사용 Vm 에서만 지원 됩니다.
> RDMA는 이더넷 네트워크를 통해 사용 하도록 설정 되지 않습니다.

- CentOS, RHEL, Ubuntu, SUSE와 같은 **운영 체제** Linux 배포판이 일반적으로 사용 됩니다. Windows Server 2016 이상 버전은 모든 HPC 시리즈 Vm에서 지원 됩니다. Windows Server 2012 R2 및 Windows Server 2012도 SR-IOV를 사용 하지 않는 Vm에서 지원 됩니다. [Windows Server 2012 r 2는 VM 크기를 64 (가상 또는 실제) 코어 이상으로 HBv2부터 지원 되지 않습니다](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows). Marketplace에서 지원 되는 VM 이미지 목록과 적절 하 게 구성 하는 방법은 [Vm 이미지](./workloads/hpc/configure.md) 를 참조 하세요. 각 VM 크기 페이지에도 소프트웨어 스택 지원이 나열 됩니다.

- **InfiniBand 및 드라이버** -InfiniBand 사용 VM에서 RDMA를 사용 하도록 설정 하려면 적절 한 드라이버가 필요 합니다. Marketplace에서 지원 되는 VM 이미지 목록과 적절 하 게 구성 하는 방법은 [Vm 이미지](./workloads/hpc/configure.md) 를 참조 하세요. 또한 [InfiniBand를 사용 하도록 설정](./workloads/hpc/enable-infiniband.md) 하 여 VM 확장에 대해 알아보거나 InfiniBand 드라이버의 수동 설치를 참조 하세요.

- **Mpi** -AZURE에서 sr-iov를 사용 하도록 설정 된 VM 크기는 대부분의 MPI를 Mellanox OFED와 함께 사용할 수 있습니다. SR-IOV를 사용할 수 없는 Vm에서 지원 되는 MPI 구현은 Microsoft 네트워크 직접 (ND) 인터페이스를 사용 하 여 Vm 간에 통신 합니다. 따라서 Intel MPI 5.x 및 Microsoft MPI (MS-MPI) 2012 R2 이상 버전만 지원 됩니다. 최신 버전의 Intel MPI 런타임 라이브러리는 Azure RDMA 드라이버와 호환 될 수도 있고 그렇지 않을 수도 있습니다. Azure의 HPC Vm에서 MPI를 설정 하는 방법에 대 한 자세한 내용은 [hpc에 대 한 Mpi 설정](./workloads/hpc/setup-mpi.md) 을 참조 하세요.

  > [!NOTE]
  > **Rdma 네트워크 주소 공간**: AZURE의 rdma 네트워크는 주소 공간 172.16.0.0/16을 예약 합니다. Azure 가상 네트워크에 배포된 인스턴스에서 MPI 애플리케이션을 실행하려면 가상 네트워크 주소 공간이 RDMA 네트워크와 겹치지 않도록 해야 합니다.

## <a name="cluster-configuration-options"></a>클러스터 구성 옵션

Azure는 다음과 같은 RDMA 네트워크를 사용 하 여 통신할 수 있는 HPC Vm의 클러스터를 만들기 위한 몇 가지 옵션을 제공 합니다. 

- **가상 컴퓨터**  -RDMA 가능 HPC vm을 동일한 확장 집합 또는 가용성 집합 (Azure Resource Manager 배포 모델을 사용 하는 경우)에 배포 합니다. 클래식 배포 모델을 사용하는 경우 동일한 클라우드 서비스에서 VM을 배포합니다.

- **Virtual machine scale sets** -가상 머신 확장 집합에서 확장 집합 내에서 InfiniBand 통신을 위한 단일 배치 그룹으로 배포를 제한 해야 합니다. 예를 들어, Resource Manager 템플릿에서 `singlePlacementGroup` 속성을 `true`로 설정합니다. 분리 수 있는 최대 확장 집합 크기는 `singlePlacementGroup=true` 기본적으로 100 vm에 있습니다. HPC 작업 확장 요구가 단일 테 넌 트의 100 Vm 보다 높은 경우 무료로 요청을 받을 수 있습니다. 무료로 [온라인 고객 지원 요청을 열](../azure-portal/supportability/how-to-create-azure-support-request.md) 수 있습니다. 단일 확장 집합의 Vm 수에 대 한 제한을 300로 늘릴 수 있습니다. 가용성 집합을 사용 하 여 Vm을 배포 하는 경우 최대 제한은 가용성 집합 당 Vm 200에 있습니다.

  > [!NOTE]
  > **가상 컴퓨터 간의 MPI**: vm (가상 컴퓨터) 간에 RDMA (예: MPI 통신 사용)가 필요한 경우 vm이 동일한 가상 머신 확장 집합 또는 가용성 집합에 있는지 확인 합니다.

- **Azure CycleCloud** - [azure CycleCloud](/azure/cyclecloud/) 를 사용 하 여 MPI 작업을 실행 하는 HPC 클러스터를 만듭니다.

- **Azure Batch** -MPI 작업을 실행 하는 [Azure Batch](../batch/index.yml) 풀을 만듭니다. Azure Batch에서 MPI 애플리케이션 실행할 때 계산 집약적 인스턴스를 사용하려면 [다중 인스턴스 작업을 사용하여 Azure Batch에서 MPI(메시지 전달 인터페이스) 애플리케이션 실행](../batch/batch-mpi.md)을 참조하세요.

- **MICROSOFT HPC 팩**  -  [HPC 팩](/powershell/high-performance-computing/overview) 에는 Rdma 지원 Linux vm에 배포 될 때 Azure rdma 네트워크를 사용 하는 MS MPI 용 런타임 환경이 포함 되어 있습니다. 배포 예는 [MPI 응용 프로그램을 실행 하려면 HPC Pack을 사용 하 여 LINUX RDMA 클러스터 설정](/powershell/high-performance-computing/hpcpack-linux-openfoam)을 참조 하세요.

## <a name="deployment-considerations"></a>배포 고려 사항

- **Azure 구독** - 몇몇 계산 집약적 인스턴스를 배포하려면 종량제 구독 또는 기타 구매 옵션을 고려합니다. [Azure 무료 계정](https://azure.microsoft.com/free/)을 사용하는 경우, 제한된 수의 Azure 컴퓨팅 코어만 사용할 수 있습니다.

- **가격 책정 및 가용성** -Azure 지역에서 [VM 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 및 [가용성](https://azure.microsoft.com/global-infrastructure/services/) 을 확인 합니다.

- **코어 할당량** – 기본값에서 Azure 구독의 코어 할당량을 늘려야 합니다. 구독에 따라서도 H 시리즈를 포함하여 특정 VM 크기 제품군에 배포할 수 있는 코어 수가 제한될 수 있습니다. 할당량 증가를 요청하려면 무료로 [온라인 고객 지원 요청을 개설](../azure-portal/supportability/how-to-create-azure-support-request.md) 합니다. (기본 제한은 구독 범주에 따라 달라질 수 있습니다.)

  > [!NOTE]
  > 대규모 용량이 필요한 경우 Azure 지원에 문의합니다. Azure 할당량은 신용 제한이며 용량 보증이 아닙니다. 할당량에 관계 없이 사용하는 코어에 대해서만 요금이 청구됩니다.
  
- **가상 네트워크** - Azure [가상 네트워크](https://azure.microsoft.com/documentation/services/virtual-network/) 는 계산 집약적 인스턴스를 사용할 필요가 없습니다. 하지만 많은 배포에서 온-프레미스 리소스에 액세스해야 하는 경우 적어도 클라우드 기반 Azure 가상 네트워크 또는 사이트 간 연결이 필요합니다. 필요한 경우 인스턴스를 배포할 새 가상 네트워크를 만듭니다. 선호도 그룹에서 가상 네트워크에 계산 집약적 VM을 추가하는 것은 지원되지 않습니다.

- **크기 조정** – 특수 한 하드웨어로 인해 동일한 크기 제품군 (H 시리즈 또는 N 시리즈) 내에서 계산 집약적인 인스턴스 크기만 조정할 수 있습니다. 예를 들어 H 시리즈 VM 크기는 한 H 시리즈에서 다른 H 시리즈로만 조정할 수 있습니다. InfiniBand 드라이버 지원 및 NVMe 디스크에 대 한 추가 고려 사항은 특정 Vm에 대해 고려해 야 할 수 있습니다.


## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [컴퓨팅 최적화](sizes-compute.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

- [Hpc 워크 로드](./workloads/hpc/overview.md)에서 [vm을 구성](./workloads/hpc/configure.md)하 고, [InfiniBand를 사용 하도록](./workloads/hpc/enable-infiniband.md)설정 하 고, [MPI를 설정](./workloads/hpc/setup-mpi.md) 하 고, Azure 용 hpc 응용 프로그램을 최적화 하는 방법
- [HBv3 시리즈 개요](./workloads/hpc/hbv3-series-overview.md) 및 [HC 시리즈 개요](./workloads/hpc/hc-series-overview.md)를 검토 합니다.
- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항, HPC 워크 로드 예제 및 성능 결과에 대해 읽어 보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.
