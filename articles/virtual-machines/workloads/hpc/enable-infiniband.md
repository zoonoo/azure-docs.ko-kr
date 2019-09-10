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
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 7218fceae71969f204c6c25ba4793a7c94341693
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858474"
---
# <a name="enable-infiniband-with-sr-iov"></a>SR-IOV를 사용 하 여 InfiniBand 사용

HPC 용 IaaS Vm을 시작 하는 가장 간단 하 고 권장 되는 방법은 CentOS-HPC 7.6 VM OS 이미지를 사용 하는 것입니다. 사용자 지정 VM 이미지를 사용 하는 경우 InfiniBand (IB)를 사용 하 여이 이미지를 구성 하는 가장 쉬운 방법은 배포에 InfiniBandDriverLinux 또는 InfiniBandDriverWindows VM 확장을 추가 하는 것입니다.
[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) 및 [WINDOWS](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances) 에서 이러한 VM 확장을 사용 하는 방법을 알아봅니다.

SR-IOV를 사용 하도록 설정 된 Vm (현재 HB 및 HC 시리즈)에서 InfiniBand를 수동으로 구성 하려면 다음 단계를 수행 합니다. 이러한 단계는 RHEL/CentOS에만 해당 됩니다. Ubuntu (16.04 및 18.04) 및 SLES (12 SP4 및 15)의 경우 수신함 드라이버가 제대로 작동 합니다.

## <a name="manually-install-ofed"></a>수동으로 OFED 설치

[Mellanox](https://www.mellanox.com/page/products_dyn?product_family=26)에서 connectx-3-5에 대 한 최신 MLNX_OFED 드라이버를 설치 합니다.

RHEL/CentOS의 경우 (예: 7.6):

```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

Windows의 경우 [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34) 에서 connectx-3에 대 한 winof 드라이버를 다운로드 하 여 설치 합니다.

## <a name="enable-ipoib"></a>IPoIB 사용

```bash
sudo sed -i 's/LOAD_EIPOIB=no/LOAD_EIPOIB=yes/g' /etc/infiniband/openib.conf
sudo /etc/init.d/openibd restart
if [ $? -eq 1 ]
then
  sudo modprobe -rv  ib_isert rpcrdma ib_srpt
  sudo /etc/init.d/openibd restart
fi
```

## <a name="assign-an-ip-address"></a>IP 주소 할당

다음 중 하나를 사용 하 여 ib0 인터페이스에 IP 주소를 할당 합니다.

- Ib0 인터페이스 (root)에 IP 주소를 수동으로 할당 합니다.

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

또는

- WALinuxAgent를 사용 하 여 IP 주소를 할당 하 고 유지 합니다.

    ```bash
    yum install -y epel-release
    yum install -y python-pip
    python -m pip install --upgrade pip setuptools wheel
    wget "https://github.com/Azure/WALinuxAgent/archive/release-2.2.36.zip"
    unzip release-2.2.36.zip
    cd WALinuxAgent*
    python setup.py install --register-service --force
    sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
    sed -i -e 's/# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
    systemctl restart waagent
    ```

## <a name="next-steps"></a>다음 단계

Azure의 [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) 에 대해 자세히 알아보세요.
