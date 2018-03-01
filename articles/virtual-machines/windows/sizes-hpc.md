---
title: "Azure Windows VM 크기 - HPC | Microsoft Docs"
description: "Azure의 Windows 고성능 컴퓨팅 가상 머신에 사용할 수 있는 다양한 크기를 나열합니다. 이 시리즈의 크기에 대한 저장소 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다."
services: virtual-machines-windows
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/21/2018
ms.author: jonbeck
ms.openlocfilehash: dfeb6b0cbd74ff5a5b11b730c6413df189096ab8
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/22/2018
---
# <a name="high-performance-compute-vm-sizes"></a>고성능 계산 VM 크기

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="rdma-capable-instances"></a>RDMA 지원 인스턴스
계산 집약적 인스턴스(H16r, H16mr, A8 및 A9) 일부는 RDMA(원격 직접 메모리 액세스) 연결을 위한 네트워크 인터페이스로 사용됩니다. 이 인터페이스는 다른 VM 크기에서 사용할 수 있는 표준 Azure 네트워크 인터페이스 외에 추가로 사용됩니다. 
  
이 인터페이스를 사용하면 RDMA 지원 인스턴스가 InfiniBand 네트워크를 통해 통신할 수 있으며 H16r 및 H16mr 가상 머신에서는 FDR 속도로, A8 및 A9 가상 머신에서는 QDR 속도로 작동할 수 있습니다. 이러한 RDMA 기능은 MPI(Message Passing Interface) 응용 프로그램의 확장성 및 성능을 높일 수 있습니다.

다음은 RDMA 지원 Windows VM이 Azure RDMA 네트워크에 액세스하기 위한 요구 사항입니다. 

* **운영 체제** - Windows Server 2016, Windows Server 2012 R2, Windows Server 2012

* **가용성 집합 또는 클라우드 서비스** – RDMA 지원 VM을 동일한 가용성 집합(Azure Resource Manager 배포 모델을 사용하는 경우) 또는 동일한 클라우드 서비스(클래식 배포 모델을 사용하는 경우)에서 배포합니다. Azure Batch를 사용하는 경우 RDMA 지원 VM이 동일한 풀에 있어야 합니다.

* **MPI** - Microsoft MPI(MS-MPI) 2012 R2 이상, Intel MPI Library 5.x

  지원되는 MPI 구현은 Microsoft Network Direct 인터페이스를 사용하여 인스턴스 간에 통신합니다. 

* **RDMA 네트워크 주소 공간** - Azure의 RDMA 네트워크는 주소 공간 172.16.0.0/16을 예약합니다. Azure 가상 네트워크에 배포된 인스턴스에서 MPI 응용 프로그램을 실행하려면 가상 네트워크 주소 공간이 RDMA 네트워크와 겹치지 않도록 해야 합니다.

* **HpcVmDrivers VM 확장** - RDMA 지원 VM에서는 RDMA 연결용 Windows 네트워크 장치 드라이버를 설치하는 HpcVmDrivers 확장을 추가합니다. (특정 A8 및 A9 인스턴스 배포에서는 HpcVmDrivers 확장이 자동으로 추가됩니다.) VM에 VM 확장을 추가해야 하는 경우 [Azure PowerShell](/powershell/azure/overview) cmdlet을 사용할 수 있습니다. 

  
  다음 명령은 *미국 서부* 영역의 *myResourceGroup* 리소스 그룹에 배포된 *myVM*이라는 기존 RDMA 지원 VM에 최신 버전의 1.1 HpcVMDrivers 확장을 설치합니다.

  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  
  자세한 내용은 [가상 머신 확장 및 기능](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요. 또한 [클래식 배포 모델](classic/manage-extensions.md)에서 배포된 VM의 확장으로 작업할 수 있습니다.


## <a name="using-hpc-pack"></a>HPC Pack 사용

Microsoft의 무료 HPC 클러스터 및 작업 관리 솔루션인 [Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx)은 Windows 기반 MPI 응용 프로그램 및 기타 HPC 워크로드를 실행하기 위해 Azure에서 계산 클러스터를 만들기 위한 한 가지 옵션입니다. HPC Pack 2012 R2 이상 버전에는 RDMA 지원 VM에 배포할 경우 Azure RDMA 네트워크를 사용하는 MS-MPI에 대한 런타임 환경이 포함되어 있습니다.




## <a name="other-sizes"></a>기타 크기
- [범용](sizes-general.md)
- [Compute에 최적화](sizes-compute.md)
- [메모리에 최적화](../virtual-machines-windows-sizes-memory.md)
- [Storage에 최적화](../virtual-machines-windows-sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)

## <a name="next-steps"></a>다음 단계

- Windows Server에서 HPC 팩을 통해 계산 집약적 인스턴스를 사용할 때의 검사 목록은 [MPI 응용 프로그램을 실행하기 위해 HPC 팩을 사용하여 Windows RDMA 클러스터 설정](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요.

- Azure Batch에서 MPI 응용 프로그램 실행할 때 계산 집약적 인스턴스를 사용하려면 [다중 인스턴스 작업을 사용하여 Azure Batch에서 MPI(메시지 전달 인터페이스) 응용 프로그램 실행](../../batch/batch-mpi.md)을 참조하세요.

- [ACU(Azure Compute 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.




