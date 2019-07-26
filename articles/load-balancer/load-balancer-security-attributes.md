---
title: Azure Load Balancer에 대 한 보안 특성
description: Load Balancer 평가를 위한 보안 특성의 검사 목록
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 7d40618d5f4fde4a2b3fdfbde8a6de0a049ce3b6
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68440894"
---
# <a name="security-attributes-for-azure-load-balancer"></a>Azure Load Balancer에 대 한 보안 특성

이 문서에서는 Azure Load Balancer에 기본 제공 되는 보안 특성을 설명 합니다.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능) | 해당 사항 없음 | |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| N/A | |
| 암호화 키 처리 (CMK, BYOK 등)| 해당 사항 없음 | |
| 열 수준 암호화 (Azure Data Services)| N/A | |
| API 호출 암호화| 예 | [Azure Resource Manager](../azure-resource-manager/index.yml)를 통해. |

## <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| N/A | |
| VNet 삽입 지원| 해당 사항 없음 | 을 선택합니다. |
| 네트워크 격리 및 방화벽 지원| N/A |  |
| 강제 터널링 지원| 해당 사항 없음 | |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | [Azure Monitor logs for Public Basic Load Balancer을](load-balancer-monitor-log.md)참조 하세요. |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| N/A |  |
| Authorization| N/A |  |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | [Azure Monitor logs for Public Basic Load Balancer을](load-balancer-monitor-log.md)참조 하세요. |
| 데이터 평면 로깅 및 감사 | 해당 사항 없음 |  |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| N/A |  | 
