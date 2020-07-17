---
title: Azure Load Balancer에 대 한 보안 제어
description: Load Balancer 평가를 위한 보안 컨트롤의 검사 목록
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "74214893"
---
# <a name="security-controls-for-azure-load-balancer"></a>Azure Load Balancer에 대 한 보안 제어

이 문서에서는 Azure Load Balancer에 기본 제공 되는 보안 컨트롤을 설명 합니다.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 컨트롤 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| 해당 없음 | |
| VNet 삽입 지원| 해당 없음 | |
| 네트워크 격리 및 방화벽 지원| 해당 없음 |  |
| 강제 터널링 지원| 해당 없음 | |

## <a name="monitoring--logging"></a>모니터링 및 로깅

| 보안 컨트롤 | 예/아니요 | 메모|
|---|---|--|
| Azure 모니터링 지원(Log Analytics, App insights 등)| 예 | [Azure Monitor logs for Public Basic Load Balancer을](load-balancer-monitor-log.md)참조 하세요. |
| 제어와 관리 평면 로깅 및 감사| 예 | [Azure Monitor logs for Public Basic Load Balancer을](load-balancer-monitor-log.md)참조 하세요. |
| 데이터 평면 로깅 및 감사 | 해당 없음 |  |

## <a name="identity"></a>ID

| 보안 컨트롤 | 예/아니요 | 메모|
|---|---|--|
| 인증| 해당 없음 |  |
| 권한 부여| 해당 없음 |  |

## <a name="data-protection"></a>데이터 보호

| 보안 컨트롤 | 예/아니요 | 메모 |
|---|---|--|
| 미사용 서버 쪽 암호화: Microsoft 관리형 키 | 해당 없음 | |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| 해당 없음 | |
| 미사용 서버 쪽 암호화: 고객 관리형 키(BYOK) | 해당 없음 | |
| 열 수준 암호화(Azure Data Services)| 해당 없음 | |
| API 호출 암호화| 예 | [Azure Resource Manager](../azure-resource-manager/index.yml)를 통해. |

## <a name="configuration-management"></a>구성 관리

| 보안 컨트롤 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원(구성 버전 관리 등)| 해당 없음 |  | 

## <a name="next-steps"></a>다음 단계

- [Azure 서비스의 기본 제공 보안 컨트롤](../security/fundamentals/security-controls.md)에 대해 자세히 알아봅니다.