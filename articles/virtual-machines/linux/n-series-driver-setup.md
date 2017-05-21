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
ms.date: 05/02/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: de674af369080ad7eb608608685e293f2326c8e6
ms.openlocfilehash: 69f1363c26d8b5a18ffd5629c6a49c34306dd7c0
ms.contentlocale: ko-kr
ms.lasthandoff: 05/04/2017


---

# <a name="set-up-gpu-drivers-for-n-series-vms-running-linux"></a>Linux가 실행되는 N 시리즈 VM의 GPU 드라이버 설정

Linux를 실행하는 Azure N 시리즈 VM의 GPU 기능을 이용하려면 각 VM에 NVIDIA 그래픽 드라이버를 설치해야 합니다. 이 문서에서는 N 시리즈 VM을 배포한 후의 드라이버 설치 단계를 제공합니다. [Windows VM](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에 대한 드라이버 설치 정보도 사용할 수 있습니다.


N 시리즈 VM 사양, 저장소 용량 및 디스크 세부 정보는 [GPU Linux VM 크기](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. 



## <a name="supported-distributions-and-drivers"></a>지원되는 배포판 및 버전

> [!IMPORTANT]
> 현재 Linux GPU 드라이버 지원은 Azure NC VM에서만 사용할 수 있습니다. 

Azure Marketplace의 다음 배포판은 N 시리즈 Linux VM에서 NVIDIA 그래픽 드라이버를 실행하도록 지원됩니다.

### <a name="nc-vms-tesla-k80-card"></a>NC VM(Tesla K80 카드)
* Ubuntu 16.04 LTS 
* Red Hat Enterprise Linux 7.3 
* CentOS 기반 7.3 

**지원되는 드라이버**: NVIDIA CUDA 8.0, 드라이버 분기 R375 [설치 단계](#install-cuda-drivers-for-nc-vms)




> [!WARNING] 
> Red Hat 제품에 대한 타사 소프트웨어 설치는 Red Hat 지원 조건에 영향을 줄 수 있습니다. [Red Hat 기술 자료 문서](https://access.redhat.com/articles/1067)를 참조하세요.
>




## <a name="install-cuda-drivers-for-nc-vms"></a>NC VM용 NVIDIA 드라이버 설치

NVIDIA CUDA 도구 키트 8.0에서 Linux NC VM에 NVIDIA 드라이버를 설치하는 단계는 다음과 같습니다. 

경우에 따라 C 및 C++ 개발자는 GPU 가속 응용 프로그램을 빌드하기 위해 전체 도구 키트를 설치할 수도 있습니다. 자세한 내용은 [CUDA 설치 가이드](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)를 참조하세요.


> [!NOTE]
> 여기에 제공된 CUDA 드라이버 다운로드 링크는 게시 시점에 최신 링크였습니다. 최신 드라이버에 대해서는 [NVIDIA](http://www.nvidia.com/) 웹 사이트를 참조하세요.
>

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

### <a name="centos-73-or-red-hat-enterprise-linux-73"></a>CentOS 7.3 또는 Red Hat Enterprise Linux 7.3

> [!IMPORTANT] 
> 알려진 문제로 인해 CentOS 7.3 또는 Red Hat Enterprise Linux 7.3이 실행되는 NC24r VM에서 NVIDIA CUDA 드라이버 설치가 실패합니다.
>

먼저 업데이트를 다운로드합니다. 

```bash
sudo yum update

sudo reboot
```

VM에 다시 연결하고 다음 명령을 사용하여 설치를 계속합니다.

```bash
sudo yum install kernel-devel

sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

sudo yum install dkms

CUDA_REPO_PKG=cuda-repo-rhel7-8.0.61-1.x86_64.rpm

wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo yum install cuda-drivers
```

설치에는 몇 분 정도 걸릴 수 있습니다. 필요에 따라 전체 CUDA 도구 키트를 설치하려면 다음을 입력합니다.

```bash
sudo yum install cuda
```

VM 다시 부팅하고 계속해서 설치를 확인합니다.


### <a name="verify-driver-installation"></a>드라이버 설치 확인


GPU 장치 상태를 쿼리하려면 VM에 대해 SSH를 실행하고 드라이버와 설치된 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 명령줄 유틸리티를 실행합니다. 

그러면 다음과 같은 출력이 표시됩니다.

![NVIDIA 장치 상태](./media/n-series-driver-setup/smi.png)


### <a name="cuda-driver-updates"></a>CUDA 드라이버 업데이트

배포 후에 CUDA 드라이버를 정기적으로 업데이트하는 것이 좋습니다.

#### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers
```

업데이트가 완료되면 VM을 다시 시작합니다.

#### <a name="centos-73-or-red-hat-enterprise-linux-73"></a>CentOS 7.3 또는 Red Hat Enterprise Linux 7.3

```bash
sudo yum update
```

업데이트가 완료되면 VM을 다시 시작합니다.



## <a name="troubleshooting"></a>문제 해결

* Ubuntu 16.04 LTS에서 4.4.0-75 Linux 커널을 실행하는 Azure N 시리즈 VM의 CUDA 드라이버에 알려진 문제가 있습니다. 커널을 업그레이드할 때 드라이버 기능을 유지하려면 커널 버전 4.4.0-77 이상으로 업그레이드합니다. 



## <a name="next-steps"></a>다음 단계

* N 시리즈 VM의 NVIDIA GPU에 대한 자세한 내용은 다음을 참조하세요.
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html)(Azure NC VM용)
    * [NVIDIA 테슬라 M60](http://www.nvidia.com/object/tesla-m60.html) (Azure NV VM 용)

* 설치한 NVIDIA 드라이버와 함께 Linux VM 이미지를 캡처하려면 [Linux 가상 컴퓨터를 일반화하고 캡처하는 방법](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.
