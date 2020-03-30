---
title: 'Azure 익스프레스루트: 보안 제어'
description: Azure ExpressRoute 를 평가하기 위한 보안 컨트롤검사 목록
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: a288b44c07bc2df8529f07264dcee648f3af379a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74079924"
---
# <a name="security-controls-for-azure-expressroute"></a>Azure 익스프레스루트에 대한 보안 제어

이 문서에서는 Azure ExpressRoute에 기본 제공 된 보안 컨트롤을 문서화 합니다.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| 해당 없음 |  |
| VNet 주입 지원| 해당 없음 | |
| 네트워크 격리 및 방화벽 지원| yes | 각 고객은 자체 라우팅 도메인에 포함되며 자체 VNet으로 터널로 이동됩니다. |
| 강제 터널링 지원| 해당 없음 | 국경 게이트웨이 프로토콜 (BGP)을 통해. |

## <a name="monitoring--logging"></a>모니터링 & 로깅

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| Azure 모니터링 지원(로그 분석, 앱 인사이트 등)| yes | [ExpressRoute 모니터링, 메트릭 및 경고를](expressroute-monitoring-metrics-alerts.md)참조하십시오.|
| 제어 및 관리 평면 로깅 및 감사| yes |  |
| 데이터 평면 로깅 및 감사| 예 |   |

## <a name="identity"></a>Identity

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 인증| yes | 마이크로소프트 (GWM)에 대 한 게이트웨이에 대 한 서비스 계정 (컨트롤러); 그냥 시간에 (JIT) 개발 및 OP에 대한 액세스. |
| 권한 부여|  yes |마이크로소프트 (GWM)에 대 한 게이트웨이에 대 한 서비스 계정 (컨트롤러); 그냥 시간에 (JIT) 개발 및 OP에 대한 액세스. |

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 미사용 서버 측 암호화: Microsoft에서 관리하는 키 |  해당 없음 | ExpressRoute는 고객 데이터를 저장하지 않습니다. |
| 미사용 서버 측 암호화: 고객 관리 키(BYOK) | 해당 없음 |  |
| 열 수준 암호화(Azure 데이터 서비스)| 해당 없음 | |
| 전송 중 암호화(예: 익스프레스루트 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | |
| API 호출 암호화| yes | Azure 리소스 관리자 및 [HTTPS를](../azure-resource-manager/index.yml) 통해. |


## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원(구성 버전 관리 등)| yes | NRP(네트워크 리소스 공급자)를 통해. |

## <a name="next-steps"></a>다음 단계

- Azure 서비스 [에서 기본 제공 보안 컨트롤에](../security/fundamentals/security-controls.md)대해 자세히 알아봅니다.