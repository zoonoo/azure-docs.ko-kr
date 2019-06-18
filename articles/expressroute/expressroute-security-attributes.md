---
title: Azure ExpressRoute에 대 한 일반적인 보안 특성
description: Azure ExpressRoute를 평가 하기 위한 일반적인 보안 특성에 대 한 검사 목록
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: d6156715fb87831d465197fd8eec59d245221e48
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083276"
---
# <a name="common-security-attributes-for-azure-expressroute"></a>Azure ExpressRoute에 대 한 일반적인 보안 특성

보안은 Azure 서비스의 모든 측면에 통합됩니다. 이 문서에서는 Azure ExpressRoute에 기본 제공 되는 일반적인 보안 특성을 설명 합니다.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>|  N/A | ExpressRoute 고객 데이터를 저장 하지 않습니다. |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>Vnet 내부 암호화</li><li>VNet 간 암호화</ul>| 아닙니다. | |
| 암호화 키 처리 (예: CMK, BYOK)| N/A |  |
| 열 수준 암호화 (Azure Data Services)| N/A | |
| API 호출 암호화| 예 | 통해 [Azure Resource Manager](../azure-resource-manager/index.yml) 및 HTTPS입니다. |

## <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 끝점 지원| N/A |  |
| vNET 삽입 지원| N/A | |
| 네트워크 격리 및 방화벽 지원| 예 | 각 고객은 자체 라우팅 도메인에 포함 된을 자체 VNet에 터널링 |
| 강제 터널링 지원| N/A | 통해 BGP (Border Gateway Protocol). |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | 참조 [ExpressRoute 모니터링, 메트릭 및 경고](expressroute-monitoring-metrics-alerts.md)합니다.|

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예 | Gateway에 대 한 Microsoft (GWM) (컨트롤러)에 대 한 서비스 계정 개발 및 운영에 대 한 just-in-time (JIT)에 액세스 |
| 권한 부여|  예 |Gateway에 대 한 Microsoft (GWM) (컨트롤러)에 대 한 서비스 계정 개발 및 운영에 대 한 just-in-time (JIT)에 액세스 |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모| 
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 |  |
| 데이터 평면 로깅 및 감사| 아닙니다. |   |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 예 | 통해 네트워크 리소스 공급자 (NRP)입니다. |
