---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: c0ba8e76e069bf9dc1c96aecdc670699c32b3c96
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945752"
---
## <a name="object-model"></a>개체 모델

사용자 액세스 토큰을 사용하면 애플리케이션 사용자에게 Azure Communication Services 리소스에 직접 연결하는 권한을 부여할 수 있습니다. 서버에서 이러한 토큰을 생성하고 클라이언트 애플리케이션에 다시 전달한 다음, Communication Services 클라이언트 라이브러리를 초기화하는 데 사용합니다. 이를 위해서는 사용자를 인증하고 사용자 액세스 토큰을 발급할 수 있는 신뢰할 수 있는 서비스 엔드포인트를 빌드해야 합니다. 이 서비스는 애플리케이션 사용자 ID와 Azure Communication Services 사용자 간에 지속적 매핑을 유지해야 합니다.

CommunicationIdentityClient 클래스는 모든 사용자 및 액세스 토큰 관련 기능을 제공합니다. 연결 문자열을 사용하여 인스턴스화한 다음, 이를 사용하여 사용자를 관리하고 액세스 토큰을 발급합니다.

> [!WARNING]
> 토큰은 사용자의 리소스에 대한 액세스 권한을 부여하므로 중요한 데이터입니다. 따라서 토큰이 손상되지 않도록 보호하는 것이 중요합니다. 애플리케이션의 인증된 사용자에게만 액세스 토큰을 발급하는 신뢰할 수 있는 서비스 엔드포인트를 만들어야 합니다. 백업 저장소에 사용자 액세스 토큰을 캐시하는 경우 암호화를 사용하는 것이 좋습니다.

### <a name="access-token-scopes"></a>액세스 토큰 범위

범위를 사용하면 사용자 액세스 토큰에서 권한을 부여할 수 있는 정확한 Azure Communications Services 기능을 지정할 수 있습니다. 범위는 개별 사용자 액세스 토큰에 적용됩니다. Azure Communication Services는 사용자 액세스 토큰에 대해 다음과 같은 범위를 지원합니다.

| 이름   | 설명                                                                         |
| ------ | ----------------------------------------------------------------------------------- |
| `Chat` | 채팅에 참여할 수 있는 기능 부여                                         |
| `VoIP` | 통화 클라이언트 라이브러리*를 사용하여 VOIP 호출을 수행하고 받을 수 있는 기능 부여 |
| `Pstn` | 통화 클라이언트 라이브러리*를 사용하여 PSTN 호출을 수행할 수 있는 기능 부여           |

\* 기능이 아직 지원되지 않으며 곧 제공될 예정입니다.

특정 기능에 액세스하는 사용자의 기능을 제거하려는 경우 먼저 제한된 범위의 세트를 사용하여 새 토큰을 발급하기 전에 원치 않는 범위를 포함할 수 있는 [기존 액세스 토큰을 취소](#revoke-user-access-tokens)해야 합니다.
