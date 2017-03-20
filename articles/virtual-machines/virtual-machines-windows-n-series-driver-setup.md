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
ms.date: 11/30/2016
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 57d7475db8183cfaad017fc934210d0481868d5f
ms.lasthandoff: 03/03/2017


---
# <a name="set-up-gpu-drivers-for-n-series-windows-vms"></a>N 시리즈 Windows VM의 GPU 드라이버 설정
Windows Server를 실행하는 Azure N 시리즈 VM의 GPU 기능을 이용하려면 배포 후 각 VM에 NVIDIA 그래픽 드라이버를 설치해야 합니다. 이 문서는 [Linux VM](virtual-machines-linux-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에도 적용됩니다.

기본 사양의 저장소 용량 및 디스크 세부 정보는 [가상 컴퓨터 크기](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.




## <a name="supported-gpu-drivers"></a>지원되는 GPU 드라이버

각 N 시리즈 VM에 원격 데스크톱으로 연결합니다. Windows 운영 체제에 지원되는 드라이버를 다운로드, 추출 및 설치합니다. 

### <a name="nvidia-grid-drivers-for-nv-vms"></a>NV VM 용 NVIDIA GRID 드라이버

* [Windows Server 2016](https://go.microsoft.com/fwlink/?linkid=836843) (.zip)

* [Windows Server 2012 R2](https://go.microsoft.com/fwlink/?linkid=836844) (.zip)

### <a name="nvidia-tesla-drivers-for-nc-vms"></a>NC VM 용 NVIDIA 테슬라 드라이버

* [Windows Server 2016](https://go.microsoft.com/fwlink/?linkid=836841) (.zip)

* [Windows Server 2012 R2](https://go.microsoft.com/fwlink/?linkid=836842) (.zip)



## <a name="verify-driver-installation"></a>드라이버 설치 확인

Azure NV VM에서는 드라이버 설치 후 다시 시작해야 합니다. NC Vm에서는 다시 시작 필요하지 않습니다.

장치 관리자에서 드라이버 설치를 확인할 수 있습니다. 다음 예제에서는 Azure NC VM에서 K80 카드의 성공적인 구성을 보여 줍니다.

![GPU 드라이버 속성](./media/virtual-machines-windows-n-series-driver-setup/GPU_driver_properties.png)

GPU 장치 상태를 쿼리하려면 드라이버와 설치된 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 명령줄 유틸리티를 실행합니다. 

![NVIDIA 장치 상태](./media/virtual-machines-windows-n-series-driver-setup/smi.png)  

## <a name="next-steps"></a>다음 단계

* N 시리즈 VM의 NVIDIA GPU에 대한 자세한 내용은 다음을 참조하세요.
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html)(Azure NC VM용)
    * [NVIDIA 테슬라 M60](http://www.nvidia.com/object/tesla-m60.html) (Azure NV VM 용)

* NVIDIA 테슬라 GPU에 대한 GPU 가속 응용 프로그램을 빌드하는 개발자는 [CUDA 도구 키트 8](https://developer.nvidia.com/cuda-downloads) 또한 다운로드하고 설치할 수 있습니다.



