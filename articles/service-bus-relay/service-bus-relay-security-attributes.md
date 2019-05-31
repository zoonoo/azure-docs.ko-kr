---
title: Azure Service Bus Relay에 대 한 일반적인 보안 특성
description: Azure Service Bus Relay를 평가 하기 위한 일반적인 보안 특성에 대 한 검사 목록
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: d8ce3c995e8e0f20ed6d694f481cc5fc9fde4fa7
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000159"
---
# <a name="security-attributes-for-azure-service-bus-relay"></a>Azure Service Bus Relay에 대 한 보안 특성

이 문서에서는 Azure Service Bus Relay에 기본 제공 보안 특성을 설명 합니다.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>|  N/A | 릴레이 웹 소켓을 이며 데이터는 유지 되지 않습니다. |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>VNet 암호화</li><li>VNet 간 암호화</ul>| 예. | 서비스에서는 TLS 합니다. |
| 암호화 키 처리 (예: CMK, BYOK)| 아닙니다. | Microsoft TLS 인증서만 사용합니다.  |
| 열 수준 암호화 (Azure Data Services)| N/A | |
| API 호출 암호화| 예. | HTTPS입니다. |

## <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 끝점 지원| 아닙니다. |  |
| 네트워크 격리 및 방화벽 지원| 아닙니다. |  |
| 강제 터널링 지원| N/A | 릴레이 TLS 터널  |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예. | |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예. | 통해 SAS입니다. |
| 권한 부여|  예. | 통해 SAS입니다. |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예. | 통해 [Azure Resource Manager](../azure-resource-manager/index.yml)합니다. |
| 데이터 평면 로깅 및 감사| 예. | 연결 성공 / 실패 및 오류 기록 합니다.  |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 예. | 통해 [Azure Resource Manager](../azure-resource-manager/index.yml)합니다.|
