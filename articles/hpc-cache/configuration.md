---
title: Azure HPC 캐시 설정 구성
description: MTU 및 비 루트 squash 같은 캐시에 대 한 추가 설정을 구성 하는 방법 및 Azure Blob 저장소 대상에서 express 스냅숏에 액세스 하는 방법을 설명 합니다.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: v-erkel
ms.openlocfilehash: a3bab06166110a3627bb3a99d51ceb09b0c7ed80
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871422"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>추가 Azure HPC 캐시 설정 구성

Azure Portal **구성** 페이지에는 여러 설정을 사용자 지정 하는 옵션이 있습니다. 대부분의 사용자는 이러한 설정을 기본값에서 변경할 필요가 없습니다.

이 문서에서는 Azure Blob 저장소 대상에 대 한 스냅숏 기능을 사용 하는 방법에 대해서도 설명 합니다. 스냅숏 기능에 구성 가능한 설정이 없습니다.

설정을 보려면 Azure Portal에서 캐시의 **구성** 페이지를 엽니다.

![Azure Portal 구성 페이지의 스크린샷](media/configuration.png)

## <a name="adjust-mtu-value"></a>MTU 값 조정
<!-- linked from troubleshoot-nas article -->

**MTU 크기**레이블이 지정 된 드롭다운 메뉴를 사용 하 여 캐시에 대 한 최대 전송 단위 크기를 선택할 수 있습니다.

기본값은 1500 바이트 이지만 1400로 변경할 수 있습니다.

> [!NOTE]
> 캐시의 MTU 크기를 낮추는 경우 캐시와 통신 하는 클라이언트와 저장소 시스템의 MTU 설정이 동일 하거나 더 낮은 값이 있는지 확인 합니다.

캐시 MTU 값을 낮추면 나머지 캐시 네트워크에서 패킷 크기 제한을 해결 하는 데 도움이 될 수 있습니다. 예를 들어 일부 Vpn은 전체 크기 1500 바이트 패킷을 전송할 수 없습니다. VPN을 통해 전송 되는 패킷의 크기를 줄이면이 문제를 해결할 수 있습니다. 그러나 캐시 MTU 설정이 낮을수록 클라이언트 및 저장소 시스템을 비롯 하 여 캐시와 통신 하는 다른 구성 요소에도 낮은 MTU 설정이 있어야 통신 문제가 발생 하지 않습니다.

다른 시스템 구성 요소에서 MTU 설정을 변경 하지 않으려는 경우 캐시의 MTU 설정을 낮추어 서는 안 됩니다. VPN 패킷 크기 제한 문제를 해결 하는 다른 솔루션이 있습니다. 이 문제를 진단 하 고 해결 하는 방법에 대 한 자세한 내용은 NAS 문제 해결 문서에서 [VPN 패킷 크기 조정 제한](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) 을 참조 하세요.

Azure [vm에 대 한 tcp/ip 성능 튜닝](../virtual-network/virtual-network-tcpip-performance-tuning.md)을 읽어 azure virtual NETWORK의 MTU 설정에 대해 자세히 알아보세요.

## <a name="configure-root-squash"></a>Root squash 구성
<!-- linked from troubleshoot -->

**Enable root squash** 설정은 Azure HPC 캐시가 클라이언트 컴퓨터에서 루트 사용자의 요청을 처리 하는 방법을 제어 합니다.

Root squash를 사용 하도록 설정 하면 클라이언트의 루트 사용자가 Azure HPC 캐시를 통해 요청을 보낼 때 사용자에 게 자동으로 매핑됩니다. 또한 클라이언트 요청에서 UID 권한 비트를 사용 하는 것을 방지 합니다.

Root squash가 사용 하지 않도록 설정 된 경우 클라이언트 루트 사용자 (UID 0)의 요청은 백 엔드 NFS 저장소 시스템에 루트로 전달 됩니다. 이 구성은 부적절 한 파일 액세스를 허용할 수 있습니다.

캐시에서 root squash를 설정 하면 저장소 대상으로 사용 되 ``no_root_squash`` 는 NAS 시스템에서 필요한 설정을 보정할 수 있습니다. [NFS 저장소 대상 필수 구성 요소](hpc-cache-prereqs.md#nfs-storage-requirements)에 대해 자세히 알아보세요. 또한 Azure Blob 저장소 대상에서 사용 하는 경우 보안을 향상 시킬 수 있습니다.

기본 설정은 **예**입니다. 4 월 2020 이전에 만든 캐시에는 기본 설정인 **No**가 있을 수 있습니다.

## <a name="view-snapshots-for-blob-storage-targets"></a>Blob 저장소 대상에 대 한 스냅숏 보기

Azure HPC 캐시는 Azure Blob 저장소 대상에 대 한 저장소 스냅숏을 자동으로 저장 합니다. 스냅숏은 백 엔드 저장소 컨테이너의 콘텐츠에 대 한 빠른 참조 지점을 제공 합니다.

스냅숏은 데이터 백업의 대체가 아니라 캐시 된 데이터의 상태에 대 한 정보를 포함 하지 않습니다.

> [!NOTE]
> 이 스냅숏 기능은 NetApp 또는 Isilon 저장소 소프트웨어에 포함 된 스냅숏 기능과 다릅니다. 이러한 스냅숏 구현은 스냅숏을 만들기 전에 캐시에서 백 엔드 저장소 시스템으로 변경 내용을 플러시합니다.
>
> 효율성을 위해 Azure HPC 캐시 스냅숏은 변경 내용을 먼저 플러시하지 않고 Blob 컨테이너에 기록 된 데이터만 기록 합니다. 이 스냅숏은 캐시 된 데이터의 상태를 나타내지 않으므로 최근 변경 내용을 포함 하지 않을 수 있습니다.

이 기능은 Azure Blob 저장소 대상에 대해서만 사용할 수 있으며 해당 구성을 변경할 수 없습니다.

스냅숏은 8 시간 (UTC 0:00, 08:00 및 16:00) 마다 수행 됩니다.

Azure HPC 캐시는 새 항목으로 바뀔 때까지 매일, 매주 및 매월 스냅숏을 저장 합니다. 제한은 다음과 같습니다.

* 최대 20 일간의 스냅숏
* 최대 8 개의 주간 스냅숏
* 최대 3 개의 월간 스냅숏

Blob 저장소 대상의 네임 `.snapshot` 스페이스에 있는 디렉터리에서 스냅숏에 액세스 합니다.
