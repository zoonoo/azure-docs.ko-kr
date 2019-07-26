---
title: Azure API Management에 대 한 보안 특성
description: API Management 평가를 위한 보안 특성의 검사 목록
services: api-management
author: msmbaldwin
manager: barbkess
ms.service: api-management
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 2049e2349e3a25ebd4d3f4db19ec47bbaeb067de
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442275"
---
# <a name="security-attributes-for-api-management"></a>API Management에 대 한 보안 특성

이 문서에서는 API Management에 기본 제공 되는 보안 특성을 설명 합니다.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능)| 예 (서비스 쪽 암호화에만 해당) | 인증서, 키 및 비밀 이름이 지정 된 값과 같은 중요 한 데이터는 서비스 인스턴스 키 당 서비스 관리로 암호화 됩니다. |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | [Express 경로](../expressroute/index.yml) 및 VNet 암호화는 [Azure 네트워킹](../virtual-network/index.yml)에서 제공 됩니다. |
| 암호화 키 처리 (CMK, BYOK 등)| 아니요 | 모든 암호화 키는 서비스 인스턴스당 서비스 관리 됩니다. |
| 열 수준 암호화 (Azure Data Services)| 해당 사항 없음 | |
| API 호출 암호화| 예 | 관리 평면 호출은 TLS를 통해 [Azure Resource Manager](../azure-resource-manager/index.yml) 를 통해 수행 됩니다. 유효한 JSON 웹 토큰(JWT)이 필요합니다.  데이터 평면 호출은 TLS를 사용 하 고 지원 되는 인증 메커니즘 (예: 클라이언트 인증서 또는 JWT) 중 하나를 사용 하 여 보안을 설정할 수 있습니다.
 |

## <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 아니요 | |
| VNet 삽입 지원| 예 | |
| 네트워크 격리 및 방화벽 지원| 예 | NSG (네트워킹 보안 그룹) 및 Azure 애플리케이션 게이트웨이 (또는 다른 소프트웨어 어플라이언스)를 각각 사용 합니다. |
| 강제 터널링 지원| 예 | Azure 네트워킹은 강제 터널링을 제공합니다. |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | |
| Authorization| 예 | |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | [활동 로그 Azure Monitor](../azure-monitor/platform/activity-logs-overview.md) |
| 데이터 평면 로깅 및 감사| 예 | [진단 로그](../azure-monitor/platform/diagnostic-logs-overview.md) 및 (선택 사항) [정보](../azure-monitor/app/app-insights-overview.md)를 Azure Monitor Azure 애플리케이션 합니다.  |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 | [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) 사용 |

## <a name="vulnerability-scans-false-positives"></a>취약성 검색 거짓 긍정

이 섹션에서는 Azure API Management에 영향을 주지 않는 일반적인 취약성에 대해 설명 합니다.

| 취약점               | 설명                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed는 일부 F5 제품에서 발견 된 TLS SessionTicket 확장의 구현에 대 한 취약성입니다. 초기화 되지 않은 메모리에서 최대 31 바이트의 데이터를 누출 ("bleeding") 할 수 있습니다. 이는 TLS 스택이 클라이언트에서 전달 된 세션 ID를 32 비트 길이의 데이터로 전달 하기 때문에 발생 합니다. |
