---
title: Azure VPN Gateway에 대 한 보안 특성
description: Azure VPN Gateway 평가를 위한 보안 특성의 검사 목록
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: c355f70975f441609304a4c9ee2ead75f0e0ce25
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444595"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Azure VPN Gateway에 대 한 보안 특성

이 문서에서는 Azure VPN Gateway에 기본 제공 되는 보안 특성을 설명 합니다.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능) | N/A | VPN gateway에서 고객 데이터를 전송 하 고 고객 데이터를 저장 하지 않음 |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | VPN gateway는 Azure VPN 게이트웨이와 고객 온-프레미스 VPN 장치 (S2S) 또는 VPN 클라이언트 (P2S) 간에 고객 패킷을 암호화 합니다. VPN 게이트웨이는 VNet 간 암호화도 지원 합니다. |
| 암호화 키 처리 (CMK, BYOK 등)| 아니요 | 사용자가 지정한 미리 공유한 키는 미사용 시 암호화 됩니다. 하지만 CMK와는 아직 통합 되지 않았습니다. |
| 열 수준 암호화 (Azure Data Services)| 해당 사항 없음 | |
| API 호출 암호화| 예 | [Azure Resource Manager](../azure-resource-manager/index.yml) 및 HTTPS를 통해  |

## <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 해당 사항 없음 | |
| VNet 삽입 지원| 해당 사항 없음 | 을 선택합니다. |
| 네트워크 격리 및 방화벽 지원| 예 | VPN 게이트웨이는 각 고객에 대 한 전용 VM 인스턴스입니다 Virtual Network  |
| 강제 터널링 지원| 예 |  |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | [Azure Monitor 진단 로그/경고](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure Monitor 메트릭/경고](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)를 참조 하세요.  |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | 서비스 관리 및 Azure VPN gateway 구성에 대 한 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 합니다. |
| Authorization| 예 | [RBAC](../role-based-access-control/overview.md)를 통해 권한 부여를 지원 합니다. |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | 활동 로그를 Azure Resource Manager 합니다. |
| 데이터 평면 로깅 및 감사 | 예 | VPN 연결 로깅 및 감사에 대 한 [진단 로그를 Azure Monitor](../azure-resource-manager/resource-group-audit.md) 합니다. |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 | 관리 작업의 경우 Azure VPN gateway 구성 상태를 Azure Resource Manager 템플릿으로 내보내고 시간이 지남에 따라 버전을 지정할 수 있습니다. | 