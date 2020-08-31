---
title: InfiniBand 사용 H 시리즈 및 N 시리즈 Azure Virtual Machines의 구성 및 최적화
description: HPC 용 InfiniBand 사용 H 시리즈 및 N 시리즈 Vm을 구성 하 고 최적화 하는 방법에 대해 알아봅니다.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/07/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: d4661c0819d214a2c750eb1582559f8d8a5959ed
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88006607"
---
# <a name="configure-and-optimize-vms"></a>VM 구성 및 최적화

이 문서에서는 InfiniBand 사용 가능한 [H 시리즈](../../sizes-hpc.md) 및 [N 시리즈](../../sizes-gpu.md) vm for HPC를 구성 하 고 최적화 하는 알려진 기술을 공유 합니다.

## <a name="vm-images"></a>VM 이미지
InfiniBand 사용 Vm에서 RDMA를 사용 하도록 설정 하려면 적절 한 드라이버가 필요 합니다. Linux에서 Marketplace의 CentOS VM 이미지는 적절 한 드라이버를 사용 하 여 미리 구성 된 상태로 제공 됩니다. Ubuntu VM 이미지는 [여기에 설명 된 지침](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)에 따라 올바른 드라이버를 사용 하 여 구성할 수 있습니다. 적절 한 드라이버 및 구성을 사용 하 여 [사용자 지정 VM 이미지](../../linux/tutorial-custom-images.md) 를 만들고 해당 recurringly를 다시 사용 하는 것도 좋습니다.

> [!NOTE]
> GPU 사용 [N 시리즈](../../sizes-gpu.md) Vm에서는 [vm 확장](../../extensions/hpccompute-gpu-linux.md) 을 통해 또는 [수동으로](../../linux/n-series-driver-setup.md)추가할 수 있는 적절 한 gpu 드라이버가 추가로 필요 합니다. Marketplace의 일부 VM 이미지는 Nvidia GPU 드라이버와 함께 미리 설치 되어 있습니다.

### <a name="centos-hpc-vm-images"></a>CentOS-HPC VM 이미지

#### <a name="non-sr-iov-enabled-vms"></a>SR-IOV를 사용할 수 없는 Vm
SR-IOV를 사용 하도록 설정 되지 않은 [RDMA 지원 vm](../../sizes-hpc.md#rdma-capable-instances), CentOS 버전 6.5 이상 버전의 경우 Marketplace에서 최대 7.5까지 적합 합니다. 예를 들어 [H16 시리즈 vm](../../h-series.md)의 경우 7.1 ~ 7.5 버전을 권장 합니다. 이러한 VM 이미지는 RDMA 및 Intel MPI 버전 5.1에 대 한 네트워크 다이렉트 드라이버를 사용 하 여 미리 로드 됩니다.

> [!NOTE]
> SR-IOV를 사용할 수 없는 Vm에 대 한 이러한 CentOS 기반 HPC 이미지에서 커널 업데이트는 **yum** 구성 파일에서 사용할 수 없습니다. 이는 NetworkDirect Linux RDMA 드라이버가 RPM 패키지로 배포 되 고 커널이 업데이트 되는 경우 드라이버 업데이트가 작동 하지 않기 때문입니다.

#### <a name="sr-iov-enabled-vms"></a>SR-IOV 사용 Vm
  SR-IOV를 사용 하도록 설정 된 [RDMA 지원 vm](../../sizes-hpc.md#rdma-capable-instances)의 경우 Marketplace에서 [CentOS 버전 7.6 이상](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) 버전 VM 이미지가 적합 합니다. 이러한 VM 이미지는 RDMA 용 OFED 드라이버 및 널리 사용 되는 다양 한 MPI 라이브러리 및 과학적 컴퓨팅 패키지를 사용 하 여 최적화 되 고 미리 로드 되며 시작 하는 가장 쉬운 방법입니다.

  기본 CentOS Marketplace 이미지에서 CentOS-HPC 버전 7.6 이상 VM 이미지를 만드는 데 사용 되는 스크립트의 예는 [azhpc 리포지토리](https://github.com/Azure/azhpc-images/tree/master/centos)에 있습니다.

### <a name="rhelcentos-vm-images"></a>RHEL/CentOS VM 이미지
Marketplace에서 RHEL 또는 CentOS 기반의 HPC가 아닌 VM 이미지는 SR-IOV 지원 [RDMA 지원 vm](../../sizes-hpc.md#rdma-capable-instances)에서 사용 하도록 구성할 수 있습니다. [InfiniBand를 사용 하도록](enable-infiniband.md) 설정 하 고 VM에서 [MPI를 설정](setup-mpi.md) 하는 방법에 대해 자세히 알아보세요.

  기본 CentOS Marketplace 이미지에서 CentOS-HPC 버전 7.6 이상 VM 이미지를 만드는 데 사용 되는 스크립트의 예는 [azhpc 리포지토리](https://github.com/Azure/azhpc-images/tree/master/centos)에 있습니다.

### <a name="ubuntu-vm-images"></a>Ubuntu VM 이미지
Marketplace의 Ubuntu Server 16.04 LTS, 18.04 LTS 및 20.04 LTS VM 이미지는 SR-IOV 및 비-sr-iov [RDMA 지원 vm](../../sizes-hpc.md#rdma-capable-instances)모두에 대해 지원 됩니다. [InfiniBand를 사용 하도록](enable-infiniband.md) 설정 하 고 VM에서 [MPI를 설정](setup-mpi.md) 하는 방법에 대해 자세히 알아보세요.

  Ubuntu 18.04 LTS 기반 HPC VM 이미지를 만드는 데 사용할 수 있는 스크립트의 예는 [azhpc 리포지토리](https://github.com/Azure/azhpc-images/tree/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc)에 있습니다.

### <a name="suse-linux-enterprise-server-vm-images"></a>SUSE Linux Enterprise Server VM 이미지
Hpc에 대 한 SLES 12 SP3, hpc 용 SLES 12 SP3 (프리미엄), HPC 용 SLES 12 SP1, hpc 용 SLES 12 SP1 (Premium), SLES 12 SP4 및 SLES 15 VM 이미지 (Marketplace)가 지원 됩니다. 이러한 VM 이미지는 RDMA 및 Intel MPI 버전 5.1에 대 한 네트워크 다이렉트 드라이버를 사용 하 여 미리 로드 됩니다. Vm에서 [MPI를 설정](setup-mpi.md) 하는 방법에 대해 자세히 알아보세요.

## <a name="optimize-vms"></a>Vm 최적화

다음은 VM의 성능 향상을 위한 몇 가지 선택적 최적화 설정입니다.

### <a name="update-lis"></a>LIS 업데이트

기능이 나 성능에 필요한 경우에는 지원 되는 OS 배포판에서 [LIS (Linux Integration Services) 드라이버](../../linux/endorsed-distros.md) 를 설치 하거나 업데이트할 수 있습니다. 특히 사용자 지정 이미지 또는 이전 버전의 RHEL와 같은 이전 OS 버전을 사용 하 여 배포 합니다.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>메모리 확보

원격 메모리 액세스를 방지 하기 위해 메모리를 자동으로 회수 하 여 성능을 향상 시킵니다.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

VM을 다시 부팅 한 후이 작업을 유지 하려면:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>방화벽 및 SELinux 사용 안 함

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### <a name="disable-cpupower"></a>Cpupower 사용 안 함

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### <a name="configure-walinuxagent"></a>WALinuxAgent 구성

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
필요에 따라 WALinuxAgent를 사전 작업 단계로 사용 하지 않도록 설정 하 고 HPC 워크 로드에 대 한 최대 VM 리소스 가용성을 위해 다시 작업을 사용 하도록 설정할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- InfiniBand [H 시리즈](../../sizes-hpc.md) 및 [N 시리즈](../../sizes-gpu.md) vm에서 [InfiniBand을 사용 하도록 설정](enable-infiniband.md) 하는 방법에 대해 자세히 알아보세요.
- 지원 되는 다양 한 [MPI 라이브러리](setup-mpi.md) 및 vm에 대 한 최적의 구성을 설치 하는 방법에 대해 자세히 알아보세요.
- [Hb 시리즈 개요](hb-series-overview.md) 및 [HC 시리즈 개요](hc-series-overview.md) 를 검토 하 여 성능 및 확장성에 대 한 워크 로드를 최적으로 구성 하는 방법을 알아보세요.
- [Azure Compute 기술 커뮤니티 블로그의](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)최신 공지 사항 및 일부 HPC 예제 및 결과에 대해 읽어 보세요.
- 실행 중인 HPC 워크 로드에 대 한 높은 수준의 아키텍처 보기는 [Azure의 hpc (고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조 하세요.
