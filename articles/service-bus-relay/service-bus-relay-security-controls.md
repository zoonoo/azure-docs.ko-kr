---
title: Azure Service Bus 릴레이에 대 한 보안 제어
description: Azure Service Bus 릴레이를 평가 하기 위한 보안 컨트롤의 검사 목록
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 7f4e2a31673905a7e28d1dbb5520650aefc6f368
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219974"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Azure Service Bus 릴레이에 대 한 보안 제어

이 문서에서는 Azure Service Bus Relay에 기본 제공 되는 보안 컨트롤을 설명 합니다.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 참고 | 설명서 |
|---|---|--|--|
| 서비스 엔드포인트 지원| 아니요 |  |   |
| 네트워크 격리 및 방화벽 지원| 아니요 |  |   |
| 강제 터널링 지원| 해당 사항 없음 | 릴레이가 TLS 터널  |   |

## <a name="monitoring--logging"></a>& 로깅 모니터링

| 보안 제어 | 예/아니요 | 참고| 설명서 |
|---|---|--|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | |   |
| 제어 및 관리 평면 로깅 및 감사| 예 | [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| 데이터 평면 로깅 및 감사| 예 | 연결 성공/실패 및 오류 및 기록 됨  |   |

## <a name="identity"></a>ID

| 보안 제어 | 예/아니요 | 참고| 설명서 |
|---|---|--|--|
| 인증| 예 | SAS를 통해. | [Azure Relay 인증 및 권한 부여](relay-authentication-and-authorization.md) |
| Authorization|  예 | SAS를 통해. | [Azure Relay 인증 및 권한 부여](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 참고 | 설명서 |
|---|---|--|--|
| 미사용 서버 쪽 암호화: Microsoft에서 관리 하는 키 |  해당 사항 없음 | 릴레이는 웹 소켓 이며 데이터를 유지 하지 않습니다. |   |
| 미사용 서버 쪽 암호화: 고객 관리 키 (BYOK) | 아니요 | Microsoft TLS 인증서만 사용 합니다.  |   |
| 열 수준 암호화 (Azure Data Services)| 해당 사항 없음 | |   |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | 서비스에는 TLS가 필요 합니다. |   |
| API 호출 암호화| 예 | HTTP. |


## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 참고| 설명서 |
|---|---|--|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 | [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>다음 단계

- [Azure 서비스에서 기본 제공 되는 보안 컨트롤](../security/fundamentals/security-controls.md)에 대해 자세히 알아보세요.