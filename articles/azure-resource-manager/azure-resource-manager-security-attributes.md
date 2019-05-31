---
title: 일반적인 보안 특성에 대 한 Azure Resource Manager
description: Azure Resource Manager를 평가 하기 위한 일반적인 보안 특성에 대 한 검사 목록
services: azure-resource-manager
author: msmbaldwin
manager: barbkess
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mbaldwin
ms.openlocfilehash: a771d4c2ae22b7bf149c13c80fe5286ef52a4545
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002267"
---
# <a name="security-attributes-for-azure-resource-manager"></a>Azure Resource Manager에 대 한 보안 특성

이 문서에서는 Azure Resource Manager에 기본 제공 보안 특성을 설명 합니다.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>| 예. |  |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>VNet 암호화</li><li>VNet 간 암호화</ul>| 예. | HTTPS/TLS. |
| 암호화 키 처리 (예: CMK, BYOK)| N/A | Azure Resource Manager 고객 콘텐츠 없음만 컨트롤 데이터를 저장합니다. |
| 열 수준 암호화 (Azure Data Services)| 예. | |
| API 호출 암호화| 예. | |

## <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 끝점 지원| 아닙니다. | |
| VNet 삽입 지원| 예. | |
| 네트워크 격리 및 방화벽 지원| 아닙니다. |  |
| 강제 터널링 지원| 아닙니다. |  |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 아닙니다. | |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예. | [Azure Active Directory](/azure/active-directory) 기반 합니다.|
| 권한 부여| 예. | |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예. | 활동 로그에 리소스에서 수행 된 작업 (PUT, POST, DELETE)를 작성 하는 모든 노출 참조 [리소스에 대 한 작업을 감사 하려면 활동 로그 보기](resource-group-audit.md)합니다. |
| 데이터 평면 로깅 및 감사| N/A | |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 예. |  |
