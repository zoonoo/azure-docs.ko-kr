---
title: Azure Express 경로에 대 한 보안 제어
description: Azure Express 경로 평가를 위한 보안 컨트롤의 검사 목록
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ab73ba6dd4b78d3cd0be5f4c7f7a502e5c58e08
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886618"
---
# <a name="security-controls-for-azure-expressroute"></a>Azure Express 경로에 대 한 보안 제어

이 문서에서는 Azure Express 경로에 기본 제공 되는 보안 컨트롤을 설명 합니다.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| N/A |  |
| VNet 삽입 지원| 해당 사항 없음 | |
| 네트워크 격리 및 방화벽 지원| 예 | 각 고객은 자체 라우팅 도메인에 포함 되 고 자체 VNet으로 터널링 됩니다. |
| 강제 터널링 지원| 해당 사항 없음 | Via Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>& 로깅 모니터링

| 보안 제어 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | [Express 경로 모니터링, 메트릭 및 경고](expressroute-monitoring-metrics-alerts.md)를 참조 하세요.|
| 제어 및 관리 평면 로깅 및 감사| 예 |  |
| 데이터 평면 로깅 및 감사| 아니요 |   |

## <a name="identity"></a>클레임

| 보안 제어 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | Microsoft 용 게이트웨이 서비스 계정 (GWM) (컨트롤러) Dev 및 OP에 대 한 JIT (just-in-time) 액세스 |
| Authorization|  예 |Microsoft 용 게이트웨이 서비스 계정 (GWM) (컨트롤러) Dev 및 OP에 대 한 JIT (just-in-time) 액세스 |

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 서버 쪽 암호화: Microsoft에서 관리 하는 키 |  N/A | Express 경로는 고객 데이터를 저장 하지 않습니다. |
| 미사용 서버 쪽 암호화: 고객 관리 키 (BYOK) | N/A |  |
| 열 수준 암호화 (Azure Data Services)| 해당 사항 없음 | |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| 아니요 | |
| API 호출 암호화| 예 | [Azure Resource Manager](../azure-resource-manager/index.yml) 및 HTTPS를 통해 |


## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 | 네트워크 리소스 공급자 (NRP)를 통해. |

## <a name="next-steps"></a>다음 단계

- [Azure 서비스에서 기본 제공 되는 보안 컨트롤](../security/fundamentals/security-controls.md)에 대해 자세히 알아보세요.