---
title: Azure VM 크기 - HPC | 마이크로 소프트 문서
description: Azure에서 고성능 컴퓨팅 가상 컴퓨터에 사용할 수 있는 다양한 크기를 나열합니다. 이 시리즈의 크기에 대한 스토리지 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: b900a95df00ccdd0ad9b5bee3887364195c7d1c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78226724"
---
# <a name="high-performance-compute-vm-sizes"></a>고성능 컴퓨팅 VM 크기

Azure H 시리즈 가상 머신(VM)은 다양한 실제 HPC 워크로드에 대해 리더십 수준의 성능, MPI 확장성 및 비용 효율성을 제공하도록 설계되었습니다.

[HBv2 시리즈](hbv2-series.md) VM은 200Gb/sec 멜라녹스 HDR 인피니밴드를 탑재하고, HB와 HC 시리즈 VM은 100Gb/sec 멜라녹스 EDR 인피니밴드를 탑재했다. 이러한 각 VM 유형은 최적화되고 일관된 RDMA 성능을 위해 비차단 지방 트리에 연결됩니다. HBv2 VM은 어댑티브 라우팅 및 동적 커넥티드 전송(DCT, 표준 RC 및 UD 전송에 추가)을 지원합니다. 이러한 기능은 응용 프로그램 성능, 확장성 및 일관성을 향상시키며 이러한 기능을 강력하게 권장합니다.

[HB 시리즈](hb-series.md) VM은 유체 역학, 명시적 유한 요소 분석 및 날씨 모델링과 같은 메모리 대역폭에 의해 구동되는 애플리케이션에 최적화되어 있습니다. HB VM은 60AMD EPYC 7551 프로세서 코어, CPU 코어당 4GB의 RAM, 하이퍼스레딩이 없습니다. AMD EPYC 플랫폼은 초당 260GB 이상의 메모리 대역폭을 제공합니다.

[HC 시리즈](hc-series.md) VM은 암시적 유한 요소 분석, 분자 역학 및 계산 화학과 같은 고밀도 계산에 의해 구동되는 애플리케이션에 최적화되어 있습니다. HC VM은 44개의 인텔 제온 플래티넘 8168 프로세서 코어, CPU 코어당 8GB의 RAM, 하이퍼스레딩이 없습니다. 인텔 제온 플래티넘 플랫폼은 인텔 수학 커널 라이브러리와 같은 소프트웨어 도구의 풍부한 에코시스템을 지원합니다.

[H 시리즈](h-series.md) VM은 높은 CPU 주파수 또는 코어 요구 사항당 큰 메모리로 구동되는 애플리케이션에 최적화되어 있습니다. H 시리즈 VM은 8 또는 16 인텔 제온 E5 2667 v3 프로세서 코어, CPU 코어당 7 또는 14GB의 RAM을 갖추고 있으며 하이퍼스레딩이 없습니다. H 시리즈는 일관된 RDMA 성능을 위해 비차단 지방 트리 구성에 56Gb/sec Mellanox FDR InfiniBand를 제공합니다. H 시리즈 VM은 인텔 MPI 5.x 및 MS-MPI를 지원합니다.

## <a name="deployment-considerations"></a>배포 고려 사항

- **Azure 구독** - 몇몇 계산 집약적 인스턴스를 배포하려면 종량제 구독 또는 기타 구매 옵션을 고려합니다. [Azure 무료 계정](https://azure.microsoft.com/free/)을 사용하는 경우, 제한된 수의 Azure 컴퓨팅 코어만 사용할 수 있습니다.

- **가격 책정 및 가용성** - 이러한 VM 크기는 표준 가격 책정 계층에만 제공됩니다. [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/) 에서 Azure 지역의 가용성을 확인하세요.
- **코어 할당량** – 기본값에서 Azure 구독의 코어 할당량을 늘려야 합니다. 구독에 따라서도 H 시리즈를 포함하여 특정 VM 크기 제품군에 배포할 수 있는 코어 수가 제한될 수 있습니다. 할당량 증가를 요청하려면 온라인 [고객 지원 요청을](../azure-supportability/how-to-create-azure-support-request.md) 무료로 엽니다. (기본 제한은 구독 범주에 따라 달라질 수 있습니다.)

  > [!NOTE]
  > 대규모 용량이 필요한 경우 Azure 지원에 문의합니다. Azure 할당량은 신용 제한이며 용량 보증이 아닙니다. 할당량에 관계 없이 사용하는 코어에 대해서만 요금이 청구됩니다.
  
- **가상 네트워크** - Azure [가상 네트워크](https://azure.microsoft.com/documentation/services/virtual-network/) 는 계산 집약적 인스턴스를 사용할 필요가 없습니다. 하지만 많은 배포에서 온-프레미스 리소스에 액세스해야 하는 경우 적어도 클라우드 기반 Azure 가상 네트워크 또는 사이트 간 연결이 필요합니다. 필요한 경우 인스턴스를 배포할 새 가상 네트워크를 만듭니다. 선호도 그룹에서 가상 네트워크에 계산 집약적 VM을 추가하는 것은 지원되지 않습니다.
- **크기 조정** - 특수한 하드웨어로 인해 동일한 크기의 제품군(H 시리즈 또는 계산 집약적 A 시리즈) 내에서만 계산 집약적 인스턴스의 크기를 조정할 수 있습니다. 예를 들어 H 시리즈 VM 크기는 한 H 시리즈에서 다른 H 시리즈로만 조정할 수 있습니다. 또한 계산 집약적이지 않은 크기에서 계산 집약적 크기로 조정하는 것은 지원되지 않습니다.  

> [!NOTE]
> A8 – A11 VM은 2021년 3월에 출시될 예정입니다. 자세한 내용은 [HPC 마이그레이션 가이드를](https://azure.microsoft.com/resources/hpc-migration-guide/)참조하십시오.

## <a name="rdma-capable-instances"></a>RDMA 지원 인스턴스

컴퓨팅 집약적 인스턴스(A8, A9, H16r, H16mr, HB 및 HC)의 하위 집합은 RDMA(원격 직접 메모리 액세스) 연결을 위한 네트워크 인터페이스를 갖추고 있습니다. NC24rs 구성(NC24rs_v2 및 NC24rs_v3)과 같이 'r'로 지정된 선택된 N 시리즈 크기도 RDMA가 지원됩니다. 이 인터페이스는 다른 VM 크기에서 사용할 수 있는 표준 Azure 네트워크 인터페이스 외에 추가로 사용됩니다.

이 인터페이스를 통해 RDMA 지원 인스턴스는 HB, HC, H16r, H16mr 및 RDMA 지원 N 시리즈 가상 머신의 EDR 속도, A8 및 A9 가상 머신의 QDR 속도에 대해 EDR 속도로 작동하는 InfiniBand(IB) 네트워크를 통해 통신할 수 있습니다. 이러한 RDMA 기능은 특정 MPI(Message Passing Interface) 애플리케이션의 확장성 및 성능을 향상시킬 수 있습니다. 속도에 대한 자세한 내용은 이 페이지의 표에서 자세한 내용을 참조하십시오.

> [!NOTE]
> Azure에서 IB를 통해 IP는 SR-IOV 지원 VM(인피니밴드의 SR-IOV, 현재 HB 및 HC)에서만 지원됩니다. IB를 통해 RDMA는 모든 RDMA 지원 인스턴스에 대해 지원됩니다.
- **운영 체제** - 위의 모든 HPC 시리즈 VM에 윈도우 서버 2016. Windows Server 2012 R2, Windows 서버 2012는 SR-IOV가 아닌 VM에서도 지원됩니다(따라서 HB 및 HC 제외).

- **MPI** - Azure(HB, HC)에서 SR-IOV를 활성화한 VM 크기를 사용하면 MPI의 거의 모든 맛을 Mellanox OFED와 함께 사용할 수 있습니다.
SR-IOV가 아닌 VM에서 지원되는 MPI 구현은 MICROSOFT 네트워크 Direct(ND) 인터페이스를 사용하여 인스턴스 간에 통신합니다. 따라서, 만 마이크로 소프트 MPI (MS-MPI) 2012 R2 이상 및 인텔 MPI 5.x 버전이 지원됩니다. 인텔 MPI 런타임 라이브러리의 이후 버전(2017년, 2018년)은 Azure RDMA 드라이버와 호환되거나 호환되지 않을 수 있습니다.

- **인피니밴드드라이버윈도우 VM 확장** - RDMA 지원 VM에서 인피니밴드드라이버윈도우 확장을 추가하여 인피니밴드를 활성화합니다. 이 Windows VM 확장은 RDMA 연결을 위해 Windows 네트워크 직접 드라이버(SR-IOV VM이 아닌) 또는 Mellanox OFED 드라이버(SR-IOV VM)를 설치합니다.
A8 및 A9 인스턴스의 특정 배포에서는 HpcVmDriver 확장이 자동으로 추가됩니다. HpcVmDriver VM 확장은 더 이상 사용되지 않습니다. 업데이트되지 않습니다. VM에 VM 확장을 추가해야 하는 경우 [Azure PowerShell](/powershell/azure/overview) cmdlet을 사용할 수 있습니다. 

  다음 명령은 *미국 서부* 지역의 *myResourceGroup이라는* 리소스 그룹에 배포된 *myVM이라는* 기존 RDMA 지원 VM에 최신 버전 1.0 InfiniBandDriverWindows 확장을 설치합니다.

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```

  또는 다음과 같은 JSON 요소를 통해 쉽게 배포할 수 있는 Azure 리소스 관리자 템플릿에 VM 확장을 포함할 수 있습니다.

  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  다음 명령은 *myResourceGroup이라는*리소스 그룹에 배포된 *myVMSS라는* 기존 가상 머신 규모 집합의 모든 RDMA 지원 VM에 최신 버전 1.0 InfiniBandDriverWindows 확장을 설치합니다.

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  자세한 내용은 [가상 머신 확장 및 기능](./extensions/overview.md)을 참조하세요. 또한 [클래식 배포 모델](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)에서 배포된 VM의 확장으로 작업할 수 있습니다.

- **RDMA 네트워크 주소 공간** - Azure의 RDMA 네트워크는 주소 공간 172.16.0.0/16을 예약합니다. Azure 가상 네트워크에 배포된 인스턴스에서 MPI 애플리케이션을 실행하려면 가상 네트워크 주소 공간이 RDMA 네트워크와 겹치지 않도록 해야 합니다.

## <a name="cluster-configuration-options"></a>클러스터 구성 옵션

Azure에서는 다음을 비롯한 RDMA 네트워크를 사용하여 통신할 수 있는 Windows HPC VM의 클러스터를 만드는 몇 가지 옵션을 제공합니다. 

- **가상 머신** - Azure Resource Manager 배포 모델을 사용하는 경우 동일한 가용성 집합에서 RDMA 가능 HPC VM을 배포합니다. 클래식 배포 모델을 사용하는 경우 동일한 클라우드 서비스에서 VM을 배포합니다. 

- **가상 시스템 크기 집합** - 가상 시스템 크기 집합에서 배포를 단일 배치 그룹으로 제한해야 합니다. 예를 들어, Resource Manager 템플릿에서 `singlePlacementGroup` 속성을 `true`로 설정합니다. 

- **가상 시스템 간의 MPI** - 가상 시스템(VM) 간에 MPI 통신이 필요한 경우 VM이 동일한 가용성 집합또는 가상 컴퓨터와 동일한 규모 집합에 있는지 확인합니다.

- **Azure CycleCloud** - [Azure CycleCloud](/azure/cyclecloud/)에서 HPC 클러스터를 만들어서 Windows 노드에서 MPI 작업을 실행합니다.

- **Azure Batch** - [Azure Batch](/azure/batch/) 풀을 만들어서 Windows Server 컴퓨팅 노드에서 MPI 워크로드를 실행합니다. 자세한 내용은 [Batch 풀에서 RDMA 가능 또는 GPU 가능 인스턴스 사용](../batch/batch-pool-compute-intensive-sizes.md)을 참조하세요. 또한 Batch에서 컨테이너 기반 워크로드를 실행하는 방법은 [Batch Shipyard](https://github.com/Azure/batch-shipyard) 프로젝트를 참조하세요.

- **Microsoft HPC 팩** - [HPC 팩에는](https://docs.microsoft.com/powershell/high-performance-computing/overview) RDMA 지원 Linux VM에 배포할 때 Azure RDMA 네트워크를 사용하는 MS-MPI용 런타임 환경이 포함되어 있습니다. 예를 들어 배포를 참조하여 [MpI 응용 프로그램을 실행하기 위해 HPC 팩을 사용하여 Linux RDMA 클러스터 를 설정합니다.](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [컴퓨팅 최적화](sizes-compute.md)
- [메모리 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

- Windows 서버에서 HPC 팩을 사용하여 계산 집약적인 인스턴스를 사용하는 검사 목록은 [MPI 응용 프로그램을 실행하기 위해 HPC 팩을 사용하여 Linux RDMA 클러스터 를 설정합니다.](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)

- Azure Batch에서 MPI 애플리케이션 실행할 때 계산 집약적 인스턴스를 사용하려면 [다중 인스턴스 작업을 사용하여 Azure Batch에서 MPI(메시지 전달 인터페이스) 애플리케이션 실행](../batch/batch-mpi.md)을 참조하세요.

- [ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.