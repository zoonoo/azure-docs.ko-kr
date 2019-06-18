---
title: 고성능 컴퓨팅-Azure Virtual Machines | Microsoft Docs
description: Azure에서 고성능 컴퓨팅에 대해 알아봅니다.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: bcd121127721a6f76d76f11edf6574165c8e8ddb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66809814"
---
# <a name="optimization-for-linux"></a>Linux용 최적화

이 아티클에서 OS 이미지를 최적화 하는 몇 가지 주요 방법에 설명 합니다. 에 대해 자세히 알아보세요 [InfiniBand를 사용 하도록 설정 하면](enable-infiniband.md) 및 OS 이미지를 최적화 합니다.

## <a name="update-lis"></a>LIS를 업데이트 합니다.

사용자 지정 이미지 (예를 들어 CentOS/RHEL 7.4 7.5 등 이전 OS)를 사용 하 여 배포 하는 경우 VM에 LIS를 업데이트 합니다.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>메모리를 회수 합니다.

자동으로 원격 메모리 액세스 방지 하기 위해 메모리를 확보 하 여 효율성을 개선 합니다.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

VM이 다시 부팅 후 지속이 확인 합니다.

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>방화벽 및 SELinux를 사용 하지 않도록 설정

방화벽 및 SELinux를 사용 하지 않도록 설정 합니다.

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g'/etc/selinux/config
```

## <a name="disable-cpupower"></a>Cpupower를 사용 하지 않도록 설정

Cpupower를 사용 하지 않도록 설정 합니다.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>다음 단계

* 에 대해 자세히 알아보세요 [InfiniBand를 사용 하도록 설정 하면](enable-infiniband.md) 및 OS 이미지를 최적화 합니다.

* 에 대해 자세히 알아보세요 [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) Azure에서.
