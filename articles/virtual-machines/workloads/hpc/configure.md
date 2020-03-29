---
title: 고성능 컴퓨팅 - Azure 가상 머신 | 마이크로 소프트 문서
description: Azure에서 고성능 컴퓨팅에 대해 자세히 알아보세요.
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
ms.openlocfilehash: 10549abfbdacf1fc1ae6b99f4cab20a290c32a2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707817"
---
# <a name="optimization-for-linux"></a>Linux용 최적화

이 문서에서는 OS 이미지를 최적화하는 몇 가지 주요 기술을 보여 줍니다. [InfiniBand를 사용하도록 설정하고](enable-infiniband.md) OS 이미지를 최적화하는 방법에 대해 자세히 알아보십시오.

## <a name="update-lis"></a>LIS 업데이트

사용자 지정 이미지(예: CentOS/RHEL 7.4 또는 7.5와 같은 이전 OS)를 사용하여 배포하는 경우 VM에서 LIS를 업데이트합니다.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>메모리 회수

원격 메모리 액세스를 피하기 위해 메모리를 자동으로 회수하여 효율성을 향상시킵니다.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

VM을 재부팅한 후 이 작업을 계속 유지하려면 다음을 수행하십시오.

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>방화벽 및 SELinux 사용 안 함

방화벽과 SELinux를 사용하지 않도록 설정합니다.

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>CPU출력 비활성화

CPU출력을 비활성화합니다.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>다음 단계

* [InfiniBand 를 사용하도록 설정하고](enable-infiniband.md) OS 이미지를 최적화하는 방법에 대해 자세히 알아보십시오.

* Azure에서 [HPC에](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) 대해 자세히 알아보세요.
