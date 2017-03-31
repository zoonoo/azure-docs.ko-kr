---
title: "Windows의 계산 집약적 VM 정보 | Microsoft Docs"
description: "Windows VM 및 클라우드 서비스에 대한 Azure H 시리즈 및 A8, A9, A10, A11 계산 집약적 크기 사용에 관한 배경 정보와 고려 사항을 얻습니다."
services: virtual-machines-windows, cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 1ab628ff6e7e8ed23c2c3b733907500dc6d4dde7
ms.lasthandoff: 03/25/2017


---
# <a name="about-h-series-and-compute-intensive-a-series-vms-for-windows"></a>Windows용 H 시리즈 및 계산 집약적인 A 시리즈 VM 정보
여기에는 *계산 집약적* 인스턴스로 알려진 최신 Azure H 시리즈 및 이전 A8, A9, A10 및 A11 인스턴스 사용에 대한 일부 고려 사항과 배경 정보가 나와 있습니다. 이 문서는 Windows VM에 대해 이러한 인스턴스를 사용하는 것이 중점을 두고 있습니다. [Linux VM](virtual-machines-linux-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서도 사용할 수 있습니다.

기본 사양의 저장소 용량 및 디스크 세부 정보는 [가상 컴퓨터 크기](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>RDMA 네트워크에 액세스
Windows MPI 트래픽 Azure RDMA 네트워크에 액세스하려면 RDMA 지원 인스턴스는 다음의 요구 사항을 충족해야 합니다. 

* **운영 체제**
  
  * **가상 컴퓨터** - Windows Server 2012 R2, Windows Server 2012
  * **클라우드 서비스** - Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 게스트 OS 제품군

    > [!NOTE]
    > 현재 Windows Server 2016은 Azure에서 RDMA 연결을 지원하지 않습니다.
    >
    
* **MPI** - Microsoft MPI(MS-MPI) 2012 R2 이상, Intel MPI Library 5.x

  지원되는 MPI 구현은 Microsoft Network Direct 인터페이스를 사용하여 인스턴스 간에 통신합니다. 
* **HpcVmDrivers VM 확장** - RDMA 지원 VM에서는 RDMA 연결에 필요한 Windows 네트워크 장치 드라이버를 설치하는 HpcVmDrivers 확장을 추가해야 합니다. (특정 A8 및 A9 인스턴스 배포에서는 HpcVmDrivers 확장이 자동으로 추가됩니다.) VM에 VM 확장을 추가해야 하는 경우 [Azure PowerShell](/powershell/azureps-cmdlets-docs) cmdlet을 사용할 수 있습니다. 

  
  예를 들어, Resource Manager 배포 모델에서 myVM이라는 기존 RDMA 호환 VM에 최신 버전 1.1 HpcVMDrivers 확장을 설치하려면:

  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  자세한 내용은 [가상 컴퓨터 확장 및 기능](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요. 또한 [클래식 배포 모델](windows/classic/manage-extensions.md)에서 배포된 VM의 확장으로 작업할 수 있습니다.


## <a name="considerations-for-hpc-pack-and-windows"></a>HPC 팩 및 Windows에 대한 고려 사항
Microsoft의 무료 HPC 클러스터 및 작업 관리 솔루션인 [Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx)은 Windows Server에서 계산 집약적 인스턴스를 사용하는 데 반드시 필요하지는 않습니다. 그러나 Windows 기반 MPI 응용 프로그램 및 기타 HPC 워크로드를 실행하기 위해 Azure에서 계산 클러스터를 만들 수도 있습니다. HPC Pack 2012 R2 이상 버전에는 RDMA 지원 VM에 배포할 경우 Azure RDMA 네트워크를 사용할 수 있는 MS-MPI에 대한 런타임 환경이 포함되어 있습니다.

Windows Server에서 HPC 팩을 사용하여 계산 집약적 인스턴스를 사용하는 방법에 대한 자세한 내용 및 검사 목록은 [MPI 응용 프로그램을 실행하기 위해 HPC 팩을 사용하여 Windows RDMA 클러스터 설정](windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* 계산 집약적 크기의 가용성 및 가격에 대한 세부 정보는 [Virtual Machines 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) 및 [Cloud Services 가격 책정](https://azure.microsoft.com/pricing/details/cloud-services/)을 참조하세요.
* 저장소 용량 및 디스크 세부 정보는 [가상 컴퓨터 크기](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
* Windows에서 HPC 팩을 사용하여 계산 집약적 인스턴스의 배포 및 사용을 시작하려면 [MPI 응용 프로그램을 실행하기 위해 HPC 팩을 사용하여 Windows RDMA 클러스터 설정](windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요.
* 계산 집약적 인스턴스를 사용하여 Azure Batch에서 MPI 응용 프로그램 실행에 대한 자세한 내용은 [다중 인스턴스 작업을 사용하여 Azure Batch에서 MPI(메시지 전달 인터페이스) 응용 프로그램 실행](../batch/batch-mpi.md)을 참조하세요.


