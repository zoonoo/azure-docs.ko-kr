---
title: 사용 가능한 Azure Files 프로토콜 - NFS 및 SMB
description: Azure 파일 공유를 생성하기 전에, SMB(서버 메시지 블록) 및 NFS(네트워크 파일 시스템) 등 사용 가능한 프로토콜에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 792f869aff0e7a91eeacd23e52c795d2c1243c0c
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110062383"
---
# <a name="azure-file-share-protocols"></a>Azure 파일 공유 프로토콜

Azure Files는 Azure 파일 공유를 연결하고 탑재하는 두 가지 프로토콜을 제공합니다. [SMB(서버 메시지 블록) 프로토콜](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) 및 [NFS(네트워크 파일 시스템) 프로토콜](https://en.wikipedia.org/wiki/Network_File_System)(미리 보기). Azure Files는 현재 다중 프로토콜 액세스를 지원하지 않으므로, NFS 공유 또는 SMB 공유 중 하나만 사용하여 공유할 수 있습니다. 따라서 Azure 파일 공유를 만들기 전에, 어느 프로토콜이 자신의 요구 사항에 가장 적합한지 결정하는 것이 좋습니다.

## <a name="differences-at-a-glance"></a>한눈에 보는 차이점

|기능  |NFS(미리 보기)  |SMB  |
|---------|---------|---------|
|액세스 프로토콜     |NFS 4.1         |SMB 3.1.1, SMB 3.0, SMB 2.1         |
|권장 OS     |Linux 커널 버전 4.3 이상         |Windows 2008 R2 이상, Linux 커널 버전 4.11 이상         |
|[사용 가능한 계층](storage-files-planning.md#storage-tiers)     |Premium Storage         |프리미엄 스토리지, 트랜잭션 최적화, 핫, 쿨         |
|청구 모델         |[프로비저닝된 용량에 따라 비용 지불](./understanding-billing.md#provisioned-model)         |[프리미엄 계층은 프로비저닝된 용량에 따라 비용 지불](./understanding-billing.md#provisioned-model), [표준 계층은 종량제](./understanding-billing.md#pay-as-you-go-model)         |
|[중복](storage-files-planning.md#redundancy)     |LRS, ZRS         |LRS, ZRS, GRS         |
|인증     |호스트 기반 인증만        |ID 기반 인증, 사용자 기반 인증         |
|사용 권한     |UNIX 스타일 사용 권한         |NTFS 스타일 사용 권한         |
|파일 시스템 의미 체계     |POSIX 규격         |POSIX 규격이 아님         |
|대/소문자 구분     |대/소문자 구분         |대/소문자 구분하지 않음         |
|하드 링크 지원     |지원됨         |지원되지 않음         |
|기호화된 링크 지원     |지원됨         |지원되지 않음         |
|열려 있는 파일 삭제 또는 수정     |지원됨         |지원되지 않음         |
|잠금     |바이트 범위 권고 네트워크 잠금 관리자         |지원됨         |
|공용 IP 안전 목록 | 지원되지 않음 | 지원됨|
|프로토콜 interop| 지원되지 않음 | FileREST|

## <a name="nfs-shares-preview"></a>NFS 공유(미리 보기)

NFS 4.1로 Azure 파일 공유를 탑재하는 기능은 현재 미리 보기 상태입니다. 이 기능을 통해 Linux와 긴밀하게 통합할 수 있습니다. Unix 및 기타 *nix 기반 운영 체제 변형 전반에서 표준이며 POSIX 규격을 완벽하게 만족하는 제품입니다. 이 엔터프라이즈급 파일 스토리지 서비스는 스토리지 요구에 맞게 스케일 업 가능하며 수천 개의 컴퓨팅 인스턴스가 동시에 액세스할 수 있습니다.

### <a name="limitations"></a>제한 사항

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

#### <a name="regional-availability"></a>국가별 가용성

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

### <a name="best-suited"></a>가장 적합한 용도

Azure Files NFS는 다음과 같은 경우에 적합합니다.

- POSIX 규격 파일 공유, 대/소문자 구분 또는 Unix 스타일 권한(UID/GID)이 필요한 워크로드.
- Windows 액세스가 필요 없는 Linux 중심 워크로드.

### <a name="security"></a>보안

Azure Files의 모든 미사용 데이터가 암호화됩니다. 전송 중인 암호화의 경우 Azure은 [MACSec](https://en.wikipedia.org/wiki/IEEE_802.1AE)을 사용하여 Azure Datacenter 간에 전송 중인 모든 데이터에 대해 암호화 계층을 제공합니다. 이를 통해 Azure 데이터 센터 간에 데이터를 전송할 때 암호화가 존재합니다. SMB 프로토콜을 사용하는 Azure Files와 달리 NFS 프로토콜을 사용하는 파일 공유는 사용자 기반 인증을 제공하지 않습니다. NFS 공유에 대한 인증은 구성된 네트워크 보안 규칙을 기반으로 합니다. 따라서 NFS 공유에 안전한 연결만 설정되도록 하려면, 서비스 엔드포인트 또는 프라이빗 엔드포인트를 사용해야 합니다. 온-프레미스 공유에 액세스하려면, 프라이빗 엔드포인트 외에도 VPN 또는 ExpressRoute를 설정해야 합니다. 다음 원본에서 시작되지 않은 요청은 거부됩니다.

- [프라이빗 엔드포인트](storage-files-networking-overview.md#private-endpoints)
- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
    - [P2S(지점 및 사이트) VPN](../../vpn-gateway/point-to-site-about.md)
    - [사이트 간](../../vpn-gateway/design.md#s2smulti)
- [ExpressRoute](../../expressroute/expressroute-introduction.md)
- [제한된 퍼블릭 엔드포인트](storage-files-networking-overview.md#storage-account-firewall-settings)

사용 가능한 네트워킹 옵션에 대한 자세한 내용은 [Azure Files 네트워킹 고려 사항](storage-files-networking-overview.md)을 참조하세요.

## <a name="smb-shares"></a>SMB 공유

SMB가 탑재된 Azure 파일 공유는 더 많은 Azure Files 기능을 제공하며 일반적으로 사용 가능하므로 Azure Files 기능 제한이 없습니다.

### <a name="features"></a>기능

- Azure 파일 동기화
- ID 기반 인증
- Azure Backup 지원
- 스냅샷
- 일시 삭제
- 전송 중 암호화 및 미사용 시 암호화

### <a name="best-suited"></a>가장 적합한 용도

Azure Files SMB는 다음과 같은 경우에 적합합니다.

- 프로덕션 환경
- [기능](#features)에 나열된 기능을 필요로 하는 고객

## <a name="next-steps"></a>다음 단계

- [NFS 파일 공유 만들기](storage-files-how-to-create-nfs-shares.md)
- [SMB 파일 공유 만들기](storage-how-to-create-file-share.md)