---
title: Azure Resource Manager에 대 한 보안 특성
description: Azure Resource Manager 평가를 위한 보안 특성의 검사 목록
services: azure-resource-manager
author: msmbaldwin
manager: barbkess
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mbaldwin
ms.openlocfilehash: e3bfb79c54ff57adfa947f2dd0100f6c05c7af9f
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444145"
---
# <a name="security-attributes-for-azure-resource-manager"></a>Azure Resource Manager에 대 한 보안 특성

이 문서에서는 Azure Resource Manager에 기본 제공 되는 보안 특성을 설명 합니다.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능)| 예 |  |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | HTTPS/TLS. |
| 암호화 키 처리 (CMK, BYOK 등)| N/A | Azure Resource Manager는 고객 콘텐츠를 저장 하지 않고 데이터를 제어 합니다. |
| 열 수준 암호화 (Azure Data Services)| 예 | |
| API 호출 암호화| 예 | |

## <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 아니요 | |
| VNet 삽입 지원| 예 | |
| 네트워크 격리 및 방화벽 지원| 아니요 |  |
| 강제 터널링 지원| 아니요 |  |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 아니요 | |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | [Azure Active Directory](/azure/active-directory) 기반.|
| Authorization| 예 | |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | 활동 로그는 리소스에서 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)을 노출 합니다. 작업 [로그 보기를 참조 하 여 리소스에 대 한 작업을 감사](resource-group-audit.md)합니다. |
| 데이터 평면 로깅 및 감사| 해당 사항 없음 | |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 |  |
