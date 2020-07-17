---
title: Azure Relay에 대한 보안 컨트롤
description: 이 문서에서는 Azure Relay를 평가하기 위한 기본 제공 보안 컨트롤의 검사 목록을 제공합니다.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 64abee031bb20e2bdb10bf1cc3cd77e135713550
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85316624"
---
# <a name="security-controls-for-azure-relay"></a>Azure Relay에 대한 보안 컨트롤

이 문서에서는 Azure Relay에 기본 제공되는 보안 컨트롤을 설명합니다.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 컨트롤 | 예/아니요 | 메모 | 문서화 |
|---|---|--|--|
| 서비스 엔드포인트 지원| 예 |  |   |
| 네트워크 격리 및 방화벽 지원| 예 |  |   |
| 강제 터널링 지원| 해당 없음 | 릴레이가 TLS 터널입니다.  |   |

## <a name="monitoring--logging"></a>모니터링 및 로깅

| 보안 컨트롤 | 예/아니요 | 메모| 문서화 |
|---|---|--|--|
| Azure 모니터링 지원(Log Analytics, App insights 등)| 예 | |   |
| 제어와 관리 평면 로깅 및 감사| 예 | [Azure Resource Manager](../azure-resource-manager/index.yml)를 통해. |   |
| 데이터 평면 로깅 및 감사| 예 | 연결 성공 / 실패, 오류 및 기록.  |   |

## <a name="identity"></a>ID

| 보안 컨트롤 | 예/아니요 | 메모| 문서화 |
|---|---|--|--|
| 인증| 예 | SAS를 통해. | [Azure Relay 인증 및 권한 부여](relay-authentication-and-authorization.md) |
| 권한 부여|  예 | SAS를 통해. | [Azure Relay 인증 및 권한 부여](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>데이터 보호

| 보안 컨트롤 | 예/아니요 | 메모 | 문서화 |
|---|---|--|--|
| 미사용 서버 쪽 암호화: Microsoft 관리형 키 |  해당 없음 | 릴레이는 웹 소켓이며 데이터를 유지하지 않습니다. |   |
| 미사용 서버 쪽 암호화: 고객 관리형 키(BYOK) | 예 | Microsoft TLS 인증서만 사용합니다.  |   |
| 열 수준 암호화(Azure Data Services)| 해당 없음 | |   |
| 전송 중 암호화(예: ExpressRoute 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | 서비스에는 TLS가 필요합니다. |   |
| API 호출 암호화| 예 | HTTPS. |


## <a name="configuration-management"></a>구성 관리

| 보안 컨트롤 | 예/아니요 | 메모| 문서화 |
|---|---|--|--|
| 구성 관리 지원(구성 버전 관리 등)| 예 | [Azure Resource Manager](../azure-resource-manager/index.yml)를 통해.|   |

## <a name="next-steps"></a>다음 단계

- [Azure 서비스의 기본 제공 보안 컨트롤](../security/fundamentals/security-controls.md)에 대해 자세히 알아봅니다.