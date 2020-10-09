---
title: Azure Service Bus 메시징의 보안 제어
description: Azure Service Bus 메시징을 평가 하기 위한 보안 컨트롤의 검사 목록
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 3130150a227076befae3f58f65e00a36578b68d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85341632"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Azure Service Bus 메시징의 보안 제어

이 문서에서는 Azure Service Bus 메시징에 기본 제공 되는 보안 컨트롤을 설명 합니다.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 컨트롤 | 예/아니요 | 메모 | 문서화 |
|---|---|--|--|
| 서비스 엔드포인트 지원| 예 (프리미엄 계층만) | VNet 서비스 끝점은 [Service Bus 프리미엄 계층](service-bus-premium-messaging.md) 에 대해서만 지원 됩니다. |  |
| VNet 삽입 지원| 예 | |  |
| 네트워크 격리 및 방화벽 지원| 예 (프리미엄 계층만) |  |  |
| 강제 터널링 지원| 아니요 |  |  |

## <a name="monitoring--logging"></a>모니터링 및 로깅

| 보안 컨트롤 | 예/아니요 | 메모| 문서화 |
|---|---|--|--|
| Azure 모니터링 지원(Log Analytics, App insights 등)| 예 | [Azure Monitor 및 경고를](service-bus-metrics-azure-monitor.md)통해 지원 됩니다. |  |
| 제어와 관리 평면 로깅 및 감사| 예 | 작업 로그를 사용할 수 있습니다.  | [Service Bus 진단 로그](service-bus-diagnostic-logs.md) |
| 데이터 평면 로깅 및 감사| 아니요 |  |

## <a name="identity"></a>ID

| 보안 컨트롤 | 예/아니요 | 메모| 문서화 |
|---|---|--|--|
| 인증| 예 | [Azure Active Directory 관리 서비스 ID](service-bus-managed-service-identity.md)를 통해 관리 됩니다.| [인증 및 권한 부여를 Service Bus](service-bus-authentication-and-authorization.md)합니다. |
| 권한 부여| 예 | [RBAC](authenticate-application.md) 및 SAS 토큰을 통한 권한 부여를 지원 합니다. | [인증 및 권한 부여를 Service Bus](service-bus-authentication-and-authorization.md)합니다. |

## <a name="data-protection"></a>데이터 보호

| 보안 컨트롤 | 예/아니요 | 메모 | 문서화 |
|---|---|--|--|
| 미사용 서버 쪽 암호화: Microsoft 관리형 키 |  기본적으로 서버 쪽 암호화에 대 한 예입니다. |  |  |
| 미사용 서버 쪽 암호화: 고객 관리형 키(BYOK) | 예. | Azure KeyVault의 고객 관리 키를 사용 하 여 미사용 Service Bus 네임 스페이스의 데이터를 암호화할 수 있습니다. | [Azure Portal를 사용 하 여 미사용 데이터 Azure Service Bus 데이터를 암호화 하기 위해 고객이 관리 하는 키 구성](configure-customer-managed-key.md)  |
| 열 수준 암호화(Azure Data Services)| 해당 없음 | |   |
| 전송 중 암호화(예: ExpressRoute 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | 표준 HTTPS/TLS 메커니즘을 지원 합니다. |   |
| API 호출 암호화| 예 | API 호출은 [Azure Resource Manager](../azure-resource-manager/index.yml) 및 HTTPS를 통해 수행 됩니다. |   |

## <a name="configuration-management"></a>구성 관리

| 보안 컨트롤 | 예/아니요 | 메모| 문서화 |
|---|---|--|--|
| 구성 관리 지원(구성 버전 관리 등)| 예 | [AZURE RESOURCE MANAGER API](/rest/api/resources/)를 통해 리소스 공급자 버전 관리를 지원 합니다.|   |

## <a name="next-steps"></a>다음 단계

- [Azure 서비스의 기본 제공 보안 컨트롤](../security/fundamentals/security-controls.md)에 대해 자세히 알아봅니다.
