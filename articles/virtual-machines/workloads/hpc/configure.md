---
title: 고성능 컴퓨팅-Azure Virtual Machines | Microsoft Docs
description: Azure에서 고성능 컴퓨팅에 대해 알아봅니다.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 723419b97dc024a700d860dd3fe61ff48073a587
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87020001"
---
# <a name="optimization-for-linux"></a>Linux용 최적화

이 문서에서는 OS 이미지를 최적화 하는 몇 가지 주요 기법을 보여 줍니다. [InfiniBand를 사용 하도록 설정](enable-infiniband.md) 하 고 OS 이미지를 최적화 하는 방법에 대해 자세히 알아보세요.

## <a name="update-lis"></a>LIS 업데이트

사용자 지정 이미지를 사용 하 여 배포 하는 경우 (예: CentOS/RHEL 7.4 또는 7.5와 같은 이전 OS) VM에서 LIS를 업데이트 합니다.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>메모리 확보

원격 메모리 액세스를 방지 하기 위해 메모리를 자동으로 회수 하 여 효율성을 향상 시킵니다.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

VM을 다시 부팅 한 후이 작업을 유지 하려면:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>방화벽 및 SELinux 사용 안 함

방화벽 및 SELinux를 사용 하지 않도록 설정 합니다.

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>Cpupower 사용 안 함

Cpupower를 사용 하지 않도록 설정 합니다.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>다음 단계

* [InfiniBand를 사용 하도록 설정](enable-infiniband.md) 하 고 OS 이미지를 최적화 하는 방법에 대해 자세히 알아보세요.

* Azure의 [HPC](/azure/architecture/topics/high-performance-computing/) 에 대해 자세히 알아보세요.
