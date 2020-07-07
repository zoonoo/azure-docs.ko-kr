---
title: HB 시리즈 및 HC 시리즈 Vm의 알려진 문제-Azure Virtual Machines | Microsoft Docs
description: Azure에서 HB 시리즈 VM 크기의 알려진 문제에 대해 알아봅니다.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "67707788"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>HB 시리즈 및 HC 시리즈 VM의 알려진 문제

이 문서에서는 HB 시리즈 및 HC 시리즈 Vm을 사용 하는 경우 가장 일반적인 문제 및 해결 방법을 제공 합니다.

## <a name="dram-on-hb-series"></a>HB 시리즈의 DRAM

HB-시리즈 Vm은 현재 게스트 Vm에 228 GB의 RAM만 노출할 수 있습니다. 이는 게스트 VM에 예약 된 AMD CCX의 로컬 DRAM (NUMA 도메인)에 페이지가 할당 되지 않도록 방지 하기 위해 Azure 하이퍼바이저의 알려진 제한 때문입니다.

## <a name="accelerated-networking"></a>가속 네트워킹

현재 Azure 가속화 된 네트워킹은 사용 하도록 설정 되지 않지만 미리 보기 기간을 진행 합니다. 이 기능이 지원 되 면 고객에 게 알립니다.

## <a name="qp0-access-restriction"></a>qp0 액세스 제한

보안 취약성을 유발할 수 있는 낮은 수준의 하드웨어 액세스를 방지 하기 위해 게스트 Vm에서는 큐 쌍 0을 액세스할 수 없습니다. 이는 일반적으로 Connectx-3 NIC의 관리와 관련 된 작업에만 영향을 주며 ibdiagnet와 같은 일부 InfiniBand 진단을 실행 하지만 최종 사용자 응용 프로그램 자체는 실행 하지 않습니다.

## <a name="ud-transport"></a>UD 전송

시작 시 HB-및 HC 시리즈는 DCT (동적 연결 전송)를 지원 하지 않습니다. DCT에 대 한 지원은 시간이 지남에 따라 구현 됩니다. 신뢰할 수 있는 연결 (RC) 및 신뢰할 수 없는 데이터 그램 (UD) 전송이 지원 됩니다.

## <a name="gss-proxy"></a>GSS 프록시

GSS 프록시의 CentOS/RHEL 7.5에는 NFS와 함께 사용 될 때 심각한 성능 및 응답성 페널티로 매니페스트 될 수 있는 알려진 버그가 있습니다. 다음을 사용 하 여 완화할 수 있습니다.

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>캐시 정리

HPC 시스템에서는 다음 사용자에 게 동일한 노드가 할당 되기 전에 작업이 완료 된 후 메모리를 정리 하는 것이 유용한 경우가 많습니다. Linux에서 응용 프로그램을 실행 한 후에는 응용 프로그램을 실행 하지 않더라도 버퍼 메모리가 늘어나면 사용 가능한 메모리가 감소 하는 것을 알 수 있습니다.

![명령 프롬프트의 스크린샷](./media/known-issues/cache-cleaning-1.png)

`numactl -H`를 사용 하면 메모리가 버퍼링 되는 NUMAnode 표시 됩니다. Linux에서 사용자는 세 가지 방법으로 캐시를 정리 하 여 버퍼링 된 메모리 또는 캐시 된 메모리를 ' 무료 '로 되돌릴 수 있습니다. 루트 이거나 sudo 권한이 있어야 합니다.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![명령 프롬프트의 스크린샷](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>커널 경고

Linux에서 HB 시리즈 VM을 부팅할 때 다음과 같은 커널 경고 메시지가 표시 될 수 있습니다.

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

이 경고는 무시 해도 됩니다. 이는 시간이 지남에 따라 주소가 지정 되는 Azure 하이퍼바이저의 알려진 제한 때문입니다.

## <a name="next-steps"></a>다음 단계

Azure에서 [고성능 컴퓨팅](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) 에 대해 자세히 알아보세요.
