---
title: Azure HPC 캐시 설정 구성
description: MTU, 사용자 지정 NTP 및 DNS 구성, Azure Blob 저장소 대상에서 express 스냅숏에 액세스 하는 방법 등 캐시에 대 한 추가 설정을 구성 하는 방법에 대해 설명 합니다.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 06feefe3a934d1ee02793fab442852e5ef40899a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563383"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>추가 Azure HPC 캐시 설정 구성

Azure Portal의 **네트워킹** 페이지에는 여러 설정을 사용자 지정 하는 옵션이 있습니다. 대부분의 사용자는 이러한 설정을 기본값에서 변경할 필요가 없습니다.

이 문서에서는 Azure Blob 저장소 대상에 대 한 스냅숏 기능을 사용 하는 방법에 대해서도 설명 합니다. 스냅숏 기능에 구성 가능한 설정이 없습니다.

설정을 보려면 Azure Portal에서 캐시의 **네트워킹** 페이지를 엽니다.

![Azure Portal의 네트워킹 페이지 스크린샷](media/networking-page.png)

> [!NOTE]
> 이 페이지의 이전 버전에는 캐시 수준 루트 squash 설정이 포함 되어 있지만이 설정은 [클라이언트 액세스 정책](access-policies.md)으로 이동 되었습니다.

<!-- >> [!TIP]
> The [Managing Azure HPC Cache video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) shows the networking page and its settings. -->

## <a name="adjust-mtu-value"></a>MTU 값 조정
<!-- linked from troubleshoot-nas article -->

**MTU 크기** 레이블이 지정 된 드롭다운 메뉴를 사용 하 여 캐시에 대 한 최대 전송 단위 크기를 선택할 수 있습니다.

기본값은 1500 바이트 이지만 1400로 변경할 수 있습니다.

> [!NOTE]
> 캐시의 MTU 크기를 낮추는 경우 캐시와 통신 하는 클라이언트와 저장소 시스템의 MTU 설정이 동일 하거나 더 낮은 값이 있는지 확인 합니다.

캐시 MTU 값을 낮추면 나머지 캐시 네트워크에서 패킷 크기 제한을 해결 하는 데 도움이 될 수 있습니다. 예를 들어 일부 Vpn은 전체 크기 1500 바이트 패킷을 전송할 수 없습니다. VPN을 통해 전송 되는 패킷의 크기를 줄이면이 문제를 해결할 수 있습니다. 그러나 캐시 MTU 설정이 낮을수록 클라이언트 및 저장소 시스템을 비롯 하 여 캐시와 통신 하는 다른 구성 요소에도 낮은 MTU 설정이 있어야 통신 문제가 발생 하지 않습니다.

다른 시스템 구성 요소에서 MTU 설정을 변경 하지 않으려는 경우 캐시의 MTU 설정을 낮추어 서는 안 됩니다. VPN 패킷 크기 제한 문제를 해결 하는 다른 솔루션이 있습니다. 이 문제를 진단 하 고 해결 하는 방법에 대 한 자세한 내용은 NAS 문제 해결 문서에서 [VPN 패킷 크기 조정 제한](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) 을 참조 하세요.

Azure [vm에 대 한 tcp/ip 성능 튜닝](../virtual-network/virtual-network-tcpip-performance-tuning.md)을 읽어 azure virtual NETWORK의 MTU 설정에 대해 자세히 알아보세요.

## <a name="customize-ntp"></a>NTP 사용자 지정

캐시는 기본적으로 Azure 기반 시간 서버 time.microsoft.com를 사용 합니다. 캐시에서 다른 NTP 서버를 사용 하려면 **ntp 구성** 섹션에서 지정 합니다. 정규화 된 도메인 이름 또는 IP 주소를 사용 합니다.

## <a name="set-a-custom-dns-configuration"></a>사용자 지정 DNS 구성 설정

> [!CAUTION]
> 필요 하지 않은 경우 캐시 DNS 구성을 변경 하지 마세요. 구성 실수가 디렉터리 결과가 발생할 수 있습니다. 구성에서 Azure 서비스 이름을 확인할 수 없는 경우 HPC 캐시 인스턴스는 영구적으로 액세스할 수 없게 됩니다.

Azure HPC 캐시는 안전 하 고 편리한 Azure DNS 시스템을 사용 하도록 자동으로 구성 됩니다. 그러나 일부 비정상적인 구성의 경우 캐시에서 Azure 시스템 대신 별도의 온-프레미스 DNS 시스템을 사용 해야 합니다. **네트워킹** 페이지의 **DNS 구성** 섹션을 사용 하 여 이러한 종류의 시스템을 지정 합니다.

Azure 담당자에 게 문의 하거나 Microsoft 서비스 및 지원에 문의 하 여 사용자 지정 캐시 DNS 구성을 사용 해야 하는지 여부를 확인 합니다.

Azure HPC 캐시가 사용할 수 있도록 온-프레미스 DNS 시스템을 구성 하는 경우 구성에서 azure 서비스에 대 한 Azure 끝점 이름을 확인할 수 있는지 확인 해야 합니다. 필요에 따라 Azure DNS 또는 다른 서버로 특정 이름 확인 요청을 전달 하도록 사용자 지정 DNS 환경을 구성 해야 합니다.

Azure HPC 캐시에 사용 하기 전에 DNS 구성이 이러한 항목을 성공적으로 확인할 수 있는지 확인 합니다.

* ``*.core.windows.net``
* CRL (인증서 해지 목록) 다운로드 및 OCSP (온라인 인증서 상태 프로토콜) 확인 서비스. 이 [AZURE TLS 문서의](../security/fundamentals/tls-certificate-changes.md)끝에 있는 [방화벽 규칙 항목](../security/fundamentals/tls-certificate-changes.md#will-this-change-affect-me) 에는 일부 목록이 제공 되지만 Microsoft 기술 담당자에 게 문의 하 여 모든 요구 사항을 이해 해야 합니다.
* NTP 서버의 정규화 된 도메인 이름 (time.microsoft.com 또는 사용자 지정 서버)

캐시에 대해 사용자 지정 DNS 서버를 설정 해야 하는 경우 제공 된 필드를 사용 합니다.

* **DNS 검색 도메인** (선택 사항)-검색 도메인을 입력 합니다 (예:) ``contoso.com`` . 단일 값이 허용 되거나 비워 둘 수 있습니다.
* **Dns 서버** -최대 3 개의 dns 서버를 입력 합니다. IP 주소를 기준으로 지정 합니다.

<!-- 
  > [!NOTE]
  > The cache will use only the first DNS server it successfully finds. -->

### <a name="refresh-storage-target-dns"></a>저장소 대상 DNS 새로 고침

DNS 서버에서 IP 주소를 업데이트 하는 경우 관련 NFS 저장소 대상을 일시적으로 사용할 수 없게 됩니다. [저장소 대상 편집](hpc-cache-edit-storage.md#update-ip-address-custom-dns-configurations-only)에서 사용자 지정 DNS 시스템 IP 주소를 업데이트 하는 방법을 참조 하세요.

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

`.snapshot`Blob 저장소 대상의 네임 스페이스에 있는 디렉터리에서 스냅숏에 액세스 합니다.
