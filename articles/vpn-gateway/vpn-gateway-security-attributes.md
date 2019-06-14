---
title: Azure VPN Gateway에 대 한 보안 특성
description: Azure VPN Gateway를 평가 하는 것에 대 한 보안 특성에 대 한 검사 목록
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 0e58e7b3f77d9bb673e300aa60ad07ca9dba5153
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083133"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Azure VPN Gateway에 대 한 보안 특성

이 문서에서는 Azure VPN Gateway에 기본 제공 되는 일반적인 보안 특성을 설명 합니다.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| (예: 서버 쪽 암호화, 고객 관리 키를 사용 하 여 서버 쪽 암호화 및 기타 암호화 기능)는 미사용 데이터 암호화 | N/A | VPN 게이트웨이 전송 고객 데이터는 고객 데이터를 저장 하지 않습니다. |
| (예: ExpressRoute 암호화 VNet 암호화 및 VNet 대 VNet 암호화에서) 전송 중 암호화| 예 | Azure VPN gateway와 고객의 온-프레미스 VPN 장치 (S2S) 또는 VPN 클라이언트 (P2S) 간에 고객 패킷을 암호화 하는 VPN 게이트웨이. 또한 VPN gateway VNet 대 VNet 암호화를 지원합니다. |
| 암호화 키 처리 (예: CMK, BYOK)| 아닙니다. | 미리 공유한 키를 고객이 지정한, 미사용 암호화 됩니다. 하지만 아직 CMK와 통합 되지 않습니다. |
| 열 수준 암호화 (Azure Data Services)| N/A | |
| API 호출 암호화| 예 | 통해 [Azure Resource Manager](../azure-resource-manager/index.yml) 및 HTTPS  |

## <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 끝점 지원| N/A | |
| VNet 삽입 지원| N/A | . |
| 네트워크 격리 및 Firewalling 지원| 예 | VPN 게이트웨이 각 고객 가상 네트워크에 대 한 전용된 VM 인스턴스  |
| 강제 터널링 지원| 예 |  |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | 참조 [Azure Monitor 진단 로그/경고가](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure Monitor 메트릭/경고](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)합니다.  |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예 | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 서비스 관리 및 Azure VPN gateway를 구성 합니다. |
| 권한 부여| 예 | 통해 권한 부여를 지 원하는 [RBAC](../role-based-access-control/overview.md)합니다. |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | Azure Resource Manager 활동 로그입니다. |
| 데이터 평면 로깅 및 감사 | 예 | [Azure Monitor 진단 로그](../azure-resource-manager/resource-group-audit.md) 로깅 및 감사 하는 VPN 연결에 대 한 합니다. |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 예 | 관리 작업에 대 한 Azure VPN gateway 구성의 상태가 시간이 지남에 따라 Azure Resource Manager 템플릿으로 및 버전을 내보낼 수 있습니다. | 