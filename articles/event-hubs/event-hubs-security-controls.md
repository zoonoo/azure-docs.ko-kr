---
title: Azure Event Hubs에 대 한 보안 제어
description: 이 문서에서는 Azure Event Hubs (네트워크, id, 데이터 보호 등)를 평가 하기 위한 보안 컨트롤의 검사 목록을 제공 합니다.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: da20778f1e24372e445d635e675df6484905f195
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85315392"
---
# <a name="security-controls-for-azure-event-hubs"></a>Azure Event Hubs에 대 한 보안 제어

이 문서에서는 Azure Event Hubs에 기본 제공 되는 보안 컨트롤을 설명 합니다.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 컨트롤 | 예/아니요 | 메모 | 문서화 |
|---|---|--|--|
| 서비스 엔드포인트 지원| 예 |  |  |
| VNet 삽입 지원| 예 | |  |
| 네트워크 격리 및 방화벽 지원| 예 |  |  |
| 강제 터널링 지원| 아니요 |  |  |

## <a name="monitoring--logging"></a>모니터링 및 로깅

| 보안 컨트롤 | 예/아니요 | 메모| 문서화 |
|---|---|--|--|
| Azure 모니터링 지원(Log Analytics, App insights 등)| 예 | |  |
| 제어와 관리 평면 로깅 및 감사| 예 |  |  |
| 데이터 평면 로깅 및 감사| 예 |   |  |

## <a name="identity"></a>ID

| 보안 컨트롤 | 예/아니요 | 메모| 문서화 |
|---|---|--|--|
| 인증| 예 | | [Azure Event Hubs에](authorize-access-event-hubs.md)대 한 액세스 권한을 부여 하 고, [Azure Active Directory를 사용 하 여 Event Hubs 리소스에](authorize-access-azure-active-directory.md)대 한 액세스 권한을 부여 하 고, [공유 액세스 서명을 사용 하 여 Event Hubs](authorize-access-shared-access-signature.md) |
| 권한 부여|  예 | | [Event Hubs 리소스에 액세스](authenticate-managed-identity.md)하 고, Azure Active Directory를 사용 하 [여 응용 프로그램을 인증 하 여 Event Hubs 리소스에 액세스](authenticate-application.md)하 고, [SAS (공유 액세스 서명)를 사용 하 여 Event Hubs 리소스에 대 한 액세스 인증](authenticate-shared-access-signature.md) Azure Active Directory |

## <a name="data-protection"></a>데이터 보호

| 보안 컨트롤 | 예/아니요 | 메모 | 문서화 |
|---|---|--|--|
| 미사용 서버 쪽 암호화: Microsoft 관리형 키 |  예 | |  |
| 미사용 서버 쪽 암호화: 고객 관리형 키(BYOK) | 예. 전용 클러스터에 사용할 수 있습니다. | Azure KeyVault의 고객 관리 키를 사용 하 여 미사용 이벤트 허브의 데이터를 암호화할 수 있습니다. | [Azure Portal를 사용 하 여 미사용 Azure Event Hubs 데이터를 암호화 하기 위한 고객 관리 키 구성](configure-customer-managed-key.md) |
| 열 수준 암호화(Azure Data Services)| 해당 없음 | |  |
| 전송 중 암호화(예: ExpressRoute 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | |  |
| API 호출 암호화| 예 |  |  |

## <a name="configuration-management"></a>구성 관리

| 보안 컨트롤 | 예/아니요 | 메모| 문서화 |
|---|---|--|--|
| 구성 관리 지원(구성 버전 관리 등)| 예 | |  |

## <a name="next-steps"></a>다음 단계

- [Azure 서비스의 기본 제공 보안 컨트롤](../security/fundamentals/security-controls.md)에 대해 자세히 알아봅니다.
