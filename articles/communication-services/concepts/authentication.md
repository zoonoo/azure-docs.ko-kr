---
title: Azure 통신 서비스에 인증
titleSuffix: An Azure Communication Services concept document
description: 응용 프로그램 또는 서비스가 통신 서비스에 인증할 수 있는 다양 한 방법에 대해 알아봅니다.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 9edfb63f5ce43ed325b4c4a1fa67e0e9ca52dc89
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110868"
---
# <a name="authenticate-to-azure-communication-services"></a>Azure 통신 서비스에 인증

모든 클라이언트와 Azure 통신 서비스 간의 상호 작용을 인증 해야 합니다. 일반적인 아키텍처에서는 인증에 사용 되는 [클라이언트 및 서버 아키텍처](./client-and-server-architecture.md), *액세스 키* 또는 관리 되는 *id* 를 참조 하십시오.

다른 유형의 인증은 사용자 *액세스 토큰* 을 사용 하 여 사용자 참여가 필요한 서비스에 대해 인증 합니다. 예를 들어 채팅 또는 호출 서비스는 *사용자 액세스 토큰* 을 활용 하 여 사용자가 스레드에 추가 되 고 서로 대화를 할 수 있도록 합니다.

## <a name="authentication-options"></a>인증 옵션

다음 표에서는 Azure Communication Services Sdk 및 해당 인증 옵션을 보여 줍니다.

| SDK)    | 인증 옵션                               |
| ----------------- | ----------------------------------------------------|
| ID          | 액세스 키 또는 관리 Id                      |
| sms               | 액세스 키 또는 관리 Id                      |
| 전화 번호     | 액세스 키 또는 관리 Id                      |
| 호출           | 사용자 액세스 토큰                                   |
| 채팅              | 사용자 액세스 토큰                                   |

각 권한 부여 옵션에 대 한 간략 한 설명은 다음과 같습니다.

### <a name="access-key"></a>선택키

액세스 키 인증은 신뢰할 수 있는 서비스 환경에서 실행 되는 서비스 응용 프로그램에 적합 합니다. 액세스 키는 Azure Communication Services 포털에서 찾을 수 있습니다. 서비스 응용 프로그램은이를 자격 증명으로 사용 하 여 해당 Sdk를 초기화 합니다. [ID SDK](../quickstarts/access-tokens.md)에 사용 되는 방법의 예를 참조 하세요. 

액세스 키는 리소스의 연결 문자열에 포함 되기 때문에 연결 문자열을 사용 하는 인증은 액세스 키가 있는 인증과 동일 합니다.

액세스 키를 사용 하 여 ACS Api를 수동으로 호출 하려는 경우 요청에 서명 해야 합니다. 요청에 서명 하는 방법은 [자습서](../tutorials/hmac-header-tutorial.md)에서 자세히 설명 합니다.

### <a name="managed-identity"></a>관리 ID

관리 되는 Id는 다른 권한 부여 옵션을 통해 뛰어난 보안과 사용 편의성을 제공 합니다. 예를 들어 Azure AD를 사용 하 여 액세스 키 권한 부여와 마찬가지로 코드 내에 계정 액세스 키를 저장 하지 않아도 됩니다. 통신 서비스 응용 프로그램에서 액세스 키 권한 부여를 계속 사용할 수 있지만 가능 하면 Azure AD로 이동 하는 것이 좋습니다. 

관리 id를 설정 하려면 [Azure CLI에서 등록 된 응용 프로그램을 만듭니다](../quickstarts/managed-identity-from-cli.md). 그런 다음, 끝점과 자격 증명을 사용 하 여 Sdk를 인증할 수 있습니다. [관리 id](../quickstarts/managed-identity.md) 를 사용 하는 방법의 예를 참조 하세요.

### <a name="user-access-tokens"></a>사용자 액세스 토큰

사용자 액세스 토큰은 Id sdk를 사용 하 여 생성 되며 Id SDK에서 만든 사용자와 연결 됩니다. [사용자를 만들고 토큰을 생성](../quickstarts/access-tokens.md)하는 방법의 예를 참조 하세요. 그런 다음 사용자 액세스 토큰은 채팅 또는 호출 SDK에서 대화에 추가 된 참가자를 인증 하는 데 사용 됩니다. 자세한 내용은 [앱에 채팅 추가](../quickstarts/chat/get-started.md)를 참조 하세요. 사용자 액세스 토큰 인증은 보안 된 Azure 리소스가 아닌 사용자를 인증 하는 데 사용 된다는 것에 대 한 액세스 키 및 관리 되는 id 인증과 다릅니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [통신 서비스 리소스](../quickstarts/create-communication-resource.md) 
>  만들기 및 관리 Azure CLI에서 Azure Active Directory [관리 id 응용 프로그램 만들기](../quickstarts/managed-identity-from-cli.md) 
>  [사용자 액세스 토큰 만들기](../quickstarts/access-tokens.md)

자세한 내용은 다음 문서를 참조하세요.
- [클라이언트 및 서버 아키텍처에 대한 자세한 정보](../concepts/client-and-server-architecture.md)
