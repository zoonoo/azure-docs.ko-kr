---
title: Azure Load Balancer에 대 한 보안 특성
description: 부하 분산 장치를 평가 하는 것에 대 한 보안 특성에 대 한 검사 목록
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 1892b918ed35221bc73d5070d5a73ecc359aa4c2
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800082"
---
# <a name="security-attributes-for-azure-load-balancer"></a>Azure Load Balancer에 대 한 보안 특성

이 문서에서는 Azure Load Balancer에 기본 제공 되는 일반적인 보안 특성을 설명 합니다.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| (예: 서버 쪽 암호화, 고객 관리 키를 사용 하 여 서버 쪽 암호화 및 기타 암호화 기능)는 미사용 데이터 암호화 | N/A | |
| (예: ExpressRoute 암호화 VNet 암호화 및 VNet 대 VNet 암호화에서) 전송 중 암호화| 해당 사항 없음 | |
| 암호화 키 처리 (예: CMK, BYOK)| 해당 사항 없음 | |
| 열 수준 암호화 (Azure Data Services)| 해당 사항 없음 | |
| API 호출 암호화| 예 | 통해 합니다 [Azure Resource Manager](../azure-resource-manager/index.yml)합니다. |

## <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 끝점 지원| N/A | |
| VNet 삽입 지원| 해당 사항 없음 | 을 선택합니다. |
| 네트워크 격리 및 Firewalling 지원| N/A |  |
| 강제 터널링 지원| 해당 사항 없음 | |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | 참조 [공용 기본 부하 분산 장치에 대 한 Azure Monitor 로그](load-balancer-monitor-log.md)합니다. |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| N/A |  |
| Authorization| 해당 사항 없음 |  |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | 참조 [공용 기본 부하 분산 장치에 대 한 Azure Monitor 로그](load-balancer-monitor-log.md)합니다. |
| 데이터 평면 로깅 및 감사 | 해당 사항 없음 |  |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 해당 사항 없음 |  | 
