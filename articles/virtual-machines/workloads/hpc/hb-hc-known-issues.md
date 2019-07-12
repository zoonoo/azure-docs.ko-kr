---
title: 알려진 문제가 HB 시리즈 및 HC 시리즈 Vm-Azure Virtual Machines | Microsoft Docs
description: Azure에서 HB 시리즈 VM 크기를 사용 하 여 알려진된 문제에 알아봅니다.
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
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707788"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>HB 시리즈 및 HC 시리즈 VM의 알려진 문제

이 문서에서는 HB 시리즈 및 HC 시리즈 Vm을 사용 하는 경우 가장 일반적인 문제 및 솔루션을 제공 합니다.

## <a name="dram-on-hb-series"></a>DRAM HB 시리즈에서

만 HB 시리즈 Vm이 이번에 228 GB의 게스트 Vm에 RAM 노출할 수 있습니다. 로컬 DRAM의 AMD CCX의 (NUMA 도메인) 게스트 VM에 대 한 예약을 할당할에서 페이지를 방지 하기 위해 Azure 하이퍼바이저의 알려진된 제한 사항 때문입니다.

## <a name="accelerated-networking"></a>가속 네트워킹

Azure 가속 네트워킹 사용 가능 하지 이때 되지만 미리 보기 기간 동안 진행 하는 과정입니다. 우리는이 기능이 지원 되는 경우 고객에 게를 알립니다.

## <a name="qp0-access-restriction"></a>qp0 액세스 제한

보안 취약성을 큐 쌍에서 발생할 수 있는 하위 수준 하드웨어 액세스를 방지 하기 위해 0은 게스트 Vm에 액세스할 수 있습니다. 일반적으로 5 ConnectX NIC의 관리를 사용 하 여 연결 및 ibdiagnet, 하지만 최종 사용자 응용 프로그램이 아닌 자체와 같은 일부 InfiniBand 진단 실행 작업에만 영향을 줄 해야 합니다.

## <a name="ud-transport"></a>UD 전송

시작 시 HB 및 HC 시리즈는 동적으로 연결 된 전송 (DCT)을 지원 하지 않습니다. 시간이 지남에 따라 DCT에 대 한 지원이 구현 됩니다. 신뢰할 수 있는 연결 (RC) 및 신뢰할 수 없는 데이터 그램 (UD) 전송은 사용할 수 있습니다.

## <a name="gss-proxy"></a>GSS Proxy

GSS 프록시 중요 한 성능 및 응답성 저하 NFS와 함께 사용할 경우으로 나타날 수 있는 CentOS/RHEL 7.5에서 알려진된 버그를 있습니다. 이 사용 하 여 완화할 수 있습니다.

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>캐시 정리

HPC 시스템에서 유용 다음 사용자는 동일한 노드에 할당 되기 전에 작업이 완료 된 후에 메모리를 정리 합니다. Linux에서 응용 프로그램을 실행 한 후 응용 프로그램이 실행 되 고 있지도 불구 하 고 버퍼 메모리 증가 하는 동안 사용 가능한 메모리를 줄이는 것을 알 수 있습니다.

![명령 프롬프트의 스크린샷](./media/known-issues/cache-cleaning-1.png)

사용 하 여 `numactl -H` 메모리 (가능한 경우 모두)를 사용 하 여 버퍼링 되는 NUMAnode(s) 표시 됩니다. 사용자가 Linux에서 세 가지 캐시를 정리할 수를 반환 하는 방법을 버퍼링 되거나 ' 무료 ' 메모리 내 캐시입니다. 루트 또는 sudo 권한이 필요 합니다.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![명령 프롬프트의 스크린샷](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>커널 경고

Linux에서 HB 시리즈 VM을 부팅할 때 다음 커널 경고 메시지가 나타날 수 있습니다.

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

이 경고를 무시할 수 있습니다. 시간이 지남에 따라 처리할 Azure 하이퍼바이저의 알려진된 제한 사항 때문입니다.

## <a name="next-steps"></a>다음 단계

에 대해 자세히 알아보세요 [고성능 컴퓨팅](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) Azure에서.
