---
title: "Windows 용 Azure N 시리즈 드라이버 설정 | Microsoft Docs"
description: "Azure에서 Windows를 실행하는 N 시리즈 VM에 대한 NVIDIA GPU 드라이버를 설정하는 방법"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 2dbd88a2a83643130c336253d2c7a5a21d73671e
ms.lasthandoff: 03/15/2017


---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows-server"></a>Windows Server가 실행되는 N 시리즈 VM의 GPU 드라이버 설정
Windows Server 2016 또는 Windows Server 2012 R2를 실행하는 Azure N 시리즈 VM의 GPU 기능을 이용하려면 배포 후 각 VM에 NVIDIA 그래픽 드라이버를 설치해야 합니다. [Linux VM](virtual-machines-linux-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에 대한 드라이버 설치 정보도 사용할 수 있습니다.

기본 사양의 저장소 용량 및 디스크 세부 정보는 [가상 컴퓨터 크기](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요. [N 시리즈 VM에 대한 일반적인 고려 사항](#general-considerations-for-n-series-vms)도 참조하세요.



## <a name="supported-gpu-drivers"></a>지원되는 GPU 드라이버

각 N 시리즈 VM에 원격 데스크톱으로 연결합니다. Windows 운영 체제에 지원되는 드라이버를 다운로드, 추출 및 설치합니다. 

### <a name="nvidia-tesla-drivers-for-nc-vms-tesla-k80"></a>NC VM용 NVIDIA Tesla 드라이버(Tesla K80)



| OS | 드라이버 버전 |
| -------- |------------- |
| Windows Server 2016 | [376.84](http://us.download.nvidia.com/Windows/Quadro_Certified/376.84/376.84-tesla-desktop-winserver2016-international-whql.exe)(.exe) |
| Windows Server 2012 R2 | [376.84](http://us.download.nvidia.com/Windows/Quadro_Certified/376.84/376.84-tesla-desktop-winserver2008-2012r2-64bit-international-whql.exe)(.exe) |


### <a name="nvidia-grid-drivers-for-nv-vms-tesla-m60"></a>NV VM용 NVIDIA GRID 드라이버(Tesla M60)

| OS | 드라이버 버전 |
| -------- |------------- |
| Windows Server 2016 | [369.71](https://go.microsoft.com/fwlink/?linkid=836842)(.zip) |
| Windows Server 2012 R2 | [369.30](https://go.microsoft.com/fwlink/?linkid=836841)(.zip)  |

> [!NOTE]
> 여기에 제공된 드라이버 다운로드 링크는 게시 시점에 최신 링크였습니다. 최신 드라이버에 대해서는 [NVIDIA](http://www.nvidia.com/) 웹 사이트를 참조하세요.
>

## <a name="verify-gpu-driver-installation"></a>GPU 드라이버 설치 확인

Azure NV VM에서는 드라이버 설치 후 다시 시작해야 합니다. NC Vm에서는 다시 시작 필요하지 않습니다.

장치 관리자에서 드라이버 설치를 확인할 수 있습니다. 다음 예제에서는 Azure NC VM에서 Tesla K80 카드의 성공적인 구성을 보여 줍니다.

![GPU 드라이버 속성](./media/virtual-machines-windows-n-series-driver-setup/GPU_driver_properties.png)

GPU 장치 상태를 쿼리하려면 드라이버와 설치된 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 명령줄 유틸리티를 실행합니다. 

![NVIDIA 장치 상태](./media/virtual-machines-windows-n-series-driver-setup/smi.png)  

## <a name="rdma-network-for-nc24r-vms"></a>NC24r VM에 대한 RDMA 네트워크

동일한 가용성 집합에 배포된 NC24r VM에서 RDMA 네트워크 연결을 사용하도록 설정할 수 있습니다. RDMA 연결에 필요한 Windows 네트워크 장치 드라이버를 설치하는 HpcVmDrivers 확장을 추가해야 합니다. NC24r VM에 VM 확장을 추가하려면 Azure Resource Manager용 [Azure PowerShell](/powershell/azureps-cmdlets-docs) cmdlet을 사용합니다.

> [!NOTE]
> 현재 Windows Server 2012 R2는 NC24r VM에서 RDMA 네트워크를 지원합니다.
> 

미국 서부 하위 지역에서 이름이 myVM인 기존 RDMA 지원 VM에 최신 버전 1.1 HpcVMDrivers 확장을 설치하려면
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  자세한 내용은 [Windows용 가상 컴퓨터 확장 및 기능](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요.

RDMA 네트워크는 [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) 또는 Intel MPI 5.x를 사용하여 실행되는 응용 프로그램에 대한 MPI(Message Passing Interface) 트래픽을 지원합니다. 

[!INCLUDE [virtual-machines-n-series-considerations](../../includes/virtual-machines-n-series-considerations.md)]

## <a name="next-steps"></a>다음 단계

* N 시리즈 VM의 NVIDIA GPU에 대한 자세한 내용은 다음을 참조하세요.
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html)(Azure NC VM용)
    * [NVIDIA 테슬라 M60](http://www.nvidia.com/object/tesla-m60.html) (Azure NV VM 용)

* NVIDIA Tesla GPU에 대한 GPU 가속 응용 프로그램을 빌드하는 개발자는 [Windows Server 2016](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_win10-exe) 또는 [Windows Server 2012 R2](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_windows-exe)용 CUDA 도구 키트 8을 다운로드하고 설치할 수 있습니다. 자세한 내용은 [CUDA 설치 가이드](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi)를 참조하세요.



