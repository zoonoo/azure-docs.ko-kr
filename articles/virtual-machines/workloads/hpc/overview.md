---
title: H 시리즈 VM의 고성능 컴퓨팅 - Azure Virtual Machines
description: HPC에 최적화된 H 시리즈 VM의 기능에 대해 알아봅니다.
author: vermagit
ms.author: amverma
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.openlocfilehash: b3d5d003db89a11a013c3236a3afbe03ffe68557
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271012"
---
# <a name="high-performance-computing-on-h-series-vms"></a>H 시리즈 VM의 고성능 컴퓨팅

HB 시리즈 및 HC 시리즈 VM의 HPC(고성능 컴퓨팅)를 통해 Azure에서 모든 VM의 HPC 성능을 최적화할 수 있습니다. HPC에 최적화된 VM은 유체 역학, 석유 및 가스 시뮬레이션 및 날씨 모델링과 같은 가장 어려운 수학 문제를 해결하는 데 사용됩니다.

이 문서에서는 HB 시리즈 및 HC 시리즈 VM의 주요 기능, 이러한 VM이 HPC 시나리오에서 잘 작동하는 이유 그리고 시작하는 방법을 설명합니다.

## <a name="features-and-capabilities"></a>특징과 기능

HB 시리즈 및 HC 시리즈 VM은 최고의 HPC 성능, MPI(메시지 전달 인터페이스) 확장성 및 HPC 워크로드에 대한 비용 효율성을 제공하도록 설계되었습니다.

### <a name="message-passing-interface"></a>메시지 전달 인터페이스

HB 시리즈 및 HC 시리즈는 거의 모든 MPI 형식과 버전을 지원합니다. 가장 일반적으로 지원되는 MPI 형식은 다음과 같습니다. OpenMPI, MVAPICH2, Platform MPI, Intel MPI 및 모든 RDMA(원격 직접 메모리 액세스) 동사가 포함됩니다. 자세한 내용은 [HPC를 위한 메시지 전달 인터페이스 설정](setup-mpi.md)을 참조하세요.

### <a name="rdma-and-infiniband"></a>RDMA 및 InfiniBand

RDMA 인터페이스는 HB 시리즈 및 HC 시리즈 VM의 표준입니다. RDMA 지원 인스턴스는 InfiniBand 네트워크를 통해 통신하며 HB 시리즈 및 HC 시리즈 가상 머신에 대해 EDR(Enhanced Data Rate)로 작동합니다. RDMA 지원 인스턴스는 일부 MPI 애플리케이션의 확장성 및 성능을 높일 수 있습니다.

HB 시리즈 및 HC 시리즈 VM을 지원하는 InfiniBand 구성은 일관된 RDMA 성능을 위한 지름이 작은 디자인의 비중단 팻 트리입니다.

HB 시리즈나 HC 시리즈 VM에서 InfiniBand를 설정하는 방법에 대한 자세한 내용은 [InfiniBand 사용](enable-infiniband.md)을 참조하세요.

## <a name="get-started"></a>시작하기

먼저 어떤 H 시리즈 VM을 사용할지 결정합니다. HPC에 최적화된 VM에 대한 자세한 내용은 [HB 시리즈 개요](hb-series-overview.md)와 [HC 시리즈 개요](hc-series-overview.md)를 참조하세요. 사양은 [고성능 컴퓨팅 VM 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)를 참조하세요.

애플리케이션을 위한 VM을 선택하고 만든 후에는 InfiniBand를 활성화하여 VM을 구성해야 합니다. Windows 및 Linux VM 모두에서 InfiniBand를 사용하도록 설정하는 방법은 [InfiniBand 사용](enable-infiniband.md)을 참조하세요.

HPC 워크로드의 중요한 구성 요소는 MPI입니다. HB 시리즈 및 HC 시리즈는 거의 모든 MPI 형식과 버전을 지원합니다. 자세한 내용은 [HPC를 위한 메시지 전달 인터페이스 설정](setup-mpi.md)을 참조하세요.

VM 시리즈를 선택하고 Infiniband 및 MPI를 설정했으면 HPC 워크로드를 구축할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

- [HB 시리즈 개요](hb-series-overview.md)와 [HC 시리즈 개요](hc-series-overview.md)를 검토하고 주요 차이점 및 사양에 대해 알아봅니다.

- HPC 워크로드를 실행하는 상위 수준의 아키텍처 뷰는 [Azure의 HPC(고성능 컴퓨팅)](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)를 참조하세요.
