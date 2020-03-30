---
title: Azure VPN 게이트웨이에 대한 보안 제어
description: Azure VPN 게이트웨이 를 평가하기 위한 보안 제어 검사 목록
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: cdf616b29a93e786ef26af83b5d3b3541f94d67c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972288"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Azure VPN 게이트웨이에 대한 보안 제어

이 문서에서는 Azure VPN 게이트웨이에 기본 제공 된 보안 컨트롤을 문서화 합니다.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| 해당 없음 | |
| VNet 주입 지원| 해당 없음 | |
| 네트워크 격리 및 방화벽 지원| yes | VPN 게이트웨이는 각 고객 가상 네트워크에 대한 전용 VM 인스턴스입니다.  |
| 강제 터널링 지원| yes |  |

## <a name="monitoring--logging"></a>모니터링 & 로깅

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| Azure 모니터링 지원(로그 분석, 앱 인사이트 등)| yes | [Azure 모니터 진단 로그/경고](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & Azure 모니터[메트릭/경고를](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)참조하십시오.  |
| 제어 및 관리 평면 로깅 및 감사| yes | Azure 리소스 관리자 활동 로그입니다. |
| 데이터 평면 로깅 및 감사 | yes | VPN 연결 로깅 및 감사를 위한 [Azure 모니터 진단 로그입니다.](../azure-resource-manager/management/view-activity-logs.md) |

## <a name="identity"></a>Identity

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 인증| yes | 서비스를 관리하고 Azure VPN 게이트웨이를 구성하기 위한 Azure [Active Directory입니다.](../active-directory/fundamentals/active-directory-whatis.md) |
| 권한 부여| yes | [RBAC를](../role-based-access-control/overview.md)통한 지원 권한 부여. |

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 미사용 서버 측 암호화: Microsoft에서 관리하는 키 | 해당 없음 | VPN 게이트웨이 전송 고객 데이터, 고객 데이터를 저장하지 않습니다. |
| 전송 중 암호화(예: 익스프레스라우팅 암호화, VNet 암호화 및 VNet-VNet 암호화)| yes | VPN 게이트웨이는 Azure VPN 게이트웨이와 고객 온-프레미스 VPN 장치(S2S) 또는 VPN 클라이언트(P2S) 간의 고객 패킷을 암호화합니다. VPN 게이트웨이는 VNet-VNet 암호화도 지원합니다. |
| 미사용 서버 측 암호화: 고객 관리 키(BYOK) | 예 | 고객이 지정한 사전 공유 키는 미사용 키에서 암호화됩니다. 하지만 아직 CMK와 통합되지 않았습니다. |
| 열 수준 암호화(Azure 데이터 서비스)| 해당 없음 | |
| API 호출 암호화| yes | Azure 리소스 관리자 및 [HTTPS를](../azure-resource-manager/index.yml) 통해  |

## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원(구성 버전 관리 등)| yes | 관리 작업의 경우 Azure VPN 게이트웨이 구성의 상태를 Azure 리소스 관리자 템플릿으로 내보내고 시간에 따라 버전이 정할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

- Azure 서비스 [에서 기본 제공 보안 컨트롤에](../security/fundamentals/security-controls.md)대해 자세히 알아봅니다.
