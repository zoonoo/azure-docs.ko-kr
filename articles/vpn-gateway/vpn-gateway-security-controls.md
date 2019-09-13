---
title: Azure VPN Gateway에 대 한 보안 제어
description: Azure VPN Gateway 평가를 위한 보안 컨트롤의 검사 목록
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 1babb892063da6d460ea2bc4c567da954731956f
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886511"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Azure VPN Gateway에 대 한 보안 제어

이 문서에서는 Azure VPN Gateway에 기본 제공 되는 보안 컨트롤을 설명 합니다.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| N/A | |
| VNet 삽입 지원| 해당 사항 없음 | |
| 네트워크 격리 및 방화벽 지원| 예 | VPN 게이트웨이는 각 고객에 대 한 전용 VM 인스턴스입니다 Virtual Network  |
| 강제 터널링 지원| 예 |  |

## <a name="monitoring--logging"></a>& 로깅 모니터링

| 보안 제어 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | [Azure Monitor 진단 로그/경고](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure Monitor 메트릭/경고](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)를 참조 하세요.  |
| 제어 및 관리 평면 로깅 및 감사| 예 | 활동 로그를 Azure Resource Manager 합니다. |
| 데이터 평면 로깅 및 감사 | 예 | VPN 연결 로깅 및 감사에 대 한 [진단 로그를 Azure Monitor](../azure-resource-manager/resource-group-audit.md) 합니다. |

## <a name="identity"></a>클레임

| 보안 제어 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | 서비스 관리 및 Azure VPN gateway 구성에 대 한 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 합니다. |
| Authorization| 예 | [RBAC](../role-based-access-control/overview.md)를 통해 권한 부여를 지원 합니다. |

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 서버 쪽 암호화: Microsoft에서 관리 하는 키 | N/A | VPN gateway에서 고객 데이터를 전송 하 고 고객 데이터를 저장 하지 않음 |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | VPN gateway는 Azure VPN 게이트웨이와 고객 온-프레미스 VPN 장치 (S2S) 또는 VPN 클라이언트 (P2S) 간에 고객 패킷을 암호화 합니다. VPN 게이트웨이는 VNet 간 암호화도 지원 합니다. |
| 미사용 서버 쪽 암호화: 고객 관리 키 (BYOK) | 아니요 | 사용자가 지정한 미리 공유한 키는 미사용 시 암호화 됩니다. 하지만 CMK와는 아직 통합 되지 않았습니다. |
| 열 수준 암호화 (Azure Data Services)| 해당 사항 없음 | |
| API 호출 암호화| 예 | [Azure Resource Manager](../azure-resource-manager/index.yml) 및 HTTPS를 통해  |

## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 | 관리 작업의 경우 Azure VPN gateway 구성 상태를 Azure Resource Manager 템플릿으로 내보내고 시간이 지남에 따라 버전을 지정할 수 있습니다. | 

## <a name="next-steps"></a>다음 단계

- [Azure 서비스에서 기본 제공 되는 보안 컨트롤](../security/fundamentals/security-controls.md)에 대해 자세히 알아보세요.