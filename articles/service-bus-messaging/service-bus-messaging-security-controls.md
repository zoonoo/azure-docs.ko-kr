---
title: Azure 서비스 버스 메시징에 대한 보안 제어
description: Azure 서비스 버스 메시징 을 평가하기 위한 보안 컨트롤의 검사 목록
services: service-bus-messaging
ms.service: service-bus-messaging
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: af119ef026b70fcb4a56b4f823d20c0e9eddddc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75903257"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Azure 서비스 버스 메시징에 대한 보안 제어

이 문서에서는 Azure Service Bus 메시징에 기본 제공 된 보안 컨트롤을 문서화 합니다.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 메모 | 설명서 |
|---|---|--|--|
| 서비스 엔드포인트 지원| 예(프리미엄 계층만) | VNet 서비스 끝점은 [서비스 버스 프리미엄 계층에만](service-bus-premium-messaging.md) 지원됩니다. |  |
| VNet 주입 지원| 예 | |  |
| 네트워크 격리 및 방화벽 지원| 예(프리미엄 계층만) |  |  |
| 강제 터널링 지원| 예 |  |  |

## <a name="monitoring--logging"></a>모니터링 & 로깅

| 보안 제어 | 예/아니요 | 메모| 설명서 |
|---|---|--|--|
| Azure 모니터링 지원(로그 분석, 앱 인사이트 등)| yes | Azure 모니터 및 경고를 통해 [지원됩니다.](service-bus-metrics-azure-monitor.md) |  |
| 제어 및 관리 평면 로깅 및 감사| yes | 작업 로그를 사용할 수 있습니다.  | [Service Bus 진단 로그](service-bus-diagnostic-logs.md) |
| 데이터 평면 로깅 및 감사| 예 |  |

## <a name="identity"></a>Identity

| 보안 제어 | 예/아니요 | 메모| 설명서 |
|---|---|--|--|
| 인증| yes | Azure [Active Directory 관리 되는 서비스 ID를](service-bus-managed-service-identity.md)통해 관리 됩니다.| [서비스 버스 인증 및 권한 부여.](service-bus-authentication-and-authorization.md) |
| 권한 부여| yes | RBAC 및 SAS [토큰을](authenticate-application.md) 통해 승인을 지원합니다. | [서비스 버스 인증 및 권한 부여.](service-bus-authentication-and-authorization.md) |

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 메모 | 설명서 |
|---|---|--|--|
| 미사용 서버 측 암호화: Microsoft에서 관리하는 키 |  기본적으로 서버 측 암호화-미사용 암호화의 경우 예입니다. |  |  |
| 미사용 서버 측 암호화: 고객 관리 키(BYOK) | 예. | Azure KeyVault에서 고객 관리 키를 사용하여 미사용 서비스 버스 네임스페이스의 데이터를 암호화할 수 있습니다. | [Azure 포털을 사용하여 미사용 Azure Service Bus 데이터를 암호화하기 위한 고객 관리 키 구성](configure-customer-managed-key.md)  |
| 열 수준 암호화(Azure 데이터 서비스)| 해당 없음 | |   |
| 전송 중 암호화(예: 익스프레스루트 암호화, VNet 암호화 및 VNet-VNet 암호화)| yes | 표준 HTTPS/TLS 메커니즘을 지원합니다. |   |
| API 호출 암호화| yes | API 호출은 [Azure 리소스 관리자](../azure-resource-manager/index.yml) 및 HTTPS를 통해 이루어집니다. |   |

## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 메모| 설명서 |
|---|---|--|--|
| 구성 관리 지원(구성 버전 관리 등)| yes | [Azure 리소스 관리자 API를](/rest/api/resources/)통해 리소스 공급자 버전 관리 를 지원합니다.|   |

## <a name="next-steps"></a>다음 단계

- Azure 서비스 [에서 기본 제공 보안 컨트롤에](../security/fundamentals/security-controls.md)대해 자세히 알아봅니다.
