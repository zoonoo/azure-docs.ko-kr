---
title: Azure Service Bus 메시징의 보안 특성
description: Azure Service Bus 메시지를 평가 하기 위한 보안 특성의 검사 목록
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: 0e1d6e041b47a261b549fb8b608cf09d0d6362dd
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443892"
---
# <a name="security-attributes-for-azure-service-bus-messaging"></a>Azure Service Bus 메시징의 보안 특성

이 문서에서는 Azure Service Bus 메시징에 기본 제공 되는 보안 특성을 설명 합니다.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능)|  기본적으로 서버 쪽 암호화에 대 한 예입니다. | 고객 관리 키 및 BYOK는 아직 지원 되지 않습니다. 클라이언트 쪽 암호화는 클라이언트의 책임입니다. |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | 표준 HTTPS/TLS 메커니즘을 지원 합니다. |
| 암호화 키 처리 (CMK, BYOK 등)| 아니요 |   |
| 열 수준 암호화 (Azure Data Services)| 해당 사항 없음 | |
| API 호출 암호화| 예 | API 호출은 [Azure Resource Manager](../azure-resource-manager/index.yml) 및 HTTPS를 통해 수행 됩니다. |

## <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 (프리미엄 계층만) | VNet 서비스 끝점은 [Service Bus 프리미엄 계층](service-bus-premium-messaging.md) 에 대해서만 지원 됩니다. |
| VNet 삽입 지원| 아니요 | |
| 네트워크 격리 및 방화벽 지원| 예 (프리미엄 계층만) |  |
| 강제 터널링 지원| 아니요 |  |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | [Azure Monitor 및 경고를](service-bus-metrics-azure-monitor.md)통해 지원 됩니다. |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | [Azure Active Directory 관리 서비스 ID](service-bus-managed-service-identity.md)를 통해 관리 [Service Bus 인증 및 권한 부여를](service-bus-authentication-and-authorization.md)참조 하세요.|
| Authorization| 예 | [RBAC](service-bus-role-based-access-control.md) (미리 보기) 및 SAS 토큰을 통해 권한 부여를 지원 합니다. [Service Bus 인증 및 권한 부여를](service-bus-authentication-and-authorization.md)참조 하세요. |



## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | 작업 로그를 사용할 수 있습니다. [Service Bus 진단 로그](service-bus-diagnostic-logs.md)를 참조 하세요.  |
| 데이터 평면 로깅 및 감사| 아니요 |  |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 | [AZURE RESOURCE MANAGER API](/rest/api/resources/)를 통해 리소스 공급자 버전 관리를 지원 합니다.|
