---
title: Azure Event Hubs에 대 한 보안 제어
description: Azure Event Hubs 평가를 위한 보안 컨트롤의 검사 목록
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 277a745d26961ed509258d5423fc3c0da9b79a24
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219401"
---
# <a name="security-controls-for-azure-event-hubs"></a>Azure Event Hubs에 대 한 보안 제어

이 문서에서는 Azure Event Hubs에 기본 제공 되는 보안 컨트롤을 설명 합니다.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 참고 | 설명서 |
|---|---|--|--|
| 서비스 엔드포인트 지원| 예 |  |  |
| VNet 삽입 지원| 아니요 | |  |
| 네트워크 격리 및 방화벽 지원| 예 |  |  |
| 강제 터널링 지원| 아니요 |  |  |

## <a name="monitoring--logging"></a>& 로깅 모니터링

| 보안 제어 | 예/아니요 | 참고| 설명서 |
|---|---|--|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | |  |
| 제어 및 관리 평면 로깅 및 감사| 예 |  |  |
| 데이터 평면 로깅 및 감사| 예 |   |  |

## <a name="identity"></a>ID

| 보안 제어 | 예/아니요 | 참고| 설명서 |
|---|---|--|--|
| 인증| 예 | | [Azure Event Hubs에](authorize-access-event-hubs.md)대 한 액세스 권한을 부여 하 고, [Azure Active Directory를 사용 하 여 Event Hubs 리소스에](authorize-access-azure-active-directory.md)대 한 액세스 권한을 부여 하 고, [공유 액세스 서명을 사용 하 여 Event Hubs](authorize-access-shared-access-signature.md) |
| Authorization|  예 | | [Azure Active Directory를 사용 하 여 관리 되는 id를 인증](authenticate-managed-identity.md)하 고, Event Hubs 리소스에 액세스 하 고, Azure Active Directory를 사용 하 여 [응용 프로그램을 인증 하 Event Hubs 리소스에 액세스](authenticate-application.md)하 Event Hubs 고, [ 공유 액세스 서명 (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 참고 | 설명서 |
|---|---|--|--|
| 미사용 서버 쪽 암호화: Microsoft에서 관리 하는 키 |  예 | |  |
| 미사용 서버 쪽 암호화: 고객 관리 키 (BYOK) | 아니요 |  |  |
| 열 수준 암호화 (Azure Data Services)| 해당 사항 없음 | |  |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | |  |
| API 호출 암호화| 예 |  |  |

## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 참고| 설명서 |
|---|---|--|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 | |  |

## <a name="next-steps"></a>다음 단계

- [Azure 서비스에서 기본 제공 되는 보안 컨트롤](../security/fundamentals/security-controls.md)에 대해 자세히 알아보세요.
