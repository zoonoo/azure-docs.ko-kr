---
title: Azure 이벤트 허브에 대한 보안 제어
description: 이 문서에서는 Azure Event Hubs(네트워크, ID, 데이터 보호 등)를 평가하기 위한 보안 컨트롤의 검사 목록을 제공합니다.
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 0769e88eb72b5b347dd9ebf4b1634501ca54098e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76309509"
---
# <a name="security-controls-for-azure-event-hubs"></a>Azure 이벤트 허브에 대한 보안 제어

이 문서에서는 Azure 이벤트 허브에 기본 제공 된 보안 컨트롤을 문서화 합니다.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 메모 | 설명서 |
|---|---|--|--|
| 서비스 엔드포인트 지원| yes |  |  |
| VNet 주입 지원| 예 | |  |
| 네트워크 격리 및 방화벽 지원| yes |  |  |
| 강제 터널링 지원| 예 |  |  |

## <a name="monitoring--logging"></a>모니터링 & 로깅

| 보안 제어 | 예/아니요 | 메모| 설명서 |
|---|---|--|--|
| Azure 모니터링 지원(로그 분석, 앱 인사이트 등)| yes | |  |
| 제어 및 관리 평면 로깅 및 감사| yes |  |  |
| 데이터 평면 로깅 및 감사| yes |   |  |

## <a name="identity"></a>Identity

| 보안 제어 | 예/아니요 | 메모| 설명서 |
|---|---|--|--|
| 인증| yes | | [Azure Event Hubs에 대한 액세스 권한 부여,](authorize-access-event-hubs.md) [Azure Active Directory를 사용하여 이벤트 허브 리소스에](authorize-access-azure-active-directory.md)대한 액세스 권한 부여, 공유 액세스 서명을 사용하여 이벤트 허브 [리소스에 대한 액세스 권한 부여](authorize-access-shared-access-signature.md) |
| 권한 부여|  yes | | [Azure Active Directory를 사용하여 관리되는 ID를 인증하여 이벤트 허브 리소스에 액세스하고,](authenticate-managed-identity.md) [Azure Active Directory를 사용하여 응용 프로그램을 인증하여 이벤트 허브 리소스에 액세스하고,](authenticate-application.md) [공유 액세스 서명(SAS)을 사용하여 이벤트 허브 리소스에 대한 액세스를 인증합니다.](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 메모 | 설명서 |
|---|---|--|--|
| 미사용 서버 측 암호화: Microsoft에서 관리하는 키 |  yes | |  |
| 미사용 서버 측 암호화: 고객 관리 키(BYOK) | 예. 전용 클러스터에서 사용할 수 있습니다. | Azure KeyVault에서 고객 관리 키를 사용하여 미사용 이벤트 허브의 데이터를 암호화할 수 있습니다. | [Azure 포털을 사용하여 미사용 Azure Event Hubs 데이터를 암호화하기 위한 고객 관리 키 구성](configure-customer-managed-key.md) |
| 열 수준 암호화(Azure 데이터 서비스)| 해당 없음 | |  |
| 전송 중 암호화(예: 익스프레스루트 암호화, VNet 암호화 및 VNet-VNet 암호화)| yes | |  |
| API 호출 암호화| yes |  |  |

## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 메모| 설명서 |
|---|---|--|--|
| 구성 관리 지원(구성 버전 관리 등)| yes | |  |

## <a name="next-steps"></a>다음 단계

- Azure 서비스 [에서 기본 제공 보안 컨트롤에](../security/fundamentals/security-controls.md)대해 자세히 알아봅니다.
