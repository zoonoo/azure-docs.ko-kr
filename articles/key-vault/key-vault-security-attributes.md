---
title: Azure Key Vault에 대 한 일반적인 보안 특성
description: Azure Key Vault를 평가하기 위한 일반적인 보안 특성 검사 목록
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 1c2265ff5f4c444121bf70c35145703f1b9fe981
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000190"
---
# <a name="security-attributes-for-azure-key-vault"></a>Azure Key Vault에 대 한 보안 특성

이 문서에서는 Azure Key Vault에 기본 제공 보안 특성을 설명 합니다. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>| 예. | 모든 개체가 암호화됩니다. |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>VNet 암호화</li><li>VNet 간 암호화</ul>| 예. | 모든 통신은 암호화된 API 호출을 통해 수행됩니다. |
| 암호화 키 처리 (예: CMK, BYOK)| 예. | 고객 키 자격 증명 모음에 있는 모든 키를 제어합니다. 하드웨어 보안 모듈 (HSM) 지원 키를 지정한 경우에 FIPS 수준 2 HSM 키, 인증서 또는 암호를 보호 합니다. |
| 열 수준 암호화 (Azure Data Services)| N/A |  |
| API 호출 암호화| 예. | HTTPS를 사용합니다. |

## <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 끝점 지원| 예. | 가상 네트워크 (VNet) 서비스 끝점을 사용합니다. |
| VNet 삽입 지원| 아닙니다. |  |
| 네트워크 격리 및 방화벽 지원| 예. | VNet 방화벽 규칙을 사용합니다. |
| 강제 터널링 지원| 아닙니다. |  |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예. | Log Analytics를 사용합니다. |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예. | 인증은 Azure Active Directory를 통해 수행됩니다. |
| 권한 부여| 예. | Key Vault 액세스 정책을 사용합니다. |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 컨트롤/관리 평면 로깅 및 감사| 예. | Log Analytics를 사용합니다. |
| 데이터 평면 로깅 및 감사| 예. | Log Analytics를 사용합니다. |

## <a name="access-controls"></a>액세스 제어

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 컨트롤/관리 평면 액세스 컨트롤 | 예. | Azure Resource Manager 역할 기반 Access Control(RBAC) |
| 데이터 평면 액세스 컨트롤(모든 서비스 수준에서) | 예. | Key Vault 액세스 정책 |