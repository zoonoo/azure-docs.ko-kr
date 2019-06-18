---
title: Azure API Management에 대 한 보안 특성
description: API Management를 평가 하기 위한 일반적인 보안 특성에 대 한 검사 목록
services: api-management
author: msmbaldwin
manager: barbkess
ms.service: api-management
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 3b5826d472b80179c5eb76e0e3a6b1c7ee282487
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66001085"
---
# <a name="security-attributes-for-api-management"></a>API Management에 대 한 보안 특성

이 문서에서는 API Management에 기본 제공 보안 특성을 설명 합니다.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>| 예 (서비스 쪽 암호화에만 해당) | 인증서, 키 및 비밀 이름의 값 같은 중요 한 데이터가 암호로 암호화는 서비스 관리, 서비스 인스턴스 키 당 합니다. |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>VNet 암호화</li><li>VNet 간 암호화</ul>| 예 | [Express 경로](../expressroute/index.yml) VNet 암호화를 제공 하 고 [Azure 네트워킹](../virtual-network/index.yml)합니다. |
| 암호화 키 처리 (예: CMK, BYOK)| 아닙니다. | 모든 암호화 키는 서비스 인스턴스당 되며 관리 되는 서비스입니다. |
| 열 수준 암호화 (Azure Data Services)| N/A | |
| API 호출 암호화| 예 | 관리 평면 호출을 통해 이루어집니다 [Azure Resource Manager](../azure-resource-manager/index.yml) tls 합니다. 유효한 JSON 웹 토큰(JWT)이 필요합니다.  TLS 및 지원 되는 인증 메커니즘 (예: 클라이언트 인증서 또는 JWT) 중 하나를 사용 하 여 데이터 평면 호출을 보호할 수 있습니다.
 |

## <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 끝점 지원| 아닙니다. | |
| VNet 삽입 지원| 예 | |
| 네트워크 격리 및 방화벽 지원| 예 | 네트워킹 보안 그룹 (NSG)을 사용 하 고 Azure Application Gateway (또는 다른 소프트웨어 어플라이언스) 각각. |
| 강제 터널링 지원| 예 | Azure 네트워킹은 강제 터널링을 제공합니다. |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예 | |
| 권한 부여| 예 | |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | [Azure Monitor 활동 로그](../azure-monitor/platform/activity-logs-overview.md) |
| 데이터 평면 로깅 및 감사| 예 | [Azure Monitor 진단 로그](../azure-monitor/platform/diagnostic-logs-overview.md) (선택 사항) 및 [Azure Application Insights](../azure-monitor/app/app-insights-overview.md)합니다.  |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 예 | 사용 하 여 [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) |

## <a name="vulnerability-scans-false-positives"></a>취약성 검사 거짓 긍정

이 섹션에서는 Azure API Management에는 영향을 주지 않는 일반적인 보안 문제를 설명 합니다.

| 취약점               | 설명                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed는 취약점으로 인 한 일부 F5 제품 있는 TLS SessionTicket 확장의 구현입니다. ("Bleeding") 최대 31 바이트의 초기화 되지 않은 메모리에서 데이터의 누출 수 있습니다. 이 32 비트 길이로 수 있도록 데이터를 사용 하 여 클라이언트에서 전달 된 세션 ID를 패딩 TLS 스택을 때문일 수 있습니다. |
