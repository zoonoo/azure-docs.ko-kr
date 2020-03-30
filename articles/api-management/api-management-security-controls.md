---
title: Azure API 관리를 위한 보안 제어
description: API 관리 평가를 위한 보안 제어 검사 목록
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: 670050efe01fb658fab52a43914f193e9798b828
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751130"
---
# <a name="security-controls-for-api-management"></a>API 관리를 위한 보안 제어

이 문서에서는 API 관리에 기본제공된 보안 컨트롤에 대해 간증권을 간증권으로 작성합니다.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 메모 | 설명서 |
|---|---|--|--|
| 서비스 엔드포인트 지원| 예 | |  |
| VNet 주입 지원| yes | |  |
| 네트워크 격리 및 방화벽 지원| yes | NSG(네트워킹 보안 그룹) 및 Azure 응용 프로그램 게이트웨이(또는 기타 소프트웨어 어플라이언스)를 각각 사용합니다. |  |
| 강제 터널링 지원| yes | Azure 네트워킹은 강제 터널링을 제공합니다. |  |

## <a name="monitoring--logging"></a>모니터링 & 로깅

| 보안 제어 | 예/아니요 | 메모| 설명서 |
|---|---|--|--|
| Azure 모니터링 지원(로그 분석, 앱 인사이트 등)| yes | | |
| 제어 및 관리 평면 로깅 및 감사| yes | [Azure 모니터 활동 로그](../azure-monitor/platform/platform-logs-overview.md) | |
| 데이터 평면 로깅 및 감사| yes | [Azure 모니터 진단 로그](../azure-monitor/platform/platform-logs-overview.md) 및 (선택적으로) [Azure 응용 프로그램 인사이트](../azure-monitor/app/app-insights-overview.md).  | |


## <a name="identity"></a>Identity

| 보안 제어 | 예/아니요 | 메모| 설명서 |
|---|---|--|--|
| 인증| yes | |  |
| 권한 부여| yes | |  |

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 메모 | 설명서 |
|---|---|--|--|
| 미사용 서버 측 암호화: Microsoft에서 관리하는 키 | yes | 인증서, 키 및 비밀 이름 값과 같은 중요한 데이터는 서비스 인스턴스 키에 따라 서비스 관리로 암호화됩니다. |  |
| 미사용 서버 측 암호화: 고객 관리 키(BYOK) | 예 | 모든 암호화 키는 서비스 인스턴스에 따라 관리되며 서비스 관리됩니다. |  |
| 열 수준 암호화(Azure 데이터 서비스)| 해당 없음 | |  |
| 전송 중 암호화(예: 익스프레스루트 암호화, VNet 암호화 및 VNet-VNet 암호화)| yes | [익스프레스 경로](../expressroute/index.yml) 및 VNet 암호화는 [Azure 네트워킹에서](../virtual-network/index.yml)제공됩니다. |  |
| API 호출 암호화| yes | 관리 평면 호출은 TLS를 통해 [Azure 리소스 관리자를](../azure-resource-manager/index.yml) 통해 이루어집니다. 유효한 JSON 웹 토큰(JWT)이 필요합니다.  데이터 평면 호출은 TLS 및 지원되는 인증 메커니즘(예: 클라이언트 인증서 또는 JWT) 중 하나로 보호할 수 있습니다. |   |
 |

## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 메모| 설명서 |
|---|---|--|--|
| 구성 관리 지원(구성 버전 관리 등)| yes | Azure [API 관리 데브옵스 리소스 키트](https://aka.ms/apimdevops) 사용 |  |

## <a name="vulnerability-scans-false-positives"></a>취약점이 거짓 긍정을 검사합니다.

이 섹션에서는 Azure API 관리에 영향을 주지 않는 일반적인 취약점을 간역합니다.

| 취약점               | 설명                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 티켓블리드 (CVE-2016-9244) | Ticketbleed는 일부 F5 제품에서 발견되는 TLS SessionTicket 확장의 구현에 취약점입니다. 초기화되지 않은 메모리에서 최대 31바이트의 데이터가 누수("출혈")할 수 있습니다. 이는 클라이언트에서 전달된 세션 ID를 32비트 길이로 만드는 TLS 스택 패딩으로 인해 발생합니다. |

## <a name="next-steps"></a>다음 단계

- Azure 서비스 [에서 기본 제공 보안 컨트롤에](../security/fundamentals/security-controls.md)대해 자세히 알아봅니다.