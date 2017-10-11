---
title: "Batch에서 계산 집약적인 Azure VM 사용 | Microsoft Docs"
description: "Azure Batch 풀에서 RDMA 가능 또는 GPU 가능 VM 크기를 활용하는 방법"
services: batch
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: 
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: danlep
ms.openlocfilehash: c52a054e4fc8f61f871acd9f35b9a3e6247e48ef
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2017
---
# <a name="use-rdma-capable-or-gpu-enabled-instances-in-batch-pools"></a>Batch 풀에서 RDMA 가능 또는 GPU 가능 인스턴스 사용

특정 Batch 작업을 실행하려면 대규모 계산을 위해 설계된 Azure VM 크기를 활용하는 것이 좋습니다. 예를 들어, 다중 인스턴스 [MPI 작업](batch-mpi.md)을 실행하려면 RDMA(Remote Direct Memory Access)에 대한 네트워크 인터페이스가 있는 A8, A9 또는 H 시리즈 크기를 선택할 수 있습니다. 이러한 크기는 MPI 응용 프로그램을 가속화할 수 있는 노드 간 통신에 대한 InfiniBand 네트워크에 연결합니다. 또는 CUDA 응용 프로그램의 경우 NVIDIA Tesla GPU(그래픽 처리 장치) 카드를 포함하는 N 시리즈 크기를 선택할 수 있습니다.

이 문서는 Batch 풀에서 Azure의 특별한 크기 중 일부를 사용하는 지침과 예제를 제공합니다. 사양 및 백그라운드는 다음을 참조하세요.

* 고성능 계산 VM 크기([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* GPU 가능 VM 크기([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 


## <a name="subscription-and-account-limits"></a>구독 및 계정 제한

* **할당량** - 하나 이상의 Azure 할당량은 Batch 풀에 추가할 수 있는 노드 형식의 수를 제한할 수 있습니다. RDMA 가능, GPU 가능 또는 기타 멀티 코어 VM 크기를 선택하면 제한될 가능성이 높습니다. 만든 Batch 계정 형식에 따라 할당량은 계정 자체나 구독 할당량에 적용할 수 있습니다.

    * **Batch 서비스** 구성에서 Batch 계정을 만든 경우 [Batch 계정당 전용 코어 할당량](batch-quota-limit.md#resource-quotas)에 의해 제한됩니다. 기본적으로 이 할당량은 20개의 코어입니다. 코어를 사용하는 경우 [낮은 우선 순위 VM](batch-low-pri-vms.md)에 별도 할당량이 적용됩니다. 

    * **사용자 구독** 구성에서 계정을 만든 경우 구독은 지역당 VM 코어의 수를 제한합니다. [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요. 또한 구독은 HPC 및 GPU 인스턴스를 포함한 특정 VM 크기에 지역 할당량을 적용합니다. 사용자 구독 구성에서 추가 할당량이 Batch 계정에 적용되지 않습니다. 

  Batch에서 특별한 VM 크기를 사용하는 경우 하나 이상의 할당량을 증가시켜야 합니다. 할당량 증가를 요청하려면 추가 비용 없이 [온라인 고객 지원 요청](../azure-supportability/how-to-create-azure-support-request.md)을 개설합니다.

* **지역 가용성** - 계산 집약적인 VM은 Batch 계정을 만든 지역에서 사용하지 못할 수도 있습니다. 크기를 사용할 수 있는지를 확인하려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/)을 참조하세요.


## <a name="dependencies"></a>종속성

계산 집약적인 크기의 RDMA 및 GPU 기능은 특정 운영 체제에서만 지원됩니다. 운영 체제에 따라 추가 드라이버 또는 다른 소프트웨어를 설치하거나 구성할 수 있습니다. 다음 표에서는 이러한 종속성을 요약합니다. 자세한 내용은 연결된 문서를 참조하세요. Batch 풀을 구성하는 옵션은 이 문서의 뒷부분을 참조하세요.


### <a name="linux-pools---virtual-machine-configuration"></a>Linux 풀 - 가상 컴퓨터 구성

| 크기 | 기능 | 운영 체제 | 필수 소프트웨어 | 풀 설정 |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances) | RDMA | SUSE Linux Enterprise Server 12 HPC 또는<br/>CentOS 기반 HPC<br/>(Azure Marketplace) | Intel MPI 5 | 노드 간 통신 사용, 동시 작업 실행 사용 안 함 |
| [NC 시리즈*](../virtual-machines/linux/n-series-driver-setup.md#install-cuda-drivers-for-nc-vms) | NVIDIA Tesla K80 GPU | Ubuntu 16.04 LTS.<br/>Red Hat Enterprise Linux 7.3 또는<br/>CentOS 기반 7.3<br/>(Azure Marketplace) | NVIDIA CUDA Toolkit 8.0 드라이버 | 해당 없음 | 
| [NV 시리즈](../virtual-machines/linux/n-series-driver-setup.md#install-grid-drivers-for-nv-vms) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS<br/>Red Hat Enterprise Linux 7.3<br/>CentOS 기반 7.3<br/>(Azure Marketplace) | NVIDIA GRID 4.3 드라이버 | 해당 없음 |

*NC24r VM의 RDMA 연결은 Intel MPI와 CentOS 기반 7.3 HPC에서 지원됩니다.



### <a name="windows-pools---virtual-machine-configuration"></a>Windows 풀 - 가상 컴퓨터 구성

| 크기 | 기능 | 운영 체제 | 필수 소프트웨어 | 풀 설정 |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2012 R2 또는<br/>Windows Server 2012(Azure Marketplace) | Microsoft MPI 2012 R2 이상 또는<br/> Intel MPI 5<br/><br/>HpcVMDrivers Azure VM 확장 | 노드 간 통신 사용, 동시 작업 실행 사용 안 함 |
| [NC 시리즈*](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla K80 GPU | Windows Server 2016 또는 <br/>Windows Server 2012 R2(Azure Marketplace) | NVIDIA Tesla 드라이버 또는 CUDA Toolkit 8.0 드라이버| 해당 없음 | 
| [NV 시리즈](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 또는<br/>Windows Server 2012 R2(Azure Marketplace) | NVIDIA GRID 4.3 드라이버 | 해당 없음 |

*NC24r VM의 RDMA 연결은 HpcVMDrivers 확장 및 Microsoft MPI 또는 Intel MPI를 사용하는 Windows Server 2012 R2에서 지원됩니다.

### <a name="windows-pools---cloud-services-configuration"></a>Windows 풀 - 클라우드 서비스 구성

> [!NOTE]
> N 시리즈 크기는 클라우드 서비스 구성을 사용하는 Batch 풀에서 지원되지 않습니다.
>

| 크기 | 기능 | 운영 체제 | 필수 소프트웨어 | 풀 설정 |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2012 R2,<br/>Windows Server 2012 또는<br/>Windows Server 2008 R2(게스트 OS 제품군) | Microsoft MPI 2012 R2 이상 또는<br/>Intel MPI 5<br/><br/>HpcVMDrivers Azure VM 확장 | 노드 간 통신 사용<br/> 동시 작업 실행 사용 안 함 |





## <a name="pool-configuration-options"></a>풀 구성 옵션

Batch 풀에 특별한 VM 크기를 구성하려면 Batch API 및 도구는 다음을 포함하여 필수 소프트웨어 또는 드라이버를 설치하는 몇 가지 옵션을 제공합니다.

* [시작 작업](batch-api-basics.md#start-task) - Batch 계정과 동일한 지역에서 Azure Storage 계정에 리소스 파일로 설치 패키지를 업로드합니다. 풀이 시작될 때 리소스 파일을 자동으로 설치하는 시작 작업 명령줄을 만듭니다. 자세한 내용은 [REST API 설명서](/rest/api/batchservice/add-a-pool-to-an-account#bk_starttask)를 참조하세요.

  > [!NOTE] 
  > 시작 태스크는 상승된(관리자) 권한으로 실행해야 하며 성공을 기다려야 합니다.
  >

* [응용 프로그램 패키지](batch-application-packages.md) - Batch 계정에 압축된 설치 패키지를 추가하고 풀에서 패키지 참조를 구성합니다. 이 설정은 풀에 있는 모든 노드에 패키지를 업로드하고 압축을 풉니다. 패키지가 설치 관리자인 경우 자동으로 모든 풀 노드에서 앱을 설치하는 시작 작업 명령줄을 만듭니다. 필요에 따라 작업이 노드에서 실행되도록 예약되는 경우 패키지를 설치합니다.

* [풀 이미지 사용자 정의](batch-api-basics.md#pool) - 드라이버와 소프트웨어 또는 VM 크기에 필요한 기타 설정을 포함하는 사용자 지정 Windows 또는 Linux VM 이미지를 만듭니다. 사용자 구독 구성에서 Batch 계정을 만든 경우 Batch 풀에 대한 사용자 지정 이미지를 지정합니다. (사용자 지정 이미지는 Batch 서비스 구성에 있는 계정에서 지원되지 않습니다.) 사용자 지정 이미지는 가상 컴퓨터 구성에서 풀과 함께 사용될 수 있습니다.

  > [!IMPORTANT]
  > Batch 풀에서 Managed Disks 또는 Premium Storage로 만든 사용자 지정 이미지를 현재 사용할 수 없습니다.
  >



* [Batch Shipyard](https://github.com/Azure/batch-shipyard)는 Azure Batch에서 컨테이너화된 워크로드를 사용하여 투명하게 작동하도록 자동으로 GPU 및 RDMA를 구성합니다. Batch Shipyard는 구성 파일에 의해 전적으로 결정됩니다. [CNTK GPU 레시피](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI)와 같은 GPU 및 RDMA의 워크로드를 사용할 수 있는 여러 가지 샘플 레시피 구성이 있습니다. 이 기능은 N 시리즈 VM에서 GPU 드라이버를 미리 구성하고 Microsoft Cognitive Toolkit 소프트웨어를 Docker 이미지로 로드합니다.


## <a name="example-microsoft-mpi-on-an-a8-vm-pool"></a>예제: A8 VM 풀의 Microsoft MPI

Azure A8 노드의 풀에서 Windows MPI 응용 프로그램을 실행하려면 지원되는 MPI 구현을 설치해야 합니다. Batch 응용 프로그램 패키지를 사용하여 Windows 풀에서 [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx)를 설치하는 샘플 단계는 다음과 같습니다.

1. 최신 버전의 Microsoft MPI에 대한 [설치 패키지](http://go.microsoft.com/FWLink/p/?LinkID=389556)(MSMpiSetup.exe)를 다운로드합니다.
2. 패키지의 zip 파일을 만듭니다.
3. Batch 계정에 패키지를 업로드합니다. 단계는 [응용 프로그램 패키지](batch-application-packages.md) 지침을 참조하세요. *MSMPI*와 같은 응용 프로그램 ID 및 *8.1*과 같은 버전을 지정합니다. 
4. Batch API 또는 Azure Portal을 사용하여 원하는 수의 노드 및 규모로 클라우드 서비스 구성에서 풀을 만듭니다. 다음 표에서는 시작 태스크를 사용하여 무인 모드에서 MPI를 설정하는 샘플 설정을 보여줍니다.

| 설정 | 값 |
| ---- | ----- | 
| **이미지 형식** | 클라우드 서비스 |
| **OS 제품군** | Windows Server 2012 R2(OS 제품군 4) |
| **노드 크기** | A8 표준 |
| **노드 간 통신 사용** | True |
| **노드당 최대 작업** | 1 |
| **응용 프로그램 패키지 참조** | MSMPI |
| **시작 작업 사용** | True<br>**명령 줄** - `"cmd /c %AZ_BATCH_APP_PACKAGE_MSMPI#8.1%\\MSMpiSetup.exe -unattend -force"`<br/>**사용자 ID** - 풀 autouser, 관리자<br/>**성공 대기** - True

## <a name="example-nvidia-tesla-drivers-on-nc-vm-pool"></a>예제: NC VM 풀의 NVIDIA Tesla 드라이버

Linux NC 노드의 풀에서 CUDA 응용 프로그램을 실행하려면 노드에서 CUDA Toolkit 8.0을 설치해야 합니다. 이 Toolkit은 필요한 NVIDIA Tesla GPU 드라이버를 설치합니다. GPU 드라이버에서 사용자 지정 Ubuntu 16.04 LTS 이미지를 배포하는 예제 단계는 다음과 같습니다.

1. Ubuntu 16.04 LTS를 실행하는 Azure NC6 VM을 배포합니다. 예를 들어, 미국 중남부 지역에서 VM을 만듭니다. 표준 저장소로 Managed Disks *없이* VM을 만들 수 있는지 확인합니다.
2. VM에 연결하고 [CUDA 드라이버 설치](../virtual-machines/linux/n-series-driver-setup.md#install-cuda-drivers-for-nc-vms)하는 단계를 수행합니다.
3. Linux 에이전트의 프로비전을 해제하고 Azure CLI 1.0 명령을 사용하여 Linux VM 이미지를 캡처합니다. 단계는 [Azure에서 실행되는 Linux 가상 컴퓨터 캡처하기](../virtual-machines/linux/capture-image-nodejs.md)를 참조하세요. 이미지 URI를 기록해 둡니다.
  > [!IMPORTANT]
  > Azure Batch에 대한 이미지를 캡처하기 위해 Azure CLI 2.0 명령을 사용하지 않습니다. 현재 CLI 2.0 명령은 Managed Disks를 사용하여 만든 VM만을 캡처합니다.
  >
4. 사용자 구독 구성을 사용하여 NC VM을 지원하는 지역에서 Batch 계정을 만듭니다.
5. Batch API 또는 Azure Portal을 사용하여 원하는 수의 노드 및 규모로 사용자 지정 이미지를 사용하는 풀을 만듭니다. 다음 표에서는 이미지의 샘플 풀 설정을 보여줍니다.

| 설정 | 값 |
| ---- | ---- |
| **이미지 형식** | 사용자 지정 이미지 |
| **사용자 지정 이미지** | 양식의 이미지 URI `https://yourstorageaccountdisks.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd` |
| **노드 에이전트 SKU** | batch.node.ubuntu 16.04 |
| **노드 크기** | NC6 표준 |



## <a name="next-steps"></a>다음 단계

* Azure Batch 풀에서 MPI 작업을 실행하려면 [Windows](batch-mpi.md) 또는 [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) 예제를 참조하세요.

* Batch에서 GPU 워크로드의 예제는 [Batch Shipyard](https://github.com/Azure/batch-shipyard/) 레시피를 참조하세요.