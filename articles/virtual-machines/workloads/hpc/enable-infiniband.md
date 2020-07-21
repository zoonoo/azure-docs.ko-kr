---
title: SR-IOV를 사용 하 여 InifinBand 사용-Azure Virtual Machines | Microsoft Docs
description: SR-IOV로 InfiniBand를 사용 하도록 설정 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: de61403b62f80bea7872d5ab3561567ae2109590
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500071"
---
# <a name="enable-infiniband-with-sr-iov"></a>SR-IOV를 사용 하 여 InfiniBand 사용

Azure NC, ND 및 H 시리즈의 Vm은 모두 전용 InfiniBand 네트워크에서 지원 됩니다. 모든 RDMA 지원 크기는 Intel MPI를 사용 하 여 해당 네트워크를 활용할 수 있습니다. 일부 VM 시리즈는 SR-IOV를 통해 모든 MPI 구현 및 RDMA 동사를 확장 하 여 지원 합니다. RDMA 지원 Vm에는 [GPU 최적화](../../sizes-gpu.md) 및 [HPC (고성능 계산)](../../sizes-hpc.md) vm이 포함 됩니다.

## <a name="choose-your-installation-path"></a>설치 경로 선택

시작 하려면 가장 간단한 옵션은 InfiniBand에 대해 미리 구성 된 플랫폼 이미지를 사용 하는 것입니다 (사용 가능한 경우).

- **Hpc Iaas vm** – Hpc 용 iaas vm을 시작 하려면 가장 간단한 솔루션은 InfiniBand로 이미 구성 된 [CENTOS HPC 7.6 VM OS 이미지](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)를 사용 하는 것입니다. 이 이미지는 이미 InfiniBand를 사용 하 여 구성 되었으므로 수동으로 구성할 필요가 없습니다. 호환 되는 Windows 버전은 [WINDOWS RDMA 지원 인스턴스](../../sizes-hpc.md#rdma-capable-instances)를 참조 하세요.

- **Gpu IaaS vm** – [CENTOS HPC 7.6 VM OS 이미지](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)를 제외 하 고 현재 gpu 최적화 vm에 대해 플랫폼 이미지가 미리 구성 되어 있지 않습니다. InfiniBand를 사용 하 여 사용자 지정 이미지를 구성 하려면 [수동으로 MELLANOX OFED 설치](#manually-install-mellanox-ofed)를 참조 하세요.

사용자 지정 VM 이미지 또는 [GPU 최적화](../../sizes-gpu.md) vm을 사용 하는 경우 배포에 InfiniBandDriverLinux 또는 InfiniBandDriverWindows vm 확장을 추가 하 여 InfiniBand를 사용 하 여 구성 해야 합니다. [Linux](../../sizes-hpc.md#rdma-capable-instances) 및 [WINDOWS](../../sizes-hpc.md#rdma-capable-instances)에서 이러한 VM 확장을 사용 하는 방법에 대해 알아봅니다.

## <a name="manually-install-mellanox-ofed"></a>수동으로 Mellanox OFED 설치

SR-IOV를 사용 하 여 InfiniBand를 수동으로 구성 하려면 다음 단계를 사용 합니다. 이 단계의 예제에서는 RHEL/CentOS에 대 한 구문을 보여 주지만 일반적인 단계는 Ubuntu (16.04, 18.04 19.04) 및 SLES (12 SP4 및 15)와 같은 호환 되는 운영 체제에 사용할 수 있습니다. 또한 수신함 드라이버가 작동 하지만, Mellanox OpenFabrics 드라이버에서 더 많은 기능을 제공 합니다.

Mellanox 드라이버에 대해 지원 되는 배포에 대 한 자세한 내용은 최신 [Mellanox openfabrics 드라이버](https://www.mellanox.com/page/products_dyn?product_family=26)를 참조 하세요. Mellanox OpenFabrics 드라이버에 대 한 자세한 내용은 [mellanox 사용자 가이드](https://docs.mellanox.com/category/mlnxofedib)를 참조 하세요.

Linux에서 InfiniBand을 구성 하는 방법에 대 한 다음 예제를 참조 하세요.

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

Windows의 경우 [windows 용 MELLANOX OFED 드라이버](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)를 다운로드 하 여 설치 합니다.

## <a name="enable-ip-over-infiniband"></a>InfiniBand를 통해 IP 사용

다음 명령을 사용 하 여 InfiniBand에서 IP를 사용 하도록 설정 합니다.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>다음 단계

Azure의 [HPC](/azure/architecture/topics/high-performance-computing/) 에 대해 자세히 알아보세요.
