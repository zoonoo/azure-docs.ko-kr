---
title: HPC Vm에서 InifinBand 사용-Azure Virtual Machines | Microsoft Docs
description: Azure HPC Vm에서 InfiniBand을 사용 하도록 설정 하는 방법에 대해 알아봅니다.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/01/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 88f1c120ac4578e077e1c51f59bcaf53b1de2083
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87538900"
---
# <a name="enable-infiniband"></a>InfiniBand 사용

[RDMA 지원](../../sizes-hpc.md#rdma-capable-instances) [H 시리즈](../../sizes-hpc.md) 및 [N 시리즈](../../sizes-gpu.md) vm은 낮은 대기 시간 및 높은 대역폭 InfiniBand 네트워크를 통해 통신 합니다. 이러한 상호 연결에 대 한 RDMA 기능은 분산 노드 HPC 및 AI 워크 로드의 확장성과 성능을 향상 시키기 위해 매우 중요 합니다. InfiniBand 사용 H 시리즈 및 N 시리즈 Vm은 최적화 되 고 일관 된 RDMA 성능을 위한 낮은 지름 디자인을 사용 하는 비 블로킹 fat 트리에 연결 됩니다.

지원 되는 VM 크기에 대 한 InfiniBand를 사용 하도록 설정 하는 다양 한 방법이 있습니다.

## <a name="vm-images-with-infiniband-drivers"></a>InfiniBand 드라이버를 사용 하는 VM 이미지
Marketplace에서 지원 되는 VM 이미지 목록은 [Vm 이미지](configure.md#vm-images) 를 참조 하세요 .이 이미지는 sr-iov 또는 sr-iov가 아닌 Vm 용 InfiniBand 드라이버를 사용 하 여 미리 로드 하거나 적절 한 드라이버를 사용 하 여 구성할 수 있습니다.
SR-IOV를 사용 하도록 설정 된 [RDMA 지원 vm](../../sizes-hpc.md#rdma-capable-instances)의 경우 Marketplace에서 [CentOS 버전 7.6 이상](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) 버전 VM 이미지를 시작 하는 것이 가장 쉬운 방법입니다.
Ubuntu VM 이미지는 [여기에 나와 있는 지침](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)을 사용 하 여 sr-iov 및 sr-iov를 사용 하지 않는 vm 모두에 적합 한 드라이버로 구성할 수 있습니다.

## <a name="infiniband-driver-vm-extensions"></a>InfiniBand 드라이버 VM 확장
Linux에서는 [INFINIBANDDRIVERLINUX vm 확장](../../extensions/hpc-compute-infiniband-linux.md) 을 사용 하 여 Mellanox OFED 드라이버를 설치 하 고 Sr-iov 사용 H-및 N 시리즈 Vm에서 InfiniBand를 사용 하도록 설정할 수 있습니다.

Windows에서 [INFINIBANDDRIVERWINDOWS vm 확장](../../extensions/hpc-compute-infiniband-windows.md) 은 RDMA 연결에 Windows 네트워크 직접 드라이버 (sr-iov vm이 아닌 vm) 또는 Mellanox OFED 드라이버 (sr-iov vm)를 설치 합니다. A8 및 A9 인스턴스의 특정 배포에서 HpcVmDrivers 확장이 자동으로 추가 됩니다. HpcVmDrivers VM 확장은 더 이상 사용 되지 않습니다. 업데이트 되지 않습니다.

VM에 VM 확장을 추가해야 하는 경우 [Azure PowerShell](/powershell/azure/) cmdlet을 사용할 수 있습니다. 자세한 내용은 [가상 머신 확장 및 기능](../../extensions/overview.md)을 참조하세요. 또한 [클래식 배포 모델](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)에서 배포된 VM의 확장으로 작업할 수 있습니다.

## <a name="manual-installation"></a>수동 설치
[OFED (Mellanox openfabrics 드라이버)](https://www.mellanox.com/products/InfiniBand-VPI-Software) 는 [sr-iov 사용](../../sizes-hpc.md#rdma-capable-instances) [H 시리즈](../../sizes-hpc.md) 및 [N 시리즈](../../sizes-gpu.md) vm에 수동으로 설치할 수 있습니다.

### <a name="linux"></a>Linux
아래 예제를 사용 하 여 [Linux 용 OFED 드라이버](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed) 를 설치할 수 있습니다. 여기에 나와 있는 예제는 RHEL/CentOS에 대 한 것 이지만 일반적인 단계 이며 Ubuntu (16.04, 18.04 19.04, 20.04) 및 SLES (12 SP4 및 15)와 같은 호환 되는 Linux 운영 체제에 사용할 수 있습니다. 수신함 드라이버도 작동 하지만, Mellanox OFED 드라이버는 더 많은 기능을 제공 합니다.

```bash
MLNX_OFED_DOWNLOAD_URL=http://content.mellanox.com/ofed/MLNX_OFED-5.0-2.1.8.0/MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz
# Optinally verify checksum
tar zxvf MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz

KERNEL=( $(rpm -q kernel | sed 's/kernel\-//g') )
KERNEL=${KERNEL[-1]}
# Uncomment the lines below if you are running this on a VM
#RELEASE=( $(cat /etc/centos-release | awk '{print $4}') )
#yum -y install http://olcentgbl.trafficmanager.net/centos/${RELEASE}/updates/x86_64/kernel-devel-${KERNEL}.rpm
yum install -y kernel-devel-${KERNEL}
./MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64/mlnxofedinstall --kernel $KERNEL --kernel-sources /usr/src/kernels/${KERNEL} --add-kernel-support --skip-repo
```

### <a name="windows"></a>Windows
Windows의 경우 [windows 용 MELLANOX OFED 드라이버](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2)를 다운로드 하 여 설치 합니다.

## <a name="enable-ip-over-infiniband-ib"></a>IP over InfiniBand (IB) 사용
MPI 작업을 실행 하려는 경우 일반적으로 IPoIB가 필요 하지 않습니다. Mpi 라이브러리는 MPI 라이브러리의 TCP/IP 채널을 명시적으로 사용 하지 않는 한 IB 통신을 위한 동사 인터페이스를 사용 합니다. 그러나 통신에 TCP/IP를 사용 하는 앱이 있는 경우 IB를 통해 실행 하려면 IB 인터페이스를 통해 IPoIB를 사용할 수 있습니다. RHEL/CentOS의 경우 다음 명령을 사용 하 여 InfiniBand를 통해 IP를 사용 하도록 설정 합니다.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>다음 단계

- 지원 되는 다양 한 [MPI 라이브러리](setup-mpi.md) 및 vm에 대 한 최적의 구성을 설치 하는 방법에 대해 자세히 알아보세요.
- [Hb 시리즈 개요](hb-series-overview.md) 및 [HC 시리즈 개요](hc-series-overview.md) 를 검토 하 여 성능 및 확장성에 대 한 워크 로드를 최적으로 구성 하는 방법을 알아보세요.
- [Azure Compute 기술 커뮤니티 블로그의](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)최신 공지 사항 및 일부 HPC 예제 및 결과에 대해 읽어 보세요.
- 실행 중인 HPC 워크 로드에 대 한 높은 수준의 아키텍처 보기는 [Azure의 hpc (고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조 하세요.
