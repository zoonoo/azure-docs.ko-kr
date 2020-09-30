---
title: 사용 가능한 Azure Files 프로토콜-NFS 및 SMB
description: SMB (서버 메시지 블록) 및 NFS (네트워크 파일 시스템)를 포함 하 여 Azure 파일 공유를 만들기 전에 사용 가능한 프로토콜에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 5149024f8621754451520e0ae249ed61f0b07f99
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91568474"
---
# <a name="azure-file-share-protocols"></a>Azure 파일 공유 프로토콜

Azure Files은 Azure 파일 공유를 연결 하 고 탑재 하기 위한 두 가지 프로토콜을 제공 합니다. [SMB (서버 메시지 블록) 프로토콜](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) 및 [NFS (네트워크 파일 시스템) 프로토콜](https://en.wikipedia.org/wiki/Network_File_System) (미리 보기) Azure Files는 현재 다중 프로토콜 액세스를 지원 하지 않으므로 공유는 NFS 공유 이거나 SMB 공유 일 수 있습니다. 이로 인해 Azure 파일 공유를 만들기 전에 요구 사항에 가장 적합 한 프로토콜을 결정 하는 것이 좋습니다.

## <a name="differences-at-a-glance"></a>한 눈에 차이점

|기능  |NFS (미리 보기)  |SMB  |
|---------|---------|---------|
|액세스 프로토콜     |NFS 4.1         |SMB 2.1, SMB 3.0         |
|지원되는 OS     |Linux 커널 버전 4.3 이상         |Windows 2008 R2 이상, Linux 커널 버전 4.11 이상         |
|[사용 가능한 계층](storage-files-planning.md#storage-tiers)     |Premium Storage         |Premium storage, 트랜잭션 최적화, 핫, 쿨         |
|[중복](storage-files-planning.md#redundancy)     |LRS         |LRS, ZRS, GRS         |
|인증     |호스트 기반 인증만        |Id 기반 인증, 사용자 기반 인증         |
|권한     |UNIX 스타일 사용 권한         |NTFS 스타일 사용 권한         |
|파일 시스템 의미 체계     |POSIX 규격         |POSIX 규격이 아님         |
|대/소문자 구분     |대/소문자 구분         |대/소문자 구분하지 않음         |
|하드 링크 지원     |지원됨         |지원되지 않음         |
|기호화 된 링크 지원     |지원됨         |지원되지 않음         |
|열려 있는 파일 삭제 또는 수정     |지원됨         |지원되지 않음         |
|잠금     |바이트 범위 advise 네트워크 잠금 관리자         |지원됨         |
|공용 IP 안전 목록 | 지원되지 않음 | 지원됨|
|프로토콜 interop| 지원되지 않음 | FileREST|

## <a name="nfs-shares-preview"></a>NFS 공유 (미리 보기)

NFS 4.1를 사용 하 여 Azure 파일 공유를 탑재 하는 것은 현재 미리 보기 상태입니다. Linux와의 긴밀 한 통합을 제공 합니다. 이는 Unix 및 기타 * nix 기반 운영 체제의 다양 한 변형에서 제공 되는 완전히 POSIX 규격 제품입니다. 이 엔터프라이즈급 파일 저장소 서비스는 저장소 요구를 충족 하기 위해 확장 되며 수천 개의 계산 인스턴스로 동시에 액세스할 수 있습니다.

### <a name="limitations"></a>제한 사항

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

#### <a name="regional-availability"></a>국가별 가용성

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

### <a name="best-suited"></a>가장 적합 한

Azure Files 있는 NFS는 다음과 같은 경우에 적합 합니다.

- POSIX 규격 파일 공유, 대/소문자 구분 또는 Unix 스타일 권한 (UID/GID)이 필요한 작업입니다.
- Windows 액세스를 필요로 하지 않는 Linux 중심 워크 로드

### <a name="security"></a>보안

모든 Azure Files 데이터는 미사용 시 암호화 됩니다. 전송 중인 암호화의 경우 Azure는 [Macsec](https://en.wikipedia.org/wiki/IEEE_802.1AE)를 사용 하 여 Azure 데이터 센터 간에 전송 중인 모든 데이터에 대 한 암호화 계층을 제공 합니다. 이를 통해 Azure 데이터 센터 간에 데이터를 전송할 때 암호화가 존재 합니다. SMB 프로토콜을 사용 하는 Azure Files와 달리 NFS 프로토콜을 사용 하는 파일 공유는 사용자 기반 인증을 제공 하지 않습니다. NFS 공유에 대 한 인증은 구성 된 네트워크 보안 규칙을 기반으로 합니다. 이로 인해 NFS 공유에 대 한 보안 연결만 설정 되도록 하려면 서비스 끝점이 나 개인 끝점 중 하나를 사용 해야 합니다. 온-프레미스에서 공유에 액세스 하려면 개인 끝점 외에 VPN 또는 Express 경로를 설정 해야 합니다. 다음 원본에서 시작 하지 않은 요청은 거부 됩니다.

- [개인 끝점](storage-files-networking-overview.md#private-endpoints)
- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
    - [지점 및 사이트 간 (P2S) VPN](../../vpn-gateway/point-to-site-about.md)
    - [사이트 간](https://docs.microsoft.com/azure/vpn-gateway/design#s2smulti)
- [ExpressRoute](../../expressroute/expressroute-introduction.md)
- [제한 된 공용 끝점](storage-files-networking-overview.md#storage-account-firewall-settings)

사용 가능한 네트워킹 옵션에 대 한 자세한 내용은 [Azure Files 네트워킹 고려 사항](storage-files-networking-overview.md)을 참조 하세요.

## <a name="smb-shares"></a>SMB 공유

SMB로 탑재 된 Azure 파일 공유는 더 많은 Azure Files 기능을 제공 하며 일반 공급 되므로 Azure Files 기능 제한이 없습니다.

### <a name="features"></a>기능

- Azure 파일 동기화
- Id 기반 인증
- Azure Backup 지원
- 스냅샷
- 일시 삭제
- 암호화-전송 중 및 미사용 암호화

### <a name="best-suited"></a>가장 적합 한

Azure Files SMB는 다음과 같은 경우에 적합 합니다.

- 프로덕션 환경
- [기능](#features) 에 나열 된 기능을 필요로 하는 고객

## <a name="next-steps"></a>다음 단계

- [NFS 파일 공유 만들기](storage-files-how-to-create-nfs-shares.md)
- [SMB 파일 공유 만들기](storage-how-to-create-file-share.md)