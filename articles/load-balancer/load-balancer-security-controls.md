---
title: Azure 로드 밸러저에 대한 보안 제어
description: 로드 밸러서 를 평가하기 위한 보안 제어 검사 목록
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74214893"
---
# <a name="security-controls-for-azure-load-balancer"></a>Azure 로드 밸러저에 대한 보안 제어

이 문서에서는 Azure 로드 밸러터에 기본제공된 보안 컨트롤에 대해 간증권을 간역합니다.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| 해당 없음 | |
| VNet 주입 지원| 해당 없음 | |
| 네트워크 격리 및 방화벽 지원| 해당 없음 |  |
| 강제 터널링 지원| 해당 없음 | |

## <a name="monitoring--logging"></a>모니터링 & 로깅

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| Azure 모니터링 지원(로그 분석, 앱 인사이트 등)| yes | [공용 기본 로드 밸런서에 대한 Azure 모니터 로그를](load-balancer-monitor-log.md)참조하십시오. |
| 제어 및 관리 평면 로깅 및 감사| yes | [공용 기본 로드 밸런서에 대한 Azure 모니터 로그를](load-balancer-monitor-log.md)참조하십시오. |
| 데이터 평면 로깅 및 감사 | 해당 없음 |  |

## <a name="identity"></a>Identity

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 인증| 해당 없음 |  |
| 권한 부여| 해당 없음 |  |

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 미사용 서버 측 암호화: Microsoft에서 관리하는 키 | 해당 없음 | |
| 전송 중 암호화(예: 익스프레스라우팅 암호화, VNet 암호화 및 VNet-VNet 암호화)| 해당 없음 | |
| 미사용 서버 측 암호화: 고객 관리 키(BYOK) | 해당 없음 | |
| 열 수준 암호화(Azure 데이터 서비스)| 해당 없음 | |
| API 호출 암호화| yes | Azure [리소스 관리자를](../azure-resource-manager/index.yml)통해 . |

## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원(구성 버전 관리 등)| 해당 없음 |  | 

## <a name="next-steps"></a>다음 단계

- Azure 서비스 [에서 기본 제공 보안 컨트롤에](../security/fundamentals/security-controls.md)대해 자세히 알아봅니다.