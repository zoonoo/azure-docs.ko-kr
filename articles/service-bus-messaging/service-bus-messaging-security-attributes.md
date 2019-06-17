---
title: Azure Service Bus 메시징에 대 한 일반적인 보안 특성
description: Azure Service Bus 메시징 평가 하기 위한 일반적인 보안 특성에 대 한 검사 목록
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: d68ffe6561da6a23c288dfabd1d3eb6b34099bb3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66003103"
---
# <a name="security-attributes-for-azure-service-bus-messaging"></a>Azure Service Bus 메시징에 대 한 보안 특성

이 문서에서는 Azure Service Bus 메시징에 기본 제공 보안 특성을 설명 합니다.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>|  기본적으로 서버 쪽 암호화 미사용 예입니다. | 고객 관리 키 및 BYOK 아직 지원 되지 않습니다. 클라이언트 쪽 암호화는 클라이언트의 책임 |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>VNet 암호화</li><li>VNet 간 암호화</ul>| 예 | 표준 HTTPS/TLS 메커니즘을 지원합니다. |
| 암호화 키 처리 (예: CMK, BYOK)| 아닙니다. |   |
| 열 수준 암호화 (Azure Data Services)| N/A | |
| API 호출 암호화| 예 | API 호출을 통해 이루어집니다 [Azure Resource Manager](../azure-resource-manager/index.yml) 및 HTTPS입니다. |

## <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 끝점 지원| 예 (프리미엄 계층에만 해당) | VNet 서비스 끝점에 대해 지원 됩니다 [Service Bus 프리미엄 계층](service-bus-premium-messaging.md) 만 합니다. |
| VNet 삽입 지원| 아닙니다. | |
| 네트워크 격리 및 방화벽 지원| 예 (프리미엄 계층에만 해당) |  |
| 강제 터널링 지원| 아닙니다. |  |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | 통해 지원 [Azure Monitor 및 경고](service-bus-metrics-azure-monitor.md)합니다. |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예 | 통해 관리 되는 [Azure Active Directory 관리 서비스 Id](service-bus-managed-service-identity.md); 참조 [Service Bus 인증 및 권한 부여](service-bus-authentication-and-authorization.md)합니다.|
| 권한 부여| 예 | 통해 권한 부여를 지 원하는 [RBAC](service-bus-role-based-access-control.md) (미리 보기) 및 SAS 토큰을 참조 하세요 [Service Bus 인증 및 권한 부여](service-bus-authentication-and-authorization.md)합니다. |



## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | 작업 로그를 사용할 수 있습니다. 참조 [Service Bus 진단 로그](service-bus-diagnostic-logs.md)합니다.  |
| 데이터 평면 로깅 및 감사| 아닙니다. |  |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 예 | 통해 리소스 공급자의 버전 관리를 지원 합니다 [Azure Resource Manager API](/rest/api/resources/)합니다.|
