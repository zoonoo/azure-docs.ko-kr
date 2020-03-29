---
title: HB 시리즈 및 HC 시리즈 VM에 대한 알려진 문제 - Azure 가상 머신 | 마이크로 소프트 문서
description: Azure의 HB 시리즈 VM 크기와 관련된 알려진 문제에 대해 알아봅니다.
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
ms.openlocfilehash: 8d4b57fb2fee3849e102868c86fe3cab465fc70d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707788"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>HB 시리즈 및 HC 시리즈 VM의 알려진 문제

이 문서에서는 HB 시리즈 및 HC 시리즈 VM을 사용할 때 가장 일반적인 문제와 해결 방안을 제공합니다.

## <a name="dram-on-hb-series"></a>HB 시리즈의 DRAM

HB 시리즈 VM은 현재 게스트 VM에 228GB의 RAM만 노출할 수 있습니다. 이는 게스트 VM을 위해 예약된 AMD CCX(NUMA 도메인)의 로컬 DRAM에 페이지가 할당되는 것을 방지하기 위해 Azure 하이퍼바이저의 알려진 제한 때문입니다.

## <a name="accelerated-networking"></a>가속 네트워킹

Azure 가속 네트워킹은 현재 활성화되어 있지 않지만 미리 보기 기간 동안 진행됩니다. 이 기능이 지원되면 고객에게 알립니다.

## <a name="qp0-access-restriction"></a>qp0 액세스 제한

보안 취약점을 초래할 수 있는 하위 수준 하드웨어 액세스를 방지하기 위해 Queue Pair 0은 게스트 VM에서 액세스할 수 없습니다. 이는 일반적으로 ConnectX-5 NIC의 관리와 관련된 작업과 관련이 있으며 ibdiagnet과 같은 일부 InfiniBand 진단을 실행하는 작업에만 영향을 주지만 최종 사용자 응용 프로그램 자체에는 영향을 주지 않습니다.

## <a name="ud-transport"></a>UD 전송

출시 시 HB- 및 HC 계열은 DCT(동적 커넥티드 전송)를 지원하지 않습니다. DCT에 대한 지원은 시간이 지남에 따라 구현됩니다. 신뢰할 수 있는 연결(RC) 및 UD(신뢰할 수 없는 데이터그램) 전송이 지원됩니다.

## <a name="gss-proxy"></a>GSS 프록시

GSS 프록시는 NFS와 함께 사용할 때 상당한 성능 및 응답 성 페널티로 나타날 수 있는 CentOS/RHEL 7.5에 알려진 된 버그. 이 완화될 수 있습니다.

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>캐시 정리

HPC 시스템에서는 다음 사용자에게 동일한 노드가 할당되기 전에 작업이 완료된 후 메모리를 정리하는 것이 유용한 경우가 많습니다. Linux에서 응용 프로그램을 실행한 후에는 응용 프로그램을 실행하지 않아도 버퍼 메모리가 증가하는 동안 사용 가능한 메모리가 줄어듭니다.

![명령 프롬프트의 스크린샷](./media/known-issues/cache-cleaning-1.png)

사용 `numactl -H` 하면 메모리가 버퍼링 된 NUMAnode (아마도 모두)가 표시 됩니다. Linux에서 사용자는 버퍼링되거나 캐시된 메모리를 '사용 중'으로 되돌리는 세 가지 방법으로 캐시를 정리할 수 있습니다. 루트이거나 sudo 권한이 있어야 합니다.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![명령 프롬프트의 스크린샷](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>커널 경고

Linux에서 HB 시리즈 VM을 부팅할 때 다음과 같은 커널 경고 메시지가 표시될 수 있습니다.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```

이 경고는 무시할 수 있습니다. 이는 시간이 지남에 따라 해결될 Azure 하이퍼바이저의 알려진 제한 때문입니다.

## <a name="next-steps"></a>다음 단계

Azure의 [고성능 컴퓨팅에](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) 대해 자세히 알아보세요.
