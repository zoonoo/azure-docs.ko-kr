---
title: H 시리즈 - Azure 가상 시스템
description: H 시리즈 VM에 대한 사양.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 03/10/2020
ms.author: lahugh
ms.openlocfilehash: 6e0257f8a32d05f49dd67195f22d387319358ef2
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314141"
---
# <a name="h-series"></a>H 시리즈

H 시리즈 VM은 높은 CPU 주파수 또는 코어 요구 사항당 큰 메모리로 구동되는 애플리케이션에 최적화되어 있습니다. H 시리즈 VM은 8 또는 16 인텔 제온 E5 2667 v3 프로세서 코어, CPU 코어당 최대 14GB의 RAM, 하이퍼스레딩이 없습니다. H 시리즈는 일관된 RDMA 성능을 위해 비차단 지방 트리 구성에 56Gb/sec Mellanox FDR InfiniBand를 제공합니다. H 시리즈 VM은 인텔 MPI 5.x 및 MS-MPI를 지원합니다.

ACU: 290-300

Premium Storage: 지원되지 않음

Premium Storage 캐싱: 지원되지 않음

라이브 마이그레이션: 지원되지 않음

업데이트 메모리 보존: 지원되지 않음

| 크기 | vCPU | 프로세서 | 메모리(GB) | 메모리 대역폭 GB/s | 기본 CPU 주파수(GHz) | 모든 코어 주파수(GHz, 피크) | 단일 코어 주파수(GHz, 피크) | RDMA 성능(Gb/s) | MPI 지원 | 임시 저장 장치(GB) | 최대 데이터 디스크 수 | 최대 디스크 처리량: IOPS | 맥스 이더넷 NIC |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | 인텔 제온 E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | 인텔 5.x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16  | 16 | 인텔 제온 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | 인텔 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H8m  | 8  | 인텔 제온 E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | 인텔 5.x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16m | 16 | 인텔 제온 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | 인텔 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | 인텔 제온 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | 인텔 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | 인텔 제온 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | 인텔 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |

<sup>1</sup> MPI 애플리케이션의 경우 FDR InfiniBand 네트워크에서 전용 RDMA 백엔드 네트워크를 사용할 수 있습니다.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>지원되는 OS 이미지(Linux)
 
Azure 마켓플레이스에는 RDMA 연결을 지원하는 많은 Linux 배포판이 있습니다.
  
* **CentOS 기반 HPC** - SR-IOV가 아닌 VM, CentOS 기반 버전 6.5 HPC 또는 이후 버전의 경우 최대 7.5가 적합합니다. H 시리즈 VM의 경우 7.1 ~ 7.5 버전을 권장합니다. RDMA 드라이버 및 Intel MPI 5.1은 VM에 설치됩니다.
  SR-IOV VM의 경우 CentOS-HPC 7.6은 RDMA 드라이버와 설치된 다양한 MPI 패키지로 최적화되고 사전 로드됩니다.
  다른 RHEL/CentOS VM 이미지의 경우 인피니밴드리눅스 확장을 추가하여 인피니밴드를 활성화합니다. 이 리눅스 VM 확장 RDMA 연결을 위해 (SR-IOV VM에) 멜라녹스 OFED 드라이버를 설치합니다. 다음 PowerShell cmdlet은 기존 RDMA 지원 VM에 인피니밴드드라이버리눅스 확장의 최신 버전(버전 1.0)을 설치합니다. RDMA 지원 VM은 *myVM으로* 명명되며 다음과 같이 *미국 서부* 지역의 *myResourceGroup이라는* 리소스 그룹에 배포됩니다.

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  또는 다음 JSON 요소를 사용하면 VM 확장을 Azure 리소스 관리자 템플릿에 포함할 수 있습니다.
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  다음 명령은 *myResourceGroup이라는*리소스 그룹에 배포된 *myVMSS라는* 기존 가상 머신 규모 집합의 모든 RDMA 지원 VM에 최신 버전 1.0 InfiniBandDriverLinux 확장을 설치합니다.
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > CentOS 기반 HPC 이미지에서 커널 업데이트는 **yum** 구성 파일에서 사용할 수 없습니다. 이는 Linux RDMA 드라이버가 RPM 패키지로 배포되고 커널이 업데이트되면 드라이버 업데이트가 작동하지 않을 수 있기 때문입니다.
  >
  

* **SUSE Linux 엔터프라이즈 서버** - HPC용 SLES 12 SP3, HPC용 SLES 12 SP3(프리미엄), HPC용 SLES 12 SP1, HPC용 SLES 12 SP1, SLES 12 SP4(프리미엄), SLES 12 SP4 및 SLES 15. RDMA 드라이버가 설치되고 Intel MPI 패키지가 VM에 배포됩니다. 다음 명령을 실행하여 MPI를 설치합니다.

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **우분투** - 우분투 서버 16.04 LTS, 18.04 LTS. Intel MPI를 다운로드하도록 VM에서 RDMA 드라이버를 구성하고 Intel에 등록:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  인피니밴드 활성화, MPI 설정에 대한 자세한 내용은 [인피니밴드 사용(InfiniBand)](./workloads/hpc/enable-infiniband.md)을 참조하세요.

## <a name="other-sizes"></a>기타 크기

- [범용 가상 컴퓨터](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
