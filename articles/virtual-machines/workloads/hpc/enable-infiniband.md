---
title: InifinBand SR-IOV-Azure Virtual Machines를 사용 하 여 사용 하도록 설정 | Microsoft Docs
description: SR-IOV를 사용 하 여 InfiniBand를 사용 하는 방법에 알아봅니다.
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
ms.openlocfilehash: 2e28627359f339a3bf818a15d6a5c8e456fb554a
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797533"
---
# <a name="enable-infiniband-with-sr-iov"></a>SR-IOV를 사용 하 여 InfiniBand를 사용 하도록 설정

InfiniBand (IB) 사용 하 여 사용자 지정 VM 이미지를 구성 하는 가장 간단 하 고 권장 되는 방법은 배포 InfiniBandDriverLinux 또는 InfiniBandDriverWindows VM 확장을 추가 하는 것입니다.
이러한 VM 확장을 사용 하는 방법을 알아봅니다 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) 고 [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

SR-IOV에 InfiniBand를 수동으로 구성 하려면 (현재: HB 및 HC 시리즈) Vm을 사용 하도록 설정, 다음 단계를 수행 합니다. 이러한 단계가 RHEL/CentOS에만 합니다. Ubuntu 16.04과 18.04 및 SLES (SP4 12 및 15)에 대 한 받은 편지함 드라이버는 잘 작동 합니다.

## <a name="manually-install-ofed"></a>OFED를 수동으로 설치

ConnectX 5에 대 한 최신 MLNX_OFED 드라이버를 설치 [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=26)합니다.

RHEL/centos (7.6에 대 한 아래 예제):

```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

Windows를 다운로드 하 여 ConnectX-5에서 WinOF 2 드라이버 설치 [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

## <a name="enable-ipoib"></a>IPoIB를 사용 하도록 설정

```bash
sudo sed -i 's/LOAD_EIPOIB=no/LOAD_EIPOIB=yes/g' /etc/infiniband/openib.conf
sudo /etc/init.d/openibd restart
if [ $? -eq 1 ]
then
  sudo modprobe -rv  ib_isert rpcrdma ib_srpt
  sudo /etc/init.d/openibd restart
fi
```

## <a name="assign-an-ip-address"></a>IP 주소를 할당 합니다.

Ib0 인터페이스 중 하나를 사용 하 여 IP 주소를 할당 합니다.

- 루트로 ib0 인터페이스에 IP 주소를 수동으로 할당 합니다.

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

또는

- WALinuxAgent를 사용 하 여 IP 주소 할당을 유지 하도록 합니다.

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

에 대해 자세히 알아보세요 [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) Azure에서.
