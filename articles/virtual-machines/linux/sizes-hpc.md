---
title: Azure Linux VM 크기 - HPC | Microsoft Docs
description: Azure의 Linux 고성능 컴퓨팅 가상 머신에 사용할 수 있는 다양한 크기를 나열합니다. 이 시리즈의 크기에 대한 스토리지 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: ee99869c2b7a7b3ab38fdd9eae0687862ea53819
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100871"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>고성능 컴퓨팅 가상 머신 크기

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Azure에서 SR-IOV 사용 VM 크기를 사용 하면 MPI의 거의 모든 버전을 사용할 수 있습니다.
SR-IOV를 사용할 수 없는 Vm에서 Intel MPI 5.x 버전만 지원 됩니다. Intel MPI 런타임 라이브러리의 이후 버전 (2017, 2018)은 Azure Linux RDMA 드라이버와 호환 될 수도 있고 그렇지 않을 수도 있습니다.


### <a name="supported-os-images"></a>지원되는 OS 이미지
 
Azure Marketplace에는 RDMA 연결을 지 원하는 다양 한 Linux 배포판이 있습니다.
  
* **CentOS 기반 HPC** -sr-iov를 사용 하도록 설정 되지 않은 vm의 경우 CentOS 기반 버전 6.5 hpc 이상 버전의 경우 최대 7.5가 적합 합니다. H 시리즈 Vm의 경우 7.1 ~ 7.5 버전을 권장 합니다. RDMA 드라이버 및 Intel MPI 5.1은 VM에 설치됩니다.
  SR-IOV Vm의 경우 CentOS-HPC 7.6는 RDMA 드라이버와 다양 한 MPI 패키지가 설치 된 상태에서 최적화 및 미리 로드 됩니다.
  다른 RHEL/CentOS VM 이미지에 대 한 InfiniBandLinux 확장을 추가 하 여 InfiniBand를 사용 하도록 설정 합니다. 이 Linux VM 확장은 RDMA 연결에 대 한 Mellanox OFED 드라이버 (SR-IOV Vm)를 설치 합니다. 다음 PowerShell cmdlet은 기존 RDMA 지원 VM에 InfiniBandDriverLinux 확장의 최신 버전 (버전 1.0)을 설치 합니다. RDMA 지원 VM 이름은 *Myvm* 으로 지정 되며 다음과 같이 *미국 서 부* 지역에 *myvm* 이라는 리소스 그룹에 배포 됩니다.

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  또는 다음 JSON 요소를 사용 하 여 쉽게 배포할 수 있도록 VM 확장을 Azure Resource Manager 템플릿에 포함할 수 있습니다.
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  다음 명령은 *Myresourcegroup*이라는 리소스 그룹에 배포 된 *myvmss* 라는 기존 vm 확장 집합의 모든 RDMA 지원 vm에 최신 버전 1.0 InfiniBandDriverLinux 확장을 설치 합니다.
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > CentOS 기반 HPC 이미지에서 커널 업데이트는 **yum** 구성 파일에서 사용할 수 없습니다. 이는 Linux RDMA 드라이버가 RPM 패키지로 배포 되 고 커널이 업데이트 되는 경우 드라이버 업데이트가 작동 하지 않기 때문입니다.
  >
  

* **SUSE Linux Enterprise Server** -hpc 용 SLES 12 sp3, HPC 용 SLES 12 Sp3 (프리미엄), HPC 용 SLES 12 sp1, HPC 용 SLES 12 Sp1 (premium), SLES 12 SP4 및 SLES 15. RDMA 드라이버가 설치되고 Intel MPI 패키지가 VM에 배포됩니다. 다음 명령을 실행하여 MPI를 설치합니다.

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** -ubuntu Server 16.04 lts, 18.04 lts Intel MPI를 다운로드하도록 VM에서 RDMA 드라이버를 구성하고 Intel에 등록:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  InfiniBand를 설정 하는 방법에 대 한 자세한 내용은 [InfiniBand 사용](../workloads/hpc/enable-infiniband.md)을 참조 하세요.


### <a name="cluster-configuration-options"></a>클러스터 구성 옵션

Azure에서는 다음을 비롯한 RDMA 네트워크를 사용하여 통신할 수 있는 Linux HPC VM의 클러스터를 만드는 몇 가지 옵션을 제공합니다. 

* **가상 머신** - Azure Resource Manager 배포 모델을 사용하는 경우 동일한 가용성 집합에서 RDMA 가능 HPC VM을 배포합니다. 클래식 배포 모델을 사용하는 경우 동일한 클라우드 서비스에서 VM을 배포합니다. 

* **Virtual machine scale sets** -가상 머신 확장 집합에서 배포를 단일 배치 그룹으로 제한 해야 합니다. 예를 들어, Resource Manager 템플릿에서 `singlePlacementGroup` 속성을 `true`로 설정합니다. 

* **가상 컴퓨터 간의 mpi** -Mpi 통신이 vm (가상 컴퓨터) 간에 필요한 경우 vm이 동일한 가용성 집합 또는 가상 컴퓨터의 동일한 확장 집합에 있는지 확인 합니다.

* **Azure CycleCloud** - [Azure CycleCloud](/azure/cyclecloud/)에서 HPC 클러스터를 만들어서 Linux 노드에서 MPI 작업을 실행합니다.

* **Azure Batch** - [Azure Batch](/azure/batch/) 풀을 만들어서 Linux 컴퓨팅 노드에서 MPI 워크로드를 실행합니다. 자세한 내용은 [Batch 풀에서 RDMA 가능 또는 GPU 가능 인스턴스 사용](../../batch/batch-pool-compute-intensive-sizes.md)을 참조하세요. 또한 Batch에서 컨테이너 기반 워크로드를 실행하는 방법은 [Batch Shipyard](https://github.com/Azure/batch-shipyard) 프로젝트를 참조하세요.

* **Microsoft HPC 팩** - [HPC 팩](https://docs.microsoft.com/powershell/high-performance-computing/overview)은 여러 Linux 배포를 지원하여 RDMA 가능 Azure VM에서 배포된 컴퓨팅 노드에서 실행되고 Windows Server 헤드 노드에서 관리됩니다. 배포 예제는 [Azure에서 HPC 팩 Linux RDMA 클러스터 만들기](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)를 참조하세요.


### <a name="network-considerations"></a>네트워크 고려 사항
* Azure에서 SR-IOV 및 RDMA 지원 Linux Vm에서 eth1은 RDMA 네트워크 트래픽용으로 예약 되어 있습니다. Eth1 설정 또는이 네트워크를 참조 하는 구성 파일의 정보를 변경 하지 마세요.
* SR-IOV를 사용 하도록 설정 된 Vm (HB 및 HC 시리즈)에서 ib0는 RDMA 네트워크 트래픽용으로 예약 되어 있습니다.
* Azure의 RDMA 네트워크는 주소 공간 172.16.0.0/16을 예약합니다. Azure 가상 네트워크에 배포된 인스턴스에서 MPI 애플리케이션을 실행하려면 가상 네트워크 주소 공간이 RDMA 네트워크와 겹치지 않도록 해야 합니다.
* 선택한 클러스터 관리 도구에 따라 MPI 작업을 실행하도록 추가 시스템 구성이 필요할 수 있습니다. 예를 들어 vm 클러스터에서 ssh 키를 생성 하거나 암호 없는 ssh 로그인을 설정 하 여 클러스터 노드 간에 트러스트를 설정 해야 할 수 있습니다.


## <a name="other-sizes"></a>기타 크기
- [범용](sizes-general.md)
- [컴퓨팅 최적화](sizes-compute.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

- Azure에서 [HPC 작업](../workloads/hpc/configure.md) 을 설정, 최적화 및 확장 하는 방법에 대해 자세히 알아보세요.
- [ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
