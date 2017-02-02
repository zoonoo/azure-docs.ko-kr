---
title: "Linux용 Azure N 시리즈 드라이버 설치 | Microsoft Docs"
description: "Azure에서 Linux를 실행하는 N 시리즈 VM의 NVIDIA GPU 드라이버를 설정하는 방법"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/07/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 4bf470737f961219250c37d890ed81eb62409626
ms.openlocfilehash: f03026212dfcbe2faa22188ebfb2e953114a87fd


---
# <a name="set-up-gpu-drivers-for-n-series-vms"></a>N 시리즈 VM의 GPU 드라이버 설정
지원되는 Linux 배포를 실행하는 Azure N 시리즈 VM의 GPU 기능을 이용하려면 배포 후 각 VM에 NVIDIA 그래픽 드라이버를 설치해야 합니다. 이 문서는 [Windows VM](virtual-machines-windows-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에도 적용됩니다.

N 시리즈 VM 사양, 저장소 용량 및 디스크 세부 정보는 [Virtual Machines 크기](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.



## <a name="supported-gpu-drivers"></a>지원되는 GPU 드라이버


> [!NOTE]
> 현재 Linux GPU 지원은 Ubuntu Server 16.04 LTS를 실행하는 Azure NC VM에서만 사용할 수 있습니다.

### <a name="nvidia-tesla-drivers-for-nc-vms"></a>NC VM용 NVIDIA Tesla 드라이버

* [Ubuntu 16.04 LTS](https://go.microsoft.com/fwlink/?linkid=836899)(.run 자동 압축 풀기 설치 관리자)

## <a name="tesla-driver-installation-on-ubuntu-1604-lts"></a>Ubuntu 16.04 LTS에서 Tesla 드라이버 설치

1. Azure N 시리즈 VM에 대한 SSH 연결을 확인합니다.

2. 시스템에 CUDA 호환 GPU가 있는지 확인하려면 다음 명령을 실행합니다.

    ```bash
    lspci | grep -i NVIDIA
    ```
    다음 예제와 비슷한 출력이 표시됩니다(NVIDIA Tesla K80 카드 표시).

    ![lspci 명령 출력](./media/virtual-machines-linux-n-series-driver-setup/lspci.png)

3. 배포에 필요한 드라이버에 맞는 .run 파일을 다운로드합니다. 다음은 /tmp 디렉터리에 Ubuntu 16.04 LTS Tesla 드라이버를 다운로드하는 예제 명령입니다.

    ```bash
    wget -O /tmp/NVIDIA-Linux-x86_64-367.48.run https://go.microsoft.com/fwlink/?linkid=836899
    ```

4. 시스템에 `gcc` 및 `make`를 설치해야 할 경우(테슬라 드라이버에 필수임) 다음을 입력합니다.

    ```bash
    sudo apt-get update
    
    sudo apt install gcc

    sudo apt install make
    ```

4. 드라이버 설치 프로그램이 포함된 디렉터리로 변경하여 다음과 비슷한 명령을 실행합니다.

    ```bash
    chmod +x NVIDIA-Linux-x86_64-367.48.run
    
    sudo sh ./NVIDIA-Linux-x86_64-367.48.run
    ```

## <a name="verify-driver-installation"></a>드라이버 설치 확인


GPU 장치 상태를 쿼리하려면 드라이버와 함께 설치된 명령줄 유틸리티인 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface)를 실행합니다. 

![NVIDIA 장치 상태](./media/virtual-machines-linux-n-series-driver-setup/smi.png)

## <a name="optional-installation-of-nvidia-cuda-toolkit-on-ubuntu-1604-lts"></a>Ubuntu 16.04 LTS의 NVIDIA CUDA Toolkit 선택적 설치

Ubuntu 16.04 LTS를 실행하는 NC VM에 필요에 따라 NVIDIA CUDA Toolkit 8.0을 설치할 수 있습니다. GPU 드라이버 외에도 Toolkit는 GPU 가속 응용 프로그램을 빌드하는 C 및 C++ 개발자를 위한 포괄적인 개발 환경을 제공합니다.

CUDA Toolkit를 설치하려면 다음과 비슷한 명령을 실행합니다.

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.44-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers
```

설치에는 몇 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

* N 시리즈 VM의 NVIDIA GPU에 대한 자세한 내용은 다음을 참조하세요.
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html)(Azure NC VM용)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html)(Azure NC VM용)




<!--HONumber=Dec16_HO2-->


