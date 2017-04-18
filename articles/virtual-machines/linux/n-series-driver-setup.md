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
ms.date: 03/10/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e7f6c840be3a284f635114287a69c151f671531d
ms.lasthandoff: 04/03/2017


---

# <a name="set-up-gpu-drivers-for-n-series-vms-running-linux"></a>Linux가 실행되는 N 시리즈 VM의 GPU 드라이버 설정

지원되는 Linux 배포를 실행하는 Azure N 시리즈 VM의 GPU 기능을 이용하려면 배포 후 각 VM에 NVIDIA 그래픽 드라이버를 설치해야 합니다. [Windows VM](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에 대한 드라이버 설치 정보도 사용할 수 있습니다.


> [!IMPORTANT]
> 현재 Linux GPU 지원은 Ubuntu Server 16.04 LTS를 실행하는 Azure NC VM에서만 사용할 수 있습니다.
> 

N 시리즈 VM 사양, 저장소 용량 및 디스크 세부 정보는 [Virtual Machines 크기](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. [N 시리즈 VM에 대한 일반적인 고려 사항](#general-considerations-for-n-series-vms)도 참조하세요.



## <a name="install-nvidia-cuda-drivers"></a>NVIDIA CUDA 드라이버 설치

NVIDIA CUDA 도구 키트 8.0에서 Linux NC VM에 NVIDIA 드라이버를 설치하는 단계는 다음과 같습니다. 경우에 따라 C 및 C++ 개발자는 GPU 가속 응용 프로그램을 빌드하기 위해 전체 도구 키트를 설치할 수도 있습니다. 자세한 내용은 [CUDA 설치 가이드](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)를 참조하세요.


> [!NOTE]
> 여기에 제공된 드라이버 다운로드 링크는 게시 시점에 최신 링크였습니다. 최신 드라이버에 대해서는 [NVIDIA](http://www.nvidia.com/) 웹 사이트를 참조하세요.

CUDA 도구 키트를 설치하려면 각 VM에 SSH 연결을 설정합니다. 시스템에 CUDA 호환 GPU가 있는지 확인하려면 다음 명령을 실행합니다.

```bash
lspci | grep -i NVIDIA
```
다음 예제와 비슷한 출력이 표시됩니다(NVIDIA Tesla K80 카드 표시).

![lspci 명령 출력](./media/n-series-driver-setup/lspci.png)

그런 다음 배포 관련 특정 명령을 실행합니다.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.61-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers

```
설치에는 몇 분 정도 걸릴 수 있습니다.

필요에 따라 전체 CUDA 도구 키트를 설치하려면 다음을 입력합니다.

```bash
sudo apt-get install cuda
```

VM 다시 부팅하고 계속해서 설치를 확인합니다.

## <a name="verify-driver-installation"></a>드라이버 설치 확인


GPU 장치 상태를 쿼리하려면 VM에 대해 SSH를 실행하고 드라이버와 설치된 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 명령줄 유틸리티를 실행합니다. 

![NVIDIA 장치 상태](./media/n-series-driver-setup/smi.png)

## <a name="cuda-driver-updates"></a>CUDA 드라이버 업데이트

배포 후에 CUDA 드라이버를 정기적으로 업데이트하는 것이 좋습니다.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers
```

업데이트가 완료되면 VM을 다시 시작합니다.


[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Ubuntu NC VM에 최신 드라이버를 사용하는 X 서버 또는 기타 시스템을 설치하는 것은 권장하지 않습니다. NVIDIA GPU 드라이버를 설치하기 전에 최신 드라이버를 사용하지 않도록 설정 해야 합니다.  

* NVIDIA 드라이버를 설치한 Linux VM의 이미지를 캡처하려면 [Linux 가상 컴퓨터를 일반화하고 캡처하는 방법](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* N 시리즈 VM의 NVIDIA GPU에 대한 자세한 내용은 다음을 참조하세요.
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html)(Azure NC VM용)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html)(Azure NC VM용)


