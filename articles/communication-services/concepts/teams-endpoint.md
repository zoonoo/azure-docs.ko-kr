---
title: 사용자 지정 Teams 엔드포인트
titleSuffix: An Azure Communication Services concept document
description: 사용자 지정 Teams 엔드포인트 빌드
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: acbd6a332d74a9b244f34230ac4b4eb591a1ab2d
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108066"
---
# <a name="custom-teams-endpoint"></a>사용자 지정 Teams 엔드포인트

> [!IMPORTANT]
> 사용자 지정 Teams 엔드포인트 환경을 사용하거나 사용하지 않도록 설정하려면 [이 양식](https://forms.office.com/r/B8p5KqCH19)을 작성합니다.

Azure Communication Services를 사용하여 Microsoft Teams 클라이언트 또는 다른 사용자 지정 Teams 엔드포인트와 통신하는 사용자 지정 Teams 엔드포인트를 빌드할 수 있습니다. 사용자 지정 Teams 엔드포인트를 사용하면 Teams 사용자에 대한 음성, 비디오, 채팅 및 화면 공유 환경을 사용자 지정할 수 있습니다.

Azure Communication Services ID SDK를 사용하여 Teams의 액세스 토큰에 대한 AAD 사용자 토큰을 교환할 수 있습니다. 다음 다이어그램에서는 Contoso 회사의 고객인 Fabrikam이 다중 테넌트 사용 사례를 보여줍니다.

## <a name="calling"></a>호출 

음성, 비디오 및 화면 공유 기능은 Azure Communication Services Calling SDK를 통해 제공됩니다. 다음 다이어그램은 통화 환경을 사용자 지정 Teams 엔드포인트와 통합할 때 따라야 하는 프로세스의 개요를 보여줍니다.

![사용자 지정 Teams 엔드포인트 환경을 위한 통화 기능을 사용하도록 설정하는 프로세스](./media/teams-identities/teams-identity-calling-overview.png)

## <a name="chat"></a>채팅

사용자 지정 Teams 엔드포인트를 사용하여 필요에 따라 Graph API를 통해 채팅 기능을 통합할 수도 있습니다. [설명서](https://docs.microsoft.com/graph/api/channel-post-messages)에서 Graph API에 대해 자세히 알아보세요. 


![사용자 지정 Teams 엔드포인트 환경을 위한 채팅 기능을 사용하도록 설정하는 프로세스](./media/teams-identities/teams-identity-chat-overview.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Teams 액세스 토큰 발급](../quickstarts/manage-teams-identity.md)

다음 문서는 사용자에게 유용할 수 있습니다.

- [Teams 상호 운용성](./teams-interop.md)에 대해 알아보기
