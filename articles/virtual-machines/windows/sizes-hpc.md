---
title: Azure Windows VM 크기-HPC
description: Azure의 Windows 고성능 컴퓨팅 가상 머신에 사용할 수 있는 다양한 크기를 나열합니다. 이 시리즈의 크기에 대한 스토리지 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: e96c74eb4b9b96459bb5de7db2faeb47ed99a82e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74065447"
---
# <a name="high-performance-compute-vm-sizes"></a>고성능 컴퓨팅 VM 크기

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **운영** 체제-위의 모든 HPC 시리즈 vm에서 Windows Server 2016. Windows Server 2012 R2, Windows Server 2012는 SR-IOV를 사용 하지 않는 Vm (HB 및 HC 제외) 에서도 지원 됩니다.

* **Mpi** -AZURE에서 sr-iov를 사용 하도록 설정 된 VM 크기 (hb, HC)는 모든 MPI의 모든 버전을 Mellanox OFED와 함께 사용할 수 있습니다.
SR-IOV를 사용할 수 없는 Vm에서 지원 되는 MPI 구현은 Microsoft Network Direct (ND) 인터페이스를 사용 하 여 인스턴스 간에 통신 합니다. 따라서 Microsoft MPI (MS MPI) 2012 R2 이상 및 Intel MPI 5.x 버전만 지원 됩니다. Intel MPI 런타임 라이브러리의 이후 버전 (2017, 2018)은 Azure RDMA 드라이버와 호환 될 수도 있고 그렇지 않을 수도 있습니다.

* **INFINIBANDDRIVERWINDOWS vm 확장** -RDMA 지원 Vm에서 InfiniBandDriverWindows 확장을 추가 하 여 InfiniBand를 사용 하도록 설정 합니다. 이 Windows VM 확장은 RDMA 연결에 Windows 네트워크 직접 드라이버 (SR-IOV가 아닌 Vm) 또는 Mellanox OFED 드라이버 (SR-IOV Vm)를 설치 합니다.
A8 및 A9 인스턴스의 특정 배포에서 HpcVmDrivers 확장이 자동으로 추가 됩니다. HpcVmDrivers VM 확장은 더 이상 사용 되지 않습니다. 업데이트 되지 않습니다. VM에 VM 확장을 추가해야 하는 경우 [Azure PowerShell](/powershell/azure/overview) cmdlet을 사용할 수 있습니다. 

  다음 명령은 *미국 서 부* 지역에 *myvm* 이라는 리소스 그룹에 배포 된 *MYVM* 이라는 기존 RDMA 지원 VM에 최신 버전 1.0 InfiniBandDriverWindows 확장을 설치 합니다.

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```
  또는 편리한 배포를 위해 다음 JSON 요소를 사용 하 여 VM 확장을 Azure Resource Manager 템플릿에 포함할 수 있습니다.
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  다음 명령은 *Myresourcegroup*이라는 리소스 그룹에 배포 된 *myvmss* 라는 기존 vm 확장 집합의 모든 RDMA 지원 vm에 최신 버전 1.0 InfiniBandDriverWindows 확장을 설치 합니다.

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  자세한 내용은 [가상 머신 확장 및 기능](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요. 또한 [클래식 배포 모델](classic/manage-extensions.md)에서 배포된 VM의 확장으로 작업할 수 있습니다.

* **RDMA 네트워크 주소 공간** - Azure의 RDMA 네트워크는 주소 공간 172.16.0.0/16을 예약합니다. Azure 가상 네트워크에 배포된 인스턴스에서 MPI 애플리케이션을 실행하려면 가상 네트워크 주소 공간이 RDMA 네트워크와 겹치지 않도록 해야 합니다.


### <a name="cluster-configuration-options"></a>클러스터 구성 옵션

Azure에서는 다음을 비롯한 RDMA 네트워크를 사용하여 통신할 수 있는 Windows HPC VM의 클러스터를 만드는 몇 가지 옵션을 제공합니다. 

* **가상 머신** - Azure Resource Manager 배포 모델을 사용하는 경우 동일한 가용성 집합에서 RDMA 가능 HPC VM을 배포합니다. 클래식 배포 모델을 사용하는 경우 동일한 클라우드 서비스에서 VM을 배포합니다. 

* **Virtual machine scale sets** -가상 머신 확장 집합에서 배포를 단일 배치 그룹으로 제한 해야 합니다. 예를 들어, Resource Manager 템플릿에서 `singlePlacementGroup` 속성을 `true`로 설정합니다. 

* **가상 컴퓨터 간의 mpi** -Mpi 통신이 vm (가상 컴퓨터) 간에 필요한 경우 vm이 동일한 가용성 집합 또는 가상 컴퓨터의 동일한 확장 집합에 있는지 확인 합니다.

* **Azure CycleCloud** - [Azure CycleCloud](/azure/cyclecloud/)에서 HPC 클러스터를 만들어서 Windows 노드에서 MPI 작업을 실행합니다.

* **Azure Batch** - [Azure Batch](/azure/batch/) 풀을 만들어서 Windows Server 컴퓨팅 노드에서 MPI 워크로드를 실행합니다. 자세한 내용은 [Batch 풀에서 RDMA 가능 또는 GPU 가능 인스턴스 사용](../../batch/batch-pool-compute-intensive-sizes.md)을 참조하세요. 또한 Batch에서 컨테이너 기반 워크로드를 실행하는 방법은 [Batch Shipyard](https://github.com/Azure/batch-shipyard) 프로젝트를 참조하세요.

* **Microsoft HPC 팩** - [HPC 팩](https://docs.microsoft.com/powershell/high-performance-computing/overview)에는 RDMA 지원 Windows VM에 배포할 경우 Azure RDMA 네트워크를 사용하는 MS-MPI에 대한 런타임 환경이 포함되어 있습니다. 예제 배포는 [MPI 애플리케이션을 실행하기 위해 HPC Pack을 사용하여 Windows RDMA 클러스터 설정](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요.

## <a name="other-sizes"></a>기타 크기
- [범용](sizes-general.md)
- [컴퓨팅 최적화](sizes-compute.md)
- [메모리에 최적화](../virtual-machines-windows-sizes-memory.md)
- [Storage에 최적화](../virtual-machines-windows-sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

- Windows Server에서 HPC 팩을 통해 계산 집약적 인스턴스를 사용할 때의 검사 목록은 [MPI 애플리케이션을 실행하기 위해 HPC 팩을 사용하여 Windows RDMA 클러스터 설정](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요.

- Azure Batch에서 MPI 애플리케이션 실행할 때 계산 집약적 인스턴스를 사용하려면 [다중 인스턴스 작업을 사용하여 Azure Batch에서 MPI(메시지 전달 인터페이스) 애플리케이션 실행](../../batch/batch-mpi.md)을 참조하세요.

- [ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
