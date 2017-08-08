---
title: "Azure Linux VM 크기 - HPC | Microsoft Docs"
description: "Azure의 Linux 고성능 컴퓨팅 가상 컴퓨터에 사용할 수 있는 다양한 크기를 나열합니다."
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/28/2017
ms.author: jonbeck
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 4962c26e6bf2ed36ce670cc78f4ecfcab871a8bf
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="high-performance-compute-linux-vm-sizes"></a>고성능 계산 Linux VM 크기

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="rdma-capable-instances"></a>RDMA 지원 인스턴스
계산 집약적 인스턴스(H16r, H16mr, A8 및 A9) 일부는 RDMA(원격 직접 메모리 액세스) 연결을 위한 네트워크 인터페이스로 사용됩니다. 이 인터페이스는 다른 VM 크기에서 사용할 수 있는 표준 Azure 네트워크 인터페이스 외에 추가로 사용됩니다. 
  
이 인터페이스를 사용하면 RDMA 지원 인스턴스가 InfiniBand 네트워크를 통해 통신할 수 있으며 H16r 및 H16mr 가상 컴퓨터에서는 FDR 속도로, A8 및 A9 가상 컴퓨터에서는 QDR 속도로 작동할 수 있습니다. 이러한 RDMA 기능은 MPI(Message Passing Interface) 응용 프로그램의 확장성 및 성능을 높일 수 있습니다.

다음은 RDMA 지원 Linux VM이 Azure RDMA 네트워크에 액세스하기 위한 요구 사항입니다.
 
* **배포판** - Azure 마켓플레이스의 RDMA 지원 SLES(SUSE Linux Enterprise Server) 또는 Rogue Wave Software(이전의 OpenLogic) CentOS 기반 HPC 이미지에서 VM을 배포해야 합니다. 다음 Marketplace 이미지는 RDMA 연결을 지원합니다.
  
    * HPC용 SLES 12 SP1 또는 HPC용 SLES 12 SP1(Premium)
    
    * CentOS 기반 7.3 HPC, CentOS 기반 7.1 HPC, CentOS 기반 6.8 HPC 또는 CentOS 기반 6.5 HPC  
 
        > [!NOTE]
        > H 시리즈 VM의 경우 HPC용 SLES 12 SP1 이미지 또는 CentOS 기반 7.1 이상 HPC 이미지가 권장됩니다.
        >
        > CentOS 기반 HPC 이미지에서 커널 업데이트는 **yum** 구성 파일에서 사용할 수 없습니다. 즉 Linux RDMA 드라이버가 RPM 패키지로 배포되기 때문에 커널이 업데이트되는 경우 드라이버 업데이트가 작동하지 않을 수 있습니다.
        > 
        > 
* **MPI** - Intel MPI Library 5
  
    선택한 마켓플레이스 이미지에 따라 다음과 같이 Intel MPI의 별도 라이선스, 설치 또는 구성이 필요할 수 있습니다. 
  
  * **HPC 이미지의 SLES 12 SP1** - VM에 Intel MPI 패키지를 배포합니다. 다음 명령을 실행하여 설치합니다.

      ```bash
      sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
      ```

  * **CentOS 기반 HPC 이미지** - Intel MPI 5.1은 미리 설치되어 있습니다.  
    
    클러스터된 VM에서 MPI 작업을 실행하기 위해 추가 시스템 구성이 필요합니다. 예를 들어 VM 클러스터에서 계산 노드 간에 트러스트를 설정해야 합니다. 일반적인 설정에 대해서는 [MPI 응용 프로그램을 실행하도록 Linux RDMA 클러스터 설정](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)을 참조하세요.

### <a name="network-topology-considerations"></a>네트워크 토폴로지 고려 사항
* Azure의 RDMA 지원 Linux VM에서 Eth1은 RDMA 네트워크 트래픽용으로 예약됩니다. Eth1 설정 또는 이 네트워크를 참조하는 구성 파일의 정보를 변경하지 마세요. Eth0은 일반 Azure 네트워크 트래픽용으로 예약됩니다.

* Azure에서 IP over IB(Infiniband)는 지원되지 않습니다. RDMA over IB만 지원됩니다.

## <a name="using-hpc-pack"></a>HPC Pack 사용
Microsoft의 무료 HPC 클러스터 및 작업 관리 솔루션인 [HPC Pack](https://technet.microsoft.com/library/jj899572.aspx)은 Linux에서 계산 집약적 인스턴스를 사용하기 위한 한 가지 옵션입니다. HPC 팩의 최신 릴리스는 여러 Linux 배포를 지원하여 Windows Server 헤드 노드를 통해 관리되는 Azure VM에서 배포된 계산 노드에서 실행할 수 있습니다. RDMA 지원 Linux 계산 노드에서 Intel MPI가 실행되는 경우 HPC Pack은 RDMA 네트워크에 액세스하는 Linux MPI 응용 프로그램을 예약하고 실행할 수 있습니다. [Azure에서 HPC 팩 클러스터의 Linux 계산 노드 시작](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)을 참조하세요.

## <a name="other-sizes"></a>기타 크기
- [범용](sizes-general.md)
- [Compute에 최적화](sizes-compute.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)


## <a name="next-steps"></a>다음 단계

- Linux에서 RDMA를 사용하여 계산 집약적 크기를 배포하고 사용하려면 [MPI 응용 프로그램을 실행하기 위해 Linux RDMA 클러스터 설정](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)을 참조하세요.

- [ACU(Azure Compute 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.





