---
title: Azure 키 자격 증명 모음에 대한 보안 제어
description: Azure 키 자격 증명 모음을 평가하기 위한 보안 컨트롤검사 목록
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: cd6602f68b63e2c236e7f3905d33b88fbda36ed2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429864"
---
# <a name="security-controls-for-azure-key-vault"></a>Azure 키 자격 증명 모음에 대한 보안 제어

이 문서에서는 Azure Key Vault에 기본 제공된 보안 컨트롤에 대해 간증권으로 작성합니다. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 | VNet(가상 네트워크) 서비스 끝점을 사용합니다. |
| VNet 주입 지원| 예 |  |
| 네트워크 격리 및 방화벽 지원| 예 | VNet 방화벽 규칙을 사용. |
| 강제 터널링 지원| 예 |  |

## <a name="monitoring--logging"></a>모니터링 & 로깅

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| Azure 모니터링 지원(로그 분석, 앱 인사이트 등)| 예 | Log Analytics를 사용합니다. |
| 컨트롤/관리 평면 로깅 및 감사| 예 | Log Analytics를 사용합니다. |
| 데이터 평면 로깅 및 감사| 예 | Log Analytics를 사용합니다. |

## <a name="identity"></a>ID

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 인증| 예 | 인증은 Azure Active Directory를 통해 수행됩니다. |
| 권한 부여| 예 | Key Vault 액세스 정책을 사용합니다. |

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 미사용 서버 측 암호화: Microsoft에서 관리하는 키 | 예 | 모든 개체가 암호화됩니다. |
| 미사용 서버 측 암호화: 고객 관리 키(BYOK) | 예 | 고객은 키 자격 증명 모음의 모든 키를 제어합니다. HSM(하드웨어 보안 모듈) 백업 키가 지정되면 FIPS 수준 2 HSM이 키, 인증서 또는 비밀을 보호합니다. |
| 열 수준 암호화(Azure 데이터 서비스)| 해당 없음 |  |
| 전송 중 암호화(예: 익스프레스루트 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | 모든 통신은 암호화된 API 호출을 통해 수행됩니다. |
| API 호출 암호화| 예 | HTTPS를 사용합니다. |

## <a name="access-controls"></a>액세스 제어

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 컨트롤/관리 평면 액세스 컨트롤 | 예 | Azure Resource Manager 역할 기반 Access Control(RBAC) |
| 데이터 평면 액세스 컨트롤(모든 서비스 수준에서) | 예 | Key Vault 액세스 정책 |

## <a name="next-steps"></a>다음 단계

- Azure 서비스 [에서 기본 제공 보안 컨트롤에](../../security/fundamentals/security-controls.md)대해 자세히 알아봅니다.