---
title: Azure VM 크기-HPC | Microsoft Docs
description: Azure에서 고성능 컴퓨팅 가상 머신에 사용할 수 있는 다양 한 크기를 나열 합니다. 이 시리즈의 크기에 대한 스토리지 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.
author: vermagit
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 2d52287d1c343ada58ed4f7e5e1d3e85a4e7162e
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85850449"
---
# <a name="high-performance-computing-vm-sizes"></a>고성능 컴퓨팅 VM 크기

Azure H 시리즈 Vm (가상 머신)은 다양 한 실제 HPC 워크 로드에 대 한 리더십 클래스 성능, MPI 확장성 및 비용 효율성을 제공 하도록 설계 되었습니다.

[HBv2 시리즈](hbv2-series.md) Vm 기능 200 g b/초 Mellanox HDR InfiniBand가 있지만 HB 및 HC 시리즈 Vm은 100 g b/초 Mellanox EDR InfiniBand입니다. 이러한 각 VM 유형은 최적화 되 고 일관 된 RDMA 성능을 위해 차단 되지 않는 fat 트리에 연결 됩니다. HBv2 Vm은 적응 라우팅 및 DCT (표준 RC 및 UD 전송에 대 한 추가)를 지원 합니다. 이러한 기능은 응용 프로그램의 성능, 확장성 및 일관성을 향상 시키고 이러한 기능을 사용 하는 것이 좋습니다.

[Hb-시리즈](hb-series.md) Vm은 유체 dynamics, 명시적 유한 요소 분석, 날씨 모델링 등 메모리 대역폭을 기반으로 하는 응용 프로그램에 최적화 되어 있습니다. HB Vm 기능 60 AMD EPYC 7551 프로세서 코어, CPU 코어 당 4gb RAM, 하이퍼스레딩을 없음 AMD EPYC 플랫폼은 260 g b/초 이상의 메모리 대역폭을 제공 합니다.

[HC 시리즈](hc-series.md) Vm은 암시적 유한 요소 분석, 분자 dynamics 및 계산 연금술과 같이 조밀한 계산으로 구동 되는 응용 프로그램에 최적화 되어 있습니다. HC Vm 기능 44 Intel Xeon 플래티넘 8168 프로세서 코어, CPU 코어 당 8gb RAM, 하이퍼스레딩을 없음 Intel Xeon Platinum 플랫폼은 intel 수학 커널 라이브러리와 같은 소프트웨어 도구에 대 한 다양 한 소프트웨어 에코 시스템을 지원 합니다.

[H 시리즈](h-series.md) Vm은 높은 CPU 주파수 또는 핵심 요구 사항 당 대용량 메모리를 기반으로 하는 응용 프로그램에 최적화 되어 있습니다. H 시리즈 Vm은 8 또는 16 Intel Xeon E5 2667 v3 프로세서 코어, 7 개 또는 14gb의 RAM 및 CPU 코어 당 RAM이 없으며 하이퍼스레딩을 갖지 않습니다. H 시리즈 기능은 일관 된 RDMA 성능을 위해 비 블로킹 된 fat 트리 구성에서 56 g b/초 Mellanox Mellanox DR InfiniBand을 제공 합니다. H 시리즈 Vm은 Intel MPI 5.x 및 MS MPI를 지원 합니다.

> [!NOTE]
> A8 ~ A11 VM은 2021년 3월에 사용 중지될 예정입니다. 자세한 내용은 [HPC 마이그레이션 가이드](https://azure.microsoft.com/resources/hpc-migration-guide/)를 참조하세요.

## <a name="rdma-capable-instances"></a>RDMA 지원 인스턴스

대부분의 HPC VM 크기 (HBv2, HB, HC, H16r, H16mr, A8 및 A9)는 RDMA (원격 직접 메모리 액세스) 연결을 위한 네트워크 인터페이스를 지원 합니다. NC24rs 구성 (NC24rs_v3, NC24rs_v2 및 NC24r)과 같이 ' r '로 지정 된 [N 시리즈](https://docs.microsoft.com/azure/virtual-machines/nc-series) 크기를 선택 하면 RDMA도 가능 합니다. 이 인터페이스는 다른 VM 크기에서 사용할 수 있는 표준 Azure 네트워크 인터페이스에 추가 됩니다.

이 인터페이스를 사용 하면 RDMA 지원 인스턴스가 InfiniBand (IB) 네트워크를 통해 통신할 수 있으며, HBv2에 대 한 HDR 요금, HC, H16r, H16mr 및 RDMA 지원에 대 한 FDR 요금, A8 및 A9 Vm에 대 한 QDR 요금에 대 한 EDR 요금으로 작동할 수 있습니다. 이러한 RDMA 기능은 특정 MPI(Message Passing Interface) 애플리케이션의 확장성 및 성능을 향상시킬 수 있습니다. 속도에 대 한 자세한 내용은이 페이지의 테이블에 있는 세부 정보를 참조 하세요.

> [!NOTE]
> Azure HPC에는 InfiniBand에 대해 SR-IOV를 사용 하도록 설정 되었는지 여부에 따라 Vm의 두 가지 클래스가 있습니다. 현재 InfiniBand 사용 Vm에 대 한 SR-IOV는 HBv2, HB, HC, NCv3 및 NDv2입니다. InfiniBand 사용 Vm의 나머지는 SR-IOV를 사용 하도록 설정 되어 있지 않습니다.
> RDMA over IB는 모든 RDMA 지원 Vm에 대해 지원 됩니다.
> IP over IB는 SR-IOV 사용 Vm 에서만 지원 됩니다.

- **운영 체제** -LINUX는 HPC vm에 대해 매우 잘 지원 됩니다. CentOS, RHEL, Ubuntu, SUSE 등의 배포판은 일반적으로 사용 됩니다. Windows 지원과 관련 하 여 Windows Server 2016 이상 버전은 모든 HPC 시리즈 Vm에서 지원 됩니다. Windows Server 2012 R2, Windows Server 2012는 SR-IOV를 사용 하지 않는 Vm (H16r, H16mr, A8 및 A9) 에서도 지원 됩니다. [Windows Server 2012 r 2는 HBv2 및 64 (가상 또는 실제) 코어가 있는 다른 vm에서 지원 되지](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)않습니다.

- **Mpi** -AZURE에서 SR-IOV 지원 VM 크기 (HBv2, HB, HC, NCv3, NDv2)는 모든 MPI의 모든 버전을 Mellanox OFED와 함께 사용할 수 있습니다.
SR-IOV를 사용할 수 없는 Vm에서 지원 되는 MPI 구현은 Microsoft 네트워크 직접 (ND) 인터페이스를 사용 하 여 Vm 간에 통신 합니다. 따라서 Microsoft MPI (MS MPI) 2012 R2 이상 및 Intel MPI 5.x 버전만 지원 됩니다. Intel MPI 런타임 라이브러리의 이후 버전 (2017, 2018)은 Azure RDMA 드라이버와 호환 될 수도 있고 그렇지 않을 수도 있습니다.

- **InfiniBandDriver<Linux | Windows> VM 확장** -RDMA 지원 vm에서 InfiniBandDriver<Linux를 추가 합니다. InfiniBand를 사용 하도록 설정 하는 Windows> 확장 Linux에서 InfiniBandDriverLinux VM 확장은 RDMA 연결에 대 한 Mellanox OFED 드라이버 (SR-IOV Vm)를 설치 합니다. Windows에서 InfiniBandDriverWindows VM 확장은 RDMA 연결에 Windows 네트워크 직접 드라이버 (SR-IOV Vm이 아닌 Vm) 또는 Mellanox OFED 드라이버 (SR-IOV Vm)를 설치 합니다.
A8 및 A9 인스턴스의 특정 배포에서 HpcVmDrivers 확장이 자동으로 추가 됩니다. HpcVmDrivers VM 확장은 더 이상 사용 되지 않습니다. 업데이트 되지 않습니다.
VM에 VM 확장을 추가해야 하는 경우 [Azure PowerShell](/powershell/azure/overview) cmdlet을 사용할 수 있습니다. 

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

  다음 명령은 *Myresourcegroup*이라는 리소스 그룹에 배포 된 *myvmss* 라는 기존 가상 머신 확장 집합의 모든 RDMA 지원 vm에 최신 버전 1.0 InfiniBandDriverWindows 확장을 설치 합니다.

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

- **가상 컴퓨터** -RDMA 가능 HPC vm을 동일한 확장 집합 또는 가용성 집합 (Azure Resource Manager 배포 모델을 사용 하는 경우)에 배포 합니다. 클래식 배포 모델을 사용하는 경우 동일한 클라우드 서비스에서 VM을 배포합니다.

- **Virtual machine scale sets** -vmss (가상 머신 확장 집합)에서 배포를 단일 배치 그룹으로 제한 하 여 vmss 내의 InfiniBand 통신을 확인 합니다. 예를 들어, Resource Manager 템플릿에서 `singlePlacementGroup` 속성을 `true`로 설정합니다. 속성으로 분리 수 있는 최대 VMSS 크기는 `singlePlacementGroup` `true` 기본적으로 100 vm에 있습니다. HPC 작업 규모 요구가 단일 VMSS 테 넌 트의 100 Vm 보다 높은 경우, 무료로 무료로 [온라인 고객 지원 요청을 열](../azure-supportability/how-to-create-azure-support-request.md) 수 있습니다. 단일 VMSS의 Vm 수에 대 한 제한을 300로 늘릴 수 있습니다. 가용성 집합을 사용 하 여 Vm을 배포 하는 경우 최대 제한은 가용성 집합 당 Vm 200에 있습니다.

- **가상 머신 간의 MPI** -가상 머신 (vm) 간에 RDMA (예: MPI 통신 사용)가 필요한 경우 vm이 동일한 가상 머신 확장 집합 또는 가용성 집합에 있는지 확인 합니다.

- **Azure CycleCloud** - [AZURE CycleCloud](/azure/cyclecloud/) 에서 HPC 클러스터를 만들어 MPI 작업을 실행 합니다.

- **Azure Batch** -MPI 작업을 실행 하는 [Azure Batch](/azure/batch/) 풀을 만듭니다. Azure Batch에서 MPI 애플리케이션 실행할 때 계산 집약적 인스턴스를 사용하려면 [다중 인스턴스 작업을 사용하여 Azure Batch에서 MPI(메시지 전달 인터페이스) 애플리케이션 실행](../batch/batch-mpi.md)을 참조하세요.

- **MICROSOFT HPC 팩**  -  [HPC 팩](https://docs.microsoft.com/powershell/high-performance-computing/overview) 에는 Rdma 지원 Linux vm에 배포 될 때 Azure rdma 네트워크를 사용 하는 MS MPI 용 런타임 환경이 포함 되어 있습니다. 배포 예는 [MPI 응용 프로그램을 실행 하려면 HPC Pack을 사용 하 여 LINUX RDMA 클러스터 설정](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)을 참조 하세요.

## <a name="deployment-considerations"></a>배포 고려 사항

- **Azure 구독** - 몇몇 계산 집약적 인스턴스를 배포하려면 종량제 구독 또는 기타 구매 옵션을 고려합니다. [Azure 무료 계정](https://azure.microsoft.com/free/)을 사용하는 경우, 제한된 수의 Azure 컴퓨팅 코어만 사용할 수 있습니다.

- **가격 책정 및 가용성** - 이러한 VM 크기는 표준 가격 책정 계층에만 제공됩니다. [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/) 에서 Azure 지역의 가용성을 확인하세요.

- **코어 할당량** – 기본값에서 Azure 구독의 코어 할당량을 늘려야 합니다. 구독에 따라서도 H 시리즈를 포함하여 특정 VM 크기 제품군에 배포할 수 있는 코어 수가 제한될 수 있습니다. 할당량 증가를 요청하려면 무료로 [온라인 고객 지원 요청을 개설](../azure-supportability/how-to-create-azure-support-request.md) 합니다. (기본 제한은 구독 범주에 따라 달라질 수 있습니다.)

  > [!NOTE]
  > 대규모 용량이 필요한 경우 Azure 지원에 문의합니다. Azure 할당량은 신용 제한이며 용량 보증이 아닙니다. 할당량에 관계 없이 사용하는 코어에 대해서만 요금이 청구됩니다.
  
- **가상 네트워크** - Azure [가상 네트워크](https://azure.microsoft.com/documentation/services/virtual-network/) 는 계산 집약적 인스턴스를 사용할 필요가 없습니다. 하지만 많은 배포에서 온-프레미스 리소스에 액세스해야 하는 경우 적어도 클라우드 기반 Azure 가상 네트워크 또는 사이트 간 연결이 필요합니다. 필요한 경우 인스턴스를 배포할 새 가상 네트워크를 만듭니다. 선호도 그룹에서 가상 네트워크에 계산 집약적 VM을 추가하는 것은 지원되지 않습니다.

- **크기 조정** - 특수한 하드웨어로 인해 동일한 크기의 제품군(H 시리즈 또는 계산 집약적 A 시리즈) 내에서만 계산 집약적 인스턴스의 크기를 조정할 수 있습니다. 예를 들어 H 시리즈 VM 크기는 한 H 시리즈에서 다른 H 시리즈로만 조정할 수 있습니다. 또한 계산 집약적이지 않은 크기에서 계산 집약적 크기로 조정하는 것은 지원되지 않습니다.  


## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [컴퓨팅 최적화](sizes-compute.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

- Azure 용 HPC 응용 프로그램 최적화 및 [Hpc 작업](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/overview) 의 몇 가지 예제에 대해 자세히 알아보세요. 

- [ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
