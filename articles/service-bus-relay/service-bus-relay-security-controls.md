---
title: Azure 서비스 버스 릴레이에 대한 보안 제어
description: 이 문서에서는 Azure Service Bus 릴레이를 평가하기 위한 기본 제공 보안 컨트롤의 검사 목록을 제공합니다.
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 28d3ba14aa7769ac4f3fc22bd2b5bd7acd30557c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514020"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Azure 서비스 버스 릴레이에 대한 보안 제어

이 문서에서는 Azure Service Bus 릴레이에 기본 제공 된 보안 컨트롤을 문서화 합니다.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 메모 | 설명서 |
|---|---|--|--|
| 서비스 엔드포인트 지원| 예 |  |   |
| 네트워크 격리 및 방화벽 지원| 예 |  |   |
| 강제 터널링 지원| 해당 없음 | 릴레이는 TLS 터널입니다.  |   |

## <a name="monitoring--logging"></a>모니터링 & 로깅

| 보안 제어 | 예/아니요 | 메모| 설명서 |
|---|---|--|--|
| Azure 모니터링 지원(로그 분석, 앱 인사이트 등)| yes | |   |
| 제어 및 관리 평면 로깅 및 감사| yes | [Azure 리소스 관리자를](../azure-resource-manager/index.yml)통해 . |   |
| 데이터 평면 로깅 및 감사| yes | 연결 성공/실패 및 오류 및 기록.  |   |

## <a name="identity"></a>Identity

| 보안 제어 | 예/아니요 | 메모| 설명서 |
|---|---|--|--|
| 인증| yes | SAS를 통해. | [Azure Relay 인증 및 권한 부여](relay-authentication-and-authorization.md) |
| 권한 부여|  yes | SAS를 통해. | [Azure Relay 인증 및 권한 부여](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 메모 | 설명서 |
|---|---|--|--|
| 미사용 서버 측 암호화: Microsoft에서 관리하는 키 |  해당 없음 | 릴레이는 웹 소켓이며 데이터를 유지하지 않습니다. |   |
| 미사용 서버 측 암호화: 고객 관리 키(BYOK) | 예 | Microsoft TLS 인증서만 사용합니다.  |   |
| 열 수준 암호화(Azure 데이터 서비스)| 해당 없음 | |   |
| 전송 중 암호화(예: 익스프레스루트 암호화, VNet 암호화 및 VNet-VNet 암호화)| yes | 서비스에는 TLS가 필요합니다. |   |
| API 호출 암호화| yes | HTTPS. |


## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 메모| 설명서 |
|---|---|--|--|
| 구성 관리 지원(구성 버전 관리 등)| yes | [Azure 리소스 관리자를](../azure-resource-manager/index.yml)통해 .|   |

## <a name="next-steps"></a>다음 단계

- Azure 서비스 [에서 기본 제공 보안 컨트롤에](../security/fundamentals/security-controls.md)대해 자세히 알아봅니다.