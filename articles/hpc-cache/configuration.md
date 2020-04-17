---
title: Azure HPC 캐시 설정 구성
description: MTU 및 루트 스쿼시와 같은 캐시에 대한 추가 설정을 구성하는 방법과 Azure Blob 저장소 대상에서 익스프레스 스냅숏에 액세스하는 방법을 설명합니다.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: 137e41a3f7d346bb612605660d7798ac2fc297d1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538818"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>추가 Azure HPC 캐시 설정 구성

Azure 포털의 **구성** 페이지에는 여러 설정을 사용자 지정하는 옵션이 있습니다. 대부분의 사용자는 기본값에서 변경할 필요가 없습니다.

이 문서에서는 Azure Blob 저장소 대상에 대 한 스냅숏 기능을 사용 하는 방법에 대해서도 설명 합니다. 스냅샷 기능에 는 구성 가능한 설정이 없습니다.

설정을 보려면 Azure 포털에서 캐시의 **구성** 페이지를 엽니다.

![Azure 포털의 구성 페이지의 스크린샷](media/configuration.png)

## <a name="adjust-mtu-value"></a>MTU 값 조정
<!-- linked from troubleshoot-nas article -->

MTU 크기라는 드롭다운 메뉴를 사용하여 캐시의 최대 전송 단위 크기를 선택할 수 **있습니다.**

기본값은 1500바이트이지만 1400으로 변경할 수 있습니다.

> [!NOTE]
> 캐시의 MTU 크기를 낮추는 경우 캐시와 통신하는 클라이언트 및 저장소 시스템에 동일한 MTU 설정또는 더 낮은 값이 있는지 확인합니다.

캐시 MTU 값을 낮추면 나머지 캐시 네트워크에서 패킷 크기 제한을 해결할 수 있습니다. 예를 들어 일부 VPN은 전체 크기 1500바이트 패킷을 성공적으로 전송할 수 없습니다. VPN을 통해 전송되는 패킷의 크기를 줄이면 이러한 문제가 해결될 수 있습니다. 그러나 캐시 MTU 설정이 낮을수록 클라이언트 및 저장소 시스템을 포함하여 캐시와 통신하는 다른 구성 요소도 캐시와의 통신 문제를 방지하려면 더 낮은 설정이 있어야 합니다.

다른 시스템 구성 요소의 MTU 설정을 변경하지 않으려면 캐시의 MTU 설정을 낮추어서는 안 됩니다. VPN 패킷 크기 제한을 해결하기 위한 다른 솔루션도 있습니다. 이 문제를 진단하고 해결하는 방법에 대해 자세히 알아보려면 NAS 문제 해결 문서에서 [VPN 패킷 크기 제한 조정을](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) 읽어보십시오.

Azure [VM에 대한 TCP/IP 성능 튜닝을](../virtual-network/virtual-network-tcpip-performance-tuning.md)읽고 Azure 가상 네트워크의 MTU 설정에 대해 자세히 알아보십시오.

## <a name="configure-root-squash"></a>루트 스쿼시 구성
<!-- linked from troubleshoot -->

**루트 스쿼시 활성화** 설정은 Azure HPC 캐시에서 루트 액세스를 허용하는 방법을 제어합니다. 루트 스쿼시는 권한이 없는 클라이언트의 루트 수준 액세스를 방지하는 데 도움이 됩니다.

이 설정을 사용하면 캐시 수준에서 루트 액세스를 제어할 수 ``no_root_squash`` 있으며, 이를 통해 스토리지 대상으로 사용되는 NAS 시스템에 필요한 설정을 보정할 수 있습니다. [(NFS 스토리지 대상 전제 조건](hpc-cache-prereqs.md#nfs-storage-requirements)자세히 보기. ) 또한 Azure Blob 저장소 대상과 함께 사용할 때 보안을 향상시킬 수 있습니다.

기본 설정은 **예입니다.** (2020년 4월 이전에 생성된 캐시에는 기본 설정 **No.)** 이 기능을 사용하면 클라이언트 요청에서 캐시에 대한 UID 권한 비트 집합을 사용할 수도 있습니다.

## <a name="view-snapshots-for-blob-storage-targets"></a>Blob 저장소 대상에 대한 스냅샷 보기

Azure HPC 캐시는 Azure Blob 저장소 대상에 대한 저장소 스냅숏을 자동으로 저장합니다. 스냅숏은 백 엔드 저장소 컨테이너의 내용에 대한 빠른 참조점을 제공합니다. 스냅숏은 데이터 백업을 대체하지 않으며 캐시된 데이터의 상태에 대한 정보는 포함하지 않습니다.

> [!NOTE]
> 이 스냅샷 기능은 NetApp, Isilon 또는 ZFS 스토리지 소프트웨어에 포함된 스냅샷 기능과 다릅니다. 이러한 스냅숏 구현은 스냅숏을 생성하기 전에 캐시에서 백 엔드 저장소 시스템으로 변경 내용을 플러시합니다.
>
> 효율성을 위해 Azure HPC 캐시 스냅숏은 변경 내용을 먼저 플러시하지 않으며 Blob 컨테이너에 기록된 데이터만 기록합니다. 이 스냅숏은 캐시된 데이터의 상태를 나타내지 않으므로 최근 변경 내용이 제외될 수 있습니다.

이 기능은 Azure Blob 저장소 대상에서만 사용할 수 있으며 구성을 변경할 수 없습니다.

스냅샷은 UTC 0:00, 08:00 및 16:00에 8시간마다 생성됩니다.

Azure HPC 캐시는 새 스냅숏으로 대체될 때까지 매일, 매주 및 월별 스냅숏을 저장합니다. 제한은 다음과 같습니다.

* 최대 20개의 일일 스냅샷
* 최대 8개의 주간 스냅샷
* 최대 3개의 월간 스냅샷

Blob 저장소 대상의 네임스페이스에 `.snapshot` 있는 디렉터리에서 스냅숏에 액세스합니다.
