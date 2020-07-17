---
title: H 시리즈-Azure Virtual Machines
description: H 시리즈 Vm에 대 한 사양입니다.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 03/10/2020
ms.author: jushiman
ms.openlocfilehash: f79dcb8886985d60a1ed82e1a77d231cf7d3ad24
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84678684"
---
# <a name="h-series"></a>H 시리즈

H 시리즈 Vm은 높은 CPU 주파수 또는 핵심 요구 사항 당 대용량 메모리를 기반으로 하는 응용 프로그램에 최적화 되어 있습니다. H 시리즈 Vm은 8 또는 16 Intel Xeon E5 2667 v3 프로세서 코어, CPU 코어 당 최대 14gb RAM, 하이퍼스레딩을 갖지 않습니다. H 시리즈 기능은 일관 된 RDMA 성능을 위해 비 블로킹 된 fat 트리 구성에서 56 g b/초 Mellanox Mellanox DR InfiniBand을 제공 합니다. H 시리즈 Vm은 Intel MPI 5.x 및 MS MPI를 지원 합니다.

ACU: 290-300

Premium Storage:  지원되지 않음

Premium Storage 캐싱: 지원되지 않음

실시간 마이그레이션: 지원되지 않음

메모리 보존 업데이트: 지원되지 않음

| 크기 | vCPU | 프로세서 | 메모리(GB) | 메모리 대역폭 (GB/초) | 기본 CPU 빈도 (GHz) | 모든 코어 빈도 (GHz, 최고) | 단일 코어 빈도 (GHz, 최고) | RDMA 성능 (Gb/s) | MPI 지원 | 임시 저장소 (GB) | 최대 데이터 디스크 수 | 최대 디스크 처리량: IOPS | 최대 이더넷 Nic |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |

<sup>1</sup> MPI 응용 프로그램의 경우 Fdr InfiniBand 네트워크를 통해 전용 RDMA 백 엔드 네트워크를 사용할 수 있습니다.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>지원 되는 OS 이미지 (Linux)
 
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
  
  다음 명령은 *Myresourcegroup*이라는 리소스 그룹에 배포 된 *myvmss* 라는 기존 가상 머신 확장 집합의 모든 RDMA 지원 vm에 최신 버전 1.0 InfiniBandDriverLinux 확장을 설치 합니다.
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

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  InfiniBand를 설정 하는 방법에 대 한 자세한 내용은 [InfiniBand 사용](./workloads/hpc/enable-infiniband.md)을 참조 하세요.

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
