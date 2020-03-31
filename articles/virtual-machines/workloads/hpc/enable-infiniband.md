---
title: SR-IOV - Azure 가상 머신으로 InifinBand 사용 | 마이크로 소프트 문서
description: SR-IOV를 사용하여 인피니밴드를 활성화하는 방법에 대해 알아봅니다.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/17/2019
ms.author: amverma
ms.openlocfilehash: 7f7907482da886d9da17ef1e7844b205f3e4b906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196777"
---
# <a name="enable-infiniband-with-sr-iov"></a>SR-IOV를 사용하여 인피니밴드 사용

Azure NC, ND 및 H 시리즈는 모두 전용 InfiniBand 네트워크에 의해 지원됩니다. 모든 RDMA 지원 크기는 인텔 MPI를 사용하여 해당 네트워크를 활용할 수 있습니다. 일부 VM 계열은 SR-IOV를 통해 모든 MPI 구현 및 RDMA 동사에 대한 지원을 확장했습니다. RDMA 지원 VM에는 [GPU 최적화](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) 및 [고성능 컴퓨팅(HPC)](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) VM이 포함됩니다.

## <a name="choose-your-installation-path"></a>설치 경로 선택

시작하기 위해 가장 간단한 옵션은 사용 가능한 경우 InfiniBand에 대해 미리 구성된 플랫폼 이미지를 사용하는 것입니다.

- **HPC IaaS VM** – HPC용 IaaS VM을 시작하려면 가장 간단한 해결책은 이미 인피니밴드로 구성된 [CentOS-HPC 7.6 VM OS 이미지를](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)사용하는 것입니다. 이 이미지는 이미 InfiniBand로 구성되어 있으므로 수동으로 구성할 필요가 없습니다. 호환되는 Windows 버전의 경우 [Windows RDMA 지원 인스턴스를](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)참조하십시오.

- **GPU IaaS VM** – [센트로스-HPC 7.6 VM OS 이미지를](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)제외한 GPU 최적화 VM을 위해 현재 플랫폼 이미지가 미리 구성되지 않았습니다. InfiniBand로 사용자 지정 이미지를 구성하려면 [수동으로 Mellanox OFED 설치를](#manually-install-mellanox-ofed)참조하십시오.

사용자 지정 VM 이미지 또는 [GPU 최적화](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) 된 VM을 사용 하는 경우, 인피니 밴드 드라이버 리눅스 또는 InfiniBandDriverWindows VM 확장을 배포에 추가 하 여 InfiniBand로 구성 해야 합니다. [리눅스와](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) [윈도우와](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)이러한 VM 확장을 사용하는 방법에 대해 알아봅니다.

## <a name="manually-install-mellanox-ofed"></a>수동으로 멜라녹스 OFED를 설치

SR-IOV를 사용하여 InfiniBand를 수동으로 구성하려면 다음 단계를 따르라. 이 단계의 예제에서는 RHEL/CentOS에 대한 구문을 보여 주지만 단계는 일반적이며 우분투(16.04, 18.04 19.04) 및 SLES(12 SP4 및 15)와 같은 호환되는 운영 체제에 사용할 수 있습니다. 받은 편지함 드라이버도 작동하지만 Mellanox OpenFabrics 드라이버는 더 많은 기능을 제공합니다.

Mellanox 드라이버에 대한 지원되는 배포판에 대한 자세한 내용은 최신 [Mellanox OpenFabrics 드라이버를](https://www.mellanox.com/page/products_dyn?product_family=26)참조하십시오. 멜라녹스 오픈패브릭 드라이버에 대한 자세한 내용은 [멜라녹스 사용자 가이드를](https://docs.mellanox.com/category/mlnxofedib)참조하십시오.

리눅스에서 InfiniBand를 구성하는 방법에 대한 다음 예제를 참조하십시오.

```bash
# Modify the variable to desired Mellanox OFED version
MOFED_VERSION=#4.7-1.0.0.1
# Modify the variable to desired OS
MOFED_OS=#rhel7.6
pushd /tmp
curl -fSsL https://www.mellanox.com/downloads/ofed/MLNX_OFED-${MOFED_VERSION}/MLNX_OFED_LINUX-${MOFED_VERSION}-${MOFED_OS}-x86_64.tgz | tar -zxpf -
cd MLNX_OFED_LINUX-*
sudo ./mlnxofedinstall
popd
```

윈도우의 경우, 다운로드 및 [윈도우 드라이버에 대한 ED의 멜라녹스를 설치합니다.](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

## <a name="enable-ip-over-infiniband"></a>인피니밴드를 통해 IP 활성화

다음 명령을 사용하여 InfiniBand를 통해 IP를 사용하도록 설정합니다.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>다음 단계

Azure에서 [HPC에](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) 대해 자세히 알아보세요.
